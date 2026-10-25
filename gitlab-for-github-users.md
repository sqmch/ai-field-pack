# GitLab for a GitHub user

> Generic edition, built 2026-10-26: company-specific and personal content removed. See the pack README.

Written 2026-10-21 for engineers who know GitHub and are starting on GitLab. It covers
what's different and what matters for the factory. GitLab's own guide is the best
next read: [Migrate from GitHub Actions](https://docs.gitlab.com/ci/migration/github_actions/).

## The words

| GitHub | GitLab |
|---|---|
| Organization | Top-level group |
| (nothing) | Subgroup: a group inside a group, nested as deep as needed |
| Repository | Project: a repo plus its issues, merge requests, CI, registry and settings |
| Pull request #12 | Merge request !12 (issues are still #12) |
| Draft pull request | Draft merge request (title starts with `Draft:`) |
| Actions | GitLab CI/CD |
| `.github/workflows/*.yml` | One `.gitlab-ci.yml` at the repo root, which can include other files |
| Workflow run | Pipeline |
| Job with steps | Job with a `script:` list; there are no separate steps |
| `uses: owner/action@v1` | `include: component: ...@1`, from the CI/CD Catalog |
| Reusable workflow | `include:` of a local file, another project's file, or a component |
| Marketplace | CI/CD Catalog |
| Secrets | CI/CD variables, which can be masked, hidden and protected |
| `GITHUB_TOKEN` | `CI_JOB_TOKEN` |
| `gh` | `glab` |
| Branch protection rules | Protected branches |
| GitHub App acting as a bot | A service account (on GitLab.com, available on Free since 18.11, up to 100 per top-level group), or a project or group access token, which creates a bot user and needs a paid tier on GitLab.com |

## Groups, and how settings flow down

A top-level group holds subgroups and projects. Members, CI/CD variables, runners and
many settings set on a group apply to everything below it. That's why the factory's
shared pieces sit at group level: set a runner or a key once, and every project
under the group can use it. Only group Owners can manage group-level CI/CD variables;
project variables need Maintainer or Owner.

## Roles

In the order GitLab lists them: Minimal Access (paid tiers only), Guest, Planner,
Reporter, Security Manager (a beta role, off by default), Developer, Maintainer,
Owner. For CI and the factory, three matter:

- **Developer** can push to unprotected branches, open merge requests and run
  pipelines. Because the pipeline config comes from the branch being tested, a
  Developer can also change the jobs in their own merge request's pipeline.
- **Maintainer** can change project settings, CI/CD variables and protected branches.
- **Owner** administers the group.

Give bot users the lowest role their job needs, and below the role allowed to merge.

## Merge requests

- `!42` is merge request 42 in the same project. `#42` is an issue.
- A `Draft:` title blocks merging.
- **Approvals.** On Free, approvals are optional. Required approval rules, and
  approval by code owners, need Premium.
- **"Pipelines must succeed"** is a project setting that blocks merging on a red
  pipeline.
- **Auto-merge** merges once all merge checks pass, including the pipeline.
- **Quick actions** are commands typed in a comment, such as `/assign me`, `/label`,
  `/approve` or `/draft`. GitLab runs them, including in comments posted through the
  API. That's why text from a model must never be posted to a merge request as is.
- **Push options** let you open a merge request from the command line: `git push -o merge_request.create -o merge_request.target=main origin HEAD`.

## Pipelines, translated

- **Triggers.** GitHub's `on:` becomes rules. `workflow: rules:` decides whether a
  pipeline runs at all, and each job's `rules:` decides whether that job runs. Rules
  test predefined variables, for example
  `$CI_PIPELINE_SOURCE == "merge_request_event"`.
- **Two kinds of pipeline.** A branch pipeline runs for a push to a branch. A merge
  request pipeline runs for a merge request, which is closest to GitHub's
  `pull_request`. Rules decide which kind each job joins.
- **Order.** `stages:` run in order. `needs:` lets a job start as soon as the jobs it
  names finish, or at once with `needs: []`.
- **Other keywords you'll meet:** `allow_failure:` (a red job that doesn't fail the
  pipeline), `artifacts:` (files a job keeps), `cache:` (speeds up later jobs),
  `image:` (the container a job runs in).
- **Where the config comes from.** The branch being tested. An MR can change its own
  pipeline, which matters for any review agent job.
- **Protected variables** only reach pipelines on protected branches and tags by
  default, so a merge request pipeline from a feature branch doesn't get them.
  GitLab has an option that lets merge request pipelines use them under certain
  conditions.
- **Check before you push** with the pipeline editor's validation (Build > Pipeline
  editor).

## GitLab.com or self-managed

- **GitLab.com:** GitLab runs the service, and you can use its hosted runners, paid
  in compute minutes. The monthly allowance is 400 minutes on Free, 10,000 on Premium
  and 50,000 on Ultimate (`knowledge/02` section 2.1).
