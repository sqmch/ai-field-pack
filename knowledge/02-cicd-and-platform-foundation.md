# CI/CD and platform foundation

> Generic edition, built 2026-10-26: company-specific and personal content removed. See the pack README. Sections removed in this edition are omitted, and the numbering is kept so pointers resolve.

Written 2026-10-15. Scope: the CI/CD and platform layer an agentic development
factory runs on, centred on GitLab. GitLab facts were read on docs.gitlab.com and
about.gitlab.com on 2026-10-14 and 2026-10-15 unless a source says otherwise. GitLab
ships a release every month and moves features between tiers, so re-check tiers and
versions before quoting them.

Version context: GitLab 19.4 came out on 17 September 2026 [1]. 19.5 is scheduled for
15 October 2026 [3]. On 15 October its release page was still titled "Upcoming
release", while saying the features are already live on GitLab.com; the docs list the
GitLab MCP server and GitLab Secrets Manager as generally available in 19.5 [2]
[verified 2026-10-15]. Self-Managed availability of 19.5 features is only just landing.

Labels: [verified] read on an official vendor or project page; [secondary] from a
third party; [inferred] my reading, may be wrong; [not re-checked] known from earlier
reading of official docs but not opened for this file. Numbers in square brackets
point to the sources at the end. Tiers are GitLab's Free, Premium and Ultimate.
Offerings are GitLab.com (multi-tenant SaaS), Self-Managed and Dedicated
(single-tenant SaaS).

Out of scope here, covered elsewhere in this folder: factory models and agent tools
(`01-agentic-factory-state-of-the-art.md`), regulation in depth
(`03-regulation-and-standards.md`), agent threats and hardening
(`04-ai-security-and-hardening.md`), AI plans, gateways and key custody
(`05-controlled-ai-architecture.md`), and policy and onboarding
(`06-policy-operating-model-and-enablement.md`).

## 1. What matters most

1. **The GitLab tier decides which guardrails can be enforced**. Required
   approvals, Code Owners, push rules, protected environments and merge trains need
   Premium. Cross-project enforcement (pipeline execution, scan execution and merge
   request approval policies), built-in DORA metrics, dependency scanning and secret
   push protection need Ultimate [17][19][20][23][25][38][39][45]. Check the tier
   first. The plan forks on the answer.
2. **A shared component library is the cheapest way to standardise**. CI/CD
   components take typed inputs, carry semantic versions and publish to a private
   catalog, on every tier [4][5]. One `python-checks` component used by every repo
   turns "each team does it differently" into "teams opt out on purpose".
3. **Without Ultimate, enforcement is review plus visibility**. Premium can
   require approvals and Code Owner sign-off on `.gitlab-ci.yml`. It cannot inject
   jobs into a pipeline a team controls [19][23]. Plan for "the default is good and
   drift is visible", not "everything is forced".
4. **Merge controls make agent output trustworthy, not the agent**. Protected
   default branch, merge request required, pipeline must pass, author cannot approve,
   approvals reset on new commits [16][18]. GitLab's own Duo code review approval
   does not count toward required approvals [81], which matches Pipedrive's 2025
   report that human review stays [89].
5. **GitLab's agents run as CI jobs on runners**. Duo flows and external
   agents (Claude Code, Codex) execute in CI/CD on runners tagged `gitlab--duo`, under
   a composite identity with an OAuth token scoped to `ai_workflows` [78][79][80].
   Runner isolation and network egress are agent controls as much as CI controls.
6. **Composite identity answers "who authored this change"**. Agent commits show
   the service account. The merge request is attributed to the human who triggered
   the flow, so "author cannot approve" still means something [79].
7. **Long-lived secrets in CI variables are the likely weak spot** [inferred].
   GitLab's own docs say masking is not a guaranteed protection [28]. OIDC
   `id_tokens`, on every tier, replace stored cloud keys with tokens bound to project,
   ref and environment claims [31].
8. **Runners are a trust boundary**. Shared, non-ephemeral, privileged or shell
   runners let anyone with the Developer role reach other projects' code and secrets
   [11]. Deploys belong on protected runners. Everything else runs in throwaway
   containers.
9. **Measure before changing anything**. DORA now uses five metrics [48].
   GitLab's built-in DORA view needs Ultimate [45]. The Deployments API, on Free,
   returns enough data to compute deployment frequency and lead time yourself [47]. A
   baseline taken first turns later claims into a before-and-after comparison.
10. **The realistic supply chain target is SLSA Build L2, SBOMs and pinned
    dependencies**. GitLab's runner provenance covers L1 and L2. Its L3
    attestations are an experiment for public GitLab.com projects only [57][58]. The
    EU Cyber Resilience Act's reporting duty has applied since 11 September 2026 [62].
11. **Python gets the same fast path as everything else**. uv lockfiles, ruff
    (which lints notebooks by default), mypy and pytest. GitLab's SBOM-based
    dependency scanner reads `uv.lock` [40][65][66]. Data scientists add notebook
    gates on top: nbQA, Jupytext and papermill smoke runs [69][70][71].
12. **Models are build artefacts**. GitLab's model registry is on Free and speaks
    the MLflow client protocol, so a CI job can register a model version linked to the
    pipeline that produced it [72][73].
13. **At 50 to 100 people, golden paths beat portals**. A repo template, a
    component library and a short guide per role cover most of what Backstage
    offers, without a portal application to run [54] [inferred].
14. **Every standard should ship as four artefacts**. A component or template, an
    ADR, a one-page how-to, and a check that shows who has adopted it. Onboarding then
    reuses the same pages [inferred].

## 2. GitLab CI/CD in depth

### 2.1 Tiers at a glance

What the company can use depends on the tier. All rows are [verified] against the
cited page except where marked.

| Capability | Free | Premium | Ultimate | Source |
|---|---|---|---|---|
| CI/CD components, inputs, private catalog | Yes | Yes | Yes | [4][5] |
| Component usage details (who uses which version) | No | Yes | Yes | [4] |
| Parent-child and multi-project pipelines | Yes | Yes | Yes | [6] |
| Artifacts across projects (`needs:project`) | No | Yes | Yes | [6] |
| Merged results pipelines, merge trains | No | Yes | Yes | [21][22] |
| Protected branches by role | Yes | Yes | Yes | [16] |
| Protected branches for named users and groups, Code Owner approval | No | Yes | Yes | [16][19] |
| Merge request approvals | Optional, not blocking | Required, with rules | Plus security approval policies | [17] |
| Approval settings (author and committers cannot approve, reset on push) | No | Yes | Yes | [18] |
| Push rules | No | Yes | Yes | [20] |
| Protected environments, deployment approvals | No | Yes | Yes | [14][15] |
| Pipeline execution, scan execution, merge request approval policies | No | No | Yes | [23][24][25] |
| Compliance frameworks as labels | No | Yes | Yes | [26] |
| Compliance requirements, controls, SOC 2 and ISO templates | No | No | Yes | [26] |
| `id_tokens` (OIDC) | Yes | Yes | Yes | [31] |
| Fine-grained job token permissions | Yes | Yes | Yes | [33] |
| External secrets (Vault, cloud secret managers) | No | Yes | Yes | [29] |
| GitLab Secrets Manager (add-on, consumes credits) | No | Yes | Yes | [30] |
| Project access tokens on GitLab.com | No | Yes | Yes | [34] |
| Service accounts | Yes (100 per top-level group on .com) | Yes | Yes | [35] |
| SAST, pipeline secret detection, IaC and container scanning | Yes, JSON artifacts only | Same as Free | Plus MR widget, vulnerability report | [36][37][41][42] |
| Secret push protection, dependency scanning, Advanced SAST | No | No | Yes | [36][38][39] |
| Group and project audit events | No | Yes | Yes | [43] |
| Audit event streaming | No | No | Yes | [44] |
| Value stream analytics | Project default stream only | Yes | Yes | [46] |
| DORA metrics | No | No | Yes | [45] |
| Feature flags per project on GitLab.com | 50 | 150 | 200 | [52] |
| Model registry, MLflow client compatibility | Yes | Yes | Yes | [72][73] |
| Duo Agent Platform | Some flows with purchased credits | Yes | Yes | [75][81][82] |
| GitLab MCP server | Yes | Yes | Yes | [83] |
| Hosted runner compute minutes per month (GitLab.com) | 400 | 10,000 | 50,000 | [9] |
| Price per user per month (GitLab.com, list) | $0, max 5 users | $29 billed annually | Not published | [9] |

Two notes on that table. Premium and Ultimate on GitLab.com currently include $12 and
$24 per user per month of GitLab Credits, labelled a limited-time promotion and marked
"GitLab.com plans only"; included credits belong to each user and cannot be shared;
extra credits cost $1 each [9] [verified 2026-10-15]. The Free tier's 5-user cap per
top-level group [9] means a company of 50 to 100 people is almost certainly on a paid
tier if it uses GitLab.com for real work [inferred].

### 2.2 Pipeline anatomy

A pipeline is a set of jobs. Each job runs a script in a container (or shell) on a
runner. Jobs sit in stages, and by default a stage starts when the previous one
finishes. `needs` overrides that and turns the pipeline into a graph, so a job starts
as soon as the jobs it depends on finish. `rules` decide whether a job is added to a
pipeline. `workflow:rules` decide whether the pipeline exists at all.

