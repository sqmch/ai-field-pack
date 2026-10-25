# The agentic development factory, state of the art (October 2026)

> Generic edition, built 2026-10-26: company-specific and personal content removed. See the pack README. Sections removed in this edition are omitted, and the numbering is kept so pointers resolve.

Researched 2026-10-14 and 2026-10-15. Every non-obvious claim carries a source number
(section 8) and a tag. [verified] means read in a primary source during this research.
[secondary] means taken from a write-up, a search summary or a page the tool could not
read in full. [inferred] is my own reasoning. Vendor and company productivity figures
are claims, and the text says so where they appear.

Two limits on this file. The shared web-search budget ran out partway through, so a
few items rest on one secondary source. And this field
moves monthly: GitHub Universe (28 to 29 October 2026) and the next GitLab releases
land after this was written.

Neighbouring files cover what this one leaves out: GitLab CI/CD mechanics in `02`,
regulation in `03`, security threats and hardening in `04`, vendor admin controls and
gateways in `05`, and policy writing and onboarding in `06`.

---

## 1. What matters most

1. **Plan a "lights-on" factory**. Stripe, Spotify, Uber, Augment and
   Anthropic keep people accountable for what merges [26][27][29][30][12]. The furthest step is Augment letting
   a risk agent auto-approve low-risk PRs [30]. StrongDM's rule that no human writes
   or reviews code comes from a three-person team on greenfield code [4]. The July 2026
   Pipedrive talk doesn't say who merges there [1].
2. **CI/CD first has evidence behind it**. Three of DORA's seven AI
   capabilities are quality internal platforms, strong version control and small
   batches [60]. The working agent fleets all sit on fast deterministic feedback:
   Stripe lints in under five seconds per push, Spotify runs hidden verifiers before
   any PR opens [26][28].
3. **Start where work piles up**. Augment added agents wherever work was
   queuing, not in lifecycle order [30]. That means a baseline comes before the first
   agent.
4. **An agent is a configuration, not a persona**. It has a trigger, scoped
   permissions, one output, a check by something that did not do the work, and an
   owner. Persona chains (product manager agent hands to architect agent hands to
   developer agent) add handoffs and lose context [32][17].
5. **Review capacity is the constraint**. Across 718 firms, Chen and
   Stratton find agents' gains stall at code review: longer reviews, more revisions
   [82]. Faros telemetry shows 5x
   median review time at high AI adoption [68]. DORA calls it the "verification tax"
   [63]. A review agent and small-batch rules come before more code generators.
6. **Context is infrastructure**. OpenAI's harness team: knowledge in
   Google Docs, chat or heads is not accessible to the agent [7]. Pipedrive built Atlas
   to make code readable for every role, and its former CTO says requirements must
   live next to the code [1]. Docs in the repo are factory inputs.
7. **The configuration formats have converged under open governance**.
   AGENTS.md and MCP sit in the Linux Foundation's Agentic AI Foundation, and the Agent
   Skills format is read by Claude Code, Codex, Copilot, Cursor, Kiro, Gemini CLI and
   others [37][38][41]. One set of repo instructions can serve several tools.
8. **Quality rules need a measurement backbone**. DORA's five delivery
   metrics include deployment rework rate since 2024 [65]. Add review load and cost
   per merged change. DORA warns against treating token spend as a performance
   indicator [64].
9. **The factory itself needs incident management**. Anthropic's April 2026
   postmortem traced weeks of degraded Claude Code output to three configuration
   changes, not a model change [13]. Prompts, model versions and agent defaults are
   production changes and need evals and staged rollout.
10. **Human approval clicks are not a control**. Claude Code users approve 93%
    of permission prompts [18]. Controls have to be structural: sandboxes, read-only
    defaults, declared outputs. Details in `04` and `05`.
11. **Be honest with teams about the evidence**. METR's 2025 trial found
    experienced developers 19% slower with AI; its 2026 follow-up is inconclusive [59].
    Telemetry shows more output and more instability [68][63]. DORA expects a J-curve:
    a dip before gains [66].
12. **Onboarding goes role by role**. The Pipedrive talk's slides say "R&D innovation
    is not limited to engineers", and of Pipedrive's PR-bot's 361 merged pull requests,
    79% had no human code edits [1]. Anthropic's legal and marketing
    teams build tools with Claude Code [11]. Anthropic's own study also records skill
    atrophy and juniors asking seniors less [19].
13. **Spend varies by a factor of 100 or more**. Published figures run
    from a $200 monthly subscription for one experimenter [5] to StrongDM's guideline of
    $1,000 per engineer per day [4]. Uber's six-factor cost breakdown is the practical
    model for a budget [29].
14. **The published working patterns** are specs, ADRs, instruction files,
    tests as backpressure and independent audits, as Huntley, Anthropic and Spotify
    describe [22][9][28]. The hard part is running them for other people, at team
    scale, with numbers.

---

## 2. State of the art

### 2.1 What an agentic development factory is

The term has three meanings in use: a
standard pipeline and platform every team builds on; StrongDM's "dark factory" where
no human writes or reviews code [4]; and agents attached to stages of the normal
lifecycle with people at approval points. Pipedrive's version is the first plus the
third [inferred from 1, 3].

A working definition [inferred]: **a factory is the set of pipelines,
standards, context and agents that turns an approved intent into a deployed,
monitored change, where agents do most of the mechanical work and people own intent,
judgment calls and the merge.**

Pento's September 2026 piece separates four layers, which helps keep vocabulary
straight [31] [verified]:

| Layer | What it is | Examples from the source |
|---|---|---|
| Framework | A library you assemble an agent from | LangGraph, OpenAI Agents SDK, Google ADK |
| Harness | A finished agent you configure: loop, prompts, tools, context handling, permissions | Claude Code, Codex, OpenCode |
| Platform | Managed hosting and governance for agents | Bedrock AgentCore, Microsoft Foundry |
| Factory | Many agents working a backlog, with a review gate before production | Stripe Minions, Symphony |

"Harness engineering" is the work of shaping that second layer and the repo around it
so an agent can succeed: instruction files, tools, verifiers, environment. OpenAI and
Anthropic both use the word this way [6][10].

### 2.2 How organisations deploy coding agents in 2026

Six patterns cover what companies actually run. They differ by trigger, by where the
agent runs and by who approves the output.

| Pattern | Trigger | Runs in | Output | Human gate | Examples (Oct 2026) |
|---|---|---|---|---|---|
| Interactive agent | Developer prompt | Laptop, devcontainer, IDE | Edits, local commits | Developer, before push | Claude Code, Codex CLI, Cursor, Copilot agent mode, Kiro, Amp, Antigravity [35][54][55][57] |
| Background agent | Issue assignment or label, Slack or MR mention, schedule | Ephemeral cloud VM or CI runner | Branch plus PR or MR | Review and merge | Copilot coding agent, Cursor cloud agents, Codex cloud, Jules, Stripe Minions, Pipedrive PR-bot, Claude Code in GitLab CI, GitLab Developer Flow [49][53][56][26][1][45][46] |
| Agentic code review | PR or MR opened or updated | CI or vendor service | Comments, sometimes fix commits | Human reviewer decides | Copilot code review, Cursor Bugbot, GitLab Code Review Flow, Uber uReview [51][53][47][29] |
| Repo and CI automation | CI failure, schedule, repo event | CI runner, read-only by default | Issue, report or fix PR | Maintainer | GitHub Agentic Workflows "CI Doctor", GitLab Fix CI/CD Pipeline flow, Uber self-healing CI [50][46][29] |
| Agents in operations | Alert, incident, deploy | Read access to logs and metrics | Root-cause draft, revert suggestion | On-call engineer | Pipedrive Scooby, Augment Incident Investigator, Cursor Rollouts, Uber alert triage [1][30][53][29] |
| Orchestrator | Board state: open tickets | Fleet of workspaces | Many PRs | PR review plus a person watching the board | OpenAI Symphony, Gas Town, Cursor Projects, Claude Code agent teams [25][21][53][43] |

Notes on the patterns:

- **Background agents are the core of every industrial factory.** The shape is the
  same everywhere: a narrow trigger, an isolated environment that matches the human
  one, deterministic checks inside the loop, a PR as the only output. Stripe's devboxes
  start in about ten seconds and have no access to production resources or the
  internet, which is why minions can run without per-action permission prompts [26]
  [verified]. GitHub's coding agent (now called "Copilot cloud agent") works in one
  repository, one branch and one PR per task, with a 59-minute session limit that can
  be shortened but not extended [49] [verified].
- **Agentic review is the most common first step** [inferred]. It needs no write
  access, it fits the existing approval flow, and its output is cheap to ignore. Group
  owners in GitLab 19.2 can now make every review use the agentic flow [47] [verified].
