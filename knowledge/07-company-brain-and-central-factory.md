# Company brain and central factory

Written 2026-10-28 from public sources, directly for this pack, so unlike files
01 to 06 it has no removed sections and no numbering gaps. It extends `01` (the
factory state of the art), `02` (CI/CD foundations) and `05` (controlled
architecture) with three questions that come up when a leadership team asks for
"a company brain" and "a centralized software factory":

- what a generated knowledge hub is, and when building one beats giving agents
  search access to the systems you already run;
- how one factory can serve dozens of repositories with different stacks and
  purposes;
- how seat-based human use and API-billed pipeline agents share the same
  factory without one displacing the other.

Tags work as in the other files. "Checked 2026-10-28" means the source was read
that day. Most vendor documentation pages carry no publish date, so treat their
facts as holding on the checked date.

## 1. What matters

1. **"Company brain" has exactly one public reference implementation:
   Pipedrive's Atlas**, a documentation hub generated from source code by
   agents, regenerated with every significant deploy, with up to nine generated
   pages per service. The July 2026 talk that showed it is the only public
   source; nothing published describes its pipeline, its accuracy controls or
   any layer above the per-service pages (section 2.1). [verified for the talk,
   checked 2026-10-28]
2. **No other company publicly runs a regenerated hub.** The pattern at
   Shopify, Spotify, Monzo, Stripe and Uber is different: agents get tool
   access to the systems that already exist (a service catalogue, logs, the
   issue tracker, Slack) through MCP, and written knowledge accumulates as
   files that agents themselves update as a by-product of work (section 2.2).
   [verified per case]
3. **The retrieval evidence points at a hybrid.** Precompute the structure an
   agent cannot cheaply re-derive, a map; retrieve the rest live; put
   correctness in checks rather than prose. Anthropic's own context
   engineering guidance, Claude Code's history of dropping an embeddings index
   for agentic search, and Cursor's eval, which found its semantic index
   helping in every configuration with the extra gains concentrated past a
   thousand files, all fit that reading (section 2.3).
4. **What a brain adds over raw access to GitLab, Confluence and Jira** is
   amortization and consistency: the organisation's structure gets reasoned
   out once, reviewed, and then read cheaply by every agent run, instead of
   being re-derived noisily on every task. What it does not add is truth; a
   generated page is a cache, and caches go stale (section 2.4).
5. **At ten to thirty repositories, generate or buy the map rather than build
   a platform.** A per-repo generated overview reviewed by its owner, the code
   platform's own graph where one exists, and disciplined instruction files
   cover most of what Atlas does at 750-service scale (sections 2.5, 2.6).
6. **A central factory centralizes the worker and the rules, not the
   pipeline.** Each repository keeps its own build, test and deploy. What is
   shared is a worker service that opens merge requests anywhere, the policy
   that gates it, the context it reads, and the metering (section 3.1).
7. **Every published fleet has that shape.** Pipedrive's PR-bot, Stripe's
   Minions, Spotify's Honk, Shopify's River and Monzo's Agent Chip are all one
   central service working many repositories whose pipelines stay their own
   (section 3.2).
8. **Seat and API billing coexist by design, not as a compromise.** Humans
   work interactively on seats. Every unattended run uses a service identity
   and API billing with spend caps at three layers. Anthropic ties each
   subscription token to the person who minted it, points shared pipelines
   and products at API keys, and a pipeline on a seat token drains that
   person's rate windows (section 4.1). [checked 2026-10-28]
9. **Both lanes end at the same gates.** Human merge requests and factory
   merge requests pass the same CI and the same review rules, and humans can
   invoke the factory mid-flow on their own branch. A factory adds on-ramps;
   it does not close the road (section 4.3).
10. **Sequence: one consequential workload first, then foundations, then the
    map, then a comment-only agent, then a narrow writer.** Each step leaves
    something the next one uses, and the measurement baseline comes before any
    of it (section 5, guide §5).

## 2. The company brain

### 2.1 The reference case: Pipedrive's Atlas

Agur Jõgi, then CTO of Pipedrive, showed Atlas in "Building an agentic
software factory: How we rebuilt product development at Pipedrive"
(WeAreDevelopers World Congress, Berlin, 10 July 2026) [1]. What the talk
establishes [verified against the talk's slides; Pipedrive's own unaudited
figures]:

- A "living architecture documentation hub", generated from source code,
  regenerated with every significant deploy, covering almost all services
  across Product and Platform. Pipedrive has about 750 microservices and
  2,200 repositories [transcript].
- Up to nine generated pages per service. The slide names eight: overview;
  architecture (components, dependencies); flow map (sequence diagrams per
  endpoint and async consumer); data flow; datastore; development; operations
  (runbooks, scaling, alerting); security (auth model, data sensitivity).
- Atlas is explicitly not ground truth and not the only source. It exists
  mainly to enable agentic ways of working: context for agents first,
  readable docs second.

What no public source says, as of 2026-10-28: how generation works (models,
agents, cross-service views), what exactly triggers regeneration, how
hallucination is controlled, how agents consume it, or whether anything sits
above the per-service pages. A search of Pipedrive's engineering blog archive
(July 2025 to August 2026), press, podcasts and job postings found no written
mention of Atlas at all, and none of PR-bot [checked 2026-10-28]. If someone
describes a brain with layers running from business goals down to UI, ask
which layers, because the public record stops at service-level pages, and the
"nine" most likely refers to the nine per-service page types [inferred].

### 2.2 What the working systems actually run

Pipedrive itself layered two different things before and around Atlas:

- **HAL** (July 2025) is classic retrieval: a cron collector pulls each
  source's API, chunks to about 512 tokens, embeds, and a Slack bot answers
  with hybrid vector and keyword search. Pipedrive reports it saves about 20
  minutes per information-seeking task, and its write-up flags that "get me
  all of X" questions defeat plain retrieval [2] [verified].
- **Scooby** (May 2026), the incident investigator, is not built on a
  generated hub. It is a Claude Code plugin wired to an internal MCP server
  (Grafana, Loki, Tempo, company metadata, deployment history, Slack,
  Confluence), with one orchestrator skill routing to domain skills, 7 at
  launch and 17 a few months in. Its knowledge accretes: after a wrong
  investigation, the team feeds it the real resolution and has it rewrite its
  own SKILL.md files, with a human as editor pushing for pattern-level rules.
  Over 1,200 investigations in two months, about 5 minutes and $1.60 each,
  CSAT 90 of 100 across 146 respondents. Pipedrive frames its output "as a
  starting hypothesis, not a verdict" and admits having no formal accuracy
  metrics [3] [verified].

The same two moves, tool access plus accreted files, show up everywhere else:

| Company | Context for agents | Scale reported | Source |
|---|---|---|---|
| Shopify | "World" monorepo of skills, AGENTS.md files, intent documents, runbooks and conventions; agents leave diffs to it behind as they work. River agent runs on the Aquifer internal platform | 1 in 8 merged PRs co-authored by River; 59,918 sessions in a 30-day period | [4], 28 May 2026 [verified] |
| Spotify | Backstage software catalogue exposed as MCP and CLI tools: ownership lookups, docs, contacting the owning team on Slack | More than 2.5 million automated maintenance PRs merged, most auto-merged | [5], 3 Jun 2026 [verified] |
| Monzo | Agent Chip gives agents an MCP gateway with "centralised access to a set of knowledge sources"; each sandboxed bundle talks through a single isolated HTTP proxy | About 10% of merged PRs; more than 1,800 tasks a day; MVP in about three weeks | [6], 13 Aug 2026 [verified] |
| Uber | Source code, engineering docs, Slack and Jira as agent "memory"; an MCP gateway with registry and sandbox | 11% of PRs opened by agents; 84% of developers using agentic tools | [7], 10 Mar 2026 [secondary] |
| Stripe | Internal MCP servers, a shared one called Toolshed; orchestration interleaving deterministic code with agent loops ("blueprints" in secondary coverage) | Over 1,000 merged Minion PRs a week | [8], 9 Feb 2026 [primary post; term and some figures via secondary coverage] |

Nobody waits for a finished brain. They wire agents to what exists, and the
written layer grows as a by-product of agents working, Scooby's SKILL.md loop
and Shopify's World being the clearest statements of that [inferred from the
cases above].

### 2.3 Precomputed map or on-demand search: the evidence