The most common mistake is duplicate pipelines: one for the branch push and one for
the merge request. The standard fix is a `workflow` block that runs merge request
pipelines when a merge request is open and branch pipelines otherwise [verified:
GitLab's `workflow` docs, https://docs.gitlab.com/ci/yaml/workflow/].

```yaml
workflow:
  rules:
    - if: $CI_PIPELINE_SOURCE == "merge_request_event"
    - if: $CI_COMMIT_BRANCH && $CI_OPEN_MERGE_REQUESTS
      when: never
    - if: $CI_COMMIT_BRANCH
    - if: $CI_COMMIT_TAG
  auto_cancel:
    on_new_commit: interruptible   # cancel superseded pipelines

stages: [check, test, build, deploy]

default:
  interruptible: true              # jobs may be cancelled by a newer push

lint:
  stage: check
  script: uv run ruff check .

unit-tests:
  stage: test
  needs: []                        # start immediately, do not wait for lint
  script: uv run pytest

build-image:
  stage: build
  needs: [unit-tests]
  rules:
    - if: $CI_COMMIT_BRANCH == $CI_DEFAULT_BRANCH
    - changes: [Dockerfile, uv.lock]   # also build in MRs that touch these files

deploy-prod:
  stage: deploy
  interruptible: false             # never cancel a deploy halfway
  rules:
    - if: $CI_COMMIT_BRANCH == $CI_DEFAULT_BRANCH
  script: ./deploy.sh production
```

Things worth knowing early:

- `$CI_PIPELINE_SOURCE` tells you why the pipeline exists: `push`,
  `merge_request_event`, `schedule`, `web` (run button), `api`, `trigger`,
  `parent_pipeline` (child pipeline) and `pipeline` (multi-project) [6].
- Since GitLab 17.7, new projects in new GitLab.com namespaces default to
  `no_one_allowed` for pipeline variables (older projects keep their setting), so nobody can override variables when starting a pipeline by
  hand or by API [28]. Keep it that way. It closes a route for skipping checks.
- `extends` and `!reference` share configuration inside one file. Components (2.3)
  share it across repos.

### 2.3 Reuse: include, templates, components and inputs

There are five kinds of `include`: `local` (same repo), `project` (a file in another
project at a ref), `remote` (a URL), `template` (GitLab's built-in templates) and
`component` [4]. Templates are GitLab-maintained YAML files, such as
`Jobs/SAST.gitlab-ci.yml`. Components are the newer unit and the one to standardise
on.

**What a component is.** A YAML file in a "component project" that declares typed
inputs in a `spec` header, followed by the jobs. Generally available since 17.0, on
all tiers and offerings [4].

**Layout.** One project, for example `acme/platform/ci-components` (`acme` stands for the
company's GitLab group), with a `templates/`
directory holding one file per component (or a folder with `template.yml`), a
`README.md`, a `LICENSE.md` and its own `.gitlab-ci.yml` that tests the components and
releases them. Up to 100 components per project since 18.5 [4].

A component for the company's Python repos might look like this (a sketch for
illustration, not run):

```yaml
# templates/python-checks.yml
spec:
  inputs:
    python_version:
      default: "3.12"
      options: ["3.11", "3.12", "3.13"]
    src_dir:
      default: src
    stage:
      default: test
---
.acme-python:
  image: ghcr.io/astral-sh/uv:0.13.1-python$[[ inputs.python_version ]]-trixie-slim
  variables:
    UV_CACHE_DIR: $CI_PROJECT_DIR/.uv-cache
    UV_LINK_MODE: copy
  cache:
    key:
      files: [uv.lock]
    paths: [.uv-cache]
  before_script:
    - uv sync --locked          # fails if uv.lock is out of date with pyproject.toml
  after_script:
    - uv cache prune --ci

acme-ruff:
  extends: .acme-python
  stage: $[[ inputs.stage ]]
  script:
    - uv run ruff check .
    - uv run ruff format --check .

acme-mypy:
  extends: .acme-python
  stage: $[[ inputs.stage ]]
  script:
    - uv run mypy $[[ inputs.src_dir ]]

acme-pytest:
  extends: .acme-python
  stage: $[[ inputs.stage ]]
  script:
    - uv run pytest --junitxml=report.xml --cov=$[[ inputs.src_dir ]] --cov-report=xml:coverage.xml
  artifacts:
    when: always
    reports:
      junit: report.xml
      coverage_report:
        coverage_format: cobertura
        path: coverage.xml
```

A consuming repo then has a short `.gitlab-ci.yml`:

```yaml
include:
  - component: $CI_SERVER_FQDN/acme/platform/ci-components/python-checks@1.2.0
    inputs:
      python_version: "3.12"
```

**Publishing.** Mark the project as a CI/CD Catalog project, then create a semantic
version tag. A release job using the `release` keyword publishes that version. The
Releases API does not work for this [4].

**Version pinning.** A component reference can be a commit SHA, a tag, a branch,
`~latest`, or a partial version such as `1` (latest `1.x.x`). GitLab recommends a
release or SHA rather than `~latest` for production [4]. A sensible rule [inferred]:
product repos pin a major version (`@1`) to receive fixes without breaking changes,
and deploy pipelines pin an exact version.

**Inputs versus variables.** Inputs are typed (`string`, `number`, `boolean`,
`array`), validated with `options` or `regex`, and fixed when the pipeline is created.
Variables can change during a run [5]. Since 17.11 the main `.gitlab-ci.yml` can also
declare pipeline-level inputs, up to 20, set from the run button, schedules, the API
or push options. Always give them defaults, because merge request and branch
pipelines cannot receive manual values [5].

**Watch for collisions.** Included configuration merges into the pipeline, so job
names and `extends` targets can clash with the project's own [4]. Prefix component
jobs (`acme-ruff`), as above.

Since 19.0, Premium and Ultimate show component usage details [4]. That is the
adoption dashboard for "who still runs `python-checks@1.0.0`".

### 2.4 Parent-child and multi-project pipelines

**Parent-child.** A job uses `trigger: include:` to start a child pipeline from
another YAML file in the same repo. Used for monorepos (one child per service) and for
pipelines generated by a script. Children can have children, but no deeper than two
levels [6].

**Multi-project.** `trigger: project:` starts a pipeline in another project, for
example "after the shared library releases, run the integration suite in the service
repo" [6].

**Status.** With `strategy: mirror` the trigger job takes the downstream pipeline's
status. The docs prefer it over `strategy: depend`, whose statuses do not always match
[6]. Without either, the parent can pass while the child fails, and a child's reports
do not reach the merge request.

```yaml
# Root .gitlab-ci.yml of a monorepo with two services
ingestion:
  trigger:
    include:
      - local: services/ingestion/.gitlab-ci.yml
    strategy: mirror
  rules:
    - changes:
        paths: [services/ingestion/**/*, libs/**/*]

optimizer:
  trigger:
    include:
      - local: services/optimizer/.gitlab-ci.yml
    strategy: mirror
  rules:
    - changes:
        paths: [services/optimizer/**/*, libs/**/*]
```

**Dynamic child pipelines.** A job writes YAML to an artifact and a trigger job runs
it (`include: - artifact: generated.yml, job: generate-config`) [6]. Useful when the
set of jobs depends on data, for example one validation job per data connector
[inferred].

### 2.5 Runners

A runner is the agent process that picks up jobs and executes them. The executor
decides where a job runs: in a container, a VM, a Kubernetes pod or directly on the
host.

| Option | What it is | Fits when |
|---|---|---|
| GitLab-hosted (GitLab.com) | Ephemeral VM per job on Google Cloud, `docker+machine` executor, default region `us-east1`. Linux small (2 vCPU, 8 GB) and medium on all tiers, large to 2xlarge (32 vCPU) on Premium and Ultimate [7]. Cost factors from 1 to 12 against the monthly minutes; GPU medium is 7 [8] | Build and test for most repos. Zero maintenance |
| Self-managed, Docker Autoscaler | Runner manager creates cloud VMs through "fleeting" plugins and runs jobs in containers on them [10] | Jobs needing the company's network, data or credentials; heavy ML builds; cost control |
| Self-managed, Kubernetes | One pod per job [10] | The company already runs Kubernetes |
| Shell | Jobs run as the runner user on the host. Maintenance mode [10] | Avoid. Any job can read other projects' checkouts [11] |
| Docker Machine | Deprecated [10] | Do not start new work on it |

Runner security, from GitLab's own guidance [11][12]:

- Privileged containers remove container isolation. If a job needs Docker-in-Docker,
  run it on ephemeral VMs and on dedicated runners limited to protected branches.
  GitLab-hosted `saas-linux-*` runners do run privileged, but each VM serves one job
  [7][11].
- Non-ephemeral runners shared across projects are the highest risk. A malicious job
  can compromise other projects that use the same runner [11].
- A **protected runner** only runs jobs on protected branches and tags [12]. Combine
  it with tags (`deploy`, `prod`) so deploy jobs land only on runners that hold deploy
  access, and nothing else lands there.
- Use `pull_policy: always` on shared runners, run as non-root, drop Linux
  capabilities, segment the network [11].
- Runner 19.4 added `services_cap_add` and `services_cap_drop` for Docker service
  containers and an option to keep cache URLs out of job logs [1].

Hosted runners run in Google Cloud's `us-east1` by default [7]. Jobs that touch
customer data, or test fixtures cut from it, belong on the company's own runners in
an EU region [inferred].

A runner layout for the company [inferred]: GitLab-hosted runners for lint, test and
build; one small self-managed autoscaling group inside the company's cloud for jobs
that need private network access; one protected, tagged runner group for production
deploys; and, once agents run in CI, a separate untrusted pool for agent jobs with no
deploy access and restricted egress (section 7).

### 2.6 Caching and artifacts

| | Cache | Artifacts |
|---|---|---|
| Purpose | Speed. Reuse downloaded dependencies between pipelines | Pass outputs between jobs and keep results |
| Guarantee | Best effort. A job must work with an empty cache | Reliable within the pipeline. Kept until `expire_in` |
| Typical content | `.uv-cache`, `node_modules` | Wheels, images' digests, test reports, coverage, SBOMs |
| Key | `cache:key:files: [uv.lock]` so it changes when the lockfile does | Not keyed |
| Shows in MR | No | `reports:` such as `junit`, `coverage_report`, security reports |

Two security notes. A cache written by an unprotected branch can be read by a
protected branch's job unless caches are separated; GitLab's project setting "Use
separate caches for protected branches" (Settings > CI/CD > General pipelines) is on
by default, so check nobody cleared it [verified, https://docs.gitlab.com/ci/caching/]. And artifacts from
merge request pipelines are produced by code that has not been reviewed yet, so a
deploy job should never consume them [inferred].

### 2.7 Environments, deployments and review apps

An **environment** is a named deploy target (`staging`, `production`,
`review/feature-x`). A job with an `environment:` block creates a **deployment**
record when it runs. Those records are what GitLab's DORA metrics, the Deployments
API and rollbacks use [13][45][47].

Environments carry a deployment tier: development, testing, staging, production or
other. GitLab guesses it from the name, or you set `deployment_tier` [13]. Use the tier
consistently. Group-level protected environments and DORA's deployment frequency key
off it [14][45].

```yaml
deploy-review:
  stage: deploy
  script: ./deploy.sh review "$CI_COMMIT_REF_SLUG"
  environment:
    name: review/$CI_COMMIT_REF_SLUG
    url: https://$CI_COMMIT_REF_SLUG.review.example.internal
    on_stop: stop-review
    auto_stop_in: 3 days
  resource_group: review/$CI_COMMIT_REF_SLUG
  rules:
    - if: $CI_PIPELINE_SOURCE == "merge_request_event"

stop-review:
  stage: deploy
  script: ./deploy.sh teardown "$CI_COMMIT_REF_SLUG"
  environment:
    name: review/$CI_COMMIT_REF_SLUG
    action: stop
  resource_group: review/$CI_COMMIT_REF_SLUG
  rules:
    - if: $CI_PIPELINE_SOURCE == "merge_request_event"
      when: manual
  allow_failure: true

deploy-production:
  stage: deploy
  script: ./deploy.sh production
  environment:
    name: production
    deployment_tier: production
  resource_group: production        # one production deploy at a time
  rules:
    - if: $CI_COMMIT_BRANCH == $CI_DEFAULT_BRANCH
```

- **Review apps** are dynamic environments per merge request. `auto_stop_in` cleans
  them up, roughly hourly [13]. They matter most for frontends and dashboards
  [inferred]. Software that writes setpoints to physical equipment, such as HVAC control
  in buildings, has no meaningful review app; its equivalent is a replay against
  recorded building management system (BMS) data (section 4.6) [inferred].
- **`resource_group`** serialises jobs that share it, so two deploys to production
  never overlap [13].
- **Protected environments** (Premium) restrict who may deploy, by role, user or
  group, and add approval rules [14]. At group level they protect by tier, so one rule
  covers `production` in every project, and subgroups cannot override it [14].
- **Deployment approvals** (Premium) block the deploy job until enough approvers
  agree. The person who triggered the pipeline cannot approve their own deployment
  unless a setting allows it [15]. The job does not need `when: manual` [15].
- **Auto Rollback** (Ultimate) redeploys the last successful deployment when a
  critical alert fires for the environment [13].

### 2.8 Merge controls

These settings decide what reaches the default branch. They matter more than any
pipeline job, because a job can be skipped and a setting cannot.

**Protected branches.** On Free you choose which roles may merge, push, and force
push, with wildcards such as `release/*`. Premium adds named users and groups,
group-level protected branches and Code Owner approval [16]. The baseline for every
active repo: default branch protected, nobody pushes directly, force push off.

**Merge request approvals.** On Free, approvals are optional and do not block. On
Premium, approval rules set how many approvals from whom, and settings can stop the
author and anyone who committed to the merge request from approving, and remove
approvals when new commits arrive [17][18]. Also turn on "pipelines must succeed" and
"all threads must be resolved" merge checks [verified: both work on Free at project
level; group-level merge checks need Premium].

**CODEOWNERS** (Premium). A file at `CODEOWNERS`, `docs/CODEOWNERS` or
`.gitlab/CODEOWNERS` maps paths to owners. With "Require approval from code owners"
set on the protected branch, a matching owner must approve [19]. Sections group rules
and can require more than one approval with `[Section name][2]` [verified: GitLab's
CODEOWNERS syntax reference]. For the factory, the useful rule is that CI configuration and agent
instructions belong to the platform owners:

```text
[CI and platform][1]
.gitlab-ci.yml        @acme/platform
.gitlab/              @acme/platform

[Agent instructions][1]
CLAUDE.md             @acme/platform
AGENTS.md             @acme/platform
.gitlab/duo/          @acme/platform
```

An agent, or a person, can then change product code freely but cannot quietly
weaken the pipeline or rewrite its own instructions [inferred].

**Push rules** (Premium) check every push: reject unverified users, require commit
author emails to match a pattern, require or reject commit message patterns, enforce
branch names, reject unsigned commits, block secret files, cap file size and require
DCO sign-off [20]. "Reject unsigned commits" interacts with agents and service
accounts, so test it before switching it on (section 7.7).

**Merged results pipelines and merge trains** (Premium). A merged results pipeline
tests the merge of source and target, not the source alone [22]. A merge train queues
merge requests and tests each against the ones ahead of it, up to 20 in parallel by
default [21]. Merge trains solve "main breaks even though every merge request was
green", which shows up with many merges per day. At 50 to 100 people they are
probably not needed in the first months; merged results pipelines are worth turning
on [inferred].

### 2.9 Central enforcement with security policies

This is where Ultimate differs most. Policies live in a separate "security policy
project" linked to a group, so project maintainers cannot edit them [23].

**Pipeline execution policies** (Ultimate, GA 17.3) inject jobs into every pipeline of
every linked project, even projects without a `.gitlab-ci.yml` [23]. `inject_policy`
adds jobs and cannot be overridden by the project. `override_project_ci` replaces the
project's configuration. Reserved stages `.pipeline-policy-pre` and
`.pipeline-policy-post` run first and last, and since 18.10 a failed pre stage skips
the rest [23]. By default, policy jobs cannot be skipped with `[skip ci]` [23]. Limit:
five policies per policy project [23]. The docs only show security examples, but the
mechanism injects any CI configuration, so a required lint or licence job works the
same way [inferred].

```yaml
# .gitlab/security-policies/policy.yml in the security policy project
pipeline_execution_policy:
  - name: Baseline checks
    description: Secret detection and the conformance job in every pipeline
    enabled: true
    pipeline_config_strategy: inject_policy
    content:
      include:
        - project: acme/platform/security-policies
          file: baseline-ci.yml
```

**Scan execution policies** (Ultimate) enforce GitLab's own scanners on pipelines or
schedules. Quick to set up, with few settings [24].

**Merge request approval policies** (Ultimate) require approval when scanners find
something, when licences match a deny list, when a dependency is in GitLab's malware
advisories (beta in 19.4; the docs say GA in 19.5), or for any merge request, optionally only those with
unsigned commits [25]. They can also override approval settings and block pushes
and branch unprotection while the policy is active. If a rule cannot be evaluated,
the default is fail closed [25].

```yaml
approval_policy:
  - name: No unsigned commits on main
    enabled: true
    rules:
      - type: any_merge_request
        branches: [main]
        commits: unsigned
    actions:
      - type: require_approval
        approvals_required: 1
        role_approvers: [maintainer]
    approval_settings:
      prevent_approval_by_author: true
      prevent_approval_by_commit_author: true
      remove_approvals_with_new_commit: true
    fallback_behavior:
      fail: closed
```

**Compliance frameworks** are labels on projects (Premium). On Ultimate a framework
can carry requirements with up to five controls each, checked continuously, and since
19.1 ships templates for SOC 2, ISO 27001:2022 and FedRAMP [26]. The older
"compliance pipelines" feature (Ultimate) is deprecated since 17.3 in favour of
pipeline execution policies. The page's banner says removal in 20.0 and a migration
section still says 19.0; the page still exists in 19.x, so 20.0 is the current target
[27] [verified 2026-10-15].

**Without Ultimate**, the closest substitutes [inferred unless marked]:

- Code Owner approval on `.gitlab-ci.yml` and agent instruction files (Premium) [19].
- A project setting that loads the CI configuration from a file in another project
  (`.gitlab-ci.yml@acme/platform/ci-config`), so the team cannot edit it [verified:
  "custom CI/CD configuration file" in GitLab's pipeline settings docs, available on
  Free].
  It is heavy-handed. Keep it for the few repos that deploy to production.
- A scheduled "conformance" pipeline in the platform project that reads every
  project through the API and reports: default branch protected, approvals required,
  last default-branch pipeline status, which component versions are included, and
  whether any CI variable looks like a long-lived cloud key. Publish the report where
  people see it.
- Component usage details (Premium, 19.0) for adoption tracking [4].

### 2.10 Variables and secrets

| Mechanism | What it does | Tier | Use |
|---|---|---|---|
| CI/CD variable, masked | Value shown as `[MASKED]` in logs. Needs 8+ characters on one line. Default since 18.3 [28] | All | Non-critical config only |
| Masked and hidden | Value cannot be read back in the UI after saving. GA in 17.6 [28] | All | Any token that must stay a variable |
| Protected | Only available to pipelines on protected branches and tags [28] | All | Every deploy credential |
| Environment-scoped | Value differs per environment. Group-level scoping needs Premium [28] | All / Premium | Staging versus production values |
| File type | Value written to a temp file, variable holds the path [28] | All | Tools that want a file, such as kubeconfig |
| `id_tokens` (OIDC) | Job gets a signed JWT with an audience; a cloud or secret store trusts it by claims [31] | All | Replace stored cloud keys |
| External secrets (`secrets:`) | Job fetches from HashiCorp Vault, GCP Secret Manager, Azure Key Vault or AWS Secrets Manager using an ID token [29] | Premium, Ultimate (page-level) | If the company already runs one of these |
| GitLab Secrets Manager | OpenBao-based store inside GitLab, scoped by project, group, environment and branch. GA in 19.5, on GitLab.com and Self-Managed, consumes GitLab Credits [30] | Premium, Ultimate | Simplest option if the company has no secret store |

GitLab's variables page states the limits plainly: masking is not a guaranteed
protection, and anyone who can change `.gitlab-ci.yml` on a branch can make a job
print or send a variable elsewhere [28]. So what protects a production key is that it
is **protected** (unreviewed branches never see it) and **short-lived** (stolen copies
expire), not that it is masked.

The OIDC pattern, here for AWS (GCP and Azure work the same way through workload
identity federation):

```yaml
deploy-production:
  id_tokens:
    AWS_OIDC_TOKEN:
      aud: https://gitlab.com          # the audience registered on the IAM OIDC provider
  variables:
    AWS_ROLE_ARN: arn:aws:iam::111111111111:role/app-deploy-production
    AWS_WEB_IDENTITY_TOKEN_FILE: /tmp/aws-oidc-token
  script:
    - printf '%s' "$AWS_OIDC_TOKEN" > "$AWS_WEB_IDENTITY_TOKEN_FILE"
    - aws sts get-caller-identity       # the CLI assumes the role from the two variables
    - ./deploy.sh production
```

The IAM role's trust policy decides which jobs get in. Tokens carry `project_path`,
`project_id`, `namespace_id`, `ref`, `ref_protected`, `environment`,
`environment_protected` and `deployment_tier` claims [31]. GitLab's guidance: trust
stable IDs (`project_id`, `namespace_id`) over paths, never trust `user_login` or
`user_email` alone, and give each service its own `aud` [31]. On GitLab.com, AWS
accepts `project_id`, `namespace_id`, `ref_protected` and other claims as condition
keys; on Self-Managed and Dedicated only `sub` and `aud` [verified,
https://docs.gitlab.com/ci/cloud_services/aws/]. Tokens expire with the job timeout, or
after 5 minutes if none is set [31].

GitLab Secrets Manager, from 19.5 [30]:

```yaml
deploy-production:
  secrets:
    DB_PASSWORD:
      gitlab_secrets_manager:
        name: db_password       # written to a temp file by default; file: false for a plain variable
  script: ./deploy.sh production
```

### 2.11 Security scanners

| Scanner | Engine | Free and Premium | Ultimate adds | Notes |
|---|---|---|---|---|
| SAST | Semgrep-based analyzer with GitLab rules; Python fully supported [36] | Job runs, JSON report artifact | Advanced SAST (cross-file), MR findings, vulnerability report [36] | `include: - template: Jobs/SAST.gitlab-ci.yml` |
| Pipeline secret detection | Scans commits in CI [37] | Yes | Management in vulnerability report [inferred] | Run on every MR |
| Secret push protection | Blocks pushes containing secrets; skips are audit-logged [38] | No | Yes, GA 17.5 | Turn on per project or by API per group |
| Dependency scanning | SBOM-based analyzer, GA 19.0; reads `uv.lock`, `poetry.lock`, `Pipfile.lock`, compiled `requirements.txt` [40] | No | Yes; malicious package detection beta in 19.4, GA in 19.5 per the docs [39] | Gemnasium analyzer deprecated, removal proposed for 20.0 [39] |
| Container scanning | Trivy [41] | Report and CycloneDX SBOM artifacts; delayed advisory data | MR and security tab results, full advisory data, registry scanning [41] | Good value on Free |
| IaC scanning | KICS; Terraform, Kubernetes, Dockerfile, CloudFormation and more [42] | JSON report artifact | MR view, approvals, rule customisation [42] | `Jobs/SAST-IaC.gitlab-ci.yml` |

On Free and Premium, findings sit in JSON artifacts nobody reads unless a job fails on
them [inferred]. A practical Premium setup: secret detection and container scanning
from GitLab, plus a small script in the shared component that fails the job on high or
critical findings, plus Renovate for dependencies (section 5.5). Ultimate pays off
when the company wants vulnerability management, approval policies and push
protection across all repos, which is also when it pays for itself in a customer
security questionnaire [inferred].

Recent changes worth knowing: Advanced SAST added Kotlin, Dart and Scala in 19.4, all
as beta;
19.5 adds customisable security configuration profiles and a closed-beta "Business
Logic Security Analyzer" that consumes credits [1][2].

### 2.12 Audit events

Group and project audit events need Premium; instance events are for Self-Managed
admins; streaming to an HTTP endpoint, Google Cloud Logging or S3 needs Ultimate
[43][44]. Audit events are kept indefinitely [43]. Events to watch for the factory
[inferred, check the event types list]: protected branch and environment changes,
approval rule changes, token creation and rotation, runner registration, member role
changes, secret push protection skips [38] and policy changes.

### 2.13 If the company is on GitHub

| Need | GitLab | GitHub |
|---|---|---|
| Shared pipeline units | Components and CI/CD Catalog [4] | Reusable workflows and composite actions [not re-checked] |
| Central enforcement | Pipeline execution policies, Ultimate [23] | Rulesets with required workflows [not re-checked] |
| OIDC to cloud | `id_tokens` [31] | `permissions: id-token: write` [not re-checked] |
| Deployment approvals | Premium [15] | Required reviewers on private repos not available on Free, Pro or Team [86] |
| Build provenance | Runner L1/L2; L3 experiment [57][58] | Artifact attestations give Build L2; L3 via a shared reusable workflow [87] |
| Secret push protection | Ultimate [38] | Secret Protection, $19 per active committer per month since April 2025 [88] |
| Dependency updates | Renovate, self-run [61] | Dependabot built in |
| Agents in CI | Duo Agent Platform flows, external agents [75][80] | Copilot coding agent; Claude Code and Codex actions [not re-checked] |

Everything in sections 3 to 8 carries over in principle. The YAML and the tier lines
do not.

## 3. Measuring delivery

### 3.1 What DORA measures now

DORA's model has five metrics in two groups [48][49] [verified 2026-10-15]:

| Group | Metric | Definition (DORA) |
|---|---|---|
| Throughput | Change lead time | Commit to running in production |
| Throughput | Deployment frequency | How often production deploys happen |
| Throughput | Failed deployment recovery time | Time to recover from a deploy that needed immediate intervention. Replaced MTTR in the 2023 report [49] |
| Instability | Change fail rate | Share of deploys that need a rollback or hotfix |
| Instability | Deployment rework rate | Share of deploys that are unplanned and caused by a production incident. Added in the 2024 report [49] |

The 2025 DORA report on AI-assisted development found that AI adoption now improves
throughput but still increases instability, and that AI amplifies whatever an
organisation already has [50][93] [secondary summary]. Its AI Capabilities Model
lists seven capabilities, among them quality internal platforms, strong version
control practices, working in small batches and a clear and communicated AI stance
[verified 2026-10-15 against the DORA researchers' Google Cloud post; full list in
`01` section 2.6]. That finding is the argument for "CI/CD first": more output on weak
delivery foundations means more incidents.

### 3.2 GitLab's built-in analytics

- **DORA metrics** (Ultimate) [45]. Deployment frequency counts successful deploys to
  production-tier environments. Lead time is measured from **merge** to production,
  not from commit. Time to restore and change failure rate come from GitLab
  **incidents**, which must map to production deploys. Data via REST
  (`GET /projects/:id/dora/metrics` and `GET /groups/:id/dora/metrics`) and GraphQL
  [verified 2026-10-15]. One table on the page still says "commit"; the definition
  text says merge.
- **Value stream analytics** [46]. Free gets the default stream (Issue, Plan, Code,
  Test, Review, Staging) at project level only. Premium adds custom streams and
  group-level views. DORA tiles within it need Ultimate.

Note the definition gap: GitLab's lead time skips coding and review time, which is
exactly where agents change things. Measure both [inferred].

### 3.3 Measuring without Ultimate

Everything needed for the throughput metrics is in the Free Deployments API: list
deploys by environment, status and finish time, and list the merge requests shipped
with each deploy [47]. A sketch (not run) of a baseline script, which can itself run
as a scheduled job in the platform project:

```python
# dora_baseline.py: production deploy frequency and merge-to-deploy lead time
import os, statistics
from datetime import datetime, timedelta, timezone
import requests

API = os.environ["CI_API_V4_URL"]
PROJECT = os.environ["TARGET_PROJECT_ID"]
HEADERS = {"PRIVATE-TOKEN": os.environ["DORA_READ_TOKEN"]}  # read_api scope only

def get_all(path, **params):
    page = 1
    while True:
        r = requests.get(f"{API}{path}", headers=HEADERS, timeout=30,
                         params={**params, "per_page": 100, "page": page})
        r.raise_for_status()
        if not (batch := r.json()):
            return
        yield from batch
        page += 1

def ts(s): return datetime.fromisoformat(s.replace("Z", "+00:00"))

since = datetime.now(timezone.utc) - timedelta(days=90)
deploys = list(get_all(f"/projects/{PROJECT}/deployments", environment="production",
                       status="success", finished_after=since.isoformat(),
                       order_by="finished_at"))
print(f"production deploys per week: {len(deploys) / (90 / 7):.1f}")

lead_h = [(ts(d["deployable"]["finished_at"]) - ts(mr["merged_at"])).total_seconds() / 3600
          for d in deploys
          for mr in get_all(f"/projects/{PROJECT}/deployments/{d['id']}/merge_requests")
          if mr.get("merged_at")]
if lead_h:
    print(f"median merge-to-production hours: {statistics.median(lead_h):.1f}")
```

On GitLab.com Free, project access tokens are not available [34]; use a service
account's token instead [35].

For the instability metrics, pick one explicit signal and write it down [inferred]:

- A label on merge requests (`change::hotfix`, `change::revert`) whose merge counts as
  a failure of the deploy it fixes. Cheap, needs discipline.
- GitLab incidents linked to the environment, which also makes a later move to
  Ultimate's DORA view free of rework [45].
- Recovery time as "failed or reverted deploy to the next successful deploy" from the
  Deployments API.

Apache DevLake (incubating at the ASF) computes DORA from GitLab CI deployments and
merge requests, with incidents from Jira, GitHub issues or webhooks [51]. Worth it only
if the company wants cross-tool dashboards; a 60-line script and a spreadsheet cover
the first quarter [inferred].

### 3.4 What to add for an agentic factory

DORA metrics alone will not show whether agents help. Add [inferred]:

- Review load: open merge requests per reviewer, time to first review, time in
  review. Pipedrive's experience was that more junior output first raised senior
  review load [89].
- Agent merge requests: share merged, share merged without human rework commits,
  share reverted within 14 days.
- Pipeline health: default-branch success rate, p50 and p90 duration, flaky test
  count. Agents retry a slow, flaky pipeline many times over, so its cost grows with
  them.
- Spend per merged agent merge request: compute minutes plus model tokens or GitLab
  Credits.

The framing from Pipedrive's former CTO is to count delivered product impact, not
"time saved" (talk, July 2026) [89].

## 4. Platform engineering at 50 to 100 people

### 4.1 Golden paths, concretely

A golden path is the supported way to do a common thing, built so that following it
is less work than not. At this size it is a small set of artefacts [inferred]:

1. **Repo templates.** One per kind of project: Python service, data or ML project,
   frontend app. Each has `pyproject.toml` or `package.json` with the standard tools,
   `uv.lock`, a three-line `.gitlab-ci.yml` that includes the components, a
   `CODEOWNERS`, an agent instruction file, a README skeleton and an ADR folder.
   GitLab's group-level custom project templates make this a one-click choice
   [verified: Premium and Ultimate].
2. **The component library** from 2.3, versioned, with a changelog.
3. **One deploy pattern per runtime.** For example "container image to the cluster
   through a protected environment", written once as a component.
4. **A short guide per role.** "Your first merge request" for engineers, "From
   notebook to scheduled job" for data scientists.
5. **An exception process.** Teams may leave the path with a one-paragraph ADR. The
   conformance report (2.9) lists who has.

### 4.2 What is worth it at this size

| Worth it now | Why | Not yet | Why not |
|---|---|---|---|
| Shared CI components | Every repo gains from one fix | Backstage or another developer portal | A portal application to build and run; at this size there are tens of repos, not hundreds |
| 2 to 3 repo templates | New projects start compliant | A custom internal platform API | No team to own it |
| OIDC for cloud access | Removes the biggest secret risk | SLSA Build L3 | GitLab support is experimental [58] |
| Renovate | Steady, small dependency updates | Merge trains everywhere | Solves a congestion problem the company may not have |
| A service catalogue as a YAML file in one repo | Agents and people need the same map | Service mesh, multi-cluster tooling | Unrelated to the stated problem |
| ADRs per repo | Context for people and agents | Several CI systems | Splits the effort |

**Backstage, specifically.** Backstage is an open source framework for building a
developer portal, with a software catalog, software templates and TechDocs, and is a
CNCF incubating project [54]. "Framework" is the operative word: you assemble and host
your own portal from it and keep its plugins current [inferred from the framework
description]. Its benefits (one catalogue, templated project creation, docs next to
code) are reachable at this size with a `catalog.yaml` in a repo, GitLab project
templates and Markdown docs that agents can also read. Revisit it if the repo count
passes a few hundred or several teams cannot find each other's services [inferred].

### 4.3 Monorepo or polyrepo

| | Monorepo | Polyrepo |
|---|---|---|
| Shared code changes | One merge request across library and users | Release the library, then bump each user |
| CI | Needs path rules and child pipelines (2.4) | Simple pipelines, many copies |
| Agent context | One checkout holds the whole picture | Agent must fetch other repos or a catalogue |
| Access control | Coarser; Code Owners per path [19] | Per project |
| Fits | Tightly coupled services and shared data libraries | Independent products and teams |

Recommendation [inferred]: do not move repositories in the first months of the programme.
Standardise pipelines first; the layout question gets easier to answer once every
repo has the same pipeline. If data science has many small repos copying the same
helper code, a uv workspace for the shared libraries is a smaller step than a full
monorepo.

### 4.4 Trunk-based development

Everyone merges small changes to `main` at least daily through short-lived branches
and merge requests, and releases come from `main` [94] [not re-checked]. It needs a
fast, reliable pipeline and a way to merge unfinished work safely, which is what
feature flags are for. Agents fit it well: one issue, one small branch, one merge
request [inferred]. Trunk-based development is one of the capabilities in DORA's
catalogue [95] [not re-checked], and working in small batches is one of the seven in
its 2025 AI Capabilities Model [verified, see `01` section 2.6].

### 4.5 Feature flags

GitLab's feature flags are on all tiers, expose an Unleash-compatible API, and support
all users, percent rollout, user IDs and user lists as strategies [52]. Limits on
GitLab.com: 50 flags per project on Free, 150 on Premium, 200 on Ultimate [52]. One
caveat: GitLab says the Unleash Proxy reaches end of life on 26 November 2026, and its
recommended successor, Unleash Edge, is not compatible with GitLab [52]. Check the
company's setup before building on it.

For software that controls physical equipment, the useful targeting unit is probably
the site, not the user [inferred]. A new setpoint strategy could go to three pilot
sites, then a customer, then everyone, with a kill switch. That is a "user list" of
site IDs.

### 4.6 Environment strategy

A default for SaaS teams is development, review apps, staging, production [13]. When
a product changes physical systems, two extra rings make sense [inferred]:

- **Replay or shadow.** Run the candidate control software against recorded BMS data, or
  in parallel with production without writing setpoints, and compare outputs. This is
  the review app for control logic.
- **Pilot sites.** A production subset, chosen with customer success, behind a
  flag.

Name environments so the deployment tier is unambiguous, protect `production` at
group level by tier [14], and serialise deploys with `resource_group` [13].

### 4.7 Infrastructure as code basics

IaC means cloud resources are declared in versioned files and changed through merge
requests, so a pipeline can plan and apply them. GitLab has stopped distributing its
Terraform templates and images; its supported path is an OpenTofu component
(`gitlab.com/components/opentofu/validate-plan-apply`) with GitLab-managed state, on
all tiers [53]. The pattern: `plan` in the merge request with the plan as an
artifact, `apply` from `main` through a protected environment, and a scheduled
`plan` to detect drift [inferred]. IaC scanning (KICS) runs on all tiers [42].

```yaml
include:
  - component: gitlab.com/components/opentofu/validate-plan-apply@<VERSION>
    inputs:
      version: <VERSION>
      opentofu_version: <OPENTOFU_VERSION>
      root_dir: infra/
      state_name: production
stages: [validate, build, deploy]
```

## 5. Software supply chain security

### 5.1 SLSA levels

SLSA is a framework of levels for trusting how software was built. Version 1.2 was
approved in November 2025 and added a Source track next to the Build track [55][56].

| Build level | Requirement | Protects against |
|---|---|---|
| L1, provenance exists | The build platform generates provenance: who built it, how, from which inputs | Mistakes, such as building from a commit not in the repo. Easy to forge [55] |
| L2, hosted build platform | The platform signs the provenance; consumers verify it | Tampering after the build [55] |
| L3, hardened builds | Runs cannot influence each other; signing secrets out of reach of build steps | Tampering during the build, insiders, stolen credentials [55] |

GitLab's position [57][58]: the runner can generate L1 provenance for every artifact;
GitLab says it meets L2 with signing through its SLSA CI/CD component, by
self-assessment. L3 attestations (`ATTEST_BUILD_ARTIFACTS`, `ATTEST_CONTAINER_IMAGES`)
arrived in 18.3 as an experiment: Ultimate, GitLab.com only, behind a feature flag,
public projects only, artifacts under 100 MB, verified with `glab attestation verify`
[58]. GitHub's equivalent claims L2 out of the box and L3 with a shared reusable
workflow [87].

Realistic for the company in the first year [inferred]: L1 everywhere through the
shared component, L2 for the artifacts that leave the company's control (anything
installed at a customer site), and L3 only when GitLab's feature is generally
available for private projects.

### 5.2 SBOMs

An SBOM is a machine-readable list of the components inside a piece of software.

| | CycloneDX | SPDX |
|---|---|---|
| Origin | OWASP | Linux Foundation; ISO/IEC 5962 [not re-checked] |
| Focus | Security use: components, vulnerabilities, services | Licence compliance first, broadened since |
| GitLab output | Dependency scanning writes `gl-sbom-<type>-<manager>.cdx.json` (Ultimate) [40]; container scanning writes `gl-sbom-report.cdx.json` on all tiers [41] | SPDX licence expressions understood since 19.4 [1]; no SPDX document output found [inferred] |

On Premium, container scanning's CycloneDX file is the cheapest SBOM the company can
get: one per image, stored as a job artifact [41]. Attach it to each release so it
survives artifact expiry [inferred].

### 5.3 Signing and provenance

Keyless signing with Sigstore's cosign: the job requests an ID token with audience
`sigstore`, cosign exchanges it for a short-lived certificate, signs, and discards the
key [59]. On GitLab.com only, all tiers, cosign 2.0.1 or later [59].

```yaml
sign-artifact:
  stage: build
  image: alpine:latest
  variables:
    COSIGN_YES: "true"
  id_tokens:
    SIGSTORE_ID_TOKEN:
      aud: sigstore
  before_script:
    - apk add --update cosign
  script:
    - cosign sign-blob dist/package.whl --bundle cosign.bundle
  artifacts:
    paths: [dist/package.whl, cosign.bundle]
```

Verification pins the identity to project, CI file and ref, with a double slash that
GitLab says is required [59]:

```text
cosign verify-blob dist/package.whl --bundle cosign.bundle \
  --certificate-identity "https://gitlab.com/acme/app/connector//.gitlab-ci.yml@refs/heads/main" \
  --certificate-oidc-issuer "https://gitlab.com"
```

A privacy catch: public Sigstore writes signing events to a public transparency log.
GitLab restricts its L3 feature to public projects "to prevent accidental disclosure
to Rekor" [58]. For private repos, keyless signing would publish project paths and
refs [inferred]. The alternative is key-based cosign with the key in a KMS or
Secrets Manager.

Container images should be signed by digest, not tag [59].

### 5.4 Pinning and lockfiles

Every input to a build should be fixed by content, and changed only by a merge
request [inferred]:

- Python: commit `uv.lock` and run `uv sync --locked` in CI, which fails if the lock
  is stale [65].
- Images: pin `image:` by digest in deploy-critical jobs; tags move.
- Components and `include: project`: exact version or SHA for deploy pipelines (2.3).
- Tools in pre-commit: pinned `rev`, kept in step with `uv.lock`.

### 5.5 Dependency updates on GitLab

Dependabot is GitHub-only. On GitLab the standard is Renovate, run as a scheduled
pipeline from the `renovate-runner` project [61]:

```yaml
include:
  - project: renovate-bot/renovate-runner
    file: /templates/renovate.gitlab-ci.yml
# CI/CD variables:
#   RENOVATE_TOKEN       token with api, read_user, write_repository (a group access token works)
#   RENOVATE_EXTRA_FLAGS --autodiscover=true --autodiscover-filter=acme/* --onboarding=false
# Schedule: hourly, e.g. "3 * * * *"
```

Renovate's PEP 621 manager handles uv, including `uv.lock`, workspaces and lockfile
maintenance [60]. Group updates (all ruff, mypy and pytest bumps in one weekly merge
request) so reviewers are not flooded [inferred]. GitLab's beta "Agentic Breaking
Change Resolution" flow (Ultimate) fixes breaking changes in dependency bump merge
requests [75][77]; a natural second step after Renovate runs reliably.

### 5.6 EU Cyber Resilience Act, briefly

The CRA (Regulation (EU) 2024/2847) entered into force on 10 December 2024. Reporting
of actively exploited vulnerabilities and severe incidents applies from 11 September
2026; the main obligations from 11 December 2027 [62]. Reports go through ENISA's
single reporting platform, with an early warning within 24 hours [64] [secondary].
It covers products with digital elements, including the remote data processing they
need to work, when made available on the EU market [63]. Pure SaaS is generally
outside it; any on-site gateway or connector software a company ships to customer
sites could be inside, and its cloud side with it [inferred]. The legal text asks
manufacturers to document components, including an SBOM covering at least top-level
dependencies [not re-checked: Annex I, Part II]. Depth in
`03-regulation-and-standards.md`.

## 6. Python, data science and ML

### 6.1 Toolchain

| Job | Tool | Status October 2026 | Notes |
|---|---|---|---|
| Environments, dependencies, lockfile, build | uv | 0.13.x in Astral's GitLab guide [65] | `uv sync --locked`, `uv build`, `uv lock --check` |
| Lint and format | Ruff | Lints and formats notebooks by default since 0.6.0 [66] | Replaces flake8, isort, black |
| Type check | mypy (strict) or pyright | Mature [not re-checked] | Pick one per repo; the component can take the command as an input |
| Type check, new | ty | Beta since December 2025, stable targeted for 2026 [67] [secondary] | Try on one repo; do not gate on it yet |
| Tests | pytest with pytest-cov | Mature | JUnit and Cobertura reports show in merge requests (2.3) |
| Local gates | pre-commit [90] | Mature | Fast feedback only. CI is the enforcement |

OpenAI announced on 19 March 2026 that it would acquire Astral, the maker of uv, Ruff
and ty; both sides pledged the tools stay open source [68]. They are permissively
licensed, so the worst case is a fork [68]. Whether the deal has closed is
[unconfirmed]: on 2026-10-15 neither Astral's blog nor OpenAI had announced completion,
and Astral's posts still carry "© 2026 Astral Software Inc.", so do not say OpenAI owns
uv or Ruff yet. Low risk, worth one line in the dependency register [inferred].

Astral's recommended GitLab job sets `UV_LINK_MODE: copy`, because GitLab mounts the
build directory separately, caches `UV_CACHE_DIR` keyed on `uv.lock`, and runs
`uv cache prune --ci` after the job [65]. The component in 2.3 follows it.

### 6.2 Packaging

Shared libraries should be real packages: `pyproject.toml`, built with `uv build`,
published to GitLab's package registry from a tag pipeline [inferred]. The CI job
token can access the package registry, so no extra token is needed inside the same
project [32].

### 6.3 Notebooks in CI

Notebooks are JSON with embedded outputs, which makes review hard and lets secrets or
customer data hide in outputs [inferred]. A workable policy:

- Ruff lints and formats `.ipynb` files out of the box [66]. Use nbQA to run other
  tools, such as mypy, on notebooks [69].
- Pair notebooks with a `py:percent` text file using Jupytext, so merge requests show
  readable diffs [71].
- Strip outputs before commit with a pre-commit hook such as nbstripout [not
  re-checked].
- Smoke-run important notebooks in CI with papermill, which executes a notebook with
  injected parameters, on a tiny fixture dataset:
  `papermill analysis.ipynb out.ipynb -p site_id test-001 -p days 2` [70] [not
  re-checked: CLI form].
- Code that runs in production moves out of notebooks into a package with tests. The
  notebook imports it [inferred].

### 6.4 Data and model pipelines: where CI/CD touches MLOps

| Concern | What CI/CD does | GitLab feature or tool |
|---|---|---|
| Code of the pipeline | Lint, type, unit test like any service | Component from 2.3 |
| Training run | Scheduled or triggered pipeline with pinned code, image and data version | Pipeline schedules, pipeline inputs [5] |
| Experiment tracking | Log parameters and metrics per run | GitLab experiments through the MLflow client, Free, GA 17.8 [73] |
| Model registry | Register a semver'd version with lineage | GitLab model registry, Free, GA 17.6; 5 GB per file [72] |
| Promotion | Move a model to staging, then production, with approval | Environments and deployment approvals (2.7) |
| Data versions | Know which data trained which model | DVC [91], or immutable dated snapshots in object storage [inferred] |
| Data validation | Fail the pipeline on schema or range violations | pandera [92] or similar |

Connecting MLflow to GitLab [73]: set `MLFLOW_TRACKING_URI` to
`<gitlab>/api/v4/projects/<id>/ml/mlflow` and `MLFLOW_TRACKING_TOKEN` to a token with
`api` scope and at least Developer role, and remove `mlflow.set_tracking_uri()` calls.
Tag runs with `gitlab.CI_JOB_ID` to link a model version to the job, its pipeline and
its merge request. Caveats: `log_model` stores artifacts but not model data, artifact
logging needs an empty `artifact_path`, and several registry arguments are ignored
[73].

Reproducibility record for every model version [inferred]: code commit SHA,
`uv.lock` hash, image digest, data version, random seeds, training parameters,
evaluation metrics on a fixed holdout set.

### 6.5 Testing ML code and data

Google's "ML Test Score" paper lists 28 tests and monitoring checks for production
ML [74]. The ones that matter first for software that controls physical
equipment [inferred]:

- Unit tests for feature functions, with edge cases from real BMS data: missing
  points, stuck sensors, unit mix-ups, daylight-saving gaps.
- Schema and range checks on input data before training and before inference.
- A golden-dataset regression test: a fixed slice of recorded BMS data must
  produce setpoints within tolerance of the approved version.
- Safety properties, tested with property-based tests: setpoints never leave the
  configured comfort and equipment bounds, whatever the input.
- A model quality gate: the candidate must not be worse than production on the
  holdout set by more than an agreed margin before promotion.

## 7. How agents plug into CI/CD

### 7.1 GitLab Duo Agent Platform

Generally available since 18.8 (January 2026), on Premium and Ultimate for all
offerings; Free users can use some features with purchased GitLab Credits [75]. GA
features consume credits [75].

Foundational flows, maintained by GitLab [77]:

| Flow | What it does | Trigger | Notes |
|---|---|---|---|
| Developer | Issue to merge request | From an issue or Agentic Chat | Model selection added in 19.4 [1] |
| Code Review | Inline review comments; approves or requests changes | Assign `@GitLabDuo`, mention, or automatic on new merge requests | Its approval does not count toward required approvals. Reads `mr-review-instructions.yaml`, not `AGENTS.md` [81] |
| Fix CI/CD Pipeline | Diagnoses a failed pipeline; suggests fixes or opens a merge request | "Fix pipeline with Duo" button | Manual trigger. GA 18.8. Push rules must allow the service account [82] |
| Convert to GitLab CI/CD | Migrates Jenkins pipelines | Manual | [77] |
| SAST false positive detection, SAST vulnerability resolution, secret false positive detection | Triage and fix scanner findings | Security findings | Ultimate scanners underneath [75][77] |
| Recommend reviewers | Suggests reviewers | Merge request | GA in 19.5 [2] |
| Security Review, Agentic Breaking Change Resolution | Business logic review; fix dependency bump breakage | Merge request | Beta, Ultimate [75] |

Custom flows (your own steps and agents) became GA in 19.2; triggers include
"merge request created" since 19.4 and a trigger flows API is GA in 19.5 [1][2][76].

**How flows run.** Flows started from the GitLab UI run as CI/CD jobs; flows started
in an IDE run locally [78]. The runner downloads the Duo CLI, connects to GitLab's
workflow service over a WebSocket and runs file and Git tools as the model directs.
Requirements [78]:

- A runner tagged `gitlab--duo` with a container executor (`docker`,
  `docker-autoscaler`, `kubernetes`); shell is not supported. Hosted runners work on
  GitLab.com unless the top-level group has IP restrictions.
- An optional `.gitlab/duo/agent-config.yml`, read **only from the default branch**,
  with `setup_script`, `cache`, `id_tokens` and a `network_policy` of allowed domains:

```yaml
# .gitlab/duo/agent-config.yml
setup_script:
  - uv sync --locked
cache:
  key:
    files: [uv.lock]
  paths: [.uv-cache]
network_policy:
  allowed_domains:
    - pypi.org
    - files.pythonhosted.org
```

- A sandbox for network and file system isolation. The job must be able to create
  user and mount namespaces (GitLab says `privileged = true` is "not sufficient on its
  own"), and the image must be GitLab's base image, UBI 9 Minimal, or a custom image
  with the sandbox runtime installed. If no namespace mode is available, the flow runs
  **unsandboxed** with only a warning in the job log [78] [verified 2026-10-15].
- `setup_script` runs before and outside the sandbox and can read the triggering
  user's OAuth token [78] [verified]. Treat its contents as privileged code.

**Composite identity** [79]. Flows act as a service account combined with the human
who started them. The effective role is the lower of the user's role and the service
account's Developer role; project access is the intersection of both; the OAuth token
is limited to `ai_workflows` and `mcp` scopes. Commits show the service account "on
your behalf"; the merge request is attributed to the human, which GitLab justifies
by segregation-of-duties rules in SOC 2, SOX and ISO 27001 [79]. GA in 18.8.

**Model.** The overview page's "Claude Sonnet 4" label is stale. GitLab's model
selection page gives the current default as Claude Sonnet 5.5 served through Google's
Gemini Enterprise Agent Platform: since 5 October 2026 for the Code Review flow and
since 7 October 2026 for Agentic Chat, the Developer flow and other agents. Exceptions
on that page include Security Review (Sonnet 4.6) and Recommend Reviewers (Haiku 5.5)
[verified, https://docs.gitlab.com/user/duo_agent_platform/model_selection/]. The
model will keep changing. If model choice matters to the company's data policy, check
what each flow uses and where it is hosted (see `05-controlled-ai-architecture.md`).

### 7.2 External agents (Claude Code, Codex) inside GitLab

External agents let you mention or assign a third-party agent in an issue or merge
request; it runs a CI/CD pipeline and replies with a merge request or comment [80].
Claude Code and Codex use GitLab-managed credentials through GitLab's AI gateway;
Amazon Q and Gemini need your own (and are GitLab.com only) [80]. Each agent is a
service account using composite identity; the job receives `AI_FLOW_GITLAB_TOKEN`
limited to `ai_workflows` [80]. Premium and Ultimate, behind a feature flag that GitLab
support enables per namespace [80].

GitLab lists the risks itself [80]: its prompt-injection scanning does not apply to
external agents; the provider handles monitoring; and external agents do not get the
same network isolation as GitLab's own flows.

### 7.3 Running a coding agent headless in your own pipeline

The alternative is a plain CI job that runs Claude Code or Codex. Anthropic documents
a GitLab CI/CD integration (beta, maintained by GitLab) that runs `claude -p` with an
allowlist of tools, using an API key or OIDC to Amazon Bedrock or Google's Vertex
platform [84]. Codex's `codex exec` defaults to a read-only sandbox;
`--sandbox workspace-write` allows edits; and OpenAI warns against putting the API key
in a job-level variable where repository code can read it [85].

OpenAI's documented CI example splits the work in two: one job with read-only
repository access and the model key produces a patch; a second job with write access
and no model key applies it and opens a pull request [85]. The same shape on GitLab
(a sketch, not run):

```yaml
agent-propose:
  stage: agent
  tags: [agent-untrusted]            # dedicated pool: ephemeral, unprivileged, egress-limited
  image: node:24-slim
  rules:
    - if: $CI_PIPELINE_SOURCE == "trigger" && $AGENT_TASK
  id_tokens:
    MODEL_OIDC_TOKEN:
      aud: https://bedrock-gateway.acme.example   # short-lived model access, no stored key
  timeout: 30m
  script:
    - ./ci/model-login.sh "$MODEL_OIDC_TOKEN"     # exchange for temporary model credentials
    - >
      claude -p "$AGENT_TASK" --max-turns 30
      --permission-mode acceptEdits
      --allowedTools "Read Edit Write Bash(uv run pytest:*) Bash(uv run ruff:*)"
    - git diff --binary > agent.patch
  artifacts:
    paths: [agent.patch]
    expire_in: 1 day

agent-open-mr:
  stage: publish
  tags: [agent-untrusted]
  needs: [agent-propose]
  image: alpine/git
  rules:
    - if: $CI_PIPELINE_SOURCE == "trigger" && $AGENT_TASK
  # AGENT_PUSH_TOKEN: project access token, Developer role, write_repository scope only
  script:
    - git apply agent.patch
    - git switch -c "agent/$CI_PIPELINE_ID"
    - >
      git -c user.name="acme-agent-bot" -c user.email="agent-bot@acme.example"
      commit -am "Agent change for pipeline $CI_PIPELINE_ID"
      --trailer "Assisted-by: claude-code"
    - >
      git push -o merge_request.create -o merge_request.target="$CI_DEFAULT_BRANCH"
      -o merge_request.label="agent::claude-code"
      "https://agent-bot:${AGENT_PUSH_TOKEN}@${CI_SERVER_HOST}/${CI_PROJECT_PATH}.git" HEAD
```

Why the split [inferred]: the job that runs untrusted repository code (tests the
agent invokes) holds only a short-lived model credential; the job that can write to
GitLab never runs repository code or sees the model credential. The merge request
then goes through the normal pipeline and approvals. Model credential custody and
per-agent budgets are covered in `05-controlled-ai-architecture.md`.

### 7.4 Least-privilege tokens for agents

| Credential | Acts as | Scope limits | Lifetime | Tier | Fit for agents |
|---|---|---|---|---|---|
| `CI_JOB_TOKEN` | Triggering user's access, restricted to listed endpoints [32] | Inbound allowlist; fine-grained permissions GA 18.3 [33]; Git push only via the project setting "Allow Git push requests to the repository", off by default, GA 18.4, own project's pipelines only [32] | Job duration [32] | All | Reading, registries. Pushes with it do not trigger pipelines [32] [verified] |
| Project access token | A bot user with a chosen role [34] | Scopes chosen at creation | Default and max 365 days [34] | Premium on GitLab.com; any licence on Self-Managed [34] | One agent writing to one project |
| Group access token | Bot user across a group | Scopes | Default and max 365 days (400 behind a flag) [verified] | Premium on GitLab.com [verified] | Renovate across a group [61] |
| Service account token | Named non-human account, no seat [35] | Scopes; invited to chosen projects | Max 365 days, 400 with a flag [35] | All tiers; 100 per top-level group on Free [35] | A stable identity per agent |
| Composite identity OAuth | Intersection of user and service account [79] | `ai_workflows`, `mcp` [79] | Per flow [inferred] | Premium, Ultimate | Duo flows and external agents |
| `id_tokens` | Not a GitLab credential; an OIDC assertion [31] | One audience per token | Job timeout or 5 minutes [31] | All | Model providers, secret stores, cloud |
| OAuth with `mcp` scope or fine-grained PAT | The user [83] | MCP tools only; legacy PATs rejected [83] | Per token | All | MCP clients |

### 7.5 GitLab MCP server

GitLab hosts an MCP server at `https://<host>/api/v4/mcp`, GA in 19.5, on all tiers
[83]. Clients authenticate with OAuth (`mcp` scope) or a fine-grained personal access
token; classic personal, project and group tokens get `403` even with `api` scope
[83]. Tools come in toolsets: `core`, `ci`, `merge_requests`, `work_items`,
`repository` by default, `duo_agent_platform` when available, `wikis` and
`code_security` opt-in, selected with a request header [83]. Every call is
permission-checked as the user [83]. An admin or group owner must allow MCP access;
GitLab.com rate limits are 60 requests per minute per user on Free and 600 on paid
tiers [83]. The docs say these limits sit behind feature flags that are off by
default, while the 19.5 release post says enforcement starts on 15 October 2026, so
treat them as rolling out [verified 2026-10-15]. The docs put prompt injection on the
user: use MCP tools only on GitLab objects you trust [83]. 19.4 extended agent tool
governance (beta, set by top-level group Owners) to MCP tools; the only admin-level MCP
control is the "Allow connection to GitLab" checkbox under "MCP client access" (the
MCP server page calls it "Allow access to the MCP server"). It is on by default and
was switched on for existing instances in 19.5 [1][83] [verified 2026-10-24].

### 7.6 What the pipeline must guarantee before agent changes are trusted

A checklist to apply to every repo where agents write code [inferred, built from the
features above]:

1. **No direct path to `main`.** Default branch protected, no direct pushes, force
   push off [16]. Agent identities hold at most Developer.
2. **The full shared pipeline runs on every agent merge request** and must pass.
   Pipeline variables cannot be overridden by whoever starts the pipeline [28].
3. **A human who is neither the author nor the triggering person approves.** Author
   and committers cannot approve; approvals reset on new commits [18]. With composite
   identity, the triggering human counts as author [79].
4. **Agents cannot change their own rules.** Code Owner approval on `.gitlab-ci.yml`,
   `.gitlab/`, agent instruction files and `.gitlab/duo/` [19]. Duo flow
   configuration is read only from the default branch, so it already needs review
   [78].
5. **Tests cannot be weakened quietly.** A check flags merge requests that delete
   tests, add skips or lower coverage, and asks for a named reviewer [inferred].
6. **Agent jobs run on their own runner pool.** Ephemeral, unprivileged, no deploy
   credentials, limited egress [11][78]. Protected variables stay out of reach
   because agent branches are unprotected [28].
7. **Short-lived model credentials** through OIDC or per-agent keys with spend caps
   [31] (and file 05).
8. **Every agent change is labelled.** A named service account as committer, a commit
   trailer, a merge request label. Composite identity does the first part for Duo
   flows [79].
9. **Deploys stay human-gated.** Protected environments with approvals, and the
   pipeline triggerer cannot approve their own deploy [15].
10. **The trail is kept.** Audit events for settings and token changes [43]; merge
    request history for the rest.

### 7.7 Gotchas found while reading

- Pushes made with `CI_JOB_TOKEN` do not trigger pipelines [32]. An agent that pushes
  with it may leave a branch with no pipeline; use a project access token or service
  account for agent pushes, and check that the merge request pipeline runs
  [inferred].
- Duo Code Review's approval does not count toward required approvals [81]. Good, but
  people may assume it does.
- The Fix CI/CD Pipeline flow needs push rules that allow its service account [82].
  Rules like "commit author must be a GitLab user" or "reject unsigned commits" need
  testing with agent identities first [20].
- The Duo sandbox can fall back to no sandbox with only a log line (7.1). Check job
  logs for the warning when setting up runners [78].
- The composite identity rules hold for flows on runners, not for Agentic Chat in the
  UI or IDE [79].

## 8. A maturity ladder

What very good CI/CD means at a company of 50 to 100 people, as stages with exit
criteria. Stages 1 to 3 are detailed enough to start right away. All of it is
[inferred], built from the sections above.

### Stage 0. Unknown

The starting point: no one has a full picture of how each team builds, tests and deploys.

### Stage 1. Every change goes through a pipeline (weeks 1 to 4)

Actions:
1. Inventory every GitLab project: last commit, owner, language, whether it has a
   `.gitlab-ci.yml`, last default-branch pipeline status and duration, protected
   branch settings, approval settings, runners used, CI variables that look like
   cloud keys, and how it deploys. Script it against the API; one afternoon of code,
   a few days of chasing owners.
2. Classify projects: active product, active data or ML, internal tool, dormant.
3. For active projects: protect the default branch, require merge requests, switch on
   "pipelines must succeed", add the `workflow` rules from 2.2.
4. Give every active repo a minimal pipeline: lint and tests, even if the tests are
   thin.
5. Record the baseline: deploys per week, merge-to-deploy time, pipeline success rate
   and duration on `main`, open merge request age.

Exit criteria: 100 percent of active repos have a protected default branch and a
pipeline that runs on merge requests; the baseline is written down and dated.

### Stage 2. Shared and secure by default (weeks 4 to 10)

Actions:
1. Publish `acme/platform/ci-components` v1 with `python-checks`, `frontend-checks`,
   `container-build` (with container scanning) and `secret-detection`.
2. Migrate active repos to the components, starting with the most active.
3. Move cloud access to OIDC; delete long-lived keys from CI variables; mark the rest
   protected, masked and hidden.
4. Runner policy: hosted or ephemeral runners for builds, protected tagged runners
   for deploys, no shell executors.
5. Renovate on a schedule for all active repos, with grouped updates.
6. Code Owners for CI and agent instruction files (Premium).
7. Repo templates for a Python service and a data or ML project.

Exit criteria: 80 percent of active repos include the components at a supported
version; no long-lived cloud credential in any CI variable; Renovate merge requests
are reviewed weekly.

### Stage 3. Deploy only from pipelines (weeks 8 to 16)

Actions:
1. Every production deploy is a pipeline job with an `environment` and
   `deployment_tier: production`.
2. Protect production (Premium) with approval rules; serialise with
   `resource_group`.
3. Write and rehearse a rollback for each production service.
4. Automate the DORA baseline script as a scheduled job; publish a weekly table.
5. Feature flags, or a per-site switch, for control logic changes; a replay
   environment for control software.
6. Bring p50 pipeline duration on the main repos under about 10 minutes.

Exit criteria: no production deploy outside a pipeline in the last 30 days; DORA
numbers update without manual work; rollback tested at least once per critical
service.

### Stage 4. Agent-ready (months 4 to 6)

Agent identities and a token policy (7.4); a dedicated agent runner pool; the
checklist in 7.6 applied to pilot repos; a review agent on all merge requests in
pilot repos, measured with the metrics in 3.4; one issue-to-merge-request flow on a
pilot repo. On Ultimate, the baseline enforced with a pipeline execution policy; on
Premium, the conformance report.

### Stage 5. Hardened and measured (later)

Signed artifacts and L2 provenance for anything shipped to customer sites; an SBOM
per release; merge trains if `main` congestion appears; service level objectives;
agents on the operate and learn segments of the delivery loop [inferred].

## 12. Reading list

Must read (in order):

1. GitLab CI/CD components and inputs [4][5], then skim the CI/CD YAML reference.
2. GitLab pipeline security as one block: CI/CD variables [28], ID tokens [31], job
   token [32] and runner security [11].
3. Duo Agent Platform: flow execution [78], composite identity [79] and external
   agents [80].
4. DORA's metrics guide [48] and the AI capabilities chapter of the 2025 report [50].
5. SLSA v1.2 Build track basics [55].

Should read:

- Environments, protected environments and deployment approvals [13][14][15].
- Pipeline execution policies and merge request approval policies [23][25], even
  without Ultimate, to know what you are missing.
- Astral's uv GitLab guide [65].
- Claude Code GitLab CI/CD [84] and Codex non-interactive mode [85] for the split-job
  pattern.
- The ML Test Score paper [74].
- `renovate-runner` README [61].
- The talk by Pipedrive's former CTO (July 2026) [89].

Reference:

- Release notes for 19.4 and 19.5 [1][2], pricing [9], compute minutes [8], hosted
  runners [7], executors [10].
- Scanner pages [36] to [42], audit events [43][44].
- Model registry and MLflow compatibility [72][73], feature flags [52], OpenTofu [53].
- GitHub equivalents [86][87][88].

## 14. Sources

Accessed 2026-10-14 or 2026-10-15 unless a date is given.

1. GitLab 19.4 release notes (released 2026-09-17). https://docs.gitlab.com/releases/19/gitlab-19-4-released/
2. GitLab 19.5 release notes, marked upcoming on 2026-10-14. https://docs.gitlab.com/releases/19/gitlab-19-5-released/
3. GitLab Handbook, Release Managers (19.5 scheduled 2026-10-15) [secondary: read through a search result summary]. https://handbook.gitlab.com/handbook/engineering/releases/release-managers/
4. GitLab docs, CI/CD components. https://docs.gitlab.com/ci/components/
5. GitLab docs, CI/CD inputs. https://docs.gitlab.com/ci/inputs/
6. GitLab docs, Downstream pipelines. https://docs.gitlab.com/ci/pipelines/downstream_pipelines/
7. GitLab docs, Hosted runners on Linux. https://docs.gitlab.com/ci/runners/hosted_runners/linux/
8. GitLab docs, Compute minutes. https://docs.gitlab.com/ci/pipelines/compute_minutes/
9. GitLab pricing. https://about.gitlab.com/pricing/
10. GitLab Runner docs, Executors. https://docs.gitlab.com/runner/executors/
11. GitLab Runner docs, Security for self-managed runners. https://docs.gitlab.com/runner/security/
12. GitLab docs, Configuring runners. https://docs.gitlab.com/ci/runners/configure_runners/
13. GitLab docs, Environments. https://docs.gitlab.com/ci/environments/
14. GitLab docs, Protected environments. https://docs.gitlab.com/ci/environments/protected_environments/
15. GitLab docs, Deployment approvals. https://docs.gitlab.com/ci/environments/deployment_approvals/
16. GitLab docs, Protected branches. https://docs.gitlab.com/user/project/repository/branches/protected/
17. GitLab docs, Merge request approvals. https://docs.gitlab.com/user/project/merge_requests/approvals/
18. GitLab docs, Merge request approval settings. https://docs.gitlab.com/user/project/merge_requests/approvals/settings/
19. GitLab docs, Code Owners. https://docs.gitlab.com/user/project/codeowners/
20. GitLab docs, Push rules. https://docs.gitlab.com/user/project/repository/push_rules/
21. GitLab docs, Merge trains. https://docs.gitlab.com/ci/pipelines/merge_trains/
22. GitLab docs, Merged results pipelines. https://docs.gitlab.com/ci/pipelines/merged_results_pipelines/
23. GitLab docs, Pipeline execution policies. https://docs.gitlab.com/user/application_security/policies/pipeline_execution_policies/
24. GitLab docs, Scan execution policies. https://docs.gitlab.com/user/application_security/policies/scan_execution_policies/
25. GitLab docs, Merge request approval policies. https://docs.gitlab.com/user/application_security/policies/merge_request_approval_policies/
26. GitLab docs, Compliance frameworks. https://docs.gitlab.com/user/compliance/compliance_frameworks/
27. GitLab docs, Compliance pipelines (deprecated). https://docs.gitlab.com/user/compliance/compliance_pipelines/
28. GitLab docs, CI/CD variables. https://docs.gitlab.com/ci/variables/
29. GitLab docs, External secrets. https://docs.gitlab.com/ci/secrets/
30. GitLab docs, GitLab Secrets Manager. https://docs.gitlab.com/ci/secrets/secrets_manager/
31. GitLab docs, OpenID Connect (OIDC) authentication using ID tokens. https://docs.gitlab.com/ci/secrets/id_token_authentication/
32. GitLab docs, CI/CD job token. https://docs.gitlab.com/ci/jobs/ci_job_token/
33. GitLab docs, Fine-grained permissions for CI/CD job tokens. https://docs.gitlab.com/ci/jobs/fine_grained_permissions/
34. GitLab docs, Project access tokens. https://docs.gitlab.com/user/project/settings/project_access_tokens/
35. GitLab docs, Service accounts. https://docs.gitlab.com/user/profile/service_accounts/
36. GitLab docs, Static application security testing. https://docs.gitlab.com/user/application_security/sast/
37. GitLab docs, Secret detection. https://docs.gitlab.com/user/application_security/secret_detection/
38. GitLab docs, Secret push protection. https://docs.gitlab.com/user/application_security/secret_detection/secret_push_protection/
39. GitLab docs, Dependency scanning. https://docs.gitlab.com/user/application_security/dependency_scanning/
40. GitLab docs, Dependency scanning by using SBOM. https://docs.gitlab.com/user/application_security/dependency_scanning/dependency_scanning_sbom/
41. GitLab docs, Container scanning. https://docs.gitlab.com/user/application_security/container_scanning/
42. GitLab docs, Infrastructure as code scanning. https://docs.gitlab.com/user/application_security/iac_scanning/
43. GitLab docs, Audit events. https://docs.gitlab.com/user/compliance/audit_events/
44. GitLab docs, Audit event streaming. https://docs.gitlab.com/user/compliance/audit_event_streaming/
45. GitLab docs, DORA metrics. https://docs.gitlab.com/user/analytics/dora_metrics/
46. GitLab docs, Value stream analytics. https://docs.gitlab.com/user/group/value_stream_analytics/
47. GitLab docs, Deployments API. https://docs.gitlab.com/api/deployments/
48. DORA, "DORA's software delivery performance metrics" (updated 2026-01-05). https://dora.dev/guides/dora-metrics/
49. DORA, history of the metrics. https://dora.dev/insights/dora-metrics-history/
50. DORA, 2025 State of AI-assisted Software Development (PDF). https://services.google.com/fh/files/misc/2025_state_of_ai_assisted_software_development.pdf
51. Apache DevLake, DORA docs. https://devlake.apache.org/docs/DORA/
52. GitLab docs, Feature flags. https://docs.gitlab.com/operations/feature_flags/
53. GitLab docs, Infrastructure as code with OpenTofu and GitLab. https://docs.gitlab.com/user/infrastructure/iac/
54. Backstage, What is Backstage. https://backstage.io/docs/overview/what-is-backstage
55. SLSA specification v1.2 and Build track basics. https://slsa.dev/spec/v1.2/ and https://slsa.dev/spec/latest/build-track-basics
56. SLSA blog, "Announcing SLSA v1.2" (November 2025). https://slsa.dev/blog/2025/11/announce-slsa-v1.2
57. GitLab docs, SLSA. https://docs.gitlab.com/ci/pipeline_security/slsa/
58. GitLab docs, SLSA level 3 provenance attestations. https://docs.gitlab.com/ci/pipeline_security/slsa/level_3/
59. GitLab docs, Use Sigstore for keyless signing and verification. https://docs.gitlab.com/ci/yaml/signing_examples/
60. Renovate docs, PEP 621 manager. https://docs.renovatebot.com/modules/manager/pep621/
61. renovate-bot/renovate-runner README. https://gitlab.com/renovate-bot/renovate-runner
62. European Commission, Cyber Resilience Act. https://digital-strategy.ec.europa.eu/en/policies/cyber-resilience-act
63. European Commission, CRA summary. https://digital-strategy.ec.europa.eu/en/policies/cra-summary
64. European Commission, CRA reporting obligations [secondary: read through a search result summary]. https://digital-strategy.ec.europa.eu/en/policies/cra-reporting
65. Astral, Using uv in GitLab CI/CD. https://docs.astral.sh/uv/guides/integration/gitlab/
66. Astral, Ruff configuration (Jupyter notebook discovery). https://docs.astral.sh/ruff/configuration/
67. InfoWorld, "Python type checker ty now in beta" (December 2025) [secondary]. https://www.infoworld.com/article/4108979/python-type-checker-ty-now-in-beta.html
68. Simon Willison, "OpenAI acquiring Astral" (2026-03-19). https://simonwillison.net/2026/Mar/19/openai-acquiring-astral/
69. nbQA documentation. https://nbqa.readthedocs.io/
70. papermill documentation. https://papermill.readthedocs.io/
71. Jupytext documentation. https://jupytext.readthedocs.io/
72. GitLab docs, Model registry. https://docs.gitlab.com/user/project/ml/model_registry/
73. GitLab docs, MLflow client compatibility. https://docs.gitlab.com/user/project/ml/experiment_tracking/mlflow_client/
74. Breck et al., "The ML Test Score: A Rubric for ML Production Readiness and Technical Debt Reduction", IEEE Big Data 2017. https://research.google/pubs/the-ml-test-score-a-rubric-for-ml-production-readiness-and-technical-debt-reduction/
75. GitLab docs, GitLab Duo Agent Platform. https://docs.gitlab.com/user/duo_agent_platform/
76. GitLab docs, Flows. https://docs.gitlab.com/user/duo_agent_platform/flows/
77. GitLab docs, Foundational flows. https://docs.gitlab.com/user/duo_agent_platform/flows/foundational_flows/
78. GitLab docs, Flow execution. https://docs.gitlab.com/user/duo_agent_platform/flows/execution/
79. GitLab docs, Composite identity. https://docs.gitlab.com/user/duo_agent_platform/composite_identity/
80. GitLab docs, External agents. https://docs.gitlab.com/user/duo_agent_platform/agents/external/
81. GitLab docs, Code Review Flow. https://docs.gitlab.com/user/duo_agent_platform/flows/foundational_flows/code_review/
82. GitLab docs, Fix CI/CD Pipeline Flow. https://docs.gitlab.com/ci/pipelines/fix_pipeline/
83. GitLab docs, GitLab MCP server. https://docs.gitlab.com/user/model_context_protocol/mcp_server/
84. Anthropic, Claude Code GitLab CI/CD (beta, maintained by GitLab). https://code.claude.com/docs/en/gitlab-ci-cd
85. OpenAI, Codex non-interactive mode (redirected from developers.openai.com/codex/noninteractive). https://learn.chatgpt.com/docs/non-interactive-mode
86. GitHub docs, Deployments and environments. https://docs.github.com/en/actions/reference/workflows-and-actions/deployments-and-environments
87. GitHub docs, Artifact attestations. https://docs.github.com/en/actions/concepts/security/artifact-attestations
88. GitHub changelog, "Introducing GitHub Secret Protection and GitHub Code Security" (2025-03-04). https://github.blog/changelog/2025-03-04-introducing-github-secret-protection-and-github-code-security/
89. Agur Jõgi, Pipedrive's former CTO: public talk at WeAreDevelopers, July 2026 (https://www.wearedevelopers.com/videos/100261-building-an-agentic-software-factory-how-we-rebuilt-product-development-at-pipedrive), and Yash Bagal's interview about Pipedrive's AI tools (Jam.dev, 25 November 2025).
90. pre-commit [not re-checked]. https://pre-commit.com/
91. DVC documentation [not re-checked]. https://dvc.org/doc
92. pandera documentation [not re-checked]. https://pandera.readthedocs.io/
93. Splunk, "State of DevOps 2025: Review of the DORA Report" [secondary]. https://www.splunk.com/en_us/blog/learn/state-of-devops.html
94. Trunk Based Development [not re-checked]. https://trunkbaseddevelopment.com/
95. DORA, capability: trunk-based development [not re-checked]. https://dora.dev/capabilities/trunk-based-development/
