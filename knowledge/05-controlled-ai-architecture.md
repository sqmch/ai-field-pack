# Controlled AI usage and architecture

> Generic edition, built 2026-10-26: company-specific and personal content removed. See the pack README. Sections removed in this edition are omitted, and the numbering is kept so pointers resolve.

Written 2026-10-14. Scope: which AI tools a company of 50 to 100 people gives its
employees, on which plans, with which admin controls, and how they are wired
together. Vendor facts were read on the vendors' own pages on 2026-10-14 unless a
source says otherwise. Plans, prices and admin features change monthly, so re-check
any price or feature before quoting it. Updated 2026-10-22:
Team seats, consumer terms, joining an org with a personal account, Enterprise search,
and claude.ai org plugins (2.2 and 2.6, sources 183 to 193). Then connectors vs
desktop extensions, authoring skills without git, what a plugin holds, skill sprawl,
evals, and GitLab's catalogues (sources 194 to 207).

Labels: [verified] read on an official vendor or project page; [secondary] from a
third party; [inferred] my reading, may be wrong. Numbers in square brackets point to
the sources at the end.

Out of scope here, covered elsewhere in this folder: threats and incident runbooks
(`04-ai-security-and-hardening.md`), regulation (`03-regulation-and-standards.md`),
policy text and training (`06-policy-operating-model-and-enablement.md`), CI/CD
(`02-cicd-and-platform-foundation.md`) and factory approaches and tools
(`01-agentic-factory-state-of-the-art.md`).

## 1. What matters most

1. **Controls live in four places, and a policy must name all four**. Identity
   (SSO, provisioning), the vendor's admin console (plan, retention, connectors,
   spend), the client's configuration (managed settings on each laptop) and the
   network path (gateway, proxy). A rule that is only written down is not a control.
2. **The plan tier decides which controls exist**. On Anthropic, the Team
   plan has SSO but no SCIM, audit logs or Compliance API; those are Enterprise only
   [5][18]. Choosing the plan is the first policy decision, not a purchasing detail.
3. **No vendor trains on business-plan data by default, but retention differs**.
   Under Anthropic's commercial terms there is no training on Claude Code prompts,
   standard retention is 30 days, and zero data retention needs special enablement
   on Enterprise [6][23]. The real differences are retention, residency and who can
   read logs.
4. **Anthropic's first-party API has no EU inference option today**. Only
   `global` and `us` exist; EU-only inference for Claude means Amazon Bedrock `eu.`
   profiles or Google Cloud in an EU region [7][26]. This decides how "red" data is
   routed.
5. **Claude Code can be centrally locked down, but only on the machine**.
   Managed settings (server-delivered, MDM or a system file) override user and
   project settings: permission rules, sandbox, MCP allowlist, plugin marketplaces,
   models, login organisation [2][14]. Anthropic states these are a client-side
   control, "not a security boundary" on unmanaged devices [3].
6. **Server-managed settings are one policy for the whole organisation**. Per
   group policy needs MDM targeting or a self-hosted Claude apps gateway [2][4].
   At 50 to 100 people this matters less than it sounds, but data science and
   finance may need different rules from engineering.
7. **A gateway and seat-based Claude Code partly exclude each other**.
   Pointing Claude Code at a custom `ANTHROPIC_BASE_URL` skips server-managed
   settings and claude.ai features such as cloud sessions [3][14][129]. Use a gateway for
   API and agent traffic, not as a blanket rule for every developer session.