- Anthropic's context engineering guidance has agents keep lightweight
  identifiers (paths, queries, links) and load data at runtime, notes that
  "runtime exploration is slower than retrieving pre-computed data", and
  endorses a hybrid that retrieves some data up front for speed [9], 29 Sep
  2025 [verified].
- Claude Code itself started with an embeddings index and dropped it because
  agentic search worked better; the stated liabilities were index staleness,
  security and moving parts, which are exactly the liabilities a generated
  hub takes on [10] [secondary, from an engineer's account].
- Cursor's eval of its semantic index against grep-only agents: 12.5% higher
  question-answering accuracy on average, with code-retention gains
  concentrated on codebases past 1,000 files, and a 2.2% rise in dissatisfied
  follow-up requests when the index was removed. Their conclusion is both:
  the best agents use grep and the index together [11], 6 Nov 2025 [verified,
  vendor eval].
- Aider has shipped the minimal precomputed artifact since 2023: a
  graph-ranked repository map, about 1,000 tokens, sent with every request
  for orientation, with retrieval doing the depth [12] [verified].
- The counterargument: Simon Willison doesn't think his own comprehensive
  documentation helps coding agents much, since agents read code faster than
  prose;
  what helps is runnable tests, dev-server instructions, linters and type
  checkers, and prose for agents drifts where a pre-commit hook enforces [13],
  25 Oct 2025 and 18 Sep 2026 [verified, opinion].
- The useful split: when you can name the identifier, grep wins; conceptual
  questions ("where do we retry?", asked of code that never says "retry") are
  where precomputed or semantic context wins [14] [secondary]. Conceptual,
  cross-repository questions are what an architecture hub answers.

Resolution [inferred]: precompute the structure an agent cannot cheaply
re-derive per run, which services exist, who owns what, how data flows between
repositories. Retrieve everything else live through tools. Keep correctness in
checks, not in prose. No independent evaluation comparing a generated hub
against MCP retrieval for coding agents exists publicly as of 2026-10-28, so
anyone claiming one approach is proven is ahead of the evidence.

### 2.4 What a brain adds over raw GitLab, Confluence and Jira access

Giving an agent MCP access to the forge, the wiki and the tracker is the
baseline, and section 2.2 shows serious fleets running exactly that. A
precomputed layer adds four things [inferred from the sources above]:

- **Amortization.** Deriving "what talks to what" across a few dozen
  repositories costs tokens and minutes every single run. A map pays that cost once per
  regeneration. Scooby's roughly $1.60 investigations work because the
  expensive context, which skill applies, where the dashboards are, what past
  incidents taught, is already written down.
- **Consistency.** Two agents searching raw sources can reach different
  pictures of the same system. Two agents reading the same generated page
  cannot.
- **Conceptual coverage.** "Get me all services that write to X" defeats both
  keyword search and per-repo grep. It is answerable from a generated data
  flow page or a code graph.
- **A safe interface.** A read-only generated page can be handed to a narrow
  agent without also handing it credentials to five live systems. That
  matters under the Rule of Two (`04`).

What it does not add is truth. A generated page is a cache of reasoning about
the code at one commit. Every page needs a visible generation date, a
regeneration trigger and an owner who saw at least the first version, or the
brain becomes the stale wiki it was meant to replace, with better formatting
[inferred].

### 2.5 Build, buy or skip at ten to thirty repositories

The buyable versions, checked 2026-10-28; prices change, re-check before
deciding:

| Product | What it does | Pricing signal |
|---|---|---|
| GitLab Knowledge Graph / Orbit | Turns the codebase into a live graph (files, classes, functions, relationships) served to agents by a local MCP server; Orbit extends the graph to merge requests, pipelines, work items and vulnerabilities at group level. Knowledge Graph went experiment in 18.3, beta in 18.4 (2025); the docs conflict on Orbit's status, with one page saying GA in 19.5 | Knowledge Graph on all tiers; Orbit's group graph needs Premium or Ultimate [15] |
| Cognition DeepWiki / Devin Wiki | Generated wiki per repository: architecture diagrams, source links, summaries; a `wiki.json` can pin the exact page set (cap 30 pages, 80 on enterprise); consumed by Q&A and an MCP server. Freshness mechanics undocumented | Public repos free; private wikis via Devin plans: low-effort generation free, higher effort uses credits; cheapest self-serve plan $20/month (Apr 2026 plans) [16] |
| Unblocked | Codebase and history Q&A plus review, with MCP connections | $29 per user per month billed annually, $35 monthly [17] |
| Sourcegraph Deep Search | An agent that answers semantic, cross-repository, historical and architectural questions, exposed to other agents over MCP | Enterprise contract [18] |
| Glean | Enterprise search and knowledge graph across org systems, MCP server for agent hosts | Sales-only [19] [secondary] |

For a company in this pack's size range, the honest version of "build the
brain" is three thin layers, not nine [inferred]:

1. **An organisation map**: one generated overview page per repository
   (purpose, owner, interfaces, data in and out, how to run it), produced by
   an agent, reviewed by the repository's owner, regenerated on a trigger.
   Plus one hand-written page listing the repositories and how they relate.
   This is the part worth generating, because it answers the cross-cutting
   questions nothing else answers.
2. **The platform's graph**, if the forge offers one (GitLab Knowledge Graph
   above), served to agents over MCP. This is the part worth buying or
   switching on, because maintaining your own code graph is a product.