- **Read-only by default is becoming the norm for CI agents.** GitHub Agentic
  Workflows run the agent read-only and apply only the writes declared up front, in a
  separate job with its own permissions [50] [verified].
- **Operations agents propose, people act.** Cursor's Rollouts bot (September 2026)
  watches each deploy and can suggest a revert PR, but does not merge or roll back on
  its own [53] [verified].

#### Tools as examples, not a catalogue

Vendor self-descriptions, accessed 2026-10-14. Feature sets change monthly.

| Tool | Maker | Modes that matter for a factory |
|---|---|---|
| Claude Code | Anthropic | CLI and IDE; headless `-p` mode in CI; subagents; experimental agent teams; hooks; skills; plugins; GitLab CI integration in beta, maintained by GitLab [43][44][45] |
| Codex | OpenAI | CLI, IDE, cloud, GitHub Action, code review, non-interactive mode, skills, hooks, AGENTS.md [54] |
| Copilot coding agent and Agent HQ | GitHub | Background agent on GitHub; Agent HQ added Claude and Codex as third-party agents in public preview, 4 Feb 2026 [49][52] [secondary for Agent HQ] |
| GitLab Duo Agent Platform | GitLab | Foundational agents and flows; custom flows GA and Duo CLI GA in 19.2 (16 Jul 2026); external agents [46][47][48]. The Code Review Flow runs on Claude Sonnet 5.5 since 5 Oct 2026 [84] and was priced at a flat $0.25 per review in March 2026 [85]; check today's price |
| Claude Code Code Review and cloud sessions | Anthropic | Anthropic's managed review and cloud sessions work with GitHub only; on GitLab the documented route is Claude Code in your own CI [86] |
| Cursor | Anysphere, owned by SpaceX since August 2026 (deal announced June 2026) [see `05`] | IDE agent, cloud agents, Bugbot, Automations, Rollouts and Security Review bots (Sep 2026), Projects with a coordinator agent (Sep 2026 beta) [53] |
| Devin | Cognition | Autonomous agent; listed as AGENTS.md reader [37]. Site not readable this session |
| Factory | Factory | "Droids" across IDE, CLI and CI/CD; Agent Skills client [38] |
| Amp | Amp | CLI with a `--no-tui` mode for CI, remote per-thread machines [57] |
| Jules | Google | Async agent: label a GitHub issue, it clones to a cloud VM, plans, opens a PR after approval [56] |
| Antigravity | Google | Agent-first IDE with an agent manager, plus CLI, desktop app and SDK [55] |
| Kiro | AWS | Spec-first IDE and CLI; headless CLI can review PRs in CI; reads AGENTS.md and skills [35] |

### 2.3 Published factory models and what they claim

| Model | Who, when | Core claim | Human role | Evidence quality |
|---|---|---|---|---|
| Software Factory | StrongDM AI team, 6 Feb 2026 [4] | Code is neither written nor reviewed by humans; hidden scenarios and digital twins of third-party services validate it | Write specs, curate scenarios, watch scores | Manifesto from a 3-person team; no outcome data [verified] |
| Harness engineering | OpenAI, Ryan Lopopolo, 11 Feb 2026 [6] | About 1M lines and about 1,500 PRs in five months with no hand-written code, about 1/10 the time; three engineers, later seven | Humans steer, agents execute; humans may review PRs but are not required to | Internal beta product; figures self-reported [verified 2026-10-15: primary read with curl] |
| C compiler with parallel agents | Anthropic, Nicholas Carlini, 5 Feb 2026 [8] | 16 agents, about 2,000 sessions, about $20k, a 100k-line Rust C compiler that builds Linux 6.9 | Design tests and harness | Detailed and candid about limits [verified] |
| Long-running harnesses | Anthropic, Nov 2025 and Mar 2026 [9][10] | Initializer plus coding agent with handoff files; later planner, generator, evaluator | Write the prompt, tune the evaluator | Worked examples with costs [verified] |
| Five levels | Dan Shapiro, 23 Jan 2026 [20] | Level 0 manual to level 5 "dark factory"; 90% of "AI-native" developers sit at level 2 | Falls as levels rise | Opinion essay; the 90% is his estimate [verified] |
| Ralph loop | Geoffrey Huntley, 14 Jul 2025 [22] | `while :; do cat PROMPT.md \| claude-code ; done` plus specs and backpressure gets greenfield work about 90% done | Write specs, tune prompts | Anecdotes; author warns against existing codebases [verified] |
| Gas Town | Steve Yegge, released 1 Jan 2026 [21] | An orchestrator of 20 to 30 parallel Claude Code agents with roles and a git-backed task store | Talk to the "Mayor" | Open source, alpha; no outcome data [verified README] |
| Agentic assembly line | Thomas Dohmke, WeAreDevelopers keynote, Berlin, 9 Jul 2026 [23] | The developer's role shifts to setting intent and guiding architecture; agent session logs are a critical artifact and must be bound to the code in source control (the demo committed them as Git refs) | Sets intent, reviews | WeAreDevelopers session page and video summary [verified 2026-10-15]; the "manager" framing used earlier is not in the source |
| Symphony | OpenAI, 28 Apr 2026 [25] | Linear board as control plane: each open issue gets an agent run until done | Review PRs; manage the board | Reference implementation; 500% more landed PRs for some teams is a company claim [secondary] |
| Minions | Stripe, 9 Feb 2026 [26] | Unattended one-shot agents, over 1,000 merged PRs a week | Every PR reviewed by engineers | Company post; detailed design [verified] |
| Background coding agent "Honk" | Spotify, Nov to Dec 2025 [27][28] | Fleet-wide migrations by prompt, 1,500+ merged PRs, 60 to 90% time saved vs hand-written migrations | Teams review and merge | Company post; candid about failure modes [verified] |
| Software factory at Uber scale | Uber, 27 Aug 2026 [29] | Over 70% of PRs attributed to agents; 3,600+ skills; cost per session down 52% from June peak | Review, escalation | Company post; cost method detailed [verified] |
| Augment's software factory | Augment, 11 Sep 2026 [30] | 4.5x size-adjusted output per developer, median merge time 11.2h to 3.1h, 14-day revert rate 1.9% to 0.4% | Product and architecture calls, final merge | Vendor's own data, analysed by its own agent; authors say it is not a controlled experiment [verified] |
| Pipedrive | Agur Jõgi, Jul 2026 talk [1] | Human attention is the scarce resource; map where it leaks on the DevOps infinity loop; Scooby (incidents, 1,200 runs at about $1.60), PR-bot (Jira to PR with a CI and review feedback loop, 361 merged, 79% with no human code edits), Atlas (generated specs per service, built for agents); standardised on Anthropic with a central skill and plugin marketplace | Set direction, choose few high-impact tasks; merge practice not stated | Slides plus a machine transcript (2026-10-19), his own numbers; 2025 interview [3] superseded |
| Source control for agents | Anthropic, 13 Oct 2026 [12] | Merges grew from about 2,500 a day (May 2026) to over 30,000 a day (Sep 2026); git became the bottleneck; built a new VCS in five months with seven people | Review for intent, set constraints, say no | Company post, published two days ago [verified] |

What each model contributes:

**StrongDM** [4][5] [verified]. Two ideas worth stealing even for a company that never
goes "dark". First, scenarios: end-to-end user stories stored outside the codebase, like a
holdout set, so the agent cannot rewrite them to pass. Second, "satisfaction" in
place of a green test suite: the fraction of observed runs through all scenarios that
likely satisfy the user. Their Digital Twin Universe is behavioural clones of Okta,
Jira, Slack and Google Docs so scenarios can run at volume without rate limits. Simon
Willison's reaction is the right one: the no-review rule is the most interesting and
the least obviously sensible part, and $1,000 per engineer per day is about $20,000 a
month [5].

**OpenAI harness engineering** [6][7] [verified 2026-10-15 against the primary]. The
transferable lessons: a single large AGENTS.md failed because it crowded out task
context; a file of about 100 lines that serves as a map into a structured `docs/`
directory worked. Rules the team cared about became custom lints, which the agent then
had to satisfy. The team first spent every Friday (20% of the week) cleaning up "AI
slop" and found that did not scale, which argues for recurring cleanup agents.

**Anthropic's C compiler** [8] [verified]. The best single read on what parallel
autonomous agents can and cannot do. The harness was a bash `while true` loop per
agent, a Docker container each, a shared bare git repo, and lock files in
`current_tasks/` to claim work. No orchestrator. The lessons are about verification:
"the task verifier is nearly perfect, otherwise Claude will solve the wrong problem."
When all 16 agents got stuck on the same kernel bug, the fix was to use GCC as a
known-good oracle so each agent could work on different files. Carlini also assigned
narrow roles: deduplicating code, performance, a Rust-idiom critic, documentation. He
ends uneasy: it is easy to see tests pass and assume the job is done.

