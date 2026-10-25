# Field guide: AI automation, the factory and governance

Generic edition, October 2026. A field guide to AI automation, agent factories,
Claude Code skills and plugins, telemetry, security and governance for a company of
roughly 50 to 100 people. It distils public research done between 2026-10-14 and
2026-10-25, mostly the reference files in this pack. Your own company's systems and
people outrank it on anything about your company.

Tags: [verified] means checked against a primary source, [secondary] means from
reporting, [inferred] means the author's reading. A pointer such as `knowledge/02`
§2.3 names the reference file and section with the detail and the sources. Vendor
facts hold as of the date given, or 2026-10-15 when no date is given. They go stale
quickly, so check the current docs before acting on one.

## The short version

Read this page, then open a section only when a claim matters to you.

- Find where expert hours go before building anything. Keep two maps: what AI exists,
  and where the hours go (§1).
- Tag each step of a workflow. Gathering and transforming data are code; judgment and
  writing are where a model drafts and an expert approves (§1, §2).
- Pick the simplest mechanism that works. Ask what the model does that a script
  couldn't (§2).
- Scripts in a skill cost no tokens to run (only their output enters context), so
  duplicated code costs maintenance.
  Skills people launch by hand should set `disable-model-invocation: true` (§3).
- Similar skills share one engine, inside a plugin or as a versioned package. Move
  there with snapshot tests, one piece at a time (§3).
- Run shared skills like software: an owner, a version, CI and evals, released
  through the company marketplace (§3).
- Centralise what repeats: data access, distribution, identity, model budgets, checks.
  Personal use needs no shared architecture; the policy covers it (§4).
- A factory agent is the same agent loop, started by an event. Checks come first, the
  first agent reviews, and you measure before and after (§5).
- GitLab's tier decides what can be enforced, and merge controls are what make agent
  output trustworthy (§6).
- Give each agent at most two of: untrusted input, private data, and the power to
  change state or send data out. Prompts aren't security boundaries (§7).
- Controls live in identity, the vendor console, the laptop, CI, the network and the
  repo, and the plan tier decides which exist (§8).
- Policy starts as a short, clear stance: one data-tier matrix, an intake with
  published decision times, built into the ISO/IEC 27001 system if the company runs
  one (§9).
- Onboard role by role, and keep a training record as the AI Act Article 4 evidence
  (§10).

## Contents

1. Start from where expert hours go
2. Where the model belongs in a workflow
3. Building shared skills
4. What to centralise
5. The development factory
6. GitLab foundations
7. Security and hardening
8. Controlled AI usage at organisation scale
9. Policy and governance
10. Advising teams and onboarding by role
11. Facts that change the plan
12. Where to go deeper

## 1. Start from where expert hours go

A company bottlenecked on expert hours scales with AI only where AI takes work off the
experts. So the first job is to find out where their hours go, and the second is to
find out what AI already exists. Keep the two in two maps. The gap between them is
the roadmap [inferred].

The examples assume a GitLab, Jira and Confluence stack. Swap in whatever the company
actually runs.

### The supply map: what AI exists

One row per tool, skill, plugin, agent or experiment.

| Column | Notes |
|---|---|
| Name, owner, team | Owner confirmed by a person |
| Tool and account type | Which product and plan; company, personal or free account |
| What it does, for whom | One sentence |
| Where the code or config lives | Repo and path, or "only on someone's laptop" |
| Users and frequency | Count, and how often it runs |
| Data it touches | Tier from section 9 |
| Internal systems it calls | APIs, databases, upload targets |
| How it reaches users | Copied by hand, plugin marketplace, org skill library |
| Maintenance state | Tests, CI, last change, open issues |
| Credentials | Whose identity it runs as |

For tool approval, add vendor terms (training, retention, location, DPA), admin
controls, and a decision: approve, approve with conditions, migrate or stop.

GitLab shows what exists: search every repo for `SKILL.md`, `CLAUDE.md`, `AGENTS.md`,
`.claude/`, `.mcp.json`, `plugin.json` and `marketplace.json`. Jira shows where the
requests and the pain are, people show what gets used, and Confluence shows what
people intended. AI features switched on inside tools people already use count too,
and they're easy to miss because nobody installed them [inferred]. `knowledge/04`
§6.6 covers shadow AI discovery.

### The demand map: where expert hours go

This map matters more, and documents won't give it to you. Build it from interviews.

| Column | Notes |
|---|---|
| Workflow | One recurring piece of work, such as a report run, a customer onboarding or a fault investigation |
| Trigger and volume | What starts it, how many per month |
| Hours per instance, and which expert | Timed in a walk-through |
| Steps | Each step tagged as one of the five kinds below |
| Inputs | Which systems the data lives in, and who can reach them |
| Output and consumer | Who uses the result, in what form |
| Cost of an error | Money, customer trust, physical equipment behaving differently |
| What's automated today | Scripts, skills, nothing |

Tag every step as one of five kinds. The tag says what to do with it [inferred]:

1. **Gathering data.** Code, not AI. Fetch it through a shared client (section 4).
2. **Transforming data.** Code, not AI. Deterministic and testable.
3. **Judgment over data.** Where AI fits best. The model drafts, the expert approves
   or edits.
4. **Writing.** AI drafts from the data and the judgment, and a person signs it.
5. **Communicating.** Routing the result to the right person at the right time. Often
   plain automation, sometimes a model for summarising or triage.

Pure expert judgment that can't be checked cheaply stays with the expert.

An invented example, to show the shape: a monthly customer report,
started at month end, 40 a month, 3 hours each by a customer success engineer. Pull
the site data (gathering), compute the figures (transforming), pick what's worth
saying (judgment), write the summary (writing), send and file it (communicating). The
inputs sit in two internal APIs. The reader is the customer's facilities manager. The
cost of an error is a wrong savings figure in front of a customer.

### Interview script

Use the same questions everywhere so the notes compare [inferred]:

1. Walk me through the last time you did this. Where did the time go? Time the steps.
2. Which AI tools do you or your team use today, on which accounts, for what?
3. What data do you handle that would hurt the company or a customer if it leaked?
4. What have you tried with AI that worked, and what didn't?
5. What should AI rules make easy, and what should be off limits?

People with good answers to question 4 are likely champions.