3. **In-repo instruction files** (CLAUDE.md, AGENTS.md, skills), accreted
   under review as agents and people work, per `01` and the guide §3. This is
   the part that must not be generated wholesale, because its value is that
   someone meant it.

Skip the brain entirely while the repository count is small enough that one
person holds the map in their head, and write that person's map down first;
generation is for keeping it current, not for discovering it [inferred].

### 2.6 A v1 that stays honest

Rules that keep a generated hub from lying, drawn from Atlas's own framing and
the failure modes in `01` §2.8 [inferred]:

- Regenerate on deploy or on a schedule, and print the generation date and
  source commit on every page. An undated generated page is a rumor.
- The repository owner reviews the first generated version of each page;
  after that, review diffs, not pages.
- Agents consume the hub read-only, as files in a repository or through one
  read-only MCP server, never with credentials to the systems the hub
  describes.
- The generation prompt must cite file paths for claims, so a sampling check
  (a second agent verifying a handful of claims per regeneration against the
  code) is cheap to run.
- Carry Pipedrive's label visibly: a starting hypothesis, not a verdict, and
  measure use and satisfaction like a product. Scooby's team runs a CSAT
  survey; a hub nobody queries should be retired.

## 3. One factory, many repositories

### 3.1 Centralize the worker, not the pipeline

The apparent contradiction: a few dozen repositories with different stacks
cannot share a pipeline, so how can a factory be central? The resolution, visible in
every published fleet: nobody centralizes the pipeline. Build, test and deploy
stay per-repository, owned by the teams. What is central is a **worker**, a
service that takes tasks from a queue and opens merge requests in whichever
repository the task names, and the **rules** around it: who may trigger it,
what it may touch, what gates its output, what it costs [inferred from 3.2].

Put differently, the factory is a control plane over per-repo data planes.
Central: intake and triage, task routing, the agent runtime (images,
credentials, sandboxes), the context layer (section 2), the policy gates, the
skill and plugin registry, and metering. Per-repo: the stack-specific CI, the
tests that define "done", deployment, review ownership, and the instruction
files that teach any agent the repo's own rules.

### 3.2 The published fleets, read as central worker plus local pipeline

| System | The central part | What stays per-repo | The human gate |
|---|---|---|---|
| Pipedrive PR-bot [1] | One bot implementing Jira issues and upgrades "across many repositories", reacting to failed CI, review comments and merge conflicts | Each repo's checks, which the bot works against | Not stated in the talk; 361 merged PRs, 79% with no human code edits [slide] |
| Stripe Minions [8] | Task intake via Slack, deterministic-plus-agent orchestration, a warm devbox pool, shared MCP tools | Repo checks and review | Every PR reviewed by engineers |
| Spotify Honk [5] | One background agent (Claude via the Agent SDK in Kubernetes pods) doing fleet-wide maintenance | Per-component builds in the fleet | Teams review; most maintenance PRs auto-merge under policy |
| Shopify River [4] | The Aquifer platform: sessions, sandboxes, gateway, event log, credential proxy | Repo CI | Review of co-authored PRs |
| Monzo Agent Chip [6] | Task runner with sandboxed bundles behind a single isolated proxy, MCP gateway for knowledge | Repo CI | Review; about 10% of merged PRs |
| Builder.io factory skills [20] | A set of policy-gated skills on schedules: collect feedback, fix, open PR, babysit it, review the queue, ship, plus a lookback, a watchdog and a human digest | The project's own checks | Separate policy criteria for replying, approving and merging; ambiguous items routed to a human digest |