8. **The factory's agents need key custody and budgets, which consoles do not give**.
   CI agents, internal tools and experiments call model APIs with keys. A small
   gateway (or the vendor's workspace limits) gives each agent its own key, model
   allowlist and spend cap.
9. **MCP servers and plugins are the new supply chain**. Each connector is
   code or a remote service with access to data. An approved catalogue, a vetting
   checklist and managed allowlists turn "can I use this MCP server?" into a
   two-minute answer.
10. **Agent configuration is software and should ship like software**. One
    company repository for managed settings, a plugin marketplace, shared skills and
    `AGENTS.md` templates, reviewed by merge request, released in channels. Claude
    Code accepts a GitLab-hosted marketplace [15].
11. **Logging prompts is employee monitoring under EU law**. Default to metadata
    (who, which tool, tokens, cost), keep prompt capture off, and name who may read
    transcripts and why. Claude Code's telemetry redacts prompts by default [16];
    Enterprise's Compliance API can retrieve full session transcripts [12].
12. **Personal and company accounts must be separable by configuration**.
    `forceLoginOrgUUID` pins Claude Code to the company organisation, and Enterprise
    tenant restrictions block other Claude organisations on the company network
    [14][19].
13. **Onboarding is per role because data access is per role**. Engineers need
    coding agents and repository access; finance and HR mostly need chat with tight
    data rules. The table in section 2.8 is the input for that work.
14. **Make vs buy at 50 to 100 people: buy the tools, build only the glue**. Vendor
    plans plus a thin, well-reviewed configuration layer beat a self-built platform.
    The exception is the factory's own automated agents, where a gateway pays off.

## 2. Body

### 2.1 Concepts in one place

| Concept | What it is | What it does | Example at a company of 50 to 100 people |
|---|---|---|---|
| SSO | Sign-in through the company identity provider (IdP) via SAML or OIDC | One account, one offboarding point, MFA enforced by the IdP | Staff sign in to Claude with their Google or Microsoft work account |
| JIT provisioning | Account is created at first SSO sign-in | No manual invites | A new hire opens claude.ai, signs in, gets a seat |
| SCIM | IdP pushes users and groups to the vendor | Automatic deprovisioning and group sync | Removing someone in the IdP removes their Claude access without anyone logging in to Claude |
| Domain verification and capture | Vendor recognises `@company` emails as the company's | Stops shadow personal accounts on the work domain, or migrates them | Existing personal Claude accounts on the `@company` domain are claimed into the org |
| Tenant restriction | Corporate proxy injects a header naming allowed organisations | Blocks other tenants (personal accounts) from the company network | Header `anthropic-allowed-org-ids` lists only the company's org UUID [19] |
| Managed settings | A policy file the agent reads with top priority | Users cannot override it | Deny reading `.env`, disable bypass mode, allow only approved MCP servers |
| MDM | Device management (Jamf, Intune, Google endpoint management) | Puts files and policy profiles on laptops and keeps them there | Pushes `managed-settings.json` to every engineer laptop |
| AI gateway | A proxy between clients and model APIs | Holds real keys, issues virtual keys, logs, budgets, routes models | The review agent in CI calls the gateway with a key capped at $200 a month |
| MCP server | A program or service that gives an agent tools and data via the Model Context Protocol | Lets the agent read a ticket system, query a database, call an API | A read-only GitLab MCP server for issue triage |
| MCP gateway or registry | A central point that lists, authenticates and filters MCP servers | One approved catalogue, central auth, tool filtering | Only the catalogue's servers are reachable from agents |
| ZDR | Zero data retention | Vendor does not store prompts and outputs after the response | Claude Code on Enterprise, after Anthropic enables it per organisation [23] |
| DPA | Data processing agreement under GDPR Art. 28 | Makes the vendor a processor with contractual limits | Anthropic's DPA with SCCs is part of its Commercial Terms [25] |
| Data residency | Where data is processed (inference) and stored (at rest) | Keeps data in a region | Bedrock `eu.anthropic.*` profiles route inside EU regions [26] |

### 2.2 Anthropic: Claude Team, Enterprise, Console API and cloud routes

#### Plans and what they unlock

| | Team | Enterprise | Console (API) | Bedrock / Google Cloud / Foundry |
|---|---|---|---|---|
| Who it is for | 2 to 150 people [5] | Any size, 20-seat minimum on self-serve [5] | API-first, pay per token | Companies already on that cloud |
| Price (list, Oct 2026) | Standard seat $20/month annual or $25 monthly; Premium $100 annual or $125 monthly [5] | $20/seat/month annual plus usage at API rates [5] | Per token [8] | Cloud's per-token pricing |
| Usage model | Seat allowance on rolling 5-hour and weekly windows; optional usage credits [9][11] | New and self-serve: no per-seat limit, usage billed at API rates. Older contracts may be seat-based [11] | Per token, workspace spend limits [9] | Per token, cloud budgets [9] |
| Claude Code | Included in every seat [11] | Included in the Enterprise seat (new plans) [11] | Yes, via API key or Console login | Yes, via cloud credentials |
| SSO | Yes [5] | Yes | Yes (Console) [18] | Cloud IAM |
| Provisioning | JIT only [18] | JIT or SCIM [18] | JIT or SCIM [18] | Cloud IAM |
| Domain capture | Verify and block new personal accounts only [18] | Yes, claim and migrate existing accounts [18] | n/a | n/a |
| RBAC, audit logs, Compliance API | No [5] | Yes [5][12] | Activity Feed only via Admin API key [12] | Cloud audit logs |
| Custom retention | No [5] | Yes, 30-day minimum [24] | n/a | Cloud policy |
| Spend limits | Usage-credit limits at org, group, user [9] | Org, group, user, pooled group budgets (beta) [10] | Workspace limits [9] | Cloud budgets |
| Inference hooks (inline DLP) | No [5] | Yes, beta [13] | No [13] | No [13] |
| Server-managed Claude Code settings | Yes [3] | Yes [3] | Not from the claude.ai console: a Console key belongs to a separate organisation; use MDM or files [3] | Only through a Claude apps gateway [3] |
| Training on data | No by default [5][6] | No by default | No by default | No (Anthropic) |

Details that change decisions:

- **Team plan pooled API credits** (up to $500/month) cannot be spent in Claude Code
  or the Claude apps [5][11]. They are for the API only.
- **Team seats (checked 2026-10-22).** There are two seat types. Standard gives 1.25x
  Pro's per-session usage and Premium 6.25x, so no Team seat matches Max 20x. Past
  the seat allowance, Owners prepay usage credits billed at API rates. Members can
  spend them in Claude Code, and spend limits can be set for the org and for each
  member. Moving a member from Standard to Premium reassigns a seat rather than buying
  a new one [183][184][185] [verified].
- **Personal Pro or Max at work.** The EEA consumer terms (effective 2025-10-08) say in
  section 11: "You agree that you will not use our Services for any commercial or
  business purposes". The US text has no such clause. Under the commercial terms
  (Team, Enterprise, API) Anthropic may not train on customer content [186][187]
  [verified 2026-10-22]. A personal account can't reach the org's plugins, skills or
  shared projects [secondary, one research pass].
- **Enterprise search.** Team and Enterprise come with a pre-configured "Ask [org]"
  project, starred for everyone. It searches the org's connectors, and each user signs
  in to them with their own credentials. Anthropic manages its system prompt, and
  each user's chats in it stay private unless shared [188] [verified 2026-10-22].
- **Remote connectors vs desktop extensions.** These reach private servers
  differently [verified 2026-10-22]:
  - **Custom connector:** a remote MCP server that an Owner adds. Claude calls it from
    Anthropic's cloud, so a server behind a VPN won't connect unless Anthropic's IP
    ranges are allowlisted [28].
  - **MCP tunnels:** the way around that on claude.ai, but Enterprise-only, by
    request, in research preview [194].
  - **Desktop extension (`.mcpb`, formerly `.dxt`):** runs on the user's machine over
    stdio, so it can reach whatever that machine can reach, VPN included. It works
    in desktop chat and Claude Code, not in claude.ai on the web. Users install
    extensions under Settings > Extensions.
  - **Desktop extension allowlist (Team and Enterprise):** off by default, and while
    it's off users can install anything in the registry. Turning it on removes
    installs that aren't listed. Admins can upload their own `.mcpb` [27][195].
- **Several Claude Code features need a claude.ai account.** Cloud sessions,
  Routines, Code Review, Remote Control and the Chrome extension do not work with
  Console API keys or cloud credentials alone [14]. A Bedrock-only route loses them.
- **The Console org and the claude.ai org are different organisations.** Managed
  settings set in the claude.ai admin console do not reach a session that uses a
  Console API key, for example a CI job. Use a `claude setup-token` OAuth token
  authorised for the Team or Enterprise org, or a managed settings file on the
  runner [3]. This is the first trap for the factory's CI agents.
- **Anthropic's own budgeting figure for Claude Code on API pricing.** About $13
  per developer per active day and $150 to $250 per developer per month, under $30
  per active day for 90% of users [9]. Agent teams use about 7x the tokens of a
  normal session in plan mode [9]. Use these for a first estimate, then measure a
  pilot.
- **List API prices, Oct 2026, per million tokens input/output.** Opus 5.5 $4/$20,
  Sonnet 5.5 $2/$10, Haiku 5.5 $0.10/$0.50 (up to 100k-token prompts), Fable 5.1
  $10/$50 [8]. Fable models are "Covered Models" that require data retention and
  may be unavailable to zero-data-retention organisations [23].

#### Data handling

| Topic | Fact | Label |
|---|---|---|
| Training | Commercial users (Team, Enterprise, API, cloud platforms): Anthropic does not train on Claude Code code or prompts unless the customer opts in, for example the Development Partner Program [6] | [verified] |
| Retention, Claude Code | Commercial standard 30 days; ZDR available to qualified Enterprise accounts, enabled per organisation by the account team, not self-serve [6][23] | [verified] |
| Retention, claude.ai chats | Kept until deleted unless the Enterprise org sets a custom period (minimum 30 days) [24] | [verified] |
| ZDR scope | Covers Claude Code inference only. Not claude.ai chat, Cowork, analytics metadata, or third-party MCP servers. Cloud sessions, Remote Control, feedback and Artifacts are disabled under ZDR [23] | [verified] |
| Policy-violation retention | Even with ZDR, flagged inputs and outputs may be kept up to 2 years [23] | [verified] |
| Local transcripts | Claude Code stores session transcripts in plaintext under `~/.claude/projects/` for 30 days by default (`cleanupPeriodDays`) [6] | [verified] |
| Telemetry to Anthropic | Usage metrics on by default for direct API sessions, never include code, prompts or file paths; off by default on Bedrock, Google Cloud, Foundry; disable with `DISABLE_TELEMETRY` or `CLAUDE_CODE_DISABLE_NONESSENTIAL_TRAFFIC` [6] | [verified] |
| `/feedback` | Sends the conversation, including code, to Anthropic; kept 5 years; disable with `DISABLE_FEEDBACK_COMMAND=1` [6] | [verified] |
| Data residency | `inference_geo` is `global` (default) or `us` (1.1x price). Workspace geo is `us` only. No EU option on the first-party API [7] | [verified] |
| EU inference for Claude | Bedrock geographic (`eu.`) inference profiles keep processing inside the EU; CloudTrail records the region used [123]. Listed destinations include Frankfurt, Stockholm, Milan, Spain, Ireland and Paris; profiles are static and new regions mean new IDs [26] | [verified] mechanism; region list [secondary, search summary of AWS docs] |
| DPA | Anthropic's DPA with SCCs is incorporated into the Commercial Terms; Anthropic is processor for Team and Enterprise data [25] | [verified] |

#### Audit, analytics and inline control

- **Audit log export** (Enterprise): Owners export the past 180 days as CSV from
  Organization settings > Data and Privacy; metadata only, no chat content [31].
- **Compliance API** (Enterprise, and Activity Feed for Console): Activity Feed,
  users, roles, groups, effective settings, claude.ai chats, files and projects,
  and transcripts of Claude Code and Cowork sessions on users' machines when they
  are signed in to the Enterprise account. 600 requests per minute per parent org
  [12]. So whoever holds a compliance key can read every engineer's local Claude
  Code transcripts. Deciding who holds that key is a privacy decision.
- **Inference hooks** (Enterprise, beta): Anthropic POSTs each governed prompt (and
  optionally each tool call) to your HTTPS "AI security server", which answers allow
  or deny within a timeout (default 5 seconds). Covers claude.ai, Cowork, Claude
  Code and Claude Tag. Allow or deny only, no redaction. Shadow mode and percentage
  rollout exist. Not on Bedrock or Google Cloud [13]. Typical use is DLP.
- **Analytics.** The Team and Enterprise dashboard shows daily active users,
  sessions, accept rate and a leaderboard. Contribution metrics (PRs and lines
  shipped with Claude Code) need the GitHub app and cover github.com only, so a
  GitLab shop gets usage metrics only [22]. The Enterprise Analytics API returns
  per-user usage and cost; Teams gets a spend report CSV [9][22].
- **OpenTelemetry.** Claude Code exports metrics (sessions, tokens, cost, lines,
  commits, PRs, active time) and events (prompts redacted, tool details off,
  `plugin_loaded`, `mcp_server_connection`, `auth`) to your own collector. It always
  attaches `user.email` when available. Prompt text needs `OTEL_LOG_USER_PROMPTS=1`;
  raw API bodies need `OTEL_LOG_RAW_API_BODIES` [16]. Managed settings lock the
  exporter endpoint and credentials so a developer cannot redirect them [16]. They
  don't lock the exporter selectors unless managed settings also set them. Otherwise a
  developer's shell or user settings can switch a signal off or to the console, and
  repo settings can switch one off. A policy that relies on telemetry for audit should
  set `OTEL_METRICS_EXPORTER` and `OTEL_LOGS_EXPORTER` in managed settings [16]
  [verified 2026-10-23; the managed-settings fix checked 2026-10-24].
- **What OpenTelemetry content flags reveal (checked 2026-10-23)** [16] [verified]:
  - **Defaults:** prompts and responses are recorded as lengths only. Commands, file
    paths and tool arguments need `OTEL_LOG_TOOL_DETAILS=1`.
  - **Response text:** `OTEL_LOG_ASSISTANT_RESPONSES` falls back to
    `OTEL_LOG_USER_PROMPTS` when unset, so turning on prompts also turns on responses.
  - **Tracing:** `CLAUDE_CODE_ENHANCED_TELEMETRY_BETA=1` (with
    `CLAUDE_CODE_ENABLE_TELEMETRY=1` and `OTEL_TRACES_EXPORTER`) adds spans: one
    per prompt, model call and tool call. They carry timings, tokens and tool names.
  - **File contents and command output** reach spans with `OTEL_LOG_TOOL_CONTENT=1`,
    and under detailed beta tracing through `new_context`.
  - **Raw API bodies** (`OTEL_LOG_RAW_API_BODIES`) carry the whole conversation.
  - **Where it goes:** all of it goes only to the configured endpoint, never to
    Anthropic.
  - **Identity:** with a claude.ai login, events carry `user.email`, so the data is
    per person by default.
- **Where to check what is set on a machine.**
  - **Where the values live:** server-managed settings are cached in
    `~/.claude/remote-settings.json`. On Windows, endpoint-managed settings are in
    `C:\Program Files\ClaudeCode\managed-settings.json` or the registry value
    `HKLM\SOFTWARE\Policies\ClaudeCode\Settings`. `/status` names the active
    managed source.
  - **The approval dialog:** a delivered `OTEL_EXPORTER_OTLP_ENDPOINT` always shows
    it, and so does any non-falsy `OTEL_LOG_RAW_API_BODIES`. The docs don't say
    whether the other content flags trigger it, so the dialog isn't a complete
    list [3][208] [verified 2026-10-23].
- **Org data export (Team and Enterprise).** Only Primary Owners can export the
  organisation's data, which includes members' conversation data. Owners can let
  members export their own chats [209] [verified 2026-10-23]. Claude Code
  transcripts sit on the user's machine, in plaintext under `~/.claude/projects/`
  for 30 days by default [6].

#### Identity and account separation on Claude

- Each parent organisation connects to one IdP. Team plans get JIT; SCIM is
  Enterprise and Console only. Invite-only, JIT and SCIM are alternative values of a
  single "User provisioning" setting, so an org runs one of them [18] [verified
  2026-10-15].
- Domain capture (claiming existing personal accounts on the company domain) is
  Enterprise only and cannot be reversed. Prerequisites: DNS verification, SSO
  enforced, JIT or SCIM on [18].
- `forceLoginMethod` and `forceLoginOrgUUID` in managed settings restrict Claude
  Code logins to one method and one Anthropic organisation. When set, sessions
  authenticated with `ANTHROPIC_API_KEY`, `ANTHROPIC_AUTH_TOKEN` or `apiKeyHelper`
  are blocked at startup [14]. Since v2.1.285, `allowedProviders` restricts which
  API providers a machine may use [14].
- Joining an org with an existing personal account offers two choices. "Bring your
  data" moves chats, projects, files and memory, then closes the personal account and
  cancels a Pro or Max subscription bought directly, with a prorated refund. "Keep
  both" moves nothing, and the user switches accounts from the account menu [189]
  [verified 2026-10-22].
- Tenant restrictions (Enterprise and Console): a TLS-inspecting proxy overwrites the
  `anthropic-allowed-org-ids` header on every request; other orgs get a 403 [19].
  A company of 50 to 100 people may not run a TLS-inspecting proxy [inferred].

### 2.3 Claude Code at organisation scale: what you can enforce

Managed settings come from four sources, highest first: server-managed (claude.ai
admin console, or a Claude apps gateway), MDM or OS policy (macOS
`com.anthropic.claudecode` profile, Windows `HKLM\SOFTWARE\Policies\ClaudeCode`),
system files (`managed-settings.json` plus `managed-settings.d/*.json` in
`/Library/Application Support/ClaudeCode/`, `/etc/claude-code/`, or
`C:\Program Files\ClaudeCode\`), and the user-writable HKCU registry [2]. By default
the first source that delivers a policy key wins and the rest are ignored
(`managedSourcesBehavior: "first-wins"`); `"merge"` composes them [2].

| Delivery | Refresh | Strength | Fits the company if |
|---|---|---|---|
| Server-managed (claude.ai console) | Startup plus hourly poll; hooks and some env vars need user approval in a dialog [3] | Weakest on unmanaged laptops; cached file can be edited until next fetch [3] | No MDM, Team or Enterprise plan, one policy for all |
| MDM profile or HKLM registry | Every 30 minutes [2] | Needs admin rights to change [14] | Laptops are already in Jamf, Intune or similar |
| System file | On file change [2] | Admin rights to change; local admins can still edit it [2] | Linux machines, CI runners, container images |
| Claude apps gateway | Fetched at sign-in, per IdP group [4] | Server-side model enforcement plus client policy | Per-group policy or Bedrock/Google Cloud routing needed |

The controls that matter most, with their keys [14][15]:

| Control | Keys | Example policy |
|---|---|---|
| Permission rules and lockdown | `permissions.allow/ask/deny`, `allowManagedPermissionRulesOnly`, `permissions.disableBypassPermissionsMode` | Deny `Read(./.env)`, `Read(./secrets/**)`; disable `--dangerously-skip-permissions` |
| Sandbox | `sandbox.enabled`, `sandbox.network.allowedDomains`, `sandbox.network.allowManagedDomainsOnly` | Bash runs in an OS sandbox that can reach only GitLab, PyPI and the docs domains |
| MCP servers | `allowedMcpServers`, `deniedMcpServers`, `allowManagedMcpServersOnly`, `managedMcpServers`, `managed-mcp.json` | Only catalogue servers load; the company GitLab MCP server is provided to everyone |
| Plugins and marketplaces | `strictKnownMarketplaces`, `blockedMarketplaces`, `extraKnownMarketplaces`, `enabledPlugins`, `disableSideloadFlags`, `strictPluginOnlyCustomization` | Only the official and company marketplaces; the company's review plugin force-enabled |
| Hooks | `allowManagedHooksOnly`, `allowedHttpHookUrls` | Only company hooks run, for example a secret scanner on `PreToolUse` |
| Org instructions | Managed `CLAUDE.md` at the policy path, or the `claudeMd` key | "Customer site data is yellow; never paste control-system credentials" |
| Models and effort | `availableModels`, `enforceAvailableModels`, `deniedModels`, `maxEffortLevel` | Data science may use Opus; everyone else Sonnet by default |
| Login | `forceLoginMethod`, `forceLoginOrgUUID`, `allowedProviders` | Only the company's claude.ai org may sign in |
| Version | `requiredMinimumVersion`, `requiredMaximumVersion`, `minimumVersion` | Refuse to start below the version that fixed a known issue |
| Telemetry | `env` with `CLAUDE_CODE_ENABLE_TELEMETRY`, `OTEL_*`, `DISABLE_TELEMETRY` | Metrics to the company's collector; Anthropic-bound telemetry off |
| Cost display | `modelPricing` | `/usage` shows contracted rates, not list price |

On Enterprise, the claude.ai admin console also restricts models, sets a default
model and caps effort per role server-side, without deploying anything [14].

Limits to state plainly in any policy:

- Managed settings bind Claude Code only. Another tool calling the same API is not
  covered [2].
- A local administrator can edit the managed file; MDM must redeploy it [2].
- Server-managed settings are skipped when a user exports `CLAUDE_CODE_USE_*` or a
  custom `ANTHROPIC_BASE_URL` in their shell [3]. Set `forceRemoteSettingsRefresh`
  to make startup fail closed if the policy cannot be fetched [3].
- Permission rules match command text. `Bash(curl *)` does not catch `sh -c 'curl'`;
  network rules belong in the sandbox [3][14].
- Cowork sessions in Claude Desktop never fetch server-managed settings; they read
  the device policy when running locally [2].

### 2.4 Other vendors

#### OpenAI: ChatGPT Business, Enterprise, Codex and the API

| | ChatGPT Business | ChatGPT Enterprise | API platform |
|---|---|---|---|
| Price (Oct 2026) | Standard $20/user/month annual, $25 monthly; Premium $100 annual, $125 monthly, 5x Standard usage [33]. The pricing page showed €21 and €26 from an EU location [32] | Custom; credit-based or token-based contracts [32][39]. Third-party reports of $45 to $75 per seat and a 150-seat minimum are unconfirmed [secondary] | Per token |
| Seats | Minimum 2; maximum 200 for workspaces created from 24 Aug 2026 [37] | By contract | n/a |
| Training | No by default [34] | No by default [34] | No by default since 1 Mar 2023 [53] |
| Retention | Chats kept until deleted; deleted chats removed within 30 days [34]. Admin-set retention: the enterprise-privacy page's Business FAQ says admins can control it, but the same page's header limits that control to Enterprise, Healthcare and Edu; the help centre says Business chats, files and canvases are kept indefinitely, and both pricing pages list custom retention as Enterprise only [34][55] [verified 2026-10-15]. Assume none on Business until OpenAI confirms in writing | Custom retention, minimum 90 days [41]; default until deleted | Abuse-monitoring logs up to 30 days; ZDR or Modified Abuse Monitoring by approval [53] |
| SSO / SCIM | SSO (SAML or OIDC) and domain verification; no SCIM unless the tenant has an Enterprise workspace [38][42] | SSO and SCIM, per workspace or tenant-wide [42] | SSO in Admin Console; SCIM needs a custom billing plan [42] |
| Roles | Owner, Admin, Analytics Viewer, Member; members can invite members and this cannot be restricted [37] | Custom roles (RBAC) [45] | Org and project RBAC, custom roles [54] |
| Audit and compliance | No Compliance API [32][40]. Admin access to chats: the enterprise-privacy page says Business admins "can view, access, export, and delete end user conversations"; the help centre says other members do not see a member's chats and owners can export per member. Read it as no in-app reading but an export path [34] [verified 2026-10-15] | Compliance Logs Platform: append-only JSONL, kept 30 days, so you must download continuously; covers Codex (`codex_log`), which OpenAI says is not a complete record of local commands [40] | Audit logs API, usage and cost APIs [54] |
| EU residency | Storage-only residency rolling out, not yet available to all customers, chosen at checkout; abuse logs and a temporary copy of every prompt and response stay in the US [36]. The pricing table's "No" refers to the full Enterprise residency programme [32] [verified 2026-10-15] | Storage in Europe for new Enterprise customers; EU inference residency for eligible Enterprise [35]. Codex Web is excluded; Codex app and CLI are supported [35] | Per project at creation, `eu.api.openai.com`; needs MAM or ZDR approval plus a signed Modified Retention amendment; +10% for models released from 5 Mar 2026 [53] [verified] |
| Connectors | Apps on by default; admins toggle per app and per read or write action; only admins create custom MCP apps [43][44] | New apps off by default, enabled per role [43] | n/a |
| DPA | Via OpenAI's online form [34] | Same [34] | Same [34] |

**Codex controls** (the CLI, IDE extension, app and cloud):

- **Sign-in decides which rules apply.** With ChatGPT sign-in, Codex follows the
  workspace's RBAC, retention and residency; with an API key, it follows the API
  organisation's settings. Codex cloud needs ChatGPT sign-in [48].
- **Enforced configuration.** `requirements.toml` (users cannot override) at
  `/etc/codex/requirements.toml` or `%ProgramData%\OpenAI\Codex\requirements.toml`,
  plus overridable `config.toml` defaults. macOS MDM uses domain `com.openai.codex`
  with base64-encoded TOML in `requirements_toml_base64` (`config_toml_base64` for
  defaults). Business and Enterprise can also push policy from the Admin Console
  ("Agent Security", which replaced "Policies & Configuration"); if that fetch fails
  with no cache, loading returns an error rather than starting without the cloud
  layer [47] [verified 2026-10-15].
- **What it can enforce.** Allowed approval policies (`allowed_approval_policies`)
  and sandbox modes (`allowed_sandbox_modes`, which OpenAI now calls legacy; for Codex
  0.138.0 or later it recommends `allowed_permission_profiles` with
  `default_permissions`), web search mode (`allowed_web_search_modes`), an MCP server
  allowlist (empty disables all MCP), command prefix rules (`prompt` or `forbidden`),
  `deny_read` paths, managed hooks only, plugin marketplace sources, and allowed
  login methods and ChatGPT workspaces (`allowed_login_methods`,
  `allowed_chatgpt_workspaces`). Those login keys are honoured only from local files
  or MDM, not from cloud policy [47] [verified 2026-10-15].
- **Defaults.** Sandbox network off; `--yolo` removes sandbox and approvals; `.git`
  and `.codex` stay read-only inside writable folders [52].
- **Plan matrix.** `requirements.toml` works on Plus, Pro, Business, Enterprise and
  API key (Free and Go are not listed); cloud policies need Business or Enterprise;
  RBAC, retention and residency controls, analytics and the Compliance API are
  Enterprise only [49] [verified 2026-10-15].
- **GitLab.** Merge request review is beta on all ChatGPT plans and runs in Codex
  cloud; self-managed GitLab needs 19.0+ [50]. Another Codex page lists GitLab as not
  yet supported in current cloud environments [51], so treat it as unsettled.
- **AGENTS.md.** Read globally, at the repo root and in nested folders, 32 KiB
  combined by default [56].

#### GitLab Duo

| Topic | What GitLab offers (Oct 2026) |
|---|---|
| Packaging | Premium $29 per user per month annually; Ultimate custom. Duo Core (Code Suggestions, Agentic Chat) comes with both. Duo Agent Platform (agents, flows, external agents, AI Catalog, MCP) is GA since 18.8 and billed in GitLab Credits. Seat add-ons Duo Pro and Duo Enterprise are off the pricing page, but the add-ons doc still says both can be bought (Pro through the Customers Portal or Sales, Enterprise through Sales) [57][58][59][60][94] [verified 2026-10-15] |
| GitLab Credits | $1 per credit on demand. Included $12 (Premium) or $24 (Ultimate) per user per month "for a limited time", GitLab.com plans only, monthly, no rollover. Included credits belong to each user and cannot be shared; only a purchased monthly commitment is pooled. Spending order: the user's included credits, then the commitment pool, then on-demand. Per-user caps, an on-demand cap per subscription, alerts at 50, 80 and 100% (the per-user cap UI is flagged off in 19.4 while release notes call it shipped). Rates: Code Suggestions 50 executions per credit, Code Review Flow 4, Claude Opus 4.6 calls 1.1 [57][59][93][94] [verified 2026-10-15] |
| Switches | Duo is on by default; availability set at instance, group and project with an "Always on" lock. The Agent Platform is one checkbox. Foundational agents have a default-availability setting. External agents are allowed by default [61][62] |
| Tool governance (beta) | Always Allow, Always Ask or Always Deny per tool, separately for cloud, local and background runs (background allows only Allow or Deny). Project rules can only be stricter; rule errors fail closed; Owners can block whole MCP servers [63] [verified] |
| Models | Top-level group Owners set the model per feature; an allowlist exists for Agentic Chat [64]. Default since 5 to 7 October 2026: Claude Sonnet 5.5 served through Google's Gemini Enterprise Agent Platform [64] [verified] |
| External agents | Claude Code, Codex, Amazon Q and Gemini run inside CI pipelines under a service account with composite identity, so the effective permission is the lower of the user's and the service account's. GitLab-managed credentials only for Claude and Codex. Behind a flag for verified customers; GitLab's prompt scanning does not cover them [65][66]. The most direct way to put Claude Code or Codex into the company's pipeline under GitLab governance [inferred] |
| MCP | GitLab MCP server GA in 19.5 on all tiers, OAuth `mcp` scope or fine-grained token. Rate limits of 60 (Free) and 600 (Premium, Ultimate) requests per minute per user: the 19.5 release post says enforcement starts 15 Oct 2026, the docs say the limits sit behind flags that are off by default. Duo as MCP client needs an Owner to allow external MCP tools; Owners can block servers [67][68] [verified 2026-10-15] |
| Audit | AI Catalog pins agent and flow versions per project. AI audit event report is beta with session storage off by default. "Externally Connected Agents" is limited availability (beta), Premium and Ultimate. Its feature flag and "Allow external agents" are on by default in 19.5, but nothing is captured until each user runs `glab govern setup` on each machine (macOS or Linux only). That installs Claude Code hooks; Cursor and Codex are read by a background job every 30 minutes only with `--agents codex,cursor`. It sends prompts (cut at 8 KB), tool-call arguments such as commands and file paths, outcomes, model and token use; not tool output or response text; events are kept only if AI audit event storage is on. A central record of local agent use, and an employee-monitoring question [69][70][71] [verified 2026-10-15] |
| Data | GitLab does not train; sub-processors (Anthropic, Fireworks, Google, OpenAI) are restricted from training. No retention durations stated. Secret scanning before AI except in web UI chat. IP indemnity for unmodified output [74][75] |
| Residency | The GitLab-hosted AI gateway runs in regions including europe-west2/3/9 but is "not a data residency solution", and GitLab.com requests go to us-east4 "in almost all cases" [73] [verified 2026-10-15]. Dedicated can run the AI gateway in its tenant with Bedrock or own models [76]; Duo Self-Hosted keeps inference in the customer network [72] |
| Analytics | "Duo and SDLC trends" on Premium and Ultimate [77] |

#### GitHub Copilot (only if the company is on GitHub)

- **Price.** Business $19 per seat per month with 1,900 AI credits, Enterprise $39
  with 3,900; one credit is $0.01; AI credits replaced premium requests on 1 Jun
  2026 [78][79]. Budgets per user (hard stop), cost centre, organisation and
  enterprise [80].
- **Policies** at enterprise or organisation level [81]. New GA models inherit the
  "Default availability" setting until configured; Claude Fable models stay off
  whatever that setting says; from 22 Oct 2026 unconfigured GA features turn on unless
  admins act [82] [verified 2026-10-15].
- **MCP.** The GA control is an allowlist in a `managed-settings.json` (in
  `.github-private` or via MDM); registry-based restriction is preview [83][84].
- **Agents.** The cloud agent pushes only to `copilot/` branches and opens draft
  PRs; third-party Claude and Codex agents are public preview [85]. Content
  exclusion does not apply in VS Code agent modes [86].
- **Data.** No training on Business or Enterprise data; IDE prompts not kept, other
  surfaces 28 days [91]. Audit log 180 days without prompts [87]; usage metrics API
  [88]; OpenTelemetry prompt capture configurable since 12 Oct 2026, off by default
  [92]. EU data residency on GHE.com at 10% more credits [89]. The cloud agent and
  CLI read `AGENTS.md`, `CLAUDE.md` and `GEMINI.md` [90].

#### Google: Gemini in Workspace, Gemini CLI, Antigravity

Relevant if the company runs on Google Workspace.

- **Gemini is included in Workspace Business plans.** EU list prices: Starter
  €6.80, Standard €13.60, Plus €21.10 per user per month excluding tax; Starter gets
  only the Gemini app and Gmail [145].
- **Data.** No training on customer data without permission; prompts fall under
  the Cloud Data Processing Addendum; Gemini app chats are not human-reviewed.
  Gemini app history is kept 3, 18 (default) or 36 months [146][147].
- **Admin.** The Gemini app is switched per organisational unit or group; temporary
  chats and deletion can be turned off; Vault retention overrides users
  [147][148][152]. Audit events are metadata only, also via the Reporting API [151].
  Google's pages disagree on whether Business editions get Gemini data regions at
  rest; processing in region needs Enterprise Plus or Frontline Plus [149][150].
- **Coding tools changed in 2026.** Gemini Code Assist Standard and Enterprise
  stopped selling on 9 Oct 2026; existing subscriptions run to the end of their
  contract and licences can be added until 31 Jan 2027 [153] [verified 2026-10-15]; Gemini CLI is being folded into Antigravity CLI [154]. Antigravity
  comes with Gemini Enterprise Standard and Plus ($10 and $15 included credit per
  user per month), with sandbox, browser and MCP policies and a prompt-capturing
  audit toggle [157][159], but without SOC 2 or ISO 27001 coverage yet [158]. Claude
  models in Gemini Enterprise can run in an `eu` multi-region, off by default [160].
- **Gemini CLI enterprise controls.** A system settings file
  (`/etc/gemini-cli/settings.json` and Windows and macOS equivalents) overrides user
  and project settings, with `mcp.allowed`, `admin.secureModeEnabled` (blocks YOLO),
  `security.auth.enforcedType` and `telemetry.logPrompts`. Google calls it not a
  foolproof security boundary, and its reference ranks environment variables and
  flags above the file [155][156].

#### Microsoft Copilot (brief)

Only relevant if the company runs on Microsoft 365.

- **Renamed.** Microsoft 365 Copilot is now "Microsoft Copilot" [161].
- **Licensing.** It is an add-on to an eligible Microsoft 365 plan, and a
  "Copilot Business" SKU exists for Business plans [162]. Copilot Chat is free with
  an Entra work account plus a qualifying subscription, grounded in the web only
  [163]. US list prices read on 2026-10-15: Microsoft 365 Copilot $30 per user per
  month paid yearly ($31.50 paid monthly on an annual commitment); the Copilot
  Business add-on $18 per user per month yearly (reduced from $21) [182]
  [verified].
- **Data protection.** Covered by Microsoft's DPA with Microsoft as processor, and no
  training on prompts or Graph data [161].
- **EU Data Boundary.** EU traffic stays in the boundary, but Anthropic models are
  currently excluded from it [164].
- **Compliance tooling.** Purview keeps prompts and responses for eDiscovery under
  their own retention location, and records CopilotInteraction audit events
  automatically [165][166]. An Agent Registry and admin approval govern agents [167].
- **For a company without Microsoft 365, it is not a candidate** [inferred].

#### Cursor

- **Ownership and models.** SpaceX's acquisition of Cursor (Anysphere) was announced
  in June 2026; Cursor's post of 14 Aug 2026 says it is complete [168]. Cursor's own
  models are now Grok models (Grok 4.5 to 4.7, plus Composer 2.5) trained jointly with
  SpaceXAI; that is stated on Cursor's pricing and model pages, not in the acquisition
  post [169] [verified 2026-10-15]. The announcement says
  nothing about customer data and SpaceX or xAI. That is a vendor-risk question to
  settle before adoption [inferred].
- **Price.** Teams from $30 per user per month (Premium seat; Super $100, Ultra
  $200); teams created earlier keep $40 and $120 seats. Third-party models are billed
  at API list price plus a $0.25 per million token "Cursor Token Rate" from a shared
  prepaid balance with one team limit; the rate also applies to bring-your-own-key
  use, and Grok and Composer models are exempt. Enterprise is custom [169][180]
  [verified 2026-10-15].
- **Privacy mode.** No training and zero retention with model providers. It is on
  by default for Enterprise and enforceable on Teams and Enterprise. Some models
  (Claude Fable) require provider retention and need admin approval [170][171][174].
- **Residency.** US-only residency is available, and EU and UK inference-only
  coverage on request [171].
- **Identity and audit.** SSO on Teams and Enterprise; SCIM on Enterprise [176].
  Audit logs (no prompts), OpenTelemetry export and the AI Code Tracking API are
  Enterprise only [181].
- **Policy.** Model, MCP and repository allowlists are Enterprise only [175]. Team
  Rules can be required on Teams and Enterprise [177]. Hooks can deny actions but
  fail open unless `failClosed: true`; system-wide hooks live in
  `/etc/cursor/hooks.json` and equivalents [178]. `.cursorignore` does not stop
  terminal or MCP access [179].
- **Compliance.** SOC 2 Type II, ISO 27001 and ISO 42001 [172]. The DPA includes
  SCCs [173].

#### Comparison across vendors (as read on 2026-10-14)

| Plan | Training on business data | Retention default | SSO / SCIM | Audit and content export | Central agent or tool policy | EU residency | DPA | Price model |
|---|---|---|---|---|---|---|---|---|
| Claude Team | No [5] | Claude Code 30 days; chats until deleted [6][24] | SSO, JIT only [18] | No audit logs [5] | Server-managed settings, connector admin, desktop extension allowlist [3][27] | No EU geo on first-party API [7] | In Commercial Terms [25] | $20 or $100 per seat, annual [5] |
| Claude Enterprise | No | 30 days; ZDR for Claude Code by approval; chat retention 30 days minimum if set [23][24] | SSO and SCIM, domain capture [18] | Audit export, Compliance API including local Claude Code transcripts [12][31] | Above plus inference hooks, server-side model and effort limits, RBAC [13][14] | Same; EU via Bedrock or Google Cloud routes [7][26] | Same | $20 per seat plus usage at API rates [5] |
| ChatGPT Business | No [34] | Until deleted; OpenAI's pages conflict on admin-set retention, assume none [34][55] | SSO, no SCIM [38][42] | No Compliance API [32] | Codex cloud policy, app toggles [43][47] | Storage only, rolling out, pages conflict [32][36] | Form [34] | $20 or $100 per seat, annual [33] |
| ChatGPT Enterprise | No | Custom, 90 days minimum [41] | SSO and SCIM [42] | Compliance Logs Platform, 30-day window [40] | RBAC, Agent Security, apps per role [43][45][47] | EU storage and inference for eligible customers [35] | Form | Custom [32] |
| Gemini in Workspace | No without permission [146] | Gemini app 18 months default [147] | Workspace identity | Metadata log events, Vault [151][152] | Per OU or group [147] | Data regions at rest, edition-dependent, pages conflict [149][150] | Cloud DPA [146] | Included in plan [145] |
| GitLab Duo | GitLab does not train [74] | Not stated [74] | GitLab identity (not researched) | AI audit events (beta), connected-agent reporting [70][71] | Group and project toggles, tool governance, model selection [61][63][64] | Not via GitLab.com AI gateway; Dedicated or self-hosted [73][76] | Not checked | Premium $29 plus credits [57][59] |
| GitHub Copilot Business / Enterprise | No [91] | IDE prompts not kept; 28 days elsewhere [91] | GitHub identity | Audit log 180 days, no prompts [87] | Org and enterprise policies, MCP allowlist [81][83] | EU on GHE.com, +10% credits [89] | Not checked | $19 or $39 plus credits [78] |
| Cursor Teams / Enterprise | No with privacy mode [170] | Zero retention with providers, with exceptions [170][171] | SSO; SCIM Enterprise [176] | Enterprise only [181] | Allowlists Enterprise; rules and hooks Teams [175][177][178] | US-only; EU inference on request [171] | Yes, with SCCs [173] | $30+ per seat plus usage [169] |
| Microsoft Copilot | No [161] | Purview policies [165] | Entra | Purview audit [166] | Agent registry and approval [167] | EU Data Boundary, Anthropic models excluded [164] | Microsoft DPA [161] | Add-on, $30 (enterprise) or $18 (Business) per user per month, annual [182] |

My reading of the table [inferred]. Training is no longer what separates vendors.
The differences that matter are (1) whether audit logs and content export exist on
the tier you can afford, (2) whether a central policy reaches the coding agent on the
laptop, and (3) whether EU processing is available for the data that needs it.

### 2.5 Reference architecture

A minimal picture of the layers, independent of vendor:

```
            IdP (Google Workspace or Entra ID): SSO, groups, MFA, offboarding
                |               |                    |
   claude.ai / ChatGPT     Coding agents on        Factory agents in CI,
   (chat, connectors)      laptops (Claude Code,   internal tools, scripts
                           Codex, Duo, Cursor)          |
        |                       |                  AI gateway (virtual keys,
        |                 managed settings          budgets, model allowlist,
        |                 (server/MDM/file)          logs metadata)
        |                       |                        |
        +-----------+-----------+------------------------+
                    |                                    |
          Vendor SaaS (Anthropic,             Model APIs (Anthropic API, Bedrock eu.*,
          OpenAI, GitLab) with admin          Google Cloud EU) and MCP servers
          console, audit log, analytics       from the approved catalogue
                    |                                    |
          Logs and metrics: vendor audit/analytics, OTel collector, gateway DB
          -> one place the company owns, metadata by default, prompts only by exception
```

#### AI gateways: what they do, concretely

An AI gateway is an HTTP proxy that speaks the providers' APIs. Example: a merge
request review agent in GitLab CI sends `POST /v1/messages` to
`https://ai-gw.internal/` with a virtual key `factory-review`. The gateway checks
that the key may use Sonnet, that this month's spend is under $200 and the rate is
under 30 requests per minute, swaps in the real Anthropic key it holds, forwards the
request, records user, key, model, tokens and cost (not the prompt), and returns the
response. Revoking the agent means deleting one virtual key; the real key never left
the gateway.

| Product | Hosting | Keys and access | Budgets and limits | Logging control | PII and guardrails | Price (where published) | Notes |
|---|---|---|---|---|---|---|---|
| LiteLLM proxy | Self-hosted, open source, needs Postgres; Redis for multi-instance limits [95][96] | Virtual keys per user, team or agent; `models` allowlist per key or team; model access groups [95][97] | `max_budget` and `budget_duration` per key, user, team; RPM, TPM and parallel limits [96] | `turn_off_message_logging`; spend logs store no content by default [98][99] | Presidio masking (separate containers); secret redaction is Enterprise [100] | Open source free; Enterprise custom-priced; admin UI SSO free up to 5 users; audit logs and key rotation are Enterprise [101][102][103] | **PyPI supply-chain compromise on 24 Mar 2026** (versions 1.82.7 and 1.82.8 stole credentials; live for about 40 minutes from 10:39 UTC; suspected entry through the Trivy dependency in LiteLLM's CI); official Docker image unaffected [104] [verified 2026-10-15]. Several CVEs in 2026, including SQL injection in API key verification (CVE-2026-42208, 1.81.16 up to 1.83.7) and a Host-header auth bypass (CVE-2026-49468, below 1.84.0) [105] [verified]. Claude Code connects via `ANTHROPIC_BASE_URL` [106] |
| Portkey | SaaS, hybrid data plane, or open-source gateway | Virtual keys, workspaces | Budgets and rate limits (tier mapping unclear) | Log retention 3 to 30 days by tier | PII anonymiser listed for enterprise | Developer free, Production $49/month, Enterprise custom [108] | Palo Alto Networks announced the deal on 30 Apr 2026 and closed it on 29 May 2026; Portkey is now sold as "Prisma AIRS AI Gateway", GA since 16 Jul 2026 [109][110] [verified 2026-10-15] |
| Cloudflare AI Gateway | SaaS on Cloudflare | Cloudflare API tokens cannot be scoped to one gateway; no per-user virtual keys [114] | Spend limits up to 20 rules, split per metadata value such as `user_id` [112] | Logging on by default; per gateway off, or per request metadata-only [113] | DLP profiles, guardrails billed as Workers AI [111] | Core features free; log export via Logpush priced per request [111] | Not compatible with Cloudflare's Regional Services for EU data localisation [115] |
| Kong AI Gateway | Konnect SaaS (EU geo exists) or self-hosted [119] | Central provider credentials; consumer groups for model access [116] | Cost-based rate limiting, token budgets [116] | Optional body logging [116] | AI Sanitizer PII service is an enterprise plugin: a paid add-on on Konnect Plus, included in Enterprise [117][118] [verified 2026-10-15] | Konnect Plus from $25/month plus per control plane and usage; self-hosted Enterprise by sales [118] | Strong if the company already runs Kong; heavy otherwise [inferred] |
| AWS (Bedrock plus guidance) | Your AWS account | IAM principals; application inference profiles for cost tags | No native per-user budget found; AWS's multi-provider gateway guidance deploys LiteLLM on ECS or EKS [120] | Invocation logging off by default; when on, logs full bodies with the caller's IAM ARN [121] | Guardrails mask text but not tool calls, and masking does not apply to invocation logs [122] | Per token | EU geographic inference profiles keep processing in the EU [123] |
| Azure API Management | Your Azure subscription | Subscriptions, managed identity | `llm-token-limit` per counter key, token metrics [124] | Prompt logging is a per-API opt-in [125] | Content safety policy [124] | APIM tier pricing | Supports the Anthropic Messages API on v2 tiers [124] |
| Claude apps gateway | Self-hosted, in the `claude` binary; Linux, Postgres, OIDC only [4] | IdP groups map to model allowlists and managed settings; short-lived tokens [4] | Spend limits per user, group or org; fails open if Postgres is down [127] | Does not store prompt or completion content; OTLP metrics to your collector [4][126] | None built in | No licence fee; you pay inference and hosting [126] | Claude only. No CI sign-in flow (no service tokens) [128]. No web search, Remote Control or 1-hour cache [4] |

Two facts change how a gateway should be used with Claude Code [129]:

- **Do not redact by rewriting request bodies.** Changing the body breaks the
  pairing of beta headers and fields and causes 400 errors and thinking-signature
  rejections. Inspect without modifying, and block instead of rewrite.
- **Locking a machine to the gateway** is done with `allowedProviders:
  ["customEndpoint"]` plus a managed `ANTHROPIC_BASE_URL` (v2.1.285+). Do not combine
  this with `forceLoginOrgUUID`; startup is blocked. Server-managed settings do not
  reach gateway-routed sessions, so policy must come from MDM or a file.

The LiteLLM incident is the lesson to take from this table [inferred]. A gateway
holds every provider key, so it is the highest-value target in the setup. Pin
versions, install from the signed container image rather than `pip install` at
runtime, keep the gateway's own credentials minimal, and subscribe to its security
advisories.

#### When a company of 50 to 100 people needs a gateway [inferred]

A gateway is worth running when at least one of these is true:

1. Several programs (CI agents, internal apps, notebooks) call model APIs and each
   needs its own key, model allowlist and budget.
2. Traffic must be routed by data tier, for example red data only to an EU route.
3. More than one model provider is used programmatically and you want one API and
   one bill view.
4. You use Bedrock or Google Cloud and need per-user attribution and spend caps,
   which the cloud console does not give per developer [9].
5. You need a request-level audit log under your own control [14].

It is not needed, and cannot help, for seat-based SaaS: claude.ai chat, ChatGPT and
GitLab Duo traffic does not pass through your proxy. Their controls are the vendor
console, SSO and the plan tier. For seat-based Claude Code, a gateway removes
features (section 1, point 7), so use server-managed or MDM settings instead.

#### MCP: gateways, registries, an approved catalogue and vetting

State of the protocol, October 2026:

- The current MCP specification is dated 2026-07-28. It made the protocol
  stateless, deprecated Dynamic Client Registration in favour of Client ID Metadata
  Documents, and deprecated Roots, Sampling and Logging [130].
- Authorization is optional. HTTP servers should follow it (OAuth 2.1 with PKCE,
  RFC 9728 metadata, RFC 8707 `resource`); stdio servers take credentials from the
  environment. Servers must not accept or forward tokens issued for other services
  [131].
- The Enterprise-Managed Authorization extension (marked stable) lets the company
  IdP issue the token that unlocks an MCP server, so access and revocation sit in
  the IdP. Client support varies [133].
- The official MCP Registry is in preview, stores metadata only and does not list
  private servers; it recommends a private registry for those [136].

Where MCP can be controlled, by layer:

| Layer | Control | Example |
|---|---|---|
| Agent client | Claude Code `allowedMcpServers` plus `allowManagedMcpServersOnly`, or exclusive `managed-mcp.json`; match on `serverUrl` or `serverCommand`, because `serverName` labels are user-chosen and not a security control [137] | Only `https://gitlab.com/api/v4/mcp` and the company's internal docs server load |
| Agent client (others) | Codex `mcp_servers` allowlist in `requirements.toml` [47]; GitHub Copilot `managed-settings.json` allowlist [83]; GitLab Duo Owner toggle and server blocklist [67] | Same catalogue rendered into each tool's format |
| SaaS assistant | claude.ai: only Owners (and custom roles on Enterprise) add custom connectors; members connect individually [28]. ChatGPT: per-app and per-action toggles [43] | Sales gets the CRM connector read-only |
| MCP gateway | One endpoint for many servers, central auth, per-user tool filtering, logs: Docker MCP Gateway [140], Microsoft MCP Gateway (MIT, Kubernetes) [141], Cloudflare MCP server portals (up to 80 servers, Access policies) [142], Kong [144], Obot [143], Portkey [110], LiteLLM [107] | Worth it when more than a handful of remote servers serve many users [inferred] |

**An approved connector catalogue** is a table in the `ai-config` repo with, per
server: name and URL or package, owner in the company, data tier it may touch, read
or write, auth method and scopes, version pin, review date and decision. The managed
allowlists in each tool are generated from it [inferred]. Anthropic suggests an
internal page or a managed plugin marketplace as the catalogue, since Claude Code
has no built-in registry [137].

**Vetting checklist for an MCP server** [inferred, built from the sources cited per
line]:

1. **Provenance.** Who publishes it, is the repository active, is the package signed
   or pinned by hash. Supply-chain attacks are OWASP MCP04 [134].
2. **What it can do.** List every tool. Are read and write separate tools, and are
   destructive tools marked? Anthropic's directory requires both [138].
3. **Tool descriptions.** Read them. Hidden instructions in descriptions are "tool
   poisoning" (OWASP MCP03) [134].
4. **Auth.** OAuth with the narrowest scopes, no token passthrough, no tokens in
   query strings [131][132]. For local servers, credentials from a secret store, not
   a committed `.mcp.json`. Claude Code does expand `${VAR}` references in `.mcp.json`,
   except that in a remote server's `url` and `headers` it reads credential-like names
   (`ANTHROPIC_API_KEY`, cloud provider tokens, `NPM_TOKEN` and similar) as empty
   [139] [verified 2026-10-15].
5. **Untrusted content in, actions out.** Does it return text from outside the company
   (web pages, emails, tickets from customers)? Then prompt injection (OWASP MCP06,
   LLM01) can steer any write tool in the same session [134][135]. Do not combine
   such a server with write access to code or customer systems in one agent.
6. **Change control.** Can the server change its tools after approval? Pin the
   version; ChatGPT freezes approved custom apps until an admin refreshes them [44],
   and GitLab's AI Catalog pins versions per project [69].
7. **Network and data.** Where does it send data, is it covered by a DPA, does it
   need outbound internet.
8. **Logging.** Can the company see which tools were called? Claude Code shows MCP
   tool names in OTel with `OTEL_LOG_TOOL_DETAILS=1` [137]; MCP08 is lack of audit
   [134].
9. **Shadow servers.** Is the same capability already in the catalogue? OWASP MCP09
   is unmanaged "shadow" servers [134].
10. **Decision.** Approve for which tier and roles, approve read-only first, or
    reject, with a review date.

The MCP specification's own security page covers confused deputy, token
passthrough, SSRF and session-related attacks but not tool poisoning or prompt
injection [132]; the OWASP lists fill that gap. "Rug pull" (a server changing its
tools after approval) is a common term but not defined in either primary source.

#### Identity and secrets

- **One IdP, groups mirror roles.** Create groups such as `ai-engineering`,
  `ai-data-science`, `ai-business`, `ai-admins`. Map them to vendor roles (SCIM where
  the plan allows), gateway teams and, if used, Claude apps gateway policies
  [inferred].
- **No long-lived provider keys on laptops.** People sign in with SSO; programs use
  gateway virtual keys or cloud workload identity. The Claude apps gateway issues
  short-lived bearer tokens (session TTL one hour by default) and keeps the upstream
  credential server-side [4].
- **Agent credentials are service identities, not people.** A CI review agent gets
  its own key or service account with read access to the repository and comment
  rights on merge requests, nothing else [inferred].
- **Secrets never enter context.** Deny reads of `.env` and secret paths in managed
  settings, keep secrets in the CI secret store or a vault, and scan prompts for
  secrets at the gateway or with a `PreToolUse` hook [inferred].

#### Logs: where they go and who reads them

| Log | Content | Where | Who should read it [inferred] |
|---|---|---|---|
| Vendor audit log | Admin and user events, no chat content [31] | Vendor; export to the company | AI policy owner, security |
| Vendor transcripts (Compliance API) | Full chats and Claude Code session transcripts [12] | Vendor, pulled on request | Named roles, for incidents or legal holds only, each access logged |
| OTel metrics and events | Tokens, cost, tools, user email; prompts redacted by default [16] | The company's collector | Platform team for cost and adoption; aggregated for managers |
| Gateway logs | Request metadata, optionally bodies | The company's database | Platform team; bodies off by default |
| Local transcripts | Plaintext under `~/.claude/projects/`, 30 days [6] | Each laptop | The employee; require disk encryption [inferred] |

EU constraints, at the level needed for architecture (law itself is in `03`):
prompts often contain personal data about the employee and third parties, so
logging them is processing that needs a purpose, a legal basis, transparency to
staff and minimisation. Article 29 Working Party Opinion 2/2017 on data processing
at work [29] is the long-standing EU reading on monitoring employees' use of IT.
Practical defaults: metadata on, content off, short retention, named readers,
staff told in the AI policy, and no individual productivity ranking from AI
telemetry without HR and legal review [inferred]. The Claude analytics leaderboard
[22] is the feature to decide on deliberately.

#### Cost control and per-team budgets

- **Seat plans** cap cost by design; usage credits beyond the seat allowance need
  explicit limits [9].
- **Usage-based Enterprise** needs group-level per-user monthly limits as the
  starting point; org-wide limits stop everyone at once and alert at 75% and 90%
  [10].
- **API workspaces** get spend and rate limits per workspace; Claude Code gets its
  own "Claude Code" workspace automatically [9].
- **Gateways** give per-key, per-team and per-user budgets and rate limits (product
  table above).
- **Attribution.** Tag OTel with `OTEL_RESOURCE_ATTRIBUTES` such as
  `team=data-science` to split cost by team [16].
- **Behaviour is the biggest lever.** Opus left as default and long uncleared
  sessions are the usual causes of high spend [9]. Onboarding should teach `/clear`,
  model choice and subagent use.

#### Model choice by data tier [inferred]

A 2025 interview described a red, yellow, green rule set at Pipedrive. The July 2026
talk by Pipedrive's former CTO doesn't mention it, so treat this as a proposal, not
as Pipedrive's current practice. A starting draft:

| Tier | Examples | Allowed routes | Model class |
|---|---|---|---|
| Green | Public docs, open-source code, marketing drafts, generic questions | Any company-approved tool on company accounts | Any approved |
| Yellow | Internal code, architecture docs, anonymised or contract-permitted customer site telemetry, internal process docs | Company accounts on business plans with DPA, no training, standard retention | Any approved; cheaper models for bulk work |
| Red | Employee personal data, customer personal data, credentials and control-system access details, data under contractual residency clauses, security incident data | Only named routes: EU inference (Bedrock `eu.` or Google Cloud EU) or ZDR, through the gateway, with logging of access; some data never | Named models only, chosen per route |

Two checks keep this honest: the classification must match the company's customer
contracts (some may require EU processing), and every route in the table must be
enforceable by a setting listed in this file, not only by training.

### 2.6 Configuration management for agents at organisation scale

#### What is being managed

| Artefact | Purpose | Claude Code form | Cross-tool form |
|---|---|---|---|
| Org policy | Rules no one may override | Managed settings, managed `CLAUDE.md` [2][17] | Each vendor's admin console or policy file |
| Shared instructions | How to work in a repo | `CLAUDE.md`, `.claude/rules/` | `AGENTS.md`, read natively by Claude Code since v2.1.277 when no `CLAUDE.md` or `CLAUDE.local.md` exists [17] |
| Skills | Packaged procedures loaded on demand | `SKILL.md` folders | Agent Skills open format, used by Claude, Codex, Gemini CLI, Cursor, Copilot and others [21] |
| Hooks | Deterministic checks around tool calls | `hooks` in settings | Tool-specific |
| MCP config | Which tools and data agents can reach | `.mcp.json`, managed MCP keys | MCP is cross-vendor; AGENTS.md and MCP are both hosted by the Linux Foundation's Agentic AI Foundation since 9 December 2025 [20] [verified 2026-10-15 against the Linux Foundation press release] |
| Plugins | A versioned bundle of the above | Marketplace plus `enabledPlugins` [15] | Tool-specific |

#### Distribution channels

- **Policy.** Server-managed settings (one org-wide JSON) or MDM; for runners and
  containers, a system `managed-settings.json` baked into the image [2][3].
- **Company plugins and skills.** A company marketplace repository. Claude Code
  accepts a `git` source with a GitLab URL, registered on every machine through
  managed `extraKnownMarketplaces` and switched on through `enabledPlugins`. Each
  user needs read access to the repository, or the plugins can be pre-seeded with
  `CLAUDE_CODE_PLUGIN_SEED_DIR` [15].
- **claude.ai org plugins and skills (Team and Enterprise), a second route.**
  - **Adding plugins:** Owners use Organization settings > Plugins & skills to upload
    a zip, create a plugin, or sync from GitHub or GitLab. GitLab configurations are
    in public beta, self-managed GitLab is supported, sync reads the default branch,
    and nothing syncs on a schedule.
  - **Member publishing:** a policy controls it. Team defaults to Open; Enterprise
    defaults to Requires review from 2026-10-02 when nobody has set it.
  - **Reaching Claude Code:** org-enabled plugins load there as `<name>@synced` for
    users signed in with claude.ai (v2.1.273 or later). Provisioned skills sync into
    `~/.claude/skills/synced/`, which `syncClaudeAiSkills: false` blocks [190][191][192]
    [verified 2026-10-22].
  - **Using both routes:** Anthropic suggests feeding both from one repository. If
    both deliver the same plugin name, the managed-settings copy wins [193] [verified
    by one research pass, not re-checked].
  - **Reachability:** claude.ai has to reach the GitLab host to sync, so a GitLab
    behind a VPN may need an exception [inferred].
- **Authoring without git (claude.ai, Team and Enterprise)** [verified 2026-10-22]:
  - **Create:** a member makes a skill by asking Claude, with the built-in
    skill-creator turned on under Customize > Skills, or by uploading a zip.
    Plugins have a guided "Create with Claude". Skills need code execution on, and
    Owners can switch user-created skills off [196][197].
  - **Share:** sharing with named colleagues needs no review; it's governed by the
    Skill sharing setting [190][198].
  - **Publish to org:** goes through the Publishing setting (Requires review, Open or
    Off; Team defaults to Open). Owners approve in a Requests tab, no one can
    approve their own request, and look-alike items are tagged "Possible duplicate"
    [190].
  - **Scanning:** automatic skill and plugin scanning is Enterprise-only [199].
- **What a plugin holds.** Skills are only one part. A plugin can also carry commands,
  agents, hooks, MCP servers (remote or `.mcpb`), LSP servers, output styles,
  monitors, settings and `bin/` executables [200]. In claude.ai chat only skills and
  connected remote MCP servers work. Cowork adds agents and hooks, and Claude Code
  loads everything. A plugin with a top-level `bin/` can't be installed in chat or
  Cowork [201] [verified 2026-10-22].
- **Too many skills.** Claude sees every skill's name and description and opens the
  body only when it fits the task [verified 2026-10-22]:
  - **Listing budget:** in Claude Code the listing gets 1% of the context window.
    When it overflows, the descriptions of the least-used skills go first (names
    stay) [192].
  - **Anthropic's enterprise guide:** limit how many skills are active at once,
    because a too-broad new description "steals" triggers from existing ones. Test
    each skill with 3 to 5 queries, including ones that should not trigger it, plus
    a coexistence test against the existing set. Authors should not review their
    own skills [202].
  - **How Anthropic runs it internally:** no central team decides; skills start in
    a sandbox folder and the owner opens a PR into the marketplace once a skill has
    traction [203].
  - **Pipedrive (talk, July 2026):** a watchdog agent stops duplicates.
- **Evals for skills.** Anthropic's best practices say to write at least three
  evaluations before writing much of the skill, and to test on every model in use
  [204] [verified 2026-10-22]:
  - **In claude.ai chat:** skill-creator runs test prompts one by one and skips
    quantitative benchmarking [205].
  - **In Cowork and Claude Code:** it adds a with-skill vs without-skill baseline,
    pass rates and token counts [197].
  - **For plugins:** `claude plugin eval` (v2.1.269 or later) scores a plugin
    against a no-plugin baseline [206].
- **GitLab's own catalogues** [verified 2026-10-22]:
  - **AI Catalog:** holds Duo agents and flows, not skills. Skills support is an
    open epic [69].
  - **Duo Agent Skills (Premium, Ultimate):** reads project skills from `skills/` or
    `.agents/skills/`.
  - **Duo CLI plugin marketplace (experimental):** reads
    `.claude-plugin/marketplace.json` [207].
  - **Meaning of "marketplace" at a Claude shop:** a "GitLab marketplace" there most
    likely means a Claude plugin marketplace repo hosted on GitLab [inferred].
- **Repository conventions.** Committed `.claude/settings.json`, `AGENTS.md` and
  `.claude/skills/` in each repo, created from a template repo.
- **Personal preferences.** `~/.claude/settings.json` and dotfiles stay personal.
  Policy does not live there.

#### Versioning, review and release

A single `ai-config` repository in GitLab [inferred]:

```
ai-config/
  policy/managed-settings.json        # org-wide, delivered by console or MDM
  policy/groups/data-science.json     # per group, if MDM or apps gateway is used
  marketplace/.claude-plugin/marketplace.json
  marketplace/plugins/company-review/ # skills, hooks, agents, MCP config
  templates/AGENTS.md                 # repo starter
  catalogue/mcp-servers.yaml          # approved MCP servers, owner, tier, scopes
  CODEOWNERS                          # AI policy owner + security approve policy/
  .gitlab-ci.yml                      # schema check, policy tests, release
```

- **Validation in CI.** Check JSON against Claude Code's published settings schema
  (the admin console editor uses the same schema and may lag new keys) [3]; run
  `claude doctor` in a container with the file installed to list dropped entries
  [2].
- **Policy tests.** A scripted session in CI that tries a denied read and a blocked
  MCP server and expects refusal.
- **Release channels.** Two marketplaces pointing at different refs (stable and
  early access), assigned per group through MDM or the apps gateway; server-managed
  settings cannot target groups [15].
- **Change log.** Every change to policy is a merge request with a reason, which
  doubles as an audit trail.

#### Keeping personal and company accounts apart

- Pin company machines to the company org: `forceLoginMethod` plus
  `forceLoginOrgUUID` [14].
- People who also hold personal plans keep them on personal machines,
  or use a separate config directory (`CLAUDE_CONFIG_DIR`) and never point a
  personal account at company repositories [inferred].
- On Enterprise, domain capture stops new personal accounts on the work domain and
  migrates existing ones; on Team, the domain can block new personal accounts [18].
- Policy text states the rule; configuration makes the wrong path hard.

#### Detecting drift

| Signal | Tool | What it shows |
|---|---|---|
| Which policy a machine runs | `/status` "Setting sources" and "Skipped sources" lines; `claude doctor` [2][3] | Missing, overridden or skipped policy; dropped invalid entries |
| Fleet view of plugins | OTel `plugin_installed` and `plugin_loaded` events; Enterprise Analytics API plugins endpoint [15][16] | Who runs which plugin; unknown plugins |
| Edits to local settings files | `ConfigChange` hook [3] | A user changing settings files (not server or MDM changes) |
| Version spread | `requiredMinimumVersion` plus OTel `app.version` (opt-in attribute) [14][16] | Out-of-date clients |
| Login to wrong org | OTel `organization.id` and `auth` events [16] | Sessions outside the company org |
| Repo conventions | CI lint over `.claude/settings.json` and `AGENTS.md` in each repo [inferred] | Repos that allow bypass mode or unapproved MCP servers |

### 2.7 Make vs buy, and three minimal architectures

**Make vs buy at 50 to 100 people** [inferred]:

| Component | Buy or make | Why |
|---|---|---|
| Chat assistant for all staff | Buy (one vendor) | A self-hosted chat UI lags SaaS features (connectors, projects, research, desktop apps) and needs running; two assistants double the cost and the rules to maintain |
| Coding agent | Buy | Same; the value is in the vendor's agent loop |
| Identity, SSO, provisioning | Use the existing IdP | Already paid for; every vendor integrates |
| Policy and configuration | Make (thin) | A repo of JSON and TOML, CI checks, MDM or console delivery; days, not months |
| Gateway for programmatic and agent traffic | Run an existing product | LiteLLM, Claude apps gateway or a cloud-native option; never write one |
| MCP catalogue and vetting | Make (a document and a process) | No product replaces the judgement; generate allowlists from it |
| Monitoring | Buy plus glue | Vendor analytics plus an OTel collector into whatever the company already runs |

**Cost inputs used below**. All are list prices or Anthropic's own averages [5][9],
and the seat split is an assumption.

- People, as a worked example: 60, with 20 heavy coding users (engineering and data
  science) on Premium seats and 40 others on Standard seats [inferred].
- Claude Code on API pricing averages $150 to $250 per developer per month [9].
- No published figure exists for chat-only users on usage-based Enterprise [10].

| | A. Vendor consoles only | B. Consoles plus gateway for API and agents (recommended start) | C. Gateway-first |
|---|---|---|---|
| What it is | One assistant vendor on a business plan for everyone; coding agent on the same plan; GitLab Duo Core if the company has Premium; policy via server-managed settings or MDM | A, plus a small gateway that holds all programmatic keys: factory agents in CI, internal tools, data science experiments, and the EU route for red data | All model traffic through the company's gateway to API providers (Bedrock `eu.`, Google Cloud EU, Anthropic API). Claude Code via the Claude apps gateway; chat via a self-hosted UI; no SaaS seats |
| Example stack | Claude Team (or Enterprise), Claude Code, `ai-config` repo | A plus LiteLLM (pinned container) or the Claude apps gateway for Claude-only work, with Postgres | Claude apps gateway plus LiteLLM, Bedrock EU, and a chat UI such as LibreChat (MIT) or Open WebUI (branding clause above 50 users) [30] |
| Monthly cost, list | Team: 40 x $20 + 20 x $100 = **$2,800** annual billing, or $3,500 monthly billing, before any usage credits past seat limits [5][9]. Enterprise: 60 x $20 = $1,200 in seat fees plus about $3,000 to $5,000 Claude Code usage for 20 developers [5][9] plus unknown chat usage | A plus API usage of the agents (metered, budgeted per key) plus a small container and database [inferred] | API usage for everyone plus hosting and operations [inferred] |
| Controls gained | SSO, central agent policy, vendor analytics, spend limits; on Enterprise also SCIM, audit logs, Compliance API, inference hooks | Per-agent keys, budgets, model allowlists, one log of programmatic use, EU routing where needed | Per-group policy, one audit trail, EU inference for everything |
| Controls lost or missing | On Team: no audit logs, no SCIM, no Compliance API [5]. No control over API keys people create | Gateway is a high-value target to patch (see the LiteLLM incident [104]) | claude.ai features (connectors, Cowork, cloud sessions, Code Review, Remote Control) [14]; web search through the apps gateway [4]; the apps gateway has no CI sign-in [128] |
| Setup effort [inferred] | 1 to 2 weeks | A plus 1 to 2 weeks | 4 to 8 weeks |
| Running effort [inferred] | Low: seat admin, policy changes | About 0.1 FTE: gateway upgrades, key and budget admin | 0.25 to 0.5 FTE: the whole stack, plus user support for a self-hosted chat UI |
| Fits when | Few programmatic uses, no EU-only data | A factory to build, a small team, some red data | Contracts require EU processing for most work, or a strong cloud team exists |

**Recommendation as a starting position** [inferred]:

1. Start with option B, built in two steps: A first, the gateway when
   the first factory agent needs a key.
2. Choose the plan tier by what the policy needs, not only by price. Monitoring and
   incident management need audit logs and the ability to pull records after an
   incident. On Anthropic, those exist only on Enterprise [5][12].
   The extra cost over Team is mostly usage-based and capped by spend limits [10].
3. Keep a narrow slice of option C for red data only: one EU route (Bedrock `eu.`
   profile through the gateway) for the workloads whose contracts require it.
4. If the company already uses Claude, keep Anthropic as the primary assistant vendor.
   In 2026 Pipedrive moved from "a
   zoo of everything" to Anthropic (talk, July 2026). What's still open is decided
   by the criteria in this section:
   - Team or Enterprise (audit logs, Compliance API);
   - moving personal-account use onto the company org;
   - an EU route for red data;
   - what runs alongside (GitLab Duo, Copilot through Microsoft 365).

   Keep the gateway for the occasional second model, not for multiple chat
   subscriptions.

Of Claude Code and Codex, Claude Code has the deeper
documented central controls today (section 2.3), and Codex's `requirements.toml`
covers the essentials [47]. Either works under this architecture. GitLab Duo's
external agents can run either of them inside pipelines [65].

### 2.8 Role-by-role tool fit [inferred]

Everything in this table is inferred from typical work in each function. It is an
input for onboarding conversations, not a decision. The data tiers refer to the
draft in section 2.5.

| Role | Plausible daily uses | Tools | Data they touch | Main guardrails | Onboarding focus |
|---|---|---|---|---|---|
| Engineering | Coding, review, tests, migrations, CI fixes, docs | Coding agent (Claude Code or Codex), GitLab Duo if licensed, chat | Yellow code; occasionally red (secrets, customer configs) | Managed settings, sandbox, MCP allowlist, no secrets in context, human review of every merge | Permission modes, `/clear` and model choice, the company plugin, review rules |
| Data science | Notebook and pipeline code, analysis, experiment scripts, model evaluation | Coding agent, chat with file analysis, API keys through the gateway for experiments | Customer site telemetry (yellow), possibly occupancy data with personal aspects (red) | Approved datasets only, anonymise before analysis, gateway keys with budgets, no customer data in personal tools | Data tier rules with examples from the company's own data, cost of long-context runs |
| Product and design | Specs, user research synthesis, small backlog items, prototypes | Chat with projects, design tools, read-only issue tracker connector; later agent flows that close small items (a Pipedrive pattern, talk, July 2026) | Yellow product docs; red if interview notes name people | No customer personal data in prompts without consent; connectors read-only first | Writing specs agents can use, when to hand off to engineering |
| Sales | Proposal drafts, account research, call prep, quoting inputs | Chat, CRM connector (read-only first), web research | Customer contacts (red), pricing (yellow, confidential) | No personal data bulk-pasted; CRM write access only after review | Prompting for proposals, what never goes in (contracts with residency clauses) |
| Customer success and operations | Reading site documentation, mapping control-system point lists, troubleshooting, onboarding customer sites | Chat with file analysis, coding agent for scripts, later a site-onboarding automation | Customer site data (yellow), control-system credentials and network details (red) | Never paste credentials or VPN details; customer documents only on company accounts; verify every mapping the model proposes | The verification habit: model proposes, engineer or rule decides |
| Finance | Spreadsheet analysis, report drafts, variance commentary | Chat with spreadsheets; no write connectors to accounting at first | Financial data (red or confidential yellow), payroll (red) | Payroll and personal data excluded; prefer aggregated extracts | What may be uploaded, how to check numbers the model produces |
| HR and admin | Job ads, policy drafts, onboarding material, translation | Chat | Employee personal data (red) | No candidate or employee personal data unless a named route allows it | Drafting without personal data, translation quality checks |
| Leadership | Board material, strategy drafts, research, summaries | Chat with projects, research features | Confidential strategy (red or yellow by item) | Company accounts only; sharing settings on projects | Projects, what is retained and who can read it |
| AI and IT team | Everything above plus admin consoles, gateway, config repo | All tools, admin roles | Audit logs and transcripts by exception | Admin access logged and reviewed; least privilege on compliance keys | Runbooks for access, incidents and policy changes |

Each role's onboarding pack can follow one pattern [inferred]. It covers what that
role may use, three real tasks done well with it, the data that never goes in (with
the company's own examples), how to ask for a new tool or connector, and who to tell
when something goes wrong.
Teaching material and the policy text itself are in `06`.

## 6. Reading list

**Must read (5)**

1. Claude Code, "Set up Claude Code for your organization" [14]. The decision map:
   provider, delivery, what to enforce, visibility, data handling.
2. Claude Code, "Deploy managed settings" [2] and "Configure server-managed
   settings" [3]. Precedence, delivery, fail-closed behaviour and the honest limits.
3. Codex, "Managed configuration" [47]. The second agent's controls, needed to
   write one policy for both.
4. MCP "Security best practices" (2026-07-28) [132] with the OWASP MCP Top 10
   [134]. The basis of the connector vetting checklist.
5. GitLab, "Agent tool governance" [63] and "External agents" [65]. How agents are
   governed inside GitLab.

**Should read**

- Claude apps gateway overview, deployment and spend limits [4][126][127]; Claude
  Code's gateway protocol page [129].
- Plugins for organisations [15]; managed MCP [137]; monitoring with OpenTelemetry
  [16].
- Compliance API [12] and inference hooks [13], for what monitoring is possible on
  Enterprise.
- Data usage [6], zero data retention [23], data residency [7].
- ChatGPT data and inference residency [35]; Codex authentication [48]; Codex admin
  setup [46].
- LiteLLM virtual keys and budgets [95][96], logging [98], and the March 2026
  security update [104].
- GitLab Credits [59], Duo availability settings [61][62], data usage [74],
  externally connected agents [71].
- OWASP Top 10 for LLM applications [135]; MCP authorization [131].
- Article 29 Working Party Opinion 2/2017 on data processing at work [29], as
  background for the logging rules (the legal analysis is in `03`).

**Reference**

- Pricing pages: Claude [5], Claude API [8], ChatGPT [32], GitLab [57], GitHub
  Copilot [78], Google Workspace [145], Cursor [169].
- Vendor admin docs when a team asks: Gemini CLI enterprise [155], Cursor
  enterprise [174], GitHub Copilot policies [81], Microsoft Copilot [161][162].
- Gateway alternatives: Portkey [108], Cloudflare AI Gateway [111], Kong [116], AWS
  guidance [120], Azure APIM [124].

## 8. Sources

All accessed 2026-10-14 unless noted. Sources 1 to 31 were read directly for this
file. Sources 32 to 181 were read by delegated research passes on 2026-10-14 (a few
just after midnight UTC on 2026-10-15) and not all re-opened. Source 182 was added by
the 2026-10-15 fact-check (see `verification-log-tooling-security.md`). "Page date" is
the date the page shows; most docs show none.

1. Claude Code settings: https://code.claude.com/docs/en/settings
2. Claude Code, deploy managed settings: https://code.claude.com/docs/en/managed-settings
3. Claude Code, server-managed settings: https://code.claude.com/docs/en/server-managed-settings
4. Claude apps gateway: https://code.claude.com/docs/en/claude-apps-gateway
5. Claude pricing: https://claude.com/pricing
6. Claude Code data usage: https://code.claude.com/docs/en/data-usage
7. Claude Platform data residency: https://platform.claude.com/docs/en/manage-claude/data-residency
8. Claude API pricing: https://platform.claude.com/docs/en/about-claude/pricing
9. Claude Code, manage costs: https://code.claude.com/docs/en/costs
10. Claude Enterprise consumption guide: https://support.claude.com/en/articles/14782391-claude-enterprise-consumption-guide (page says "updated yesterday")
11. Use Claude Code with your Team or Enterprise plan: https://support.claude.com/en/articles/11845131-use-claude-code-with-your-team-or-enterprise-plan
12. Compliance API: https://platform.claude.com/docs/en/manage-claude/compliance-api
13. Inference hooks: https://platform.claude.com/docs/en/manage-claude/inference-hooks
14. Set up Claude Code for your organization: https://code.claude.com/docs/en/admin-setup
15. Manage plugins for your organization: https://code.claude.com/docs/en/plugins/org
16. Claude Code monitoring (OpenTelemetry): https://code.claude.com/docs/en/monitoring-usage
17. Claude Code memory, CLAUDE.md and AGENTS.md: https://code.claude.com/docs/en/memory
18. Claude SSO and provisioning (opened 2026-10-15): https://support.claude.com/en/articles/13133195-set-up-jit-or-scim-provisioning ; https://support.claude.com/en/articles/14625619-claim-and-migrate-accounts-on-your-domain ; https://support.claude.com/en/articles/10276682-important-considerations-before-enabling-single-sign-on-sso-and-jit-scim-provisioning
19. Tenant restrictions: https://support.claude.com/en/articles/13198485-enforce-network-level-access-control-with-tenant-restrictions
20. [secondary] Linux Foundation forms Agentic AI Foundation (newswire copy of the announcement, 2025-12-09): https://www.fierce-network.com/newswire/linux-foundation-forms-agentic-ai-foundation
21. Agent Skills overview: https://agentskills.io
22. Claude Code analytics: https://code.claude.com/docs/en/analytics
23. Claude Code zero data retention: https://code.claude.com/docs/en/zero-data-retention
24. Custom data retention for Enterprise (opened 2026-10-15): https://privacy.claude.com/en/articles/10440198-configure-custom-data-retention-controls-for-enterprise-plans
25. Anthropic DPA (opened 2026-10-15; processor role per DPA section B.1, https://www.anthropic.com/legal/data-processing-addendum): https://privacy.claude.com/en/articles/7996862-how-do-i-view-and-sign-your-data-processing-addendum-dpa
26. Bedrock inference profiles, supported regions and models (via search summary): https://docs.aws.amazon.com/bedrock/latest/userguide/inference-profiles-support.html
27. Claude desktop extension allowlist: https://support.claude.com/en/articles/12592343-enabling-and-using-the-desktop-extension-allowlist
28. Claude custom connectors and org-wide connector authorisation (via search summary): https://support.claude.com/en/articles/11175166-get-started-with-custom-connectors-using-remote-mcp ; https://support.claude.com/en/articles/15537633-authorize-mcp-connectors-for-your-entire-organization
29. Article 29 Working Party, Opinion 2/2017 on data processing at work (WP249), 2017-06-23: https://ec.europa.eu/newsroom/article29/items/610169
30. Open WebUI licence: https://docs.openwebui.com/license/ ; [secondary] LibreChat MIT licence per third-party summaries
31. Claude audit log export (opened 2026-10-15): https://support.claude.com/en/articles/9970975-access-audit-logs
32. ChatGPT pricing: https://chatgpt.com/pricing/
33. ChatGPT Business overview: https://help.openai.com/en/articles/8792828-chatgpt-business-overview
34. OpenAI enterprise privacy (page date 2026-01-08): https://openai.com/enterprise-privacy/
35. ChatGPT data and inference residency (about 2026-10-03): https://help.openai.com/en/articles/9903489-data-residency-and-inference-residency-for-chatgpt
36. Where ChatGPT Business content is stored: https://help.openai.com/en/articles/20001418-where-your-chatgpt-business-content-is-stored
37. ChatGPT Business members, seats and roles: https://help.openai.com/en/articles/8542216-managing-members-seat-types-and-roles-in-chatgpt-business
38. ChatGPT Business SSO: https://help.openai.com/en/articles/11489188-setting-up-single-sign-on-sso-for-chatgpt-business
39. Flexible pricing for Enterprise, Edu and Business: https://help.openai.com/en/articles/11487671-flexible-pricing-for-the-enterprise-edu-and-business-plans
40. OpenAI Compliance Platform: https://help.openai.com/en/articles/9261474-openai-compliance-platform-for-enterprise-and-edu-customers
41. OpenAI Academy, data governance and compliance (updated 2026-09-17): https://academy.openai.com/public/clubs/admins-6o6xf/resources/data-governance-and-compliance
42. OpenAI SCIM: https://help.openai.com/en/articles/10011769-scim-provisioning-and-management
43. ChatGPT admin controls for plugins and apps: https://help.openai.com/en/articles/11509118-admin-controls-security-and-compliance-for-plugins-and-apps
44. Custom MCP apps in ChatGPT: https://help.openai.com/en/articles/12584461-creating-custom-mcp-apps-in-chatgpt
45. ChatGPT RBAC: https://help.openai.com/en/articles/11750701-managing-feature-access-with-role-based-access-control-in-chatgpt
46. Codex enterprise admin setup: https://learn.chatgpt.com/docs/enterprise/admin-setup
47. Codex managed configuration: https://learn.chatgpt.com/docs/enterprise/managed-configuration
48. Codex authentication: https://learn.chatgpt.com/docs/auth
49. Codex pricing and plan matrix: https://learn.chatgpt.com/docs/pricing
50. Codex with GitLab: https://learn.chatgpt.com/docs/third-party/gitlab
51. Codex cloud environments: https://learn.chatgpt.com/docs/environments/cloud-environments
52. Codex approvals and security: https://learn.chatgpt.com/docs/agent-approvals-security
53. OpenAI API, your data: https://developers.openai.com/api/docs/guides/your-data
54. OpenAI admin APIs and RBAC: https://developers.openai.com/api/docs/guides/admin-apis ; https://developers.openai.com/api/docs/guides/rbac
55. ChatGPT retention when a member is removed: https://help.openai.com/en/articles/8266418-data-retention-when-a-member-is-removed-from-a-workspace
56. Codex AGENTS.md: https://learn.chatgpt.com/docs/agent-configuration/agents-md
57. GitLab pricing: https://about.gitlab.com/pricing/
58. GitLab subscription add-ons: https://docs.gitlab.com/subscriptions/subscription-add-ons/
59. GitLab Credits: https://docs.gitlab.com/subscriptions/gitlab_credits/
60. GitLab Duo Agent Platform: https://docs.gitlab.com/user/duo_agent_platform/
61. Turn GitLab Duo on or off: https://docs.gitlab.com/user/gitlab_duo/turn_on_off/
62. Turn the Agent Platform on or off; foundational agents: https://docs.gitlab.com/user/duo_agent_platform/turn_on_off/ ; https://docs.gitlab.com/user/duo_agent_platform/agents/foundational_agents/
63. GitLab agent tool governance: https://docs.gitlab.com/user/ai-governance/tool-governance/
64. GitLab model selection: https://docs.gitlab.com/user/duo_agent_platform/model_selection/
65. GitLab external agents: https://docs.gitlab.com/user/duo_agent_platform/agents/external/
66. GitLab composite identity: https://docs.gitlab.com/user/duo_agent_platform/composite_identity/
67. GitLab Duo MCP clients: https://docs.gitlab.com/user/gitlab_duo/model_context_protocol/mcp_clients/
68. GitLab MCP server: https://docs.gitlab.com/user/model_context_protocol/mcp_server/
69. GitLab AI Catalog: https://docs.gitlab.com/user/duo_agent_platform/ai_catalog/
70. GitLab AI audit events: https://docs.gitlab.com/user/ai-governance/ai-audit-events/
71. GitLab externally connected agents: https://docs.gitlab.com/user/ai-governance/externally-connected-agents/
72. GitLab Duo Self-Hosted: https://docs.gitlab.com/administration/gitlab_duo_self_hosted/
73. GitLab AI gateway: https://docs.gitlab.com/administration/gitlab_duo/gateway/
74. GitLab Duo data usage: https://docs.gitlab.com/user/gitlab_duo/data_usage/
75. GitLab AI Transparency Center: https://about.gitlab.com/ai-transparency-center/
76. GitLab Dedicated AI gateway (2026-08-20): https://about.gitlab.com/blog/gitlab-dedicated-ai-gateway/
77. GitLab Duo and SDLC trends: https://docs.gitlab.com/user/analytics/duo_and_sdlc_trends/
78. GitHub Copilot plans (docs): https://docs.github.com/en/copilot/get-started/plans
79. GitHub Copilot moving to usage-based billing (2026-04-27): https://github.blog/?p=95572
80. GitHub Copilot budgets: https://docs.github.com/en/copilot/concepts/billing-and-usage/organizations-and-enterprises/budgets
81. GitHub Copilot policy surfaces: https://docs.github.com/en/copilot/reference/supported-surfaces-for-policies
82. GitHub Copilot default availability: https://docs.github.com/en/copilot/concepts/enterprise/default-availability
83. GitHub Copilot MCP allowlist: https://docs.github.com/en/copilot/how-tos/administer-copilot/manage-mcp-usage/configure-enterprise-allowlist
84. GitHub Copilot MCP registry: https://docs.github.com/en/copilot/how-tos/administer-copilot/manage-mcp-usage/configure-mcp-registry
85. GitHub third-party agents: https://docs.github.com/en/copilot/concepts/agents/about-third-party-agents
86. GitHub Copilot content exclusion: https://docs.github.com/en/copilot/concepts/context/content-exclusion
87. GitHub Copilot audit logs: https://docs.github.com/en/copilot/how-tos/administer-copilot/manage-for-organization/review-activity/review-audit-logs
88. GitHub Copilot usage metrics: https://docs.github.com/en/copilot/concepts/billing-and-usage/copilot-usage-metrics/copilot-metrics
89. GitHub Copilot with data residency: https://docs.github.com/en/enterprise-cloud@latest/admin/data-residency/github-copilot-with-data-residency
90. GitHub custom instructions support: https://docs.github.com/en/copilot/reference/custom-instructions-support
91. GitHub Copilot plans page: https://github.com/features/copilot/plans
92. GitHub changelog, client telemetry capture (2026-10-12): https://github.blog/changelog/2026-10-12-client-telemetry-user-attribution-and-configurable-capture
93. GitLab Credits dashboard and caps: https://docs.gitlab.com/subscriptions/gitlab_credits_dashboard/
94. Introducing GitLab Credits (updated 2026-02-10): https://about.gitlab.com/blog/introducing-gitlab-credits/
95. LiteLLM virtual keys: https://docs.litellm.ai/docs/proxy/virtual_keys
96. LiteLLM users, teams and budgets: https://docs.litellm.ai/docs/proxy/users
97. LiteLLM model access and access groups: https://docs.litellm.ai/docs/proxy/model_access ; https://docs.litellm.ai/docs/proxy/model_access_groups
98. LiteLLM logging: https://docs.litellm.ai/docs/proxy/logging
99. LiteLLM UI and spend logs: https://docs.litellm.ai/docs/proxy/ui_logs
100. LiteLLM PII masking: https://docs.litellm.ai/docs/proxy/guardrails/pii_masking_v2
101. LiteLLM admin UI SSO: https://docs.litellm.ai/docs/proxy/admin_ui_sso
102. LiteLLM audit logs: https://docs.litellm.ai/docs/proxy/multiple_admins
103. LiteLLM pricing: https://www.litellm.ai/pricing
104. LiteLLM security update, March 2026 (updated 2026-03-27 and 2026-03-30): https://docs.litellm.ai/blog/security-update-march-2026
105. LiteLLM security advisories: https://docs.litellm.ai/blog/tags/security
106. LiteLLM with Claude Code: https://docs.litellm.ai/docs/tutorials/claude_responses_api
107. LiteLLM MCP gateway: https://docs.litellm.ai/docs/mcp
108. Portkey pricing: https://portkey.ai/pricing
109. Palo Alto Networks completes acquisition of Portkey (2026-05-29; opened 2026-10-15): https://www.paloaltonetworks.com/company/press/2026/palo-alto-networks-completes-acquisition-of-portkey-to-secure-ai-agents ; product name per https://portkey.ai/features/ai-gateway
110. Portkey MCP gateway: https://portkey.ai/docs/product/mcp-gateway.md
111. Cloudflare AI Gateway pricing (2026-09-24): https://developers.cloudflare.com/ai-gateway/reference/pricing/
112. Cloudflare AI Gateway spend limits (2026-09-30): https://developers.cloudflare.com/ai-gateway/features/spend-limits/
113. Cloudflare AI Gateway logging (2026-09-24): https://developers.cloudflare.com/ai-gateway/observability/logging/
114. Cloudflare AI Gateway authentication (2026-06-17): https://developers.cloudflare.com/ai-gateway/configuration/authentication/
115. Cloudflare data localisation compatibility (2026-10-12): https://developers.cloudflare.com/data-localization/compatibility/
116. Kong AI Gateway: https://developer.konghq.com/ai-gateway/
117. Kong AI Sanitizer: https://developer.konghq.com/plugins/ai-sanitizer/
118. Kong pricing: https://konghq.com/pricing
119. Kong Konnect geos: https://developer.konghq.com/konnect-platform/geos/
120. AWS multi-provider generative AI gateway: https://docs.aws.amazon.com/solutions/multi-provider-generative-ai-gateway-on-aws/
121. Bedrock model invocation logging: https://docs.aws.amazon.com/bedrock/latest/userguide/model-invocation-logging.html
122. Bedrock Guardrails sensitive information filters: https://docs.aws.amazon.com/bedrock/latest/userguide/guardrails-sensitive-filters.html
123. Bedrock cross-region inference: https://docs.aws.amazon.com/bedrock/latest/userguide/cross-region-inference.html ; https://docs.aws.amazon.com/bedrock/latest/userguide/geographic-cross-region-inference.html
124. Azure API Management AI gateway (updated 2026-06-25): https://learn.microsoft.com/en-us/azure/api-management/genai-gateway-capabilities
125. Azure API Management LLM logs (2026-06-12): https://learn.microsoft.com/en-us/azure/api-management/api-management-howto-llm-logs
126. Claude apps gateway deployment: https://code.claude.com/docs/en/claude-apps-gateway-deploy
127. Claude apps gateway spend limits: https://code.claude.com/docs/en/claude-apps-gateway-spend-limits
128. Claude Code gateways overview: https://code.claude.com/docs/en/gateways
129. Claude Code with LLM gateways: https://code.claude.com/docs/en/llm-gateway ; https://code.claude.com/docs/en/llm-gateway-protocol
130. MCP specification 2026-07-28 changelog and versioning: https://modelcontextprotocol.io/specification/2026-07-28/changelog ; https://modelcontextprotocol.io/specification/versioning
131. MCP authorization (2026-07-28): https://modelcontextprotocol.io/specification/2026-07-28/basic/authorization
132. MCP security best practices (2026-07-28): https://modelcontextprotocol.io/docs/2026-07-28/tutorials/security/security_best_practices
133. MCP Enterprise-Managed Authorization: https://modelcontextprotocol.io/extensions/auth/enterprise-managed-authorization
134. OWASP MCP Top 10 (beta): https://owasp.org/www-project-mcp-top-10/
135. OWASP Top 10 for LLM Applications 2025: https://genai.owasp.org/llm-top-10/
136. MCP Registry, about: https://modelcontextprotocol.io/registry/about
137. Claude Code managed MCP: https://code.claude.com/docs/en/managed-mcp
138. Anthropic connector directory review criteria: https://claude.com/docs/connectors/building/review-criteria
139. Claude Code MCP: https://code.claude.com/docs/en/mcp
140. Docker MCP Gateway: https://docs.docker.com/ai/mcp-gateway/
141. Microsoft MCP Gateway: https://github.com/microsoft/mcp-gateway
142. Cloudflare MCP server portals (2026-10-12): https://developers.cloudflare.com/cloudflare-one/access-controls/ai-controls/mcp-portals/
143. Obot docs: https://docs.obot.ai/
144. Kong MCP: https://developer.konghq.com/mcp/
145. Google Workspace pricing: https://workspace.google.com/pricing
146. Generative AI in Google Workspace privacy hub (2026-10-06): https://knowledge.workspace.google.com/admin/gemini/generative-ai-in-google-workspace-privacy-hub
147. Turn the Gemini app on or off (2026-10-13): https://knowledge.workspace.google.com/admin/generative-ai/gemini-app/turn-the-gemini-app-on-or-off
148. Gemini temporary chats and deletion controls (2026-06-16): https://workspaceupdates.googleblog.com/2026/06/temporary-chats-and-conversation-deletion-control-for-gemini.html
149. Data covered by data regions (2026-10-13): https://knowledge.workspace.google.com/admin/compliance/data-covered-by-data-regions?hl=en ; https://knowledge.workspace.google.com/admin/compliance/choose-a-geographic-location-for-your-data
150. Gemini app data regions support (2026-06-29): https://workspaceupdates.googleblog.com/2026/06/gemini-app-data-regions-support.html
151. Gemini for Workspace log events (2026-10-13): https://knowledge.workspace.google.com/admin/reports/gemini-for-workspace-log-events?hl=en
152. Retain Gemini app messages with Vault (2026-10-13): https://knowledge.workspace.google.com/vault/retention/retain-gemini-app-messages-with-vault?hl=en
153. Gemini Code Assist sunset (2026-10-13): https://docs.cloud.google.com/gemini/docs/codeassist/sunset
154. Transitioning Gemini CLI to Antigravity CLI (2026-05-19): https://developers.googleblog.com/en/an-important-update-transitioning-gemini-cli-to-antigravity-cli/
155. Gemini CLI enterprise guide: https://github.com/google-gemini/gemini-cli/blob/main/docs/cli/enterprise.md
156. Gemini CLI configuration reference: https://github.com/google-gemini/gemini-cli/blob/main/docs/reference/configuration.md
157. Antigravity for enterprise customers (2026-08-21): https://cloud.google.com/blog/products/ai-machine-learning/expanding-google-antigravity-for-enterprise-customers
158. Gemini Enterprise AI developer tools (2026-10-13): https://docs.cloud.google.com/gemini/enterprise/docs/ai-developer-tools-overview
159. Gemini Enterprise overages (2026-10-13): https://docs.cloud.google.com/gemini/enterprise/docs/overages?hl=en
160. Gemini Enterprise locations (2026-10-13): https://docs.cloud.google.com/gemini/enterprise/docs/locations
161. Microsoft Copilot enterprise data protection (2026-08-18): https://learn.microsoft.com/en-us/copilot/microsoft-365/enterprise-data-protection
162. Microsoft Copilot licensing (2026-09-30): https://learn.microsoft.com/en-us/copilot/microsoft-365/microsoft-365-copilot-licensing
163. Manage Copilot Chat (2026-10-12): https://learn.microsoft.com/en-us/copilot/manage
164. Microsoft Copilot privacy and EU Data Boundary (2026-10-08): https://learn.microsoft.com/en-us/copilot/microsoft-365/microsoft-365-copilot-privacy
165. Purview retention for Copilot (2026-06-25): https://learn.microsoft.com/en-us/purview/retention-policies-copilot
166. Purview audit for Copilot (2026-08-26): https://learn.microsoft.com/en-us/purview/audit-copilot
167. Manage Copilot agents (2026-10-13): https://learn.microsoft.com/en-us/microsoft-365/admin/manage/manage-copilot-agents-integrated-apps
168. Cursor joins SpaceX (2026-08-14): https://cursor.com/blog/joining-spacex
169. Cursor Teams pricing: https://cursor.com/docs/account/teams/pricing
170. Cursor data use (2026-09-03): https://cursor.com/data-use
171. Cursor privacy and data governance: https://cursor.com/docs/enterprise/privacy-and-data-governance
172. Cursor security (2026-08-25): https://cursor.com/security
173. Cursor DPA (2025-11-05): https://cursor.com/terms/dpa
174. Cursor enterprise docs: https://cursor.com/docs/enterprise
175. Cursor model and integration management: https://cursor.com/docs/enterprise/model-and-integration-management
176. Cursor SCIM: https://cursor.com/docs/account/teams/scim
177. Cursor rules: https://cursor.com/docs/rules
178. Cursor hooks: https://cursor.com/docs/hooks
179. Cursor ignore files: https://cursor.com/docs/context/ignore-files
180. Cursor pricing: https://cursor.com/pricing
181. Cursor compliance and monitoring: https://cursor.com/docs/enterprise/compliance-and-monitoring
182. Microsoft 365 Copilot pricing (read 2026-10-15): https://www.microsoft.com/en-us/microsoft-365-copilot/enterprise ; https://www.microsoft.com/en-us/microsoft-365-copilot/pricing

Sources 183 to 193 were read on 2026-10-22 by a research pass and re-checked by an
independent agent (193 was not re-checked).

183. What is the Team plan: https://support.claude.com/en/articles/9266767-what-is-the-team-plan
184. Purchase and manage seats on Team plans: https://support.claude.com/en/articles/12004354-purchase-and-manage-seats-on-team-plans
185. Usage credits: https://support.claude.com/en/articles/12005970
186. Anthropic Consumer Terms (EEA version; US version read via Wayback snapshot 20261022152434): https://www.anthropic.com/legal/consumer-terms
187. Anthropic Commercial Terms: https://www.anthropic.com/legal/commercial-terms
188. Use Enterprise search: https://support.claude.com/en/articles/12489464-use-enterprise-search
189. Joining an organization with a personal account: https://support.claude.com/en/articles/9267400
190. Manage plugins on claude.ai (admin): https://claude.com/docs/plugins/admin ; sync from GitHub or GitLab: https://claude.com/docs/plugins/org-sync
191. Claude Code, how plugins load: https://code.claude.com/docs/en/plugins/loading
192. Provision and manage skills for your organization: https://support.claude.com/en/articles/13119606-provision-and-manage-skills-for-your-organization ; Claude Code skills: https://code.claude.com/docs/en/skills
193. Rolling out plugins across an organization: https://claude.com/docs/plugins/org-rollout

Sources 194 to 207 were read on 2026-10-22 by two research passes and re-checked by an
independent agent.

194. MCP tunnels: https://claude.com/docs/connectors/mcp-tunnels/overview
195. Desktop extensions (MCPB): https://claude.com/docs/connectors/building/mcpb.md
196. Create and upload skills: https://support.claude.com/en/articles/12512180
197. Skills how-to (skill-creator): https://claude.com/docs/skills/how-to.md ; Create a plugin with Claude: https://claude.com/docs/plugins/create-with-claude.md
198. Share plugins and skills: https://claude.com/docs/plugins/share.md
199. Skill and plugin scanning: https://support.claude.com/en/articles/15927065-get-started-with-skill-and-plugin-scanning
200. Plugin components: https://code.claude.com/docs/en/plugins/components
201. Plugin platform support: https://claude.com/docs/plugins/platform-support.md
202. Agent Skills for enterprise: https://platform.claude.com/docs/en/agents-and-tools/agent-skills/enterprise
203. Thariq Shihipar, Lessons from building Claude Code: how we use skills (2026-06-03): https://claude.dev/blog/lessons-from-building-claude-code-how-we-use-skills
204. Skill authoring best practices: https://platform.claude.com/docs/en/agents-and-tools/agent-skills/best-practices
205. skill-creator SKILL.md: https://github.com/anthropics/skills/tree/main/skills/skill-creator
206. Plugin evals: https://code.claude.com/docs/en/plugin-evals
207. GitLab Duo Agent Skills: https://docs.gitlab.com/user/duo_agent_platform/customize/agent_skills/ ; Duo CLI customisation and plugins: https://docs.gitlab.com/user/gitlab_duo_cli/customize/
208. Claude Code, endpoint-managed settings (read 2026-10-23): https://code.claude.com/docs/en/managed-settings
209. Export your organization's data (read 2026-10-23): https://support.claude.com/en/articles/13346720-export-your-organization-s-data