- **Self-managed:** the company runs GitLab itself and brings its own runners;
  hosted runners don't exist there. Check which one your company runs.

## Access from the work laptop (checked 2026-10-24)

- **SSH or HTTPS.** An admin can allow both, only SSH or only HTTP(S). With only one,
  the project's **Code** button offers only that URL, so the button shows what your company
  allows. GitLab recommends SSH, with an ED25519 key added under Edit profile >
  Access > SSH keys. Test with `ssh -T git@<host>`.
- **HTTPS needs a token, not your password,** when 2FA or SSO is on: any non-empty
  username, and a personal access token (`read_repository`, `write_repository`) as
  the password. Git Credential Manager comes with Git for Windows. It does OAuth with
  a self-managed GitLab only after someone registers an OAuth app and sets
  `gitLabDevClientId` and `gitLabDevClientSecret` for the host.
- **`glab`.** `glab auth login --hostname <host>` with a personal access token
  (`api`, `write_repository`). It stores the token in the Windows keyring.
  `--setup-git-credential-helper` lets git use the same login over HTTPS, so one
  login covers both.
- **Token rules.** New tokens expire within 365 days by default. Fine-grained tokens
  are GA from GitLab 19.2; legacy tokens keep working unless an admin enforces
  fine-grained ones.
- **Claude Code** uses whatever git and `glab` credentials the laptop has, through its
  shell [inferred]. GitLab's MCP server (GA 19.5, all tiers) is the other route:
  `claude mcp add -s user --transport http GitLab https://<host>/api/v4/mcp`, then
  `/mcp` to sign in with OAuth. It refuses legacy tokens and has write tools,
  including one that merges. Branch protection on the server is the real guard.
  Claude Code's own `Bash(git push *)` rules miss variants such as `git -C . push`.

## Starting on a repo

1. Find the project through its group, and check your role on its Members page.
2. Read the README, any CONTRIBUTING file and `.gitlab-ci.yml`. The branch rules and
   the merge settings (method, squash, "pipelines must succeed") decide how your MR
   lands.
3. Clone with the URL from the **Code** button. Create a branch.
4. Commit. If your company links GitLab to Jira, put the issue key (`PROJ-123`) in commit
   messages and the MR title. The Jira issues integration links those; branch names
   only show in Jira's development panel, which needs the GitLab for Jira app.
5. Push. GitLab prints a link to open an MR. Or use
   `git push -o merge_request.create -o merge_request.target=main`, or
   `glab mr create`.
6. In the MR: describe it, add reviewers, keep the `Draft:` prefix until it's ready.
   The pipeline runs on it. Merge with the project's method; tick "Delete source
   branch".

## Sources

- [Migrate from GitHub Actions](https://docs.gitlab.com/ci/migration/github_actions/)
- [Service accounts](https://docs.gitlab.com/user/profile/service_accounts/) and [protected branches](https://docs.gitlab.com/user/project/repository/branches/protected/)
- [Roles and permissions](https://docs.gitlab.com/user/permissions/)
- [CI/CD YAML syntax](https://docs.gitlab.com/ci/yaml/)
- [CI/CD variables](https://docs.gitlab.com/ci/variables/)
- [Quick actions](https://docs.gitlab.com/user/project/quick_actions/) and the [Notes API](https://docs.gitlab.com/api/notes/)
- [Merge request approvals](https://docs.gitlab.com/user/project/merge_requests/approvals/)
- `knowledge/02` sections 2.1 to 2.10
- Access, read 2026-10-24: [Visibility and access controls](https://docs.gitlab.com/administration/settings/visibility_and_access_controls/), [Clone](https://docs.gitlab.com/topics/git/clone/), [SSH keys](https://docs.gitlab.com/user/ssh/), [Personal access tokens](https://docs.gitlab.com/user/profile/personal_access_tokens/), [Fine-grained tokens](https://docs.gitlab.com/auth/tokens/fine_grained_access_tokens/), [GCM and GitLab](https://github.com/git-ecosystem/git-credential-manager/blob/main/docs/gitlab.md), [glab auth login](https://docs.gitlab.com/cli/auth/login/), [glab authentication](https://docs.gitlab.com/cli/authentication/), [GitLab MCP server](https://docs.gitlab.com/user/model_context_protocol/mcp_server/), [Claude Code permissions](https://code.claude.com/docs/en/permissions)
- Workflow, read 2026-10-24: [Merge](https://docs.gitlab.com/topics/git/merge/), [Merge methods](https://docs.gitlab.com/user/project/merge_requests/methods/), [Merge requests](https://docs.gitlab.com/user/project/merge_requests/), [Jira integrations](https://docs.gitlab.com/integration/jira/)