The last row is the same shape at solo-developer scale, published as an open
repository and worth reading as a minimal reference, not as evidence that the
pattern works unattended in a company [verified repo, checked 2026-10-28].

### 3.3 The GitLab shape

Concretely, on GitLab, the central factory is two repositories and a service
account, not a platform [inferred; mechanics in `02`]:

- **Shared checks as CI components.** Each repository includes the shared
  review, lint and policy jobs from a components repo and keeps its own
  stack jobs. This is the only thing "central CI" should mean.
- **One factory repository** holding the worker: job templates, prompts,
  skills, an allowlist of target repositories, and the queue logic. Triggered
  by a webhook, a label, or a schedule, it checks out the target repository,
  runs the agent, and opens a merge request there with a service account
  scoped to that repository. Agent config is code, with owners and review
  (`04`).
- **Three hosting routes for the agent itself**, checked 2026-10-28: plain
  `claude -p` in your own job (full control, most work); Anthropic's GitLab
  CI/CD integration, in beta and maintained by GitLab, which runs the same
  CLI in your pipeline on an API key and needs your own webhook listener for
  mention-triggered runs [21]; or GitLab's own Duo external agents (Premium
  and Ultimate, behind a feature flag GitLab enables for verified customers),
  where a Claude Code agent runs on GitLab-managed credentials and reacts to
  mentions natively [22]. GitLab's packaged flows, like the
  Code Review Flow, are the buy option on the same tiers (`01` §2.2).
- **Unattended runs need explicit limits.** From Claude Code v2.1.302 a `-p`
  run starts in manual permission mode unless a mode is set, so a pipeline
  job must set one deliberately; `--max-turns` and `--max-budget-usd`
  (v2.1.217+, a client-side estimate that can overshoot) plus the job timeout
  are the per-run caps [23].

### 3.4 A ladder, not a leap

- **Level 0**: humans with seats, no shared agents. Most companies are here.
- **Level 1**: shared comment-only jobs via CI components: review, policy,
  triage. No write access. This is where the first measurable value lands
  (`01` §1, guide §5).