### Ranking and measuring

Rank workflows by hours per instance times volume. Then favour the ones that reuse
shared parts, such as the same data source or the same review step, because each one
built makes the next one cheaper. Hold back where an error reaches a customer's
systems until reviewers trust the checks [inferred].

For each automation, record before and after: hours per instance, how often and how
much the expert edits the draft, weekly users, and errors caught at review against
errors that got out. The edit rate is the most direct quality signal. If experts rewrite
most drafts, the tool saves little [inferred].

Augment added agents wherever work was queuing, not in lifecycle order [verified,
`knowledge/01` §1 item 3]. Pipedrive's July 2026 talk by its former CTO mapped where
attention leaks on the DevOps infinity loop and showed three agents aimed at three
leaks ([talk](https://www.wearedevelopers.com/videos/100261-building-an-agentic-software-factory-how-we-rebuilt-product-development-at-pipedrive))
[secondary, transcript, `knowledge/01` §2.3].

## 2. Where the model belongs in a workflow

### Start with the simplest design

Guidance from 2026 agrees: keep what you can map deterministic, and put a model only
where judgment is needed [verified 2026-10-25]:

- Google's ADK 2.0 post (July 2026) says to use determinism wherever the workflow can
  be mapped, with model steps only where judgment is needed
  ([source](https://developers.googleblog.com/en/why-we-built-adk-20/)).
- Google Cloud's agent design guide (reviewed May 2026) says non-agentic solutions
  can be more cost-effective for predictable tasks, and starts with one agent
  ([source](https://docs.cloud.google.com/architecture/choose-design-pattern-agentic-ai-system)).
- Anthropic (January 2026) says to start with one agent. Multi-agent systems use 3 to
  10 times more tokens and help mainly with context pollution, parallel work or
  specialisation
  ([source](https://claude.com/blog/building-multi-agent-systems-when-and-how-to-use-them)).
- Birgitta Böckeler (April 2026) splits an agent's controls into computational ones,
  deterministic and cheap enough to run on every change, and inferential ones judged
  by a model ([source](https://martinfowler.com/articles/harness-engineering.html)).
  Stripe's coding agents mix deterministic steps such as lint and push with agent
  steps, and hand over to a person after two CI rounds
  ([source](https://stripe.dev/blog/minions-stripes-one-shot-end-to-end-coding-agents-part-2)).

Anthropic's "Building effective agents" (December 2024) gave the same advice and named
the workflow patterns still in use, among them routing, where a classifier sends each
request down a specialised path. The page now notes that its tooling has changed and
points to Managed Agents
([source](https://www.anthropic.com/engineering/building-effective-agents)). Most
internal automation is a workflow with one or two model calls in it [inferred].

### Split code work from model work

Keep fetching, calculating, rendering and uploading deterministic. Call the model only
where judgment or language is needed. For anything that can change a customer's
physical equipment or place a binding market bid, the rule is "models propose, deterministic code decides",
and a person acts through the existing control path [inferred, `knowledge/04` §1 and
§5.13].

### The expert reviews the draft

The usual way AI frees expert hours is human-in-the-loop review [inferred]. The model
produces a draft from the data, and the expert edits and approves it. Review then
becomes the new bottleneck. Chen and Stratton's study of 718 firms found agents' gains
don't fully reach shipped output, and traced the gap to code review [verified,
`knowledge/01` §1 item 5, §2.6]. So make the review step fast: sources next to each
claim, what changed since last time, and cheap "approve" and "edit this section"
actions.

### Picking the mechanism

Ask of every AI tool what the model does that a script couldn't. If the answer is
"not much", build a script or an app [inferred].

| Mechanism | Fits when | Watch out for |
|---|---|---|
| Script or CLI | The work is deterministic | Nothing AI-specific |
| Claude Code skill | A person drives the work, the steps change per run, and they want to steer by chat | Every user's laptop becomes a deployment target: runtime, credentials, OS differences |
| MCP server | Several skills or agents need the same data or actions | It runs with the user's privileges; vet and pin it (section 7) |
| Internal web app with server-side model calls | Many non-technical users, a fixed flow | More upfront build; the payoff is one deployment, one key, central logs |
| Headless agent in CI | An event (push, issue, schedule) should start one narrow task | Least privilege and isolation (sections 5 and 7) |
| Chat project with shared instructions | Drafting and research with no system access | Data rules apply to what people paste in |

A good default is an engine that works without any model, with model calls only where
judgment is needed. A skill can launch it locally today, and the same engine can move
behind an internal web app later without a rewrite [inferred].

## 3. Building shared skills

### How Claude Code loads skills

Checked 2026-10-24 against [the skills docs](https://code.claude.com/docs/en/skills)
and the [Agent Skills overview](https://platform.claude.com/docs/en/agents-and-tools/agent-skills/overview)
[verified]:

- At startup Claude sees each skill's name and a capped description (1,536 characters
  with `when_to_use`). The listing gets 1% of the context window; over budget, the
  least-used skills lose their descriptions first.
- `disable-model-invocation: true` keeps the description out of context entirely. The
  skill then loads only when a person types `/name`.
- The SKILL.md body loads when the skill runs and stays for the session, within
  compaction limits. Other files in the skill folder load only when Claude reads them.
  Scripts run without entering the context; only their output does.
- Skills take arguments (`$ARGUMENTS`, positional `$0`, `$1`), and `/context` shows
  what fills the context window.

What follows [inferred]:

- Duplicated script code costs maintenance, not run-time tokens: every fix made
  several times, copies drifting apart, Claude reading near-identical files when asked
  to fix a bug.
- Skills that people launch by hand should set `disable-model-invocation: true`.
- One entry skill costs no more than several if its SKILL.md routes: it asks
  which variant, or takes it from `$0`, then reads that variant's instruction file.
  Putting every variant's instructions into one SKILL.md is what makes it expensive.
- A run's cost depends mostly on what Claude reads during it, such as API responses
  and generated files. Measure one real run with `/context` before optimising.

### Sharing code between skills

- **Inside one plugin.** `${CLAUDE_PLUGIN_ROOT}` is substituted inside a plugin's skill
  content, and the docs mention resources shared between a plugin's skills. So one
  plugin can hold a shared engine folder and several thin skills that call it.
  `${CLAUDE_SKILL_DIR}` is the skill's own folder [verified 2026-10-24].
- **Across plugins, in Claude Code and CI.** Publish the shared engine as a versioned
  Python package. GitLab's package registry, PyPI included, is on every tier,
  self-managed too ([docs](https://docs.gitlab.com/user/packages/pypi_repository/))
  [verified 2026-10-24]. Each skill pins an engine version, and the engine gets its
  own tests and CI [inferred].
- **claude.ai uploads.** An upload is a ZIP whose root is the skill folder
  (`my-skill/SKILL.md`) ([help](https://support.claude.com/en/articles/12512198))
  [verified 2026-10-24]. Skills there may not reach a private package registry:
  network access depends on settings, and a GitLab behind a VPN may be out of reach
  from Anthropic's cloud. Such a skill may have to bundle a copy of the engine, built
  from the one source [inferred]. A skill one person uploads is theirs. On Team and
  Enterprise, Owners can provision skills for the whole org, and members can publish
  them ("Distributing them" below).

### Shape of a shared engine

For a family of similar tools, such as several report types with the same
pick-edit-render-save flow, the classic patterns apply [inferred]. Template Method or
Strategy gives one fixed pipeline with per-variant parts plugged in, where each
variant is a definition that is mostly data: sections, data sources, template, prompt
text. Template inheritance, as in Jinja, gives one base HTML template with blocks each
variant overrides. The rule of three says to extract once a third copy appears.

To move duplicated tools onto one engine, write snapshot tests first: render each
variant from saved input and compare it with a stored output [inferred]. Then replace
one piece at a time while the old code keeps running, the pattern Martin Fowler named
the strangler fig ([source](https://martinfowler.com/bliki/StranglerFigApplication.html))
[verified 2026-10-24]. Agree the target shape with the owner in a short decision
record first [inferred].

### Cross-platform hygiene

Tools that run on people's laptops break on OS differences. The first two points were
checked 2026-10-24 against the Git for Windows installer source, the git docs and the
Python docs [verified]:

- The Git for Windows installer sets `core.autocrlf=true` by default, so files are
  checked out with CRLF line endings. A `.gitattributes` with `* text=auto eol=lf`
  overrides that for the repo; then run `git add --renormalize .`, commit, and check
  the files out again.
- On Windows, Python writes every `\n` as `\r\n` in text mode unless you pass
  `newline="\n"`; `Path.write_text` takes `newline` from Python 3.10.
- Use `pathlib` and pytest's `tmp_path` instead of hard-coded Linux paths, and run
  the tests on the users' OS if the runners allow it [inferred].

### Running shared skills like software

Give every shared skill an owner, a version, a changelog and a way to retire it, plus
CI: lint and tests for the code, snapshot tests for generated output, and saved
example cases for the model's part [inferred].

Keep the organisation's agent configuration in one repository, reviewed by merge
request [inferred layout, `knowledge/05` §2.6]:

```
ai-config/
  policy/managed-settings.json          # org-wide, delivered by console or MDM
  marketplace/.claude-plugin/marketplace.json
  marketplace/plugins/<plugin>/         # skills, hooks, agents, MCP config
  templates/AGENTS.md                   # repo starter
  catalogue/mcp-servers.yaml            # approved MCP servers, owner, tier, scopes
  CODEOWNERS                            # policy owner and security approve policy/
  .gitlab-ci.yml                        # schema check, policy tests, release
```

Its CI checks settings JSON against Claude Code's published schema, which the admin
console's editor also uses and which can lag new releases, so a warning on a newly
documented key isn't proof of an error. It also runs `claude doctor` in a container
to list invalid entries [verified 2026-10-24, `knowledge/05` §2.6]. A policy test runs a scripted
session that tries a denied read and a blocked MCP server and expects both refused
[inferred]. Releases go through two marketplaces at different refs, stable and early
access. Assigning them per group needs per-group endpoint-managed settings (a file or
an MDM profile) or the Claude apps gateway [verified, `knowledge/05` §2.6].

Test skills before and after changes [verified 2026-10-22, `knowledge/05` §2.6].
Anthropic says to write at least three evaluations before writing much of a skill,
and to test on every model in use. `claude plugin eval` scores a plugin against a
no-plugin baseline. For skills Claude may start itself, test with 3 to 5 queries,
including ones that shouldn't trigger it, plus a coexistence test against the existing
set, and have someone other than the author review it.

For agents that run unattended, build an eval set of 20 to 50 tasks taken from real
failures, and track pass^k, the chance that all k tries succeed: at 75% per try, three
in a row succeed about 42% of the time. Keep capability evals apart from regression
evals, which stay near 100% and catch regressions [verified, `knowledge/01` §2.4].

Keep repo instructions short: a root `AGENTS.md` of 60 to 100 lines with setup, test
and lint commands and pointers into `docs/` [inferred, `knowledge/01` §2.4]. Claude
Code reads `AGENTS.md` when a project has no `CLAUDE.md` or `CLAUDE.local.md`, and Codex, Copilot, Cursor
and others read the Agent Skills format [verified, `knowledge/01` §1 item 7, §2.4].

### Distributing them

Verified 2026-10-24 against the [org plugin docs](https://code.claude.com/docs/en/plugins/org)
and the [CLI reference](https://code.claude.com/docs/en/plugins/cli-reference):

- A marketplace can live in any git repo, a self-managed GitLab included. Managed
  settings add it on every laptop (`extraKnownMarketplaces`) and allowlist or block
  sources (`strictKnownMarketplaces`, `blockedMarketplaces`). Each user needs read
  access to the repo, or the plugins can be pre-seeded with
  `CLAUDE_CODE_PLUGIN_SEED_DIR`.
- A self-hosted GitLab URL without `.git` at the end may be read as a direct
  `marketplace.json` URL, so set `"source": "git"` explicitly. Plain allowlist entries
  match exactly, so `.git`, a trailing slash or `ssh://` against `https://` differ; a
  `hostPattern` entry covers the whole host.
- claude.ai has a second route on Team and Enterprise: Owners upload or sync plugins
  from GitHub or GitLab (GitLab sync in beta), members publish skills under a policy
  that, when nobody has set it, defaults to Open on Team and Requires review on
  Enterprise, and org plugins show up in Claude Code as `<name>@synced`. Anthropic
  suggests feeding both routes from one repository [verified 2026-10-24, claude.com
  plugin admin docs, `knowledge/05` §2.6].
- Inside Anthropic, skills start in a sandbox folder, and the owner opens a pull
  request into the marketplace once a skill has traction [verified 2026-10-22,
  `knowledge/05` §2.6].

## 4. What to centralise

Centralise what every use case repeats, and let teams build their own tools on top
[inferred].

In Team Topologies' terms ([source](https://teamtopologies.com/key-concepts)) an AI
enablement role is mostly an enabling team, helping others adopt a capability, with
a thin platform underneath [inferred]. Under 100 people, golden paths beat portals: a
repo template, a component library and a short guide per role, with no portal to run
[inferred, `knowledge/02` §1 item 13, §4].

### The layers that repeat

These are the places where shared architecture is worth building [inferred]:

- **Data access.** Several tools will read the same product and customer data. One
  maintained client library, plus an MCP server for agents, running on service
  credentials, beats each tool carrying its own fetch code and someone's personal
  token.
- **Distribution.** One internal plugin marketplace, versioned and tested (section 3).
- **Identity, secrets and logs.** Each tool runs as a known identity with the narrowest
  credentials that work, and its use is logged as metadata (section 9).
- **Model access and budgets.** CI agents, internal tools and experiments call model
  APIs with keys. A small gateway, or the vendor's workspace limits, gives each its own
  key, model allowlist and spend cap (`knowledge/05` §1 item 8; section 8 says when to
  run one).
- **Lifecycle.** Each shared tool needs a named owner, known users and a retirement
  path.
- **Checks.** Shared CI for the code, saved example cases for the model parts.

One person's own productivity use, such as drafting, research or their own scripts,
needs no architecture. The policy covers it with permitted tools and data tiers.

### Make or buy, and quick wins

Buy the tools: one chat assistant and one coding agent, on a business plan. Build only
what connects them: a thin configuration repo, CI checks, an MCP catalogue with a vetting
process. Run an existing gateway product rather than writing one [inferred,
`knowledge/05` §1 item 14, §2.7]. Ship each standard as a component or template, a
decision record, a one-page how-to, and a check that shows who has adopted it
[inferred, `knowledge/02` §1 item 14].

Quick wins are worth doing if each leaves a reusable piece behind: a shared library,
CI for its repo, an owner, a version. Quick wins that leave nothing behind add
duplication [inferred].

## 5. The development factory

### How a factory agent runs

A factory agent is the same coding-agent loop a developer runs in a session, started by
an event instead of a person. Something happens in GitLab, such as a push to a merge
request. A disposable container starts on a runner. The agent runs there unattended,
with a prompt, a few tools and a key, inside set limits. Fixed script steps turn its
output into something a person reads, and the container is thrown away [inferred,
built from `knowledge/01` and `02` §7.3].

### Principles

- Each agent is a configuration: a trigger, scoped permissions, one output, a check
  by something that didn't do the work, and an owner [inferred, `knowledge/01` §2.5].
  Persona chains, such as a product-manager agent handing to an architect agent, add
  handoffs and lose context [verified, `knowledge/01` §1 item 4].
- Checks come before agents. Three of DORA's seven AI capabilities are quality
  internal platforms, strong version control and small batches, and the working agent
  fleets all sit on fast deterministic feedback [verified, `knowledge/01` §1 item 2].
  Shared CI jobs live once, in a CI component that each repo includes (section 6).
- The first agent reviews and is measured. It catches defects before merge with no
  write access to code, though it still reads untrusted merge request text (section
  7). It won't cut review load by itself, so filter its comments hard and track the
  findings people act on and the ones they reject as wrong [verified evidence,
  inferred plan, `knowledge/01` §1 item 5]. A narrow code-writing agent for one task
  class, such as dependency upgrades, can follow once reviewers trust the checks.
- Each agent gets only the tools and credentials its one job needs, set by flags, and
  a person merges.
- Knowledge in chat, documents or people's heads is out of an agent's reach, so docs
  and requirements in the repo are factory inputs [verified, `knowledge/01` §1 item
  6].
- Prompts, model versions and agent defaults are production changes. Anthropic's
  April 2026 postmortem traced weeks of degraded Claude Code output to three
  product-side changes, not a model change. Keep your own eval set and pin versions
  [verified, `knowledge/01` §1 item 9].

### Measure before and after

Take four to six weeks of history from GitLab before switching anything on, then keep
measuring [inferred, `knowledge/01` §2.7]:

| Before agents | Once agents run |
|---|---|
| The DORA five: change lead time, deployment frequency, failed deployment recovery time, change fail rate, deployment rework rate | Cost per merged change, by agent workflow |
| Merge request size, time to first review, time to merge, review rounds | Review load: time to first review, review hours per merge request |
| Pipeline duration, failure rate, flaky-test rate | First-run CI pass rate for agent merge requests |
| Reverts and fix-forward merge requests within 14 days | Agent merge request acceptance and rework within 14 days |
| Current AI tool use and spend, even if informal | The review agent's acted-on rate, plus a rejected-as-wrong rate from a weekly sample |
| A short developer survey on friction | Human attention per merged change |

Report delivered changes and their quality, not "hours saved". Measure teams, never
individuals (section 9). DORA warns against treating token spend as a performance
indicator, and its June 2026 note suggests cost per accepted change and rework rate,
at team level ([source](https://dora.dev/insights/finding-balance-in-the-era-of-tokenmaxxing/))
[verified 2026-10-25, `knowledge/01` §1 item 8].

### Failure modes to design against

`knowledge/01` §2.8 lists fifteen failure modes with real cases behind them [cases
mostly verified; Replit and GitClear via reporting]. The eight that matter most here
are generators outrunning reviewers,
tests that lie, scope creep, duplication and churn, destructive action in production,
vendor-side regressions, approval fatigue (Claude Code users approve 93% of permission
prompts) and too many parallel agents. The counters are mostly structural: small
batches, hidden verifiers, an independent check of the diff against the ask, no
production credentials in agent environments, your own evals and pinned versions.

Tell teams what the evidence says. METR's 2025 trial found experienced
developers 19% slower with AI while they felt faster [verified, `knowledge/01` §1 item
11], and DORA expects a dip before gains [secondary, `knowledge/01` §2.6].

## 6. GitLab foundations

The tier decides which guardrails GitLab can enforce. Required approvals, Code Owners,
push rules, protected environments and merge trains need Premium. Cross-project
enforcement through pipeline execution, scan execution and merge request approval
policies, built-in DORA metrics, dependency scanning and secret push protection need
Ultimate [verified, `knowledge/02` §1 item 1, §2.1]. Without Ultimate, plan for "the
default is good and drift is visible" [inferred, item 3].

CI/CD components, on every tier, are the cheapest way to standardise: typed inputs,
semantic versions, a private catalog [verified, `knowledge/02` §1 item 2, §2.3].

Trust in agent output comes from merge controls: a protected default branch, merge
requests required, passing pipelines, and on Premium, no approval by the author and
approvals reset by new commits. Duo's own review approval doesn't count toward
required approvals [verified, `knowledge/02` §1 item 4, §2.8, §7.1].

Runners are a trust boundary. Shared, non-ephemeral, privileged or shell runners let
anyone with the Developer role reach other projects' code and secrets, so deploys
belong on protected runners [verified, `knowledge/02` §1 item 8, §2.5]. OIDC
`id_tokens`, on every tier, replace stored cloud keys in CI variables [verified, §2.10].
The Deployments API, on Free, gives enough to compute deployment frequency and lead
time without Ultimate [verified, §3.3].

GitLab's own agents (Duo flows, and external agents such as Claude Code) run as CI jobs
under a composite identity when started from GitLab; agent commits show the service
account, and the merge request is attributed to the person who triggered it
[verified, `knowledge/02` §1 items 5 and 6, §7.1, §7.2, §7.7].

GitLab's MCP server is generally available from 19.5 on all tiers. Its write tools
include `accept_merge_request`, which merges [verified 2026-10-24,
[docs](https://docs.gitlab.com/user/model_context_protocol/mcp_server_tools/)], so
branch protection on the server is the control that counts [inferred].

A five-stage maturity ladder [inferred, `knowledge/02` §8, with exit criteria for
stages 1 to 3]: every change goes through a pipeline; shared and secure by default;
deploy only from pipelines; agent-ready; hardened and measured.

## 7. Security and hardening

### The Rule of Two

Meta's rule (31 October 2025) says an agent session should hold at most two of three
properties [verified, `knowledge/04` §2.8]:

1. it reads untrusted input;
2. it reaches private data or sensitive systems;
3. it can change state or send data out.

If it needs all three, a person approves or the job is split. Simon Willison's "lethal
trifecta" is the same idea with a narrower third property: sending data out, where
Meta counts any change of state. Classify every agent this way, in the risk register.
Applied to likely agents [inferred, `knowledge/04` §2.8, §5.13]:

- A findings-only review agent reads untrusted diffs and private code, so it gets no
  network or write tools, and a person reads its findings.
- A developer's coding agent with repo access and web fetch holds all three by
  default. A sandbox egress allowlist narrows what shell commands reach, but web fetch
  and MCP tools sit outside it, so approval before push and fetch is what puts a
  person in the loop.
- An agent that turns customer documents into system inputs runs once per customer,
  with no egress, and sends its output to a review queue.
- No agent holds credentials that write to customer systems.

### Where the security boundary is

- Prompts and CLAUDE.md text guide well-meant work and stop no attack. The OS sandbox, the
  network egress allowlist and credential scope are the boundary [verified,
  `knowledge/04` §1].
- Claude Code's sandbox runs on macOS, Linux and WSL2 and wraps shell commands only.
  On native Windows, commands run unsandboxed. Claude's file tools, MCP servers and
  hooks run outside it [verified 2026-10-24,
  [docs](https://code.claude.com/docs/en/sandboxing)]. Know the laptop OS mix before
  promising anything.
- Hooks guard against accidents; an injected agent can write the same command in
  Python. Deliver policy hooks through managed settings with `allowManagedHooksOnly:
  true` [verified mechanics, inferred advice, `knowledge/04` §5.6].
- Researchers got code execution through `.claude/settings.json` and `.mcp.json`
  (since patched), and the Mini Shai-Hulud worm planted a `SessionStart` hook to
  persist [verified cases, `knowledge/04` §1, §4]. Agent config files need code-owner
  review [inferred, §5.9].
- In CI the recurring attack shape is untrusted text reaching a workflow or agent with
  a shell, which reaches a cache or a token, which publishes a package [verified
  cases, `knowledge/04` §1, §4, §5.10]. Split the work so the agent that writes never sees untrusted content
  [verified, `knowledge/04` §1].
- Generated code invents package names, at rates of at least 5.2% for commercial
  models in one large study [verified, `knowledge/04` §1]. Lockfiles and dependency
  review catch it.

### MCP servers and plugins

MCP servers, plugins, skills and extensions are third-party code with the user's
privileges. One malicious MCP server shipped 15 clean versions before its backdoor.
Allowlist, pin, and review each update [verified, `knowledge/04` §1, §5.8].

Vet each server on ten points [inferred, built from OWASP's MCP list and vendor docs,
`knowledge/05` §2.5]: provenance and a pinned version; every tool listed, with read
and write split; tool descriptions read for hidden instructions; OAuth with the
narrowest scopes and no token passthrough; whether it returns outside text that could
steer a write tool in the same agent; whether its tools can change after approval;
where it sends data; whether tool calls show in telemetry; whether the catalogue
already has the same capability; and a decision by tier and role, read-only first,
with a review date. Record decisions in an approved-connector catalogue and generate
each tool's managed allowlist from it.

### Baseline by agent tier

From `knowledge/04` §5.13 [inferred]:

| Tier | Isolation | Credentials | Approval |
|---|---|---|---|
| Interactive on a laptop | Sandbox where supported, bypass mode disabled | Personal; secrets denied | Ask on push, deploy, destructive commands |
| Autonomous on a laptop | Devcontainer or VM | Scoped dev tokens only | At merge request |
| Agent in CI | Runner container, egress allowlist | Per-agent service account, short-lived | Merge request approval |
| Operations, read only | Isolated runtime | Read-only tokens | None needed for reads |
| Writes to customer systems | Not applicable: the agent may not act directly | Never held by an agent | A person per action, through the existing control path |

## 8. Controlled AI usage at organisation scale

### Where controls live

A policy has to name each place [verified facts, `knowledge/05` §1, §2.2, §2.3,
`knowledge/04` §5.10, §6.1, `knowledge/02` §7.3]:

| Place | What it controls | Limit to know |
|---|---|---|
| Identity | Who gets a seat; `forceLoginOrgUUID` pins Claude Code to the company org | On Anthropic, Team has SSO but no SCIM, audit logs or Compliance API; those are Enterprise only |
| Vendor console | Plan, retention, connectors, spend, server-managed settings | Server-managed Claude Code settings are one policy for the whole org; per-group Claude Code policy needs endpoint-managed settings (file or MDM) or a Claude apps gateway |
| The laptop | Managed settings override user and project settings | Anthropic calls server-managed settings a client-side control, not a security boundary. MDM or file delivery is stronger, but a local admin can still edit the file |
| CI jobs | The job's own flags (`--bare`, `--setting-sources`, `--tools`, the `dontAsk` mode, turn and budget caps) and a managed settings file in the runner image | claude.ai admin-console settings don't reach a job that signs in with a Console API key |
| Network | Egress allowlist, sandbox, proxy or gateway | A custom `ANTHROPIC_BASE_URL` skips server-managed settings |
| Repository | Protected branches, approvals, code owners on agent config | Works only if an agent can't approve its own merge request |
| Monitoring | Claude Code and Codex telemetry, GitLab AI audit events | It only reports. GitLab's AI audit events are a beta on Premium and Ultimate, off until an Owner or Security Manager turns them on |

### Plans, seats and accounts

The plan tier decides which controls exist, so choosing the plan is the first policy
decision [verified, `knowledge/05` §1 item 2].

On a Team plan, checked 2026-10-24, a Standard seat gives 1.25x Pro's per-session usage
and a Premium seat 6.25x, with weekly limits on top. Past the allowance, Owners prepay
usage credits at API rates, which members can spend in Claude Code, with spend limits
per org and per member. Team's pooled API credits can't be spent in Claude Code or the
apps [verified, `knowledge/05` §2.2,
[Team plan](https://support.claude.com/en/articles/9266767-what-is-the-team-plan)].

Anthropic's EEA consumer terms, section 11, say the services may not be used "for any
commercial or business purposes" [verified 2026-10-22, `knowledge/05` §2.2]. On
consumer plans the user chooses whether chats and coding sessions train models. With
training on, retention is up to 5 years; with it off, 30 days. Commercial plans aren't
trained on by default
([source](https://www.anthropic.com/news/updates-to-our-consumer-terms)) [verified
2026-10-24].

Anthropic's first-party API offers only `global` and `us` inference. EU-only routes are
Amazon Bedrock `eu.` profiles or Google Cloud in an EU region, meaning the
Google-managed Vertex AI route [verified 2026-10-24,
[data residency](https://platform.claude.com/docs/en/build-with-claude/data-residency),
`knowledge/05` §1 item 4].

### When to run a gateway

A gateway is worth running when several programs call model APIs and each needs its
own key, allowlist and budget, when traffic must be routed by data tier, when more
than one provider is used programmatically, when Bedrock or Google Cloud users need
per-user attribution and spend caps, or when you need a request-level audit log under
your own control. Seat-based SaaS traffic, such as claude.ai chat or GitLab Duo, never
passes through it, so the vendor console, SSO and plan govern those. Seat-based
Claude Code loses features behind a gateway, so managed settings govern it [inferred,
`knowledge/05` §1 item 7, §2.5].

### Telemetry

Claude Code exports OpenTelemetry metrics and events and redacts prompts by default.
Default to metadata: who, which tool, tokens, cost. On Enterprise the Compliance API
can read full session transcripts, so who holds that key is a policy decision
[verified, `knowledge/05` §1 item 11]. A plugin from the company's own marketplace
counts as third-party, so its skill and plugin names show as `third-party` on cost
and token metrics, and in the Enterprise Analytics API, unless
`OTEL_LOG_TOOL_DETAILS=1` is set. That flag also exports commands, file paths, URLs
and other tool arguments
([docs](https://code.claude.com/docs/en/plugins/measure)) [verified 2026-10-25].
Logging prompt content is probably monitoring
of employees under EU law, which legal confirms (section 9) [inferred, `knowledge/04`
§6.4].

## 9. Policy and governance

### Written rules and enforced controls

A rule that's only written down isn't a control. For engineers, tooling enforces:
managed settings, CI job flags, protected branches, scoped credentials. The written
policy mostly serves everyone else, and explains the tooling to engineers [inferred,
`knowledge/05` §1 item 1, `knowledge/06` §1].

### Shape of the policy

A short, clear stance comes first. DORA counts a clear, communicated AI stance as a
capability in its own right, and uncertainty pushes people to hide their use
[verified, `knowledge/06` §1]. A one-page interim rule sheet early does more than a
complete policy late. Then three layers: the one-page sheet, a signed policy of about
1,500 to 2,000 words, and living appendices that change without re-approval (tool
register, FAQ, pattern catalogue, training record) [inferred, `knowledge/06` §2.2,
§2.3].

Rule one: a named person owns any AI output they pass on. LLVM, the Linux kernel,
Fedora, Oxide and GitLab's public team pages all say this [verified, `knowledge/06`
§1].

One matrix carries most of a readable policy: data tier against tool. A starting
proposal, to adapt with legal once the customer contracts are in hand [inferred,
`knowledge/06` §2.3]:

| Tier | Covers | May go |
|---|---|---|
| Green | Public material | Any tool on the register |
| Yellow | Internal and customer-confidential material without personal data: source code, internal docs, operational data | Approved company tools only: business terms, no training, SSO |
| Red | Personal data, data a customer contract restricts, unreleased financials, security findings | Only with written approval for that use |
| Never | Passwords, API keys, tokens, remote-access details, network maps of customer sites | No AI tool |

The one-pager can carry it as a five-question decision tree [inferred, `knowledge/06`
§2.2]:

1. Is everything you'll paste already public? Go ahead with any tool on the register.
2. Does it hold passwords, keys, tokens, remote-access or site network details? Stop.
3. Does it hold personal data or data a customer contract restricts? Red: ask first.
4. Is it other internal or customer-confidential material? Yellow: approved company
   tools only, and ask your champion about a new kind of use.
5. Will the output leave the company, go into the product or change how physical
   equipment runs? A named person checks it first and owns it.

Test the one-pager on five non-engineers with ten scenario cards. Fewer than eight
right answers means a rewrite [inferred, `knowledge/06` §2.2].

`knowledge/06` §2.3 has a nineteen-heading skeleton for the signed policy.

### Who decides, and how fast

A small structure fits a company of this size: an approver (the CTO or the CEO), a
policy owner, champions at about one per team, and a working group of five (approver,
policy owner, IT or security lead, HR lead, a legal or data-protection contact) that
meets for 30 minutes every two weeks for three months, then monthly [inferred,
`knowledge/06` §2.4, which has the full responsibility table].

New tools and use cases come through an eight-question intake form: what, which tool,
which data tier, who sees the output, whether it feeds a decision about a person,
money or how physical equipment runs, whether it acts on its own, cost, and who owns it. Routing, with
proposed decision times [inferred, `knowledge/06` §2.4]:

| Case | Route | Decision time |
|---|---|---|
| Listed tool, green or yellow data, known kind of use | Self-serve | None |
| Listed tool, new kind of yellow use | Champion | 2 working days |
| New tool, yellow data | Policy owner, IT or security | 5 working days |
| Red data, decisions about people, or autonomous action outside engineering sandboxes | Policy owner, legal or data protection, approver | 15 working days |

Default to "yes, with conditions". Record every decision with its date and reason,
publish refusals with the reason and an alternative, and publish the real median
decision time each month. Exceptions last 90 days at most, with a named owner, a
written reason and a compensating control. An exception renewed twice becomes a
policy change or ends.

### Rules and what enforces them

Where configuration can enforce a rule, the document states the rule and its reason
and leaves the mechanics to the config. When a control blocks someone, its message
says why and where to ask [inferred, `knowledge/06` §2.4]:

| Rule | Enforcement |
|---|---|
| Only approved tools receive company data | SSO, licence provisioning, extension controls, a gateway if one exists |
| Coding agents don't read secrets or skip permission checks | Managed settings with deny rules and bypass mode disabled |
| AI-written code is reviewed | Protected branches, required approvals, passing pipelines |
| Agent-written commits are disclosed | A commit-message check for `Assisted-by:` that warns |
| No secrets in repos or prompts | Secret scanning before commit and in CI |
| Yellow and red data stay where allowed | Provider region settings or gateway routing |
| Training comes before access | Licence granted after the core module |

Agents get one of four autonomy levels: suggest only; act in a sandbox; act with
approval for each action; act alone within scoped permissions. The fourth needs a
decision record and a named owner, and never touches customer systems or production
data without the approver [inferred, `knowledge/06` §2.3].

### Plug into ISO/IEC 27001

If the company runs an ISO/IEC 27001 management system, extend it rather than build a
parallel one [inferred,
`knowledge/03` §2.5, `knowledge/04` §7.3]:

- Add AI risks to the risk register, each agent classified with the Rule of Two, in
  the vocabulary of the OWASP Top 10 for Agentic Applications.
- Keep the same incident commander, channel and postmortem. AI adds triggers, evidence
  to preserve first (transcripts, telemetry, the agent's exact config and versions),
  containment levers (switch a tool off, revoke an agent's service account, rotate a
  key), and a review question: which Rule of Two property made it possible?
- Write and rehearse six runbooks [`knowledge/04` §7.4]: prompt injection caused an
  action; data sent to an unapproved AI service; an agent's destructive action; a
  malicious plugin, skill or MCP server; a leaked API key; wrong AI output reached a
  customer.
- Treat AI vendors, MCP servers and plugins as suppliers with data access, and use
  ISO/IEC 42001, which shares 27001's clause structure, as the skeleton for the AI
  parts, certified or not.
- The Annex A controls AI use touches most are 5.10 acceptable use of information,
  5.14 information transfer, 7.10 storage media, 8.1 user endpoint devices and 8.12
  data leakage prevention [control numbers and titles secondary, from a
  [published Statement of Applicability](https://www.euronext.com/sites/default/files/2025-12/Euronext%20Clearing%20SoA%20-%20ISO27001.pdf),
  since the standard is paywalled; the selection is inferred].

### Monitoring and people

Tell employees what is recorded before anyone finds out alone [inferred, `knowledge/06`
§1, §2.3]:

| Recorded | Why | Who can see it | Never used for |
|---|---|---|---|
| Tools and seats per person | Inventory, cost | IT, policy owner | Performance evaluation |
| Usage statistics | Adoption, cost | Policy owner, reported per team | Ranking individuals |
| Prompts and responses, where a plan or gateway keeps them | Security investigation | Security lead, on a defined trigger, each access logged | Browsing, performance review |
| Coding agent and CI logs | Debugging, incident review | Engineering | Same as above |
| Training records | AI literacy evidence | HR, policy owner | Not applicable |

Logging prompt content processes employees' personal data. Consent rarely works as
the legal basis at work, so it rests on legitimate interest with a documented
balancing test ([WP29 Opinion 2/2017](https://ec.europa.eu/newsroom/article29/items/610169)).
Systematic monitoring of employees is the guidelines' own example of when an impact
assessment is needed ([WP248](https://ec.europa.eu/newsroom/article29/items/611236)), and Estonia's data protection authority lists
large-scale systematic monitoring of employees as a case
([AKI](https://www.aki.ee/5-peatukk-andmekaitsealane-mojuhinnang)) [verified
2026-10-25]. France's Conseil d'État upheld real-time productivity indicators in a
warehouse case in December 2025, but found that keeping all of them for 31 days
broke data minimisation
([no. 492830](https://www.conseil-etat.fr/fr/arianeweb/CE/decision/2025-12-23/492830))
[verified 2026-10-25]. Measure teams,
never individuals. Ask HR whether staff representatives (in Estonia, the employees'
trustee) must be consulted before rolling out AI tools or agent telemetry [inferred,
`knowledge/06` §2.4].

### EU law in brief

As of October 2026, not legal advice [verified unless tagged, `knowledge/03` §1,
§2.1]:

- The AI omnibus, Regulation (EU) 2026/1744, entered into force on 27 July 2026. Annex III high-risk
  duties start 2 December 2027 and Annex I duties 2 August 2028. Prohibitions, AI
  literacy, GPAI rules and Article 50 already apply.
- Article 4 asks for measures that support AI literacy, with no guaranteed level per
  person, supervised from August 2026. A training record is the evidence, so
  role-based onboarding is also a compliance deliverable.
- Internal AI use makes a company a deployer, and the Act asks almost nothing more of
  non-high-risk use [inferred]. Putting an internal agent system into service makes the
  company a provider [verified, text], minimal-risk unless pointed at a listed use [inferred].
- AI that monitors or evaluates individual workers, such as per-engineer telemetry
  scored for managers, can be high-risk under Annex III point 4 from 2 December 2027.
  The draft guidelines include probation periods.
- GDPR does more day-to-day work than the AI Act [inferred]. Reporting clocks can stack: GDPR 72
  hours, NIS2 24 and 72 hours if in scope, the Cyber Resilience Act's 24-hour early
  warning for products with digital elements [verified, CRA from the text,
  `knowledge/03` §2.4, `knowledge/04` §7.5].
- Call a lawyer before "not high-risk" goes into a contract, before deciding NIS2 or
  CRA applicability, for AI touching employee evaluation, and for any incident with a
  reporting clock [inferred, `knowledge/03` §2.8]. `knowledge/03` §2.7 sketches answers for
  customer questionnaires about AI.

## 10. Advising teams and onboarding by role

Shadow AI is the starting state. In MIT NANDA's 2025 study, workers at over 90% of
surveyed companies used personal AI tools for work, while only 40% of the companies
had bought an official subscription [verified survey, `knowledge/06` §1]. Start with
discovery and an amnesty, then give people an approved tool that's easier than their
personal one.

Publish how long a tool request takes and meet it, since a six-week queue recreates
shadow AI [inferred, `knowledge/06` §1]. Advise with a catalogue of risky and better
patterns per function, built from interviews [inferred, `knowledge/06` §2.5].
Champions, about one per team at 30 to 60 minutes a week, answer most questions
before they reach the AI team [verified sources, sizing inferred, `knowledge/06` §1].
Mandates have drawn backlash, Duolingo's "AI-first" memo being one case [secondary],
so write each function's part of the rules with that function [inferred,
`knowledge/06` §1, §2.7].

A curriculum shape [inferred, `knowledge/06` §2.6]: a 60-minute core module for all
staff; a 90-minute hands-on role module in cohorts of 6 to 12, on their own tasks; a
45-minute clinic two to four weeks later; a new-hire module; and a training record
kept by HR as the Article 4 evidence. `knowledge/06` §2.6 has a per-role table of
goals, data rules, practice tasks and watch-outs, and `knowledge/05` §2.8 one of tool
fit and guardrails.

Measure understanding and use. DORA suggests surveying four
perceptions: AI use is expected, experimentation is supported, permitted tools are
clear, and the policy applies to my role. Add active users of approved tools, request
decision times and near-miss reports [verified for DORA, the added metrics inferred,
`knowledge/06` §1].

## 11. Facts that change the plan

Each of these forks a decision above, so
settle them from the company's own systems and people [inferred]:

| Fact | What it decides |
|---|---|
| GitLab tier, version, where runners run and who manages them | Which guardrails can be enforced (section 6); where agent jobs run |
| GitLab Duo licensed or not | Whether GitLab's own agents are a buy option |
| Claude plan (Team or Enterprise), seat types and counts | Audit logs, SCIM, Compliance API, usage headroom (section 8) |
| Company or personal accounts in use today | How much shadow AI to migrate |
| Other AI tools in use, including AI features switched on inside existing tools | The tool register, supplier reviews, data tiers per tool |
| Whether an ISO/IEC 27001 system exists, who owns it, its scope, who signs the AI policy | Where AI risks, incidents and approvals plug in (section 9) |
| Which customer contracts restrict data | Whether operational customer data is yellow or red |
| Laptop OS mix and endpoint controls | Whether Claude Code's sandbox is available; what DLP already sees |
| Whether decision records exist | Where architecture decisions go |
| Where the internal data APIs are and who owns them | The data-access layer (section 4) |

## 12. Where to go deeper

| Question | Where |
|---|---|
| Published factory models, building blocks, agent roles, evidence, measurement | `knowledge/01` §2.2 to §2.8 |
| GitLab CI/CD with YAML, tiers, components, runners, secrets, agents in CI | `knowledge/02` §2, §7 |
| Delivery metrics and a baseline script without Ultimate | `knowledge/02` §3 |
| Platform engineering for a company of under 100 people, monorepo or polyrepo | `knowledge/02` §4 |
| The maturity ladder with exit criteria | `knowledge/02` §8 |
| Supply chain: SLSA, SBOMs, signing, dependency updates | `knowledge/02` §5 |
| Python, notebooks, ML in CI | `knowledge/02` §6 |
| AI Act, GDPR, NIS2, CRA; 27001 and 42001 mapping; customer questionnaires | `knowledge/03` §2 |
| Threat frameworks, threat catalogue, 2025 to 2026 incidents | `knowledge/04` §2 to §4 |
| Hardening settings, sandbox, egress, hooks, agents in CI | `knowledge/04` §5 |
| Monitoring, DLP, shadow AI discovery, incident runbooks | `knowledge/04` §6, §7 |
| Anthropic plans and admin controls, other vendors | `knowledge/05` §2.2 to §2.4 |
| Reference architecture, gateways, MCP vetting and catalogues | `knowledge/05` §2.5 |
| Distributing agent config, plugins and skills; make or buy with costs; tool fit by role | `knowledge/05` §2.6 to §2.8 |
| Published AI policies, policy skeleton, operating model, intake | `knowledge/06` §2.1 to §2.4 |
| Advising patterns, onboarding curriculum, adoption evidence | `knowledge/06` §2.5 to §2.7 |
| GitLab concepts for a GitHub user, access from a work laptop | `gitlab-for-github-users.md` |