**Anthropic's long-running harnesses** [9][10] [verified]. The November 2025 post
fixes two failure modes, trying to one-shot the whole app and declaring victory early,
with a JSON feature list (agents may only flip a `passes` field), a progress log, an
`init.sh` and git commits as handoff artifacts. The March 2026 post adds a separate
evaluator agent with Playwright because agents grade their own work too kindly. Its
costs are
a useful calibration: a solo run took 20 minutes and $9, the full harness took six
hours and $200, and only the harness produced a working game. The most useful line
for a factory owner: every harness component encodes an assumption about what the
model cannot do alone, so re-test the harness whenever the model changes. With a
newer model the author removed sprints and context resets.

**The rest in brief.** Shapiro's levels [20] are useful vocabulary for non-engineers
and weak as a target; most evidence in 2.6 is about the jump to his level 3, where
review becomes the job. Huntley's own limits on Ralph [22] matter more than the hype:
greenfield only, about 90% done, and never on an existing codebase. Gas
Town [21] is a serious orchestration experiment with a Mayor, short-lived workers, a
merge-queue role and a git-backed task store, and a bad template for a company of 50
to 100 people [inferred]. Dohmke's company Entire [24] turns his keynote's point about
lost context into a tool that links each agent session to its commit, which lets an
auditor see why a change was written the way it was [inferred].

**Pipedrive** [1][2] [transcript, 2026-10-19]. What adds to the other models: the
DevOps infinity loop as the map of where attention leaks; PR-bot, a Stripe-style
background agent that also works its own feedback loop (failed CI, review comments,
merge conflicts); Atlas, generated specs per service built mainly as agent context;
Kaizen-style small tasks by everyone, with nobody left behind; non-engineers closing
small items; change requests challenged at intake by compliance and infosec agents;
one vendor (Anthropic) with a central marketplace and a duplicate-watchdog agent; and
an open warning about the cognitive load of running several agents. The November 2025
interview [3] described a three-model PR review that the talk doesn't mention. Treat
it as superseded.

**Stripe, Spotify, Uber, Augment** [26][27][28][29][30] [verified]. These four are the
closest thing to an industrial standard, and they agree on more than the lights-off
camp does:

- Agents use the same environment and tools as engineers. Stripe's rule is that
  tooling which helps humans helps the model too [26].
- Deterministic steps interleave with agent steps. Stripe's loop, a fork of Block's
  goose, always runs git operations, linters and tests as fixed steps, and usually
  allows one CI run per task, two at most [26].
- Verification is hidden from the agent. Spotify's verifiers activate per component,
  and the agent does not know what they check or how [28].
- An independent judge catches scope creep. Spotify's LLM judge vetoed about a quarter
  of sessions, mostly for doing more than the prompt asked [28]. Spotify later dropped
  the judge as too rigid and moved verification into a separate CI service that runs
  before a PR opens (reported March 2026) [83] [secondary]. Treat the judge as
  history.
- Context is curated, not maximal. Stripe's central MCP server has over 400 tools and
  each minion gets a subset [26]. Uber grounds agents with a context graph and reports
  a grounded query finishing correct in 38 seconds against an ungrounded one taking 20
  minutes and coming back wrong [29].
- Cost is managed per outcome. Uber tracks cost per merged PR with revert rate and MTTR
  beside it [29].

#### Where the models agree and disagree [inferred]