- **Level 2**: one central writer bot, one task class (dependency upgrades,
  then similar), a few repositories, human merge, metered. Pipedrive's PR-bot
  started life as this [inferred from the talk's framing].
- **Level 3**: queue-driven fleet with policy routing by blast radius,
  lookbacks and watchdogs. The published examples sit here, at hundreds to
  thousands of engineers. A company of 50 to 100 people rarely has the queue
  depth to need it, and should get there by data from level 2, not by
  architecture [inferred].

## 4. Seats and pipelines: two billing worlds, one factory

### 4.1 The facts, checked 2026-10-28

- Humans use seats; plan mechanics, usage credits for overflow and the rule
  that pooled API credits cannot be spent in Claude Code are in the guide §8
  and `05` §2.2.
- The documented route for shared pipelines is a Console API key created by a
  service account in a dedicated workspace, not the auto-created "Claude
  Code" workspace, with a monthly spend limit on the workspace. Keyless
  workload identity federation exists for named issuers plus custom OIDC;
  GitLab is not named, so treat a GitLab setup as unconfirmed [24].
- Subscription OAuth tokens (`claude setup-token`, valid one year, on Pro,
  Max, Team and Enterprise) are documented for CI, but each token is tied to
  the subscription of the person who minted it; the GitHub Actions page
  recommends a Console API key for a secret shared across repositories, and
  Anthropic's compliance page says products and services should use API keys. A pipeline on a seat token also drains that
  person's 5-hour and weekly windows, which are shared with their own chat
  and coding use. Running an organisation's pipelines on seat tokens is
  unconfirmed at best; get it in writing from the vendor before relying on it
  [25].
- Precedence trap: in `-p` mode a present `ANTHROPIC_API_KEY` wins over an
  OAuth token, and `--bare` ignores OAuth credentials entirely, so one stray
  variable on a runner silently changes who pays [25].
- Budget caps stack at three layers: the workspace spend limit, the per-run
  flags of 3.3, and OpenTelemetry cost metrics with the org cost-report API
  for alerting and reporting [23][24]. For rough planning, Anthropic cites an
  average of about $13 per developer per active day, with 90% of users under
  $30 [23].

### 4.2 The design that follows

Two lanes, one metering story [inferred from the facts above]:

- **The human lane.** Interactive Claude on seats, governed by the vendor
  console, SSO and managed settings (guide §8). This is where design,
  debugging, pairing and exploration happen, and nothing about a factory
  moves it anywhere.
- **The machine lane.** Every run no person is watching, review jobs, writer
  bots, scheduled regenerations of the map, runs on a service identity with
  an API key or federation, inside spend limits, with telemetry. Not because
  seats are forbidden there, but because unattended work needs an owner, a
  budget and a log that no personal account provides.

The factory question "does everything move to `claude -p` on runners?" has a
flat answer: no. The machine lane industrializes ticket-shaped work; the human
lane keeps the seats it has. The spend adds, it does not substitute, and the
mitigation is that machine-lane tasks are narrow, capped and measured per
merged change (guide §5).

### 4.3 Where the lanes meet

- **Same gates.** A human's merge request and the factory's merge request run
  the same CI components and the same review rules. One definition of done,
  whoever wrote the diff.
- **Humans invoke the factory mid-flow.** Pipedrive's PR-bot has two entry
  points: `jira-to-pr` (68 active users, 551 events in the month shown) and
  `claude-on-branch` (69 active users, 1.1K events) [slide]. The names
  suggest the second runs the bot on a person's existing branch, which would
  make human-invoked runs the majority of factory use [inferred]. The factory
  is something developers call, not only something that replaces them.
- **Human work feeds the machine lane.** Instruction files, skills and the
  map improve through interactive sessions; lookback-style jobs turn
  recurring toil into new factory skills (Builder.io's `/factory-lookback`
  [20], Scooby's SKILL.md rewrite loop [3]).
- **Work enters at any stage.** A ticket enters at triage; a branch enters at
  the bot; an incident enters at investigation; a telemetry signal enters at
  collection. The factory is a set of on-ramps with shared gates, not a
  conveyor from idea to deploy that everything must ride end to end
  [inferred].

## 5. Sequencing for a small engineering organisation

In order, each step leaving something the next one uses. The measurement
baseline from the guide §5 comes before step one [inferred; grounded in `01`
§1 and the cases above].

1. **Industrialize the one workload that already matters.** Pick the
   agent-powered thing with real consequences that today runs informally, and
   give it the factory anatomy: its own repository, versioned skills, CI with
   saved example cases, a service identity, a scheduled or triggered runner,
   logs, an owner. This proves the pattern on something people care about and
   is the template every later agent copies.
2. **Foundations.** Shared CI components, instruction files in the busiest
   repositories, and one internal MCP server for organisation data on service
   credentials (guide §4). Without these there is nothing for a factory to
   attach to.
3. **The map** (sections 2.5, 2.6): generated repo overviews, the platform
   graph, accretion rules. Start with the two or three repositories agents
   will actually work in.
4. **First cross-repo agent, comment-only.** Review or triage, attached
   through the CI components, measured by acted-on rate and rejected-as-wrong
   rate (guide §5).
5. **First narrow writer.** One task class, a few repositories, human merge,
   budget caps per section 4, a babysit loop that reacts to its own failed
   checks. Expand task classes one at a time.
6. **Only then revisit level 3.** If queue data shows coordination, not
   capacity, is the bottleneck, the orchestrator patterns in `01` §2.4 are
   the reading list.

## Sources

1. Agur Jõgi, "Building an agentic software factory: How we rebuilt product development at Pipedrive", WeAreDevelopers World Congress, 10 Jul 2026. https://www.wearedevelopers.com/videos/100261-building-an-agentic-software-factory-how-we-rebuilt-product-development-at-pipedrive
2. Ruben Silva, "HAL — How Pipedrive leverages RAG for better knowledge sharing", Pipedrive R&D blog, 22 Jul 2025. https://medium.com/pipedrive-engineering/hal-how-pipedrive-leverages-rag-for-better-knowledge-sharing-5e427c438212
3. Aleksandr Smirnov, "Scooby: How I Taught an AI to Be Our On-Call Engineer", Pipedrive R&D blog, 21 May 2026. https://medium.com/pipedrive-engineering/scooby-how-i-taught-an-ai-to-be-our-on-call-engineer-163e3b3662ab
4. Burke Libbey and Javier Moreno, "Under the River", Shopify Engineering, 28 May 2026. https://shopify.engineering/under-the-river
5. Spotify Engineering, "Coding Is No Longer the Constraint", 3 Jun 2026. https://engineering.atspotify.com/2026/6/code-with-claude-coding-is-no-longer-the-constraint ; background: "Spotify's background coding agent, part 1", Nov 2025. https://engineering.atspotify.com/2025/11/spotifys-background-coding-agent-part-1
6. Fabien Deshayes and Oli Haley, "Building Agent Chip: Monzo's in-house agentic tool", 13 Aug 2026. https://monzo.com/blog/building-agent-chip
7. The Pragmatic Engineer, "How Uber uses AI for development: inside look", 10 Mar 2026. https://newsletter.pragmaticengineer.com/p/how-uber-uses-ai-for-development
8. Alistair Gray, "Minions: Stripe's one-shot, end-to-end coding agents", stripe.dev, 9 Feb 2026. https://stripe.dev/blog/minions-stripes-one-shot-end-to-end-coding-agents ; secondary coverage: InfoQ, Mar 2026. https://www.infoq.com/news/2026/03/stripe-autonomous-coding-agents/
9. Anthropic, "Effective context engineering for AI agents", 29 Sep 2025. https://www.anthropic.com/engineering/effective-context-engineering-for-ai-agents
10. Account of Claude Code dropping its embeddings index for agentic search (secondary). https://vadim.blog/claude-code-no-indexing/
11. Cursor, "Improving agent with semantic search", 6 Nov 2025. https://cursor.com/blog/semsearch
12. Aider documentation, "Repository map". https://aider.chat/docs/repomap.html
13. Simon Willison, "Coding agent tips", 25 Oct 2025. https://simonwillison.net/2025/Oct/25/coding-agent-tips/ ; drift note, 18 Sep 2026. https://simonwillison.net/2026/Sep/18/probably-gonna-eat-you/
14. "On the Lost Nuance of Grep vs. Semantic Search". https://www.nuss-and-bolts.com/p/on-the-lost-nuance-of-grep-vs-semantic
15. GitLab documentation, Orbit (the Knowledge Graph pages moved under Orbit in 2026). https://docs.gitlab.com/orbit/
16. Cognition, DeepWiki documentation. https://docs.devin.ai/work-with-devin/deepwiki ; plans: https://www.cognition.ai/blog/new-self-serve-plans-for-devin
17. Unblocked pricing. https://getunblocked.com/pricing
18. Sourcegraph, Deep Search. https://sourcegraph.com/deep-search
19. Glean MCP coverage (secondary). https://getunblocked.com/blog/glean-mcp-vs-context-engine/
20. Builder.io skills repository (factory skills, policy gates, schedules). https://github.com/BuilderIO/skills ; walkthrough video: Steve Sewell, "Build an agentic software factory: deep dive", 23 Sep 2026. https://www.youtube.com/watch?v=pNmfMi-yjZk
21. Anthropic, Claude Code GitLab CI/CD (beta). https://code.claude.com/docs/en/gitlab-ci-cd.md
22. GitLab documentation, Duo Agent Platform external agents. https://docs.gitlab.com/ee/user/duo_agent_platform/agents/external.html
23. Anthropic, Claude Code costs and headless documentation. https://code.claude.com/docs/en/costs.md ; https://code.claude.com/docs/en/headless.md ; https://code.claude.com/docs/en/cli-reference.md
24. Anthropic platform documentation: authentication, workspaces, workload identity federation, usage and cost API. https://platform.claude.com/docs/en/manage-claude/authentication.md ; https://platform.claude.com/docs/en/manage-claude/workspaces.md ; https://platform.claude.com/docs/en/manage-claude/workload-identity-federation.md ; https://platform.claude.com/docs/en/manage-claude/usage-cost-api.md
25. Anthropic, Claude Code authentication, GitHub Actions, and legal and compliance pages. https://code.claude.com/docs/en/authentication.md ; https://code.claude.com/docs/en/github-actions.md ; https://code.claude.com/docs/en/legal-and-compliance.md