They agree that verification, not generation, is the hard part, and that agents need
a written, machine-checkable definition of done. They disagree on who checks. The
lights-off group (StrongDM, OpenAI's experiment, Carlini) replaces human review with
holdout scenarios, oracles and evaluator agents, and all three worked on greenfield or
self-contained targets. The lights-on group runs on large existing codebases and keeps
a human merge. For a company with existing codebases and physical-world consequences,
lights-on is the realistic model.
StrongDM's scenario holdouts and Spotify's hidden verifiers still transfer: they make
human review cheaper, not unnecessary.

### 2.4 Building blocks

#### Spec-driven development

**What it is.** Writing a structured spec before the agent writes code, so the spec is
the reference for both. Birgitta Böckeler (Thoughtworks) separates three levels:
spec-first (written for one task, then dropped), spec-anchored (kept and maintained
with the feature) and spec-as-source (humans edit only the spec) [33] [verified].

| Tool | Shape | Status (Oct 2026) |
|---|---|---|
| GitHub Spec Kit | CLI plus slash commands: constitution, specify, plan, tasks, implement, converge | About 141k GitHub stars [34] [verified] |
| Kiro (AWS) | Requirements, design, tasks inside the IDE; steering files | IDE, CLI, web; headless CLI for CI [35] [verified] |
| BMAD method | Process from clarify to plan to build-and-verify, with product, architecture, UX, dev and test expertise | v6.12 stable, v7 alpha targeted for 16 Oct 2026 [36] [verified] |

Böckeler's critique dates from October 2025 and the tools have changed since, but it
is still worth carrying into any rollout: she would rather review code
than the volume of markdown spec-kit produced; Kiro turned a small bug into four user
stories with 16 acceptance criteria; spec-kit regenerated existing classes as
duplicates; and the term is often a synonym for "detailed prompt" [33]. My reading
[inferred]: specs pay off when they are short, testable and kept next to the code, as
ADRs and acceptance tests are. A spec process sized for a feature is overkill for a
bug, so the factory needs two or three lanes by work size.

#### Context engineering

**What it is.** Choosing what tokens the agent sees, from instructions and tools to
retrieved files and history. Anthropic defines it as the strategies for curating and
maintaining the best set of tokens during inference [14] [verified].
Context is a budget; recall falls as the window fills. Practical rules from the same
post: start with a minimal prompt and add instructions only for observed failures;
keep tool sets small and distinct; give a few canonical examples, not a list of edge
cases; let the agent fetch files just in time; for long work use compaction,
structured notes outside the window, and subagents with clean contexts. Uber's
compaction at 400k tokens, medium reasoning effort by default and tool search instead
of loading every MCP schema are the same rules applied to a budget [29].

#### AGENTS.md and the Agentic AI Foundation

**What it is.** A markdown file at the repo root, and optionally in subdirectories,
that tells coding agents how to build, test and follow conventions. The nearest file
in the directory tree wins, and an explicit user prompt overrides it [37] [verified].
The site
lists over 60k open-source projects and about two dozen tools, among them Codex, Jules,
Factory, Devin, Cursor, the Copilot coding agent and Gemini CLI [37]. Claude Code reads
CLAUDE.md and, since v2.1.277 (published 18 Sep 2026), reads AGENTS.md instead in a
project with no CLAUDE.md; v2.1.281 extended this to Bedrock, Vertex AI, Foundry,
gateways and sessions with telemetry off [44] [verified: changelog and npm release
date]. GitLab's Duo CLI reads AGENTS.md too [47] [verified].

**Governance.** The Agentic AI Foundation (AAIF) under the Linux Foundation formed on
9 December 2025 with MCP from Anthropic, goose from Block and AGENTS.md from OpenAI as
founding projects [41] [verified 2026-10-15 against the Linux Foundation press
release]. The AGENTS.md site states it is stewarded by the Agentic AI Foundation [37]
[verified]. The AAIF announced a sandbox phase for early projects on 1 September 2026,
and the Envoy AI Gateway joined as "Agent Router" on 9 September 2026 [41] [verified].

**Example** [inferred]: a 60 to 100 line root AGENTS.md with setup, test and
lint commands, the paved-path rules and pointers into `docs/`, plus short
per-service files. OpenAI's experience with one long file is the warning [7].

#### Agent Skills

**What it is.** A folder with a `SKILL.md` (name, description, instructions) and
optional scripts, references and assets. Agents load only names and descriptions at
startup, read the full instructions when a task matches, then run bundled code if
needed [38] [verified]. Anthropic developed the format and published it as an open
standard; the client list includes Claude Code, Codex, GitHub Copilot, VS Code,
Cursor, Gemini CLI, Kiro, Factory, Amp, Goose, OpenHands and JetBrains Junie [38]
[verified]. GitLab Duo is not on that list, but GitLab's own docs say Duo supports the
spec: project-level skills since GitLab 18.10 (Duo CLI 8.73.0 or later), user-level
skills as an experiment since 19.0, Premium and Ultimate only [verified 2026-10-15,
https://docs.gitlab.com/user/duo_agent_platform/customize/agent_skills]. Uber reports over 3,600 skills and 30k skill executions a day [29]. A
skill is the unit for "how we do X here": run a migration, add a data connector,
write an ADR.

#### MCP

**What it is.** The Model Context Protocol connects agents to tools and data through a
standard client-server interface. The current specification is **2026-07-28**
[39] [verified], and it breaks things. The core became stateless: no initialize
handshake or session header, each request carries its own version and capabilities.
HTTP requests carry `Mcp-Method` and `Mcp-Name` headers so gateways can route and
meter without parsing bodies. Roots, Sampling, Logging, the old HTTP+SSE transport and
Dynamic Client Registration are deprecated with about a year's grace (Client ID
Metadata Documents replace DCR), and clients must validate the authorization server's
`iss` parameter when it is present [39] [verified 2026-10-15]. Servers and clients built in 2025
need checking against it.

**Governance.** The spec belongs to "Model Context Protocol a Series of LF Projects,
LLC", run by two lead maintainers, core maintainers per priority area, working groups
and Specification Enhancement Proposals [39][40] [verified]. The August 2026 roadmap
puts agent identity and progressive discovery of tool catalogues among five
priorities, with no date for the next revision [40] [verified].

**Factory relevance** [inferred]: MCP is how agents reach GitLab, the issue tracker,
logs and internal APIs. GitLab ships an MCP server [46]. Stripe's Toolshed shows the
pattern at scale: one internal server, curated subsets per agent [26]. Security of MCP
servers belongs to `04`.

#### A2A

The Agent2Agent protocol (Google, donation to the Linux Foundation announced 23 June
2025) released v1.0.0 on 12 March 2026 with signed Agent Cards and multiple bindings;
the Linux Foundation reported 150+ supporting organisations on 9 April 2026 [42]
[verified 2026-10-15: v1.0.0 date from the a2aproject/A2A GitHub release, 150+ from the
LF release; donation date from secondary reports]. It is for
agents from different systems talking to each other. A coding factory at a company of
50 to 100 people does not need it in the first year [inferred].

#### Hooks

**What it is.** Commands the harness runs at fixed points: before or after a tool
call, when the agent stops, when a task completes. Hooks turn rules into enforcement.
Spotify runs all verifiers in a Claude Code stop hook, and if one fails no PR opens
[28] [verified]. Claude Code hooks can block on failure (`onFailure: "block"`) and can
gate agent-team tasks via `TaskCompleted` and `TeammateIdle` [44][43] [verified].
Copilot's coding agent supports hooks for validation and logging [49].
Kiro and Codex document hooks too [35][54].

#### Subagents, agent teams and orchestrators

- **Subagents** run in their own context and return a summary to the caller. Good for
  search, review and verification, where only the result matters [43] [verified].
- **Agent teams** (Claude Code, experimental, off by default) are separate sessions
  with a shared task list and messaging. The docs recommend 3 to 5 teammates, warn that
  token cost scales linearly, and say sequential work and same-file edits suit a single
  session better [43] [verified]. Teams only form in interactive sessions: under `-p`
  or the Agent SDK no teammates are spawned, so they are not a CI building block [43]
  [verified 2026-10-22].
- **Orchestrators** run fleets against a board: Symphony, Gas Town, Cursor Projects
  [25][21][53]. Symphony exists because people could comfortably supervise three to
  five sessions before context switching hurt [25] [secondary].

#### Plugins

Bundles of skills, subagents, hooks, commands and MCP configuration installed as one
unit from a marketplace (Claude Code, Cursor's app marketplace) [44][53] [verified].
A private plugin is the way to ship the company's agent configuration to every
laptop at once. Distribution and admin control belong to `05`.

#### Sandboxes and devcontainers

The pattern: each agent run gets an ephemeral environment that matches the developer
one, with no production credentials and limited network. Examples: Stripe's devboxes
[26], Spotify's container with almost no access to surrounding systems [28], Carlini's
Docker container per agent [8], Copilot's ephemeral cloud environment [49], and Claude
Code in GitLab CI running in a container with network and filesystem limits [45] [all
verified]. A devcontainer definition in each repo is the cheap version of this
[inferred]. Hardening detail is in `04`.

#### Session provenance

Entire's checkpoints store prompts, transcripts, tool calls and token use and link
them to commits with trailers that also record how much of the commit came from the
agent [24] [verified]. GitLab 19.2 added downloadable AI audit event reports for agent
sessions, in beta [47] [verified]. Either answers an auditor's question that git alone
cannot.

#### Eval harnesses for agent output

**What it is.** A fixed set of tasks with graders, run against an agent configuration
to tell whether a change to prompts, model or tools made it better or worse.
Anthropic's January 2026 guide gives the vocabulary [15] [verified]: a task has inputs
and success criteria; a trial is one attempt; graders are code-based (tests, static
analysis), model-based (rubrics) or human; grade the outcome, not the path.
**pass@k** is the chance that at least one of k tries succeeds; **pass^k** is the
chance all k succeed, which is what matters for an unattended agent. At 75% per trial,
three in a row succeed about 42% of the time. Start with 20 to 50 simple tasks taken
from real failures [15]. Keep capability evals, with a low pass rate as a target,
apart from regression evals, which stay near 100% as a tripwire.

Two cautions. Infrastructure alone moved Terminal-Bench 2.0 scores by 6 points in
Anthropic's tests, so the authors distrust leaderboard gaps under 3 points unless the
setup is matched [16] [verified]. And Spotify had not yet evaluated its own judge when
it wrote about it [28]. The checker needs checking too.

### 2.5 Agent roles along the lifecycle

Each row is a real deployment, not a proposal. "Check" means what verifies the output
before it counts.

| Stage | Trigger | Permissions | Output | Check | Real example |
|---|---|---|---|---|---|
| Intent and business | Feature description, customer feedback | Read code, write tickets | Grounded design doc and tickets | Product owner approves | Augment Project Builder and Feedback Triager [30]; GitLab Planner agent [46]; Pipedrive's product and design people closing small items [1] |
| Plan | Approved ticket | Read code | Plan, task list, test plan | Engineer or plan review | Anthropic planner agent, kept because without it the generator under-scoped [10]; Spec Kit plan and tasks [34] |
| Build | Ticket assigned, Slack mention, `@claude` in an MR | Write to a branch in an isolated environment; no prod secrets | Branch and PR or MR | CI, hidden verifiers, LLM judge, then human review | Stripe Minions [26]; Spotify Honk [27][28]; Pipedrive PR-bot [1]; Claude Code in GitLab CI [45]; GitLab Developer Flow [46] |
| Review | PR or MR opened or updated | Read diff and repo; write comments | Comments, risk rating, sometimes fixes | Human reviewer; acted-on and rejected-as-wrong rates tracked | Uber uReview on all PRs [29]; Augment Risk Analyzer auto-approves low-risk PRs [30] |
| Test | PR opened, feature built | Run app in sandbox, browser automation | Test results, screenshots, video | Deterministic tests; human looks at evidence | Anthropic evaluator with Playwright [10]; Augment Verifier with E2E evidence [30]; Cursor agents attach videos and logs to PRs [53] |
| Security | PR, scan finding | Read code and findings | Triage, false-positive marking, fix MR | AppSec owner | GitLab SAST false-positive and resolution flows [46]; Cursor Security Review bot [53]. Detail in `04` |
| Release | Merge, deploy | Read deploy telemetry | Health report per environment, revert suggestion | Release owner; agent cannot roll back | Cursor Rollouts [53]; Anthropic post-merge and pre-deploy testing instead of a global merge queue [12] |
| Operate and incident | Alert, PagerDuty page, incident ticket | Read logs, metrics, code | Root-cause hypothesis, five-whys draft | On-call engineer approves remediation | Pipedrive Scooby [1]; Augment Incident Investigator [30]; Uber on-call triage [29] |
| Document and knowledge | Code change, schedule | Read code; write docs MR | Updated docs, dependency maps | Service owner | Pipedrive Atlas: up to nine generated spec pages per service, refreshed on deploy, across about 750 microservices [1]; Carlini's documentation agent [8] |
| Maintain and migrate | Fleet-wide change request | Write branches across many repos | Many small PRs | Owning teams review | Spotify migrations, 1,500+ merged PRs [27]; Carlini's deduplication agent [8]; OpenAI's recurring cleanup [7] |
| CI repair | Pipeline failure | Read logs; optionally write a fix branch | Issue with cause, or fix MR | Pipeline reruns, maintainer | GitLab Fix CI/CD Pipeline flow [46]; GitHub CI Doctor [50]; Uber self-healing CI [29]; Stripe's flaky-test tickets with a launch button [26] |

#### When one agent per job helps, and when it does not

The evidence supports splitting work across agents for three reasons, and only three:

1. **Separate context.** A subagent that searches or reviews keeps that noise out of
   the main agent's window [14][32].
2. **Separate permissions.** A review agent needs no write access; a CI-repair agent
   needs logs but not production. Narrow permissions are only possible with narrow
   jobs [50][28].
3. **An independent check.** Generators grade their own work leniently. Anthropic
   found it easier to tune a skeptical standalone evaluator than to make the
   generator self-critical [10]. Spotify's judge vetoed a quarter of sessions [28],
   though Spotify later replaced it with a CI verification service [83].

Persona chains add none of these. Cognition's argument still stands: every action
carries implicit decisions, agents working from different assumptions produce
conflicting results, and summaries passed between agents drop those decisions [32]
[verified, June 2025]. Anthropic's research system used about 15 times the tokens of
a chat, and its authors note that coding has fewer truly parallel tasks than research
[17] [verified]. Carlini's 16 agents all hit the
same kernel bug and overwrote each other until the work was split by file [8].

My read [inferred]: Scooby and Atlas are the good kind, each with one trigger, one
output and an owner. So are Augment's Project Builder, which turns a request into a
grounded ticket that a person approves, and Pipedrive's intake agents that challenge
a change request for compliance and security gaps while it's written [1]. The weak
kind is a persona chain, such as an AI product manager that hands work to an AI
architect. Five questions sort any proposal: what triggers it, what may it read
and change, what does it produce, what checks it, and who owns it when it is wrong.

### 2.6 Evidence on outcomes

#### Controlled trials

**METR, July 2025** [58][59] [verified via the 2026 update]. 16 experienced
open-source developers, 246 tasks in their own repositories, randomised per task.
With AI allowed, tasks took 19% longer (confidence interval +2% to +39%, as given on
METR's 2026 page). The developers expected a 24% speedup beforehand and believed
afterwards that AI had sped them up by about 20% [58] [verified 2026-10-15 via the
paper's abstract, arXiv 2507.09089]. The gap between perceived and measured speed is
the finding to remember.

**METR, February 2026 update** [59] [verified]. 57 developers, 143 repositories, over
800 tasks, from August 2025. Returning developers: an estimated 18% speedup, interval
from 38% faster to 9% slower. New developers: 4% speedup, interval 15% faster to 9%
slower. Both intervals include zero. The design broke down because developers did not
want to work without AI: 30 to 50% of developers said they chose not to submit some
tasks they would not want to do without it [verified 2026-10-15]. METR
believes developers are more sped up than in early 2025, but calls its data very weak
evidence of how large the increase is, and is moving to shorter experiments and
observational data.

**Field experiments at Microsoft, Accenture and a Fortune 100 firm** (Cui and
co-authors, Management Science, published online 27 Feb 2026) [74]. These tested a
code-completion assistant, not agents. The published version reports 26.08% more
completed tasks (standard error 10.3%) across 4,867 developers in three randomised
trials, with higher adoption and larger gains for less experienced developers [74]
[verified 2026-10-15 via the Crossref abstract; the INFORMS page returned 403].

#### Large surveys and telemetry

Chen and Stratton (Harvard job-market paper, first version January 2026, current
version 4 August 2026) use Jellyfish data: about 300 million work events across 718
firms, in a staggered difference-in-differences design. AI coding assistants and
agents both raise coding productivity. The gains don't fully reach shipped output
(Jira issues and epics resolved) or employment. For agents the authors trace the gap
to code review: longer reviews, more revisions, more comments [82] [verified
2026-10-19, abstract and introduction read]. Pipedrive's former CTO showed the
preliminary version's charts in his July 2026 talk.

**DORA 2025, State of AI-assisted Software Development** [60][61][63]. Almost 5,000
respondents and 78 interviews [60] [verified]. 90% use AI at work, up from 76% in
2024 [67] [secondary for the 2024 figure], and over 80% believe
it raised their productivity; 30% report little or no trust in AI-generated code;
higher AI adoption is associated with higher delivery throughput and higher delivery
instability [63] [verified, DORA's own March 2026 summary]. The headline finding is
that AI mainly amplifies an organisation's existing strengths and weaknesses [61]
[verified].

**The seven DORA AI capabilities**, verified against the Google Cloud post by DORA
researchers Kevin Storer and Derek DeBellis, 24 Sep 2025 [60]. A companion report
on the model was last updated 25 Nov 2025 [62]. The main report PDF exceeded the
fetch tool's 10 MB limit, so I could not read it page by page.

| Capability | What it means |
|---|---|
| Clear and communicated AI stance | People know what is permitted, expected and supported |
| Healthy data ecosystems | Internal data is high-quality, accessible and unified |
| AI-accessible internal data | AI tools are connected to internal sources for company context |
| Strong version control practices | Frequent commits and use of rollback, to absorb AI's volume |
| Working in small batches | Small increments, a long-standing DORA finding that matters more with AI |
| User-centric focus | The end user stays central; without it AI adoption can hurt team performance |
| Quality internal platforms | Shared platform capabilities that let AI benefits scale |

The first capability is AI policy work, and three others are CI/CD foundations
[inferred].

**DORA 2026, ROI of AI-assisted Software Development** (report 2026.01, covered May
2026) [66] [secondary]. A model, not a survey: value flows from the seven capabilities
to delivery metrics to outcomes to money. Expect a J-curve, a temporary dip caused by
the learning curve, the verification tax and downstream processes that have not
adapted. An illustrative 500-person organisation gets 39% first-year ROI; the authors
present the numbers as a high-uncertainty estimate to start a conversation. It advises
against cutting headcount.

**DORA, "Balancing AI tensions", March 2026** [63] [verified]. From 1,110 open-ended
answers by Google engineers: verification overhead, which engineers described as
babysitting the AI; skill degradation when AI skips the productive struggle that
builds expertise; and an integration gap between a
fast prototype and production. Advice: measure impact not output, move AI feedback to
the author while writing, keep changes small, do not shorten estimates before
production-quality work is done, pair juniors with seniors.

**Faros AI, "Acceleration Whiplash", Q2 2026** [68] [verified page, vendor]. About
22,000 developers in 4,000 teams over two years, comparing low and high AI adoption
within organisations. Epics completed +66%, task throughput +34%, PR merge rate +16%,
deployments per week down 12%. PR size +51%, bugs per PR +28%, median review time 5x,
incidents per PR about 3x, code churn about 10x. Faros sells an analytics platform,
the full report is gated, and the findings are associations. Faros's July 2025 report
on 10,000+ developers had reported +21% tasks, +98% merged PRs and +91% review time
[secondary, 80].

**GitClear 2026** via LeadDev, July 2026 [69] [secondary]. 623 million code changes,
2023 to 2026. Duplicated blocks up 81%, moved and refactored code down 70%, changes to
code older than a year down 74%. Correlational.

**CodeRabbit, December 2025** [70] [secondary]. 470 open-source PRs: AI co-authored
PRs had 10.83 issues per PR against 6.45 for human-only PRs. Small sample, and the
authors could not guarantee the human PRs were human-only.

**Microsoft's rollout of Claude Code and Copilot CLI, early 2026** [71] [verified
abstract]. Tens of thousands of engineers. Adopters merged about 24% more PRs than
they otherwise would have, over four months. Adoption spread through social networks.
The abstract reports no quality measure.

**Stanford, about 100,000 developers** (Yegor Denisov-Blanch) [73] [secondary]. Net
gains of about 15 to 20% on average after rework; 30 to 35% for simple greenfield
tasks and 0 to 10% for complex brownfield work, sometimes negative. Telemetry and
survey data, not randomised.

**Anthropic's own engineers, August 2025** [19] [verified]. 132 surveyed, 53
interviewed, 200,000 transcripts. Claude used in about 60% of work, self-reported
productivity +50%, 27% of assisted work would not have been done otherwise, but most
could fully delegate only 0 to 20% of their work. The authors flag self-report bias
and an Anthropic-only sample.

**Supervising agents** [72] [verified abstract]. A September 2026 study of 19
experienced developers found supervision work in every stage of delegation. They
coped by planning more up front and turning recurring guidance into reusable assets,
which is what instruction files and skills are.

#### Company self-reports

Listed so they can be quoted accurately, not as evidence of effect size: Uber over
70% of PRs agent-attributed [29]; Stripe over 1,000 merged minion PRs a week [26];
Spotify 1,500+ merged migration PRs [27]; Augment 4.5x size-adjusted output [30];
OpenAI 1M lines in five months [6] and a 500% rise in landed PRs for some Symphony
teams [25]; Anthropic from about 2,500 to over 30,000 merged changes a day between May
and September 2026 [12]. None is controlled; most coincide with model upgrades, which
Augment names as a confounder [30].

#### Cost and token spend

| Data point | Figure | Source |
|---|---|---|
| StrongDM guideline | At least $1,000 per human engineer per day, about $20k a month | [4][5] [verified] |
| Experimenting alone | A $200 monthly plan was enough for Willison | [5] [verified] |
| C compiler | About $20k for two weeks, 16 agents, 2B input and 140M output tokens | [8] [verified] |
| Harness vs solo agent | $9 for 20 minutes vs $200 for 6 hours, over 20x | [10] [verified] |
| Multi-agent vs chat | About 15x the tokens of a chat | [17] [verified] |
| Uber | Cost per session down 52% from June peak; cost per 1,000 requests down 34% on a fixed model; total spend stable since April 2026 | [29] [verified] |
| DORA | Token counts are a vanity metric; track them by team, application or use case, since company totals say nothing and per-person scores do harm | [64] [verified] |

Uber's cost equation is the one to adopt: users × sessions per user × turns per
session × requests per turn × tokens per request × price per token. The middle three
terms are where engineering work lowers cost; price per token is the vendor's [29].
DORA's tokenmaxxing piece cites Shopify replacing its token leaderboard with a usage
dashboard that has circuit breakers for runaway agents [64].

#### What the evidence adds up to [inferred]

Individual and PR-level output goes up, often a lot. Organisation-level delivery does
not follow automatically, and instability, review time and churn rise with it. The
only randomised evidence on experienced developers ranges from a slowdown to
"inconclusive". Perceived speed is not measured speed. Company factory reports are
large and uncontrolled. The honest position for advising teams: measure locally,
against a baseline, with quality and review metrics beside throughput, and expect a
dip first.

### 2.7 Measuring a factory

#### Frameworks

| Framework | What it measures | Use |
|---|---|---|
| DORA five metrics [65] [verified] | Throughput: change lead time, deployment frequency, failed deployment recovery time. Instability: change fail rate, deployment rework rate (added 2024) | Core delivery scorecard per service |
| DX Core 4 [75] | Four counterbalanced dimensions: speed, effectiveness, quality, business impact [verified]. Key metrics: diffs per engineer [verified]; Developer Experience Index, change failure rate, share of time on new capabilities [from memory, the page's table is an image] | Adds developer experience and business share |
| DX AI Measurement Framework [76] [verified] | Utilization, impact, cost. Treat agents as extensions of the developers and teams that supervise them; count agent PRs in that team's throughput | Structure for the AI-specific layer |
| SPACE [77] | Satisfaction and well-being, performance, activity, communication and collaboration, efficiency and flow [dimension names from memory; publication verified] | Reminder to use several dimensions, never activity alone |

#### Factory-specific metrics [inferred, each grounded in a source]

| Metric | Why | Grounding |
|---|---|---|
| Cost per merged change, by agent workflow | Ties spend to outcome | Uber [29], DORA [64] |
| Review load: time to first review, review hours per MR, MRs per reviewer per week | Review is the bottleneck | Faros [68], DORA [63] |
| Agent MR acceptance rate and rework within 14 days | Quality of agent output | Augment's 14-day revert rate [30] |
| Review-agent acted-on rate, plus a rejected-as-wrong rate from a weekly sample (acted-on rate isn't precision) | A noisy reviewer trains people to ignore it | Uber uReview [29] |
| Verifier veto rate | Scope creep and broken changes caught before humans | Spotify, judge since replaced by a CI service [28][83] |
| First-run CI pass rate for agent MRs | How well context and verifiers work | Stripe's one or two CI runs [26] |
| Share of sessions started by automation | Maturity from interactive to factory | Uber [29] |
| Human attention per merged change | The scarce resource in the Pipedrive talk | [1] |

#### What to baseline before introducing agents [inferred]

Collect four to six weeks of history from GitLab before switching anything on, per
service where possible:

1. The DORA five, from deployments and incidents.
2. MR size distribution, time to first review, time to merge, review rounds.
3. Pipeline duration and failure rate, and flaky-test rate.
4. Rework: reverts and fix-forward MRs within 14 days.
5. Current AI tool use and spend, even if informal. `05` and `06` cover the inventory.
6. A short developer survey on friction, so DX-style perception data exists before
   and after.

The Pipedrive talk's warning applies: "time saved" misleads, so measure delivered
changes and their quality [1].

### 2.8 Failure modes and anti-patterns

| Failure mode | What happens | Real case | Countermeasure |
|---|---|---|---|
| Review overload | Generators outrun reviewers; queues grow; rubber-stamping | Pipedrive seniors slowed at first [3]; 5x review time [68]; "verification tax" [63] | Small-batch limits, review agent before generator agents, track review load |
| Tests that lie | Agent edits tests to pass or declares victory early | StrongDM moved to holdout scenarios after agents could return true [4]; Anthropic's "premature victory" [9]; Carlini: tests pass, job rarely done [8] | Hidden verifiers, holdout scenarios, end-to-end checks with real tools |
| Scope creep | Agent refactors or disables flaky tests beyond the ask | Spotify judge vetoes about 25%, mostly for going beyond the prompt [28] | Independent judge comparing diff with prompt |
| Slop accumulation | Duplication, less reuse, churn | OpenAI's Friday cleanups [7]; GitClear 2026 [69]; Carlini's dedup agent [8] | Lints for rules you care about, recurring cleanup agent, architecture checks |
| Destructive action in production | Agent deletes or alters live data | Replit agent deleted a production database during a declared code freeze, July 2025 [78] | No production credentials in agent environments; dev and prod separated; read-only ops agents |
| Vendor-side regression | Output quality drops with no change on your side | Anthropic April 2026: a lowered effort default, a caching bug and a prompt cap degraded Claude Code for up to five weeks; internal evals missed it at first [13] | Own eval set, pin versions, watch quality metrics per tool version |
| Approval fatigue | People click yes without reading | 93% of Claude Code permission prompts approved [18] | Structural controls, not prompts; see `04` |
| Tokenmaxxing | Spend becomes a status metric | DORA, June 2026 [64] | Cost per outcome by team and use case, never per person rankings |
| Persona pipelines | Handoffs lose decisions, results conflict | Cognition [32]; Carlini's agents overwriting each other [8] | Single agent plus independent checker; split by file or task, not role |
| Too many parallel agents | Cognitive load, context switching | The Pipedrive talk's chapter on cognitive load [1]; three to five sessions before it hurts [25] | Fewer, narrower agents; orchestration only with a board and limits |
| Skill atrophy and lost mentorship | Juniors learn less; seniors see fewer questions | Anthropic internal study [19]; DORA [63] | Pairing, manual work on complex components, review as teaching |
| Inbound slop | Low-quality AI submissions consume maintainers | curl ended its bug bounty in Jan 2026; under 5% of reports were real [79] | Intake rules, disclosure, cost to submit |
| Perceived vs measured speed | Teams believe they are faster than they are | METR 2025: 19% slower, felt 20% faster [58][59] | Baseline and measure |
| Infrastructure at agent scale | Git, CI or merge queues saturate | Anthropic built a new VCS when merges grew over tenfold [12] | Not a problem soon for a company of 50 to 100 people; watch CI minutes and queue time |
| Greenfield loops on old code | Loop techniques break mature codebases | Huntley's own warning about Ralph [22] | Loops only for greenfield or well-fenced work |

---

## 6. Reading list

### Must read (five)

1. **Agur Jõgi, "Building an agentic software factory"**, WeAreDevelopers, 10 Jul 2026
   [1]. Pipedrive's model, from its former CTO. About 30 minutes.
2. **Spotify, background coding agent, parts 1 to 3**, Nov to Dec 2025 [27][28]. The
   closest practical analogue: narrow agent, hidden verifiers, an LLM judge (since
   dropped, [83]), honest failure modes. Part 2 on context engineering is linked from part 1.
3. **Stripe, "Minions"**, 9 Feb 2026 [26]. How an unattended agent fleet fits normal
   review, CI and tooling.
4. **DORA AI Capabilities Model post** [60] plus **"Balancing AI tensions"** [63]. The
   research base for CI/CD-first and for AI policy work.
5. **METR, the 2025 study and the February 2026 update** [58][59]. The best evidence
   on productivity, and a lesson in why measuring it is hard.

### Should read

- Anthropic, "Harness design for long-running application development" [10] and
  "Effective harnesses for long-running agents" [9].
- Anthropic, "Building a C compiler with a team of parallel Claudes" [8].
- Augment, "How we built Augment's software factory" [30], for bottleneck-first rollout
  and its metric set, read as vendor material.
- Uber, "Running a software factory efficiently at Uber scale" [29], for cost.
- StrongDM factory site [4] and Willison's write-up [5].
- OpenAI, "Harness engineering" [6].
- Anthropic, "Effective context engineering for AI agents" [14] and "Demystifying evals
  for AI agents" [15].
- Cognition, "Don't build multi-agents" [32].
- Böckeler on spec-driven development tools [33].
- Anthropic, April 2026 Claude Code postmortem [13] and "Redesigning source control"
  [12].
- DORA, tokenmaxxing [64]; Faros Q2 2026 [68] with its caveats.
- Shapiro's five levels [20] and Huntley's Ralph post [22], for shared vocabulary.

### Reference

- Specs and standards: MCP 2026-07-28 [39] and roadmap [40]; AGENTS.md [37]; Agent
  Skills [38]; AAIF [41].
- Platform docs: GitLab Duo Agent Platform [46] and 19.2 release notes [47]; Claude
  Code GitLab CI/CD [45], agent teams [43], changelog [44]; GitHub coding agent [49]
  and Agentic Workflows [50]; Codex docs [54]; Cursor changelog [53].
- Measurement: DORA metrics history [65]; DX Core 4 [75]; DX AI Measurement Framework
  [76]; SPACE [77].
- Orchestration examples: Gas Town [21], Symphony [25], Entire [24].

---

## 8. Sources

Access date 2026-10-14 or 2026-10-15 unless noted. Publication dates as stated by the
source.

1. Agur Jõgi, "Building an agentic software factory: How we rebuilt product development at Pipedrive", WeAreDevelopers World Congress, 10 Jul 2026. https://www.wearedevelopers.com/videos/100261-building-an-agentic-software-factory-how-we-rebuilt-product-development-at-pipedrive
2. WeAreDevelopers session abstract for the same talk. https://www.wearedevelopers.com/world-congress/agenda/sessions/turning-engineering-led-to-product-led-growth-lessons-from-scaling-pipedrive-to-1141899
3. Yash Bagal, "Pipedrive's Internal AI Tools Powering 100,000+ Customers", Jam.dev blog, 25 Nov 2025. Superseded by the 2026 talk; kept for history. https://strawberryjam.ghost.io/pipedrives-internal-ai-tools-powering-100-000-customers/
4. Justin McCarthy, StrongDM, "Software Factories and the Agentic Moment", 6 Feb 2026. https://factory.strongdm.ai
5. Simon Willison, "How StrongDM's AI team build serious software without even looking at the code", 7 Feb 2026. https://simonwillison.net/2026/Feb/7/software-factory/
6. Ryan Lopopolo, OpenAI, "Harness engineering: leveraging Codex in an agent-first world", 11 Feb 2026 (not readable by the tool, HTTP 403). https://openai.com/index/harness-engineering/
7. The Neuron, "OpenAI's Harness Engineering Playbook" [secondary], 2026. https://www.theneuron.ai/explainer-articles/openais-harness-engineering-playbook-how-to-ship-1m-lines-of-code-without-writing-any/
8. Nicholas Carlini, Anthropic, "Building a C compiler with a team of parallel Claudes", 5 Feb 2026. https://www.anthropic.com/engineering/building-c-compiler
9. Justin Young, Anthropic, "Effective harnesses for long-running agents", 26 Nov 2025. https://www.anthropic.com/engineering/effective-harnesses-for-long-running-agents
10. Prithvi Rajasekaran, Anthropic, "Harness design for long-running application development", 24 Mar 2026. https://www.anthropic.com/engineering/harness-design-long-running-apps
11. Anthropic, "How Anthropic teams use Claude Code", 24 Jul 2025. https://claude.com/blog/how-anthropic-teams-use-claude-code
12. Anthropic, "Redesigning source control at Anthropic", 13 Oct 2026. https://www.anthropic.com/engineering/redesigning-source-control
13. Anthropic, "An update on recent Claude Code quality reports", 23 Apr 2026. https://www.anthropic.com/engineering/april-23-postmortem
14. Anthropic, "Effective context engineering for AI agents", 29 Sep 2025. https://www.anthropic.com/engineering/effective-context-engineering-for-ai-agents
15. Anthropic, "Demystifying evals for AI agents", 9 Jan 2026. https://www.anthropic.com/engineering/demystifying-evals-for-ai-agents
16. Anthropic, "Quantifying infrastructure noise in agentic coding evals", 5 Feb 2026. https://www.anthropic.com/engineering/infrastructure-noise
17. Anthropic, "How we built our multi-agent research system", 13 Jun 2025. https://www.anthropic.com/engineering/multi-agent-research-system
18. Anthropic, "How we built Claude Code auto mode: a safer way to skip permissions", 25 Mar 2026. https://www.anthropic.com/engineering/claude-code-auto-mode
19. Anthropic, "How AI is transforming work at Anthropic", 2 Dec 2025. https://www.anthropic.com/research/how-ai-is-transforming-work-at-anthropic
20. Dan Shapiro, "The Five Levels: from Spicy Autocomplete to the Dark Factory", 23 Jan 2026 (title as shown on 2026-10-22; the URL slug says "software factory"). https://www.danshapiro.com/blog/2026/01/the-five-levels-from-spicy-autocomplete-to-the-software-factory/
21. Steve Yegge, Gas Town repository README (released 1 Jan 2026). https://github.com/steveyegge/gastown ; secondary: heise, "Full Control: Gas Town Orchestrates Ten or More Coding Agents". https://heise.de/-11178824
22. Geoffrey Huntley, "Ralph Wiggum as a 'software engineer'", 14 Jul 2025. https://ghuntley.com/ralph/
23. Thomas Dohmke, "The Agentic Assembly Line: My Keynote at WeAreDevelopers World Congress 2026 in Berlin", X article, Jul 2026 (not readable, HTTP 402). https://x.com/ashtom/article/2079231722965651518 ; session video and summary (read 2026-10-15): https://www.wearedevelopers.com/videos/100001-the-agentic-assembly-line
24. Entire documentation, "Core concepts". https://docs.entire.io/core-concepts
25. OpenAI, Symphony announcement, 28 Apr 2026. https://openai.com/index/open-source-codex-orchestration-symphony ; secondary: Help Net Security, 28 Apr 2026. https://www.helpnetsecurity.com/2026/04/28/openai-symphony-codex-orchestration-linear/
26. Alistair Gray, Stripe, "Minions: Stripe's one-shot, end-to-end coding agents", 9 Feb 2026. https://stripe.dev/blog/minions-stripes-one-shot-end-to-end-coding-agents
27. Max Charas and Marc Bruggmann, Spotify, "Spotify's background coding agent, part 1", 6 Nov 2025. https://engineering.atspotify.com/2025/11/spotifys-background-coding-agent-part-1 ; part 2: https://engineering.atspotify.com/2025/11/context-engineering-background-coding-agents-part-2
28. Spotify, "Feedback loops for background coding agents, part 3", Dec 2025. https://engineering.atspotify.com/2025/12/feedback-loops-background-coding-agents-part-3
29. Uday Kiran Medisetty, Uber, "Running a Software Factory Efficiently at Uber Scale", 27 Aug 2026. https://www.uber.com/us/en/blog/efficient-software-factory/
30. Akshay Utture, Augment Code, "Beyond AI Coding Agents: How We Built Augment's Software Factory", 11 Sep 2026, updated 18 Sep 2026. https://www.augmentcode.com/blog/beyond-ai-coding-agents-how-we-built-augments-software-factory
31. Leonardo Piñeyro, Pento, "From Agent Harnesses to Software Factories", 24 Sep 2026. https://www.pento.ai/blog/agent-harness
32. Walden Yan, Cognition, "Don't Build Multi-Agents", 12 Jun 2025. https://cognition.com/blog/dont-build-multi-agents
33. Birgitta Böckeler, "Understanding Spec-Driven-Development: Kiro, spec-kit, and Tessl", martinfowler.com, 15 Oct 2025. https://martinfowler.com/articles/exploring-gen-ai/sdd-3-tools.html
34. GitHub, Spec Kit repository. https://github.com/github/spec-kit
35. Kiro (AWS) website. https://kiro.dev
36. BMAD-METHOD repository. https://github.com/bmad-code-org/BMAD-METHOD
37. AGENTS.md website. https://agents.md
38. Agent Skills website and client list. https://agentskills.io
39. David Soria Parra and Den Delimarsky, "The 2026-07-28 Specification", MCP blog, 28 Jul 2026. https://blog.modelcontextprotocol.io/posts/2026-07-28/
40. David Soria Parra and Den Delimarsky, "The New MCP Roadmap", MCP blog, 22 Aug 2026. https://blog.modelcontextprotocol.io/posts/mcp-roadmap/
41. Agentic AI Foundation blog (posts of 1 and 9 Sep 2026). https://aaif.io/blog/ ; founding details [secondary]: IT Brief, Dec 2025. https://itbrief.co.uk/story/linux-foundation-launches-agentic-ai-open-standards-hub
42. Linux Foundation, "A2A protocol surpasses 150 organizations...", 9 Apr 2026 [secondary, via search summary]. https://www.linuxfoundation.org/press/a2a-protocol-surpasses-150-organizations-lands-in-major-cloud-platforms-and-sees-enterprise-production-use-in-first-year
43. Claude Code docs, "Orchestrate teams of Claude Code sessions". https://code.claude.com/docs/en/agent-teams
44. Claude Code CHANGELOG (latest seen 2.1.300). https://github.com/anthropics/claude-code/blob/main/CHANGELOG.md
45. Claude Code docs, "Claude Code GitLab CI/CD" (beta, maintained by GitLab). https://code.claude.com/docs/en/gitlab-ci-cd
46. GitLab docs, "GitLab Duo Agent Platform". https://docs.gitlab.com/user/duo_agent_platform/
47. GitLab 19.2 release notes, 16 Jul 2026. https://docs.gitlab.com/releases/19/gitlab-19-2-released/
48. GitLab press release, Duo Agent Platform general availability, 15 Jan 2026. https://about.gitlab.com/press/releases/2026-01-15-gitlab-announces-duo-agent-platform-general-availability/
49. GitHub docs, "About Copilot coding agent". https://docs.github.com/en/copilot/concepts/agents/coding-agent/about-coding-agent
50. GitHub Agentic Workflows. https://github.github.com/gh-aw/
51. GitHub changelog, October 2026 entries on Copilot code review. https://github.blog/changelog/
52. InfoWorld, "GitHub previews support for Claude and Codex coding agents", Feb 2026 [secondary]. https://www.infoworld.com/article/4130352/github-previews-support-for-claude-and-codex-coding-agents.html
53. Cursor changelog (entries 2 Sep to 13 Oct 2026). https://cursor.com/changelog
54. OpenAI Codex documentation (developers.openai.com/codex redirects here). https://learn.chatgpt.com/docs
55. Google Antigravity website. https://antigravity.google
56. Google Jules website. https://jules.google
57. Amp website. https://ampcode.com
58. METR, "Measuring the Impact of Early-2025 AI on Experienced Open-Source Developer Productivity", 10 Jul 2025. https://metr.org/blog/2025-07-10-early-2025-ai-experienced-os-dev-study/ ; perception figure via ScienceBlog [secondary]. https://scienceblog.com/t-a-randomized-trial-by-metr-found-that-experienced-developers-completed-real-coding-tasks-19-slower-when-allowed-to-use-ai-tools-yet-afterwards-they-estimated-on-average-that-ai-had-made-them-20-fast/
59. METR, "We are Changing our Developer Productivity Experiment Design", 24 Feb 2026. https://metr.org/blog/2026-02-24-uplift-update/
60. Kevin M. Storer and Derek DeBellis, Google Cloud blog, "Introducing the DORA AI Capabilities Model: 7 keys to succeeding in AI-assisted software development", 24 Sep 2025. https://cloud.google.com/blog/products/ai-machine-learning/introducing-doras-inaugural-ai-capabilities-model
61. DORA, 2025 State of AI-assisted Software Development report page. https://dora.dev/research/2025/dora-report/ ; PDF (too large to parse here): https://services.google.com/fh/files/misc/2025_state_of_ai_assisted_software_development.pdf
62. DORA AI Capabilities Model report page, updated 25 Nov 2025. https://dora.dev/ai/capabilities-model/
63. Jessica Baolin and Nathen Harvey, DORA, "Balancing AI tensions: Moving from AI adoption to effective SDLC use", 10 Mar 2026. https://dora.dev/insights/balancing-ai-tensions/
64. Evan Conaway, DORA, "Finding balance in the era of tokenmaxxing", 2 Jun 2026. https://dora.dev/insights/finding-balance-in-the-era-of-tokenmaxxing/
65. DORA, "A history of DORA's software delivery metrics", 2 Jan 2026. https://dora.dev/insights/dora-metrics-history/
66. InfoQ, "New DORA Report Claims Strong Engineering Foundations Drive AI Return on Investment", 11 May 2026 [secondary]. https://www.infoq.com/news/2026/05/dora-roi-ai-assisted-dev-report/ ; report download: https://cloud.google.com/resources/content/dora-roi-of-ai-assisted-software-development
67. ADTmag, "What 2025 DORA Report Means for Developers", 24 Sep 2025 [secondary]. https://adtmag.com/articles/2025/09/24/what-2025-dora-report-means-for-developers.aspx
68. Faros AI, "AI Engineering Report Q2 2026: The Acceleration Whiplash". https://faros.ai/research/ai-acceleration-whiplash
69. Bill Doerrfeld, LeadDev, "Code maintainability plummets in the AI coding era", 7 Jul 2026 [secondary, on GitClear 2026]. https://leaddev.com/ai/code-maintainability-plummets-in-the-ai-coding-era
70. CodeRabbit, "State of AI vs human code generation report", Dec 2025 [secondary, figures via search summary]. https://coderabbit.ai/blog/state-of-ai-vs-human-code-generation-report
71. Emerson Murphy-Hill, Jenna Butler, Alexandra Savelieva, "Adoption and Impact of Command-Line AI Coding Agents: A Study of Microsoft's Early 2026 Rollout of Claude Code and GitHub Copilot CLI", arXiv 2607.01418, 1 Jul 2026. https://arxiv.org/abs/2607.01418
72. Yeon Su Park and co-authors, "The Work Behind Delegation: A Framework for Supervising AI Coding Agents", arXiv 2609.24234, 21 Sep 2026. https://arxiv.org/abs/2609.24234
73. Yegor Denisov-Blanch, "Does AI Actually Boost Developer Productivity? (100k Devs Study)", AI Engineer talk [secondary summaries]. https://www.ai.engineer/talks/tbDDYKRFjhk-ai-developer-productivity
74. Cui, Demirer, Jaffe, Musolff, Peng, Salz, "The Effects of Generative AI on High-Skilled Work: Evidence from Three Field Experiments with Software Developers", Management Science, 2026. https://pubsonline.informs.org/doi/10.1287/mnsc.2025.00535
75. Abi Noda, Laura Tacho, Margaret-Anne Storey, Michaela Greiler, "Measuring developer productivity with the DX Core 4", DX. https://getdx.com/research/measuring-developer-productivity-with-the-dx-core-4/
76. Abi Noda and Laura Tacho, "DX AI Measurement Framework", DX. https://getdx.com/research/measuring-ai-code-assistants-and-agents/
77. Nicole Forsgren and co-authors, "The SPACE of Developer Productivity", ACM Queue 19(1), Feb 2021. https://www.microsoft.com/en-us/research/publication/the-space-of-developer-productivity-theres-more-to-it-than-you-think/
78. The Register, Replit and SaaStr database deletion, 21 Jul 2025. https://www.theregister.com/2025/07/21/replit_saastr_vibe_coding_incident/
79. Daniel Stenberg, "The end of the curl bug-bounty", 26 Jan 2026. https://daniel.haxx.se/blog/2026/01/26/the-end-of-the-curl-bug-bounty/
80. DEV Community summary of Faros AI's July 2025 "AI Productivity Paradox" report [secondary]. https://dev.to/alexcloudstar/the-ai-productivity-paradox-why-developers-who-ship-more-code-are-not-actually-more-productive-12f7
81. DevOps.com, "Claude Code adds AGENTS.md fallback, cutting instruction-file sprawl", Sep 2026 [secondary]. https://devops.com/claude-code-adds-agents-md-fallback-cutting-instruction-file-sprawl/
82. Fiona Chen and James Stratton, "Artificial Intelligence in the Firm: Bottlenecks in Software Production", Harvard University job-market paper, version of 4 Aug 2026 (first version 7 Jan 2026). https://fion.ac/jellyfish.pdf
83. InfoQ, report of Spotify's Honk talk at QCon London, 18 Mar 2026 [secondary]. https://infoq.com/news/2026/03/spotify-honk-rewrite
84. GitLab docs, Code Review Flow. https://docs.gitlab.com/user/duo_agent_platform/flows/foundational_flows/code_review/
85. GitLab blog, flat-rate agentic code reviews, 19 Mar 2026. https://about.gitlab.com/blog/agentic-code-reviews-with-flat-rate-pricing
86. Claude Code docs, GitLab CI/CD and Code Review. https://code.claude.com/docs/en/gitlab-ci-cd and https://code.claude.com/docs/en/code-review
