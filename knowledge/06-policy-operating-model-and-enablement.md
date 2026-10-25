# AI policy, operating model and enablement

> Generic edition, built 2026-10-26: company-specific and personal content removed. See the pack README. Sections removed in this edition are omitted, and the numbering is kept so pointers resolve.

Covers a company AI policy and its operating model, advising teams, and onboarding
every role to controlled AI use. Researched 2026-10-15.

Claim tags. [verified] means I read the primary source, in full or as excerpts from
the publisher's own page. [secondary] means someone else's summary. [inferred] is my
reading or proposal and may be wrong. Survey findings are labelled as surveys. The
web search budget ran out late in the research, so the last few checks were direct
fetches of known primary pages, and some threads stayed open.

Neighbouring files cover what this one leaves out: legal detail in
`03-regulation-and-standards.md`, hardening and AI incident runbooks in
`04-ai-security-and-hardening.md`, enterprise plans, admin controls and gateways in
`05-controlled-ai-architecture.md`, and CI/CD in `02-cicd-and-platform-foundation.md`.

## 1. What matters most

- **Clarity beats completeness.** DORA's AI capabilities model puts "clear and
  communicated AI stance" first, and Google's write-up says the capability does not
  measure the content of the policy, only whether it is clear and communicated [4].
  DORA's guidance warns that uncertainty pushes people to hide their use or avoid the
  tools [3]. A one-page interim rule sheet early does more than a complete policy
  months later. [verified]
- **One matrix carries the policy.** Data tier against tool. A November 2025
  interview described one at Pipedrive: green is safe, yellow goes to an internal
  champion, red needs legal or data-protection approval [1]. The July 2026 talk by
  Pipedrive's former CTO doesn't mention it, so treat it as an example, not as Pipedrive's current
  practice. GitHub sorts tools into two tiers [21], Harvard caps each tool at a data
  classification level [18], and DORA recommends three buckets [3]. The company's
  version should fit on one screen. [verified]
- **Every policy shares one rule.** A named person owns any AI output they pass
  on. LLVM, the Linux kernel, Fedora, Oxide and GitLab's team pages all say this in
  different words [19][23][24][25][26]. Make it rule one. [verified]
- **Enforce in tooling where you can.** For engineers, managed settings for
  coding agents, protected branches and CI checks enforce what a document can only
  ask for [47]. The written policy then mostly serves non-engineers. Mechanics live
  in files 04 and 05. [inferred]
- **Shadow AI is the starting state.** In MIT NANDA's 2025 study, workers at over
  90% of surveyed companies used personal AI tools for work, while only 40% of the
  companies had bought an official LLM subscription [36]. Microsoft's 2024 survey
  found 78% of AI users bring their own tools [37]. Treat month one as discovery with
  an amnesty, not enforcement. [verified, survey data]
- **Fast yeses make you an enabler.** GitHub describes its program lead as a role
  that enables others rather than gatekeeping [21]. Publish a decision time for tool
  requests and meet it. A queue that takes six weeks recreates shadow AI. [verified
  source, inferred conclusion]
- **Advise with the company's own patterns, not generic rules.** A catalogue of risky
  and better patterns per function, built from interviews in the first month, gives
  teams something concrete to change. Section 2.5 has a starting catalogue. [inferred]
- **Article 4 got lighter but did not go away.** Regulation (EU) 2026/1744 changed
  the AI literacy duty from ensuring "a sufficient level" to taking measures that
  support AI literacy, with no level guaranteed for any individual [7][9]. National
  authorities supervise it from August 2026 [6][7]. No certificate is required. An
  internal record of training and other measures is the evidence [6]. [verified]
- **Training hours go with regular use.** In BCG's 2025 survey, employees with five
  or more hours of training, especially in person with coaching, were much more
  likely to be regular users, and only 36% felt adequately trained [39]. This is a
  correlation. [verified, survey, BCG press release]
- **Champions handle the yellow cases.** Pipedrive sent yellow cases to champions, by
  the same 2025 account [1]. GitHub asks its volunteer advocates for 30 to 60 minutes
  a week [22]. About one champion per team can answer most questions before they
  reach the AI team. [verified sources, sizing inferred]
- **Measure understanding and use, not attendance.** DORA suggests surveying
  four perceptions: AI use is expected, experimentation is supported, permitted tools
  are clear, and the policy applies to my role [3]. Add active users of approved
  tools, request decision times and near-miss reports. [verified, plus inferred]
- **Mandates cause backlash.** Duolingo's "AI-first" memo drew enough criticism
  that its CEO later said he "did not give enough context" [45]. Oxide lists LLM
  mandates and LLM shaming as anti-patterns [19]. Write each function's section of
  the policy with that function. [verified sources for the cases, inferred advice]
- **The improvement plan is a loop with dates.** ISO/IEC 42001 control A.2.4
  expects review at planned intervals [29]. DORA warns against both a one-time policy
  and the "whiplash" of constant rule changes [3]. Run a quarterly review, batch
  changes, keep a changelog, and delete rules that no longer prevent anything.
  [secondary for ISO, verified for DORA]
- **Documentation is the evidence.** Decision records for policy choices, a
  changelog, a public tool register and a monthly note show what the programme did.
  [inferred]
- **Say what is logged.** Monitoring that people discover later costs more trust
  than any rule earns. State what is logged, why, who can read it and when. Ask HR
  whether staff representatives (in Estonia, the employees' trustee) have to be
  consulted (section 2.4). [inferred]

## 2. Body

### 2.1 Published policies and templates worth reading

What exists, what each is good for, and how much to trust my reading of it.

**Governments**

| Source | Form | What to take from it | Tag |
|---|---|---|---|
| UK, AI Playbook for the UK Government, 10 Feb 2025 [13] | Long guide, 10 principles, "practical recommendations" lists, case studies | Principles written as what you do ("You know how to use AI securely"). An AI governance board that reviews and authorises uses. Five learner groups for skills, from beginners to leaders | [verified] |
| Canada, Guide on the use of generative AI (TBS, first Sept 2023, second version since) [14] | Guide plus a short do's and don'ts summary for staff | FASTER: fair, accountable, secure, transparent, educated, relevant. Internal drafting treated as low risk, public chatbots as higher risk. The two-layer format | [verified, excerpts] |
| Australia, Policy for the responsible use of AI in government v2.0, in effect 15 Dec 2025 [15] | Mandatory policy for federal agencies | Accountable officials and use-case owners, an internal use-case register, compulsory foundational AI training for all staff | [verified, excerpts] |
| Sweden, Digg and IMY national guidelines for generative AI in public administration, Jan 2025 [16] | Non-binding guidelines | One document covering management, cybersecurity, GDPR, ethics, labour law and procurement | [verified, excerpt] |
| Estonia | No dedicated guide found | The State Audit Office's May 2025 overview recommends national guidance, training and advice for agencies [17]. A procurement document suggests the Ministry of Justice and Digital Affairs commissioned generative-AI e-course material for public servants. There is no Estonian template to copy | [verified for the audit report, secondary for the course] |

**Universities**

| Source | What to take from it | Tag |
|---|---|---|
| Harvard FAS administrative staff policy and HUIT tool listings [18] | Data entered into a tool may not exceed the tool's approved classification level. Most approved AI tools are cleared to Level 3 (confidential). Level 4 items such as government ID numbers, bank details, health data and passwords never go in. One sentence plus one table, readable by anyone | [verified, excerpts; direct fetch was blocked] |

**Companies**

| Source | What to take from it | Tag |
|---|---|---|
| Oxide, RFD 576 "Using LLMs at Oxide" [19][20] | Values first, responsibility at the top. Sections follow what people do: LLMs as readers, researchers, editors, writers, code reviewers, debuggers, programmers. Then anti-patterns: mandates, shaming, anthropomorphising. Shows how to write judgement guidance instead of a ban list | [verified] |
| GitHub, internal playbook for an AI-powered workforce, 22 Jul 2025 [21] | Eight pillars. Two tool tiers: vetted tools may take confidential data, everything else only public data, and unlisted tools default to the lower tier. Policy built with IT, HR, security and legal. A "DRI" who runs the program. 30-day and 90-day checklists | [verified] |
| GitLab handbook [23] | The central "General Purpose AI Tool Usage" requirements are internal. Public team pages add specifics: developer experience says use Duo first, note meaningful AI contributions and don't commit AI code without review; technical writing says a team member reviews all content. A layered model | [verified, excerpts] |
| Pipedrive, via a Nov 2025 interview with its then CTO [1]. Superseded: his July 2026 talk doesn't mention it | Red, yellow, green. Green is safe, yellow consults a champion, red needs legal or data-protection approval. His stated purpose: "a common rule set to avoid getting into trouble while experimenting" | [verified interview, details of champions not public] |
| Shopify CEO memo, Apr 2025 [43] | "Reflexive AI usage" made a baseline expectation and part of performance reviews. The mandate style, for contrast | [secondary] |

**Open-source contribution policies.** These are the best-tested wording for rules
about AI-written code, because maintainers have argued over every sentence.

| Project | Rule | Tag |
|---|---|---|
| LLVM [24] | Any tool, but "a human in the loop". Contributors read and check output before asking for review and must be able to answer questions about it. Disclose substantial use, for example with an `Assisted-by:` trailer. Unreviewed output that shifts work onto maintainers is an "extractive contribution". Agents acting in project spaces without human approval are banned, and so is using AI on "good first issue" items | [verified] |
| Linux kernel [25] | AI agents must not add `Signed-off-by`. Use `Assisted-by: LLM [tools]`. The human submitter reviews everything and takes full responsibility | [verified] |
| Fedora, v1.0, reviewed 24 Oct 2025 [26] | AI use permitted, contributor responsible. Disclosure required when a significant part comes unchanged from a tool. Grammar and spelling help needs no disclosure. AI cannot be the final judge of a contribution or of a person's standing | [verified, excerpts] |
| QEMU [27] | Declines any contribution where AI use is known or suspected, citing the DCO and unclear copyright. Research and debugging uses are fine | [verified] |
| Apache Software Foundation [28] | AI output allowed if the tool's terms permit it and third-party material is absent or properly licensed. Suggests a `Generated-by:` token. Keeps no approved tool list | [verified] |

**Templates and frameworks**

| Source | Use at the company | Tag |
|---|---|---|
| ISO/IEC 42001:2023 Annex A.2 [29] | A.2.2 AI policy, A.2.3 alignment with other policies, A.2.4 review. Align the structure even without certification. Detail in file 03 | [secondary] |
| NIST AI RMF, GOVERN function [30] | Gap checklist: GOVERN 1.6 inventory of AI systems, 2.1 documented roles, 2.2 training, 4.3 incident identification and sharing, 6.1 third-party risk | [verified] |
| NIST AI 600-1, Generative AI Profile, Jul 2024 [31] | Vocabulary of 12 generative-AI risks with suggested actions | [secondary] |
| CSA AI Controls Matrix, Jul 2025 [32] | 243 control objectives in 18 domains, mapped to ISO 42001 and NIST. Too big to adopt, useful to look things up | [secondary] |
| Future of Privacy Forum, Generative AI for Organizational Use: Internal Policy Considerations, updated Jun 2024 [35] | Short checklist across six areas, including training, disclosure and analysing outputs | [verified, excerpt] |
| Responsible AI Institute, AI Policy Template, Jun 2024 [34] | Plug-and-play policy informed by NIST and ISO 42001. Behind membership | [secondary] |
| SANS [33] | A SANS press release lists a sample AI acceptable use policy in its AI toolkit. I could not open it | [secondary, unconfirmed] |
| "FutureOfLife" | I found no internal AI policy template from the Future of Life Institute. The FPF document above is probably the intended one | [inferred] |

### 2.2 What makes a policy readable by non-engineers

What the readable examples have in common:

- **A short top layer.** Canada publishes a do's and don'ts summary next to the full
  guide [14]. GitLab hangs team pages under one central rule set [23].
- **Rules phrased as what the reader does.** The UK principles are written as "You
  know..." and "You use..." [13].
- **Buckets with one example each.** DORA's prohibited bucket uses one example:
  personal customer data in a public chatbot [3].
- **A tool-by-data table.** Harvard's rule fits in one sentence and one table [18].
- **Sections organised by task.** Oxide follows what people do (read, write, review,
  debug, program), not risk categories [19].
- **One searchable hub** with the policy, the approved tool list and an FAQ that grows
  from real questions [3].

Proposed targets [inferred]: a one-page rule sheet readable in five minutes; a
policy body readable in ten minutes, about 1,500 to 2,000 words; everything that
changes often (tool register, FAQ, pattern catalogue) kept outside the signed
document so it can change without re-approval. Test it: give five non-engineers ten
scenario cards. If they get fewer than eight right, rewrite.

A decision tree for the one-pager [inferred]:

1. Is everything you will paste already public? Go ahead with any tool on the register.
2. Does it contain passwords, API keys, tokens, VPN or remote-access details, or
   network details of a customer site? Stop. These never go into any AI tool.
3. Does it contain personal data (names, emails, CVs, salaries, data tied to
   individuals) or data a customer contract restricts? Red. Ask before you start.
4. Is it other internal or customer-confidential material (code, customer data
   exports, proposals, internal docs)? Yellow. Approved company tools only. For a new
   kind of use, ask your champion.
5. Will the output leave the company, go into the product, or change how physical
   equipment runs? A named person checks it first and owns it.

### 2.3 A skeleton AI policy

Three layers [inferred]:

- **Layer 1, "AI at the company on one page".** Everyone reads it. Changes rarely.
- **Layer 2, the AI policy.** Signed by the approver, version-numbered, reviewed
  quarterly.
- **Layer 3, living appendices** owned by the policy owner: tool register, data tier
  examples, pattern catalogue, intake form, exception log, incident form, FAQ,
  changelog, training record.

| # | Heading | Intent |
|---|---|---|
| 0 | Document control | Owner, approver, version, date, next review date, link to the changelog |
| 1 | Why and how we use AI | The stance: AI use is expected and supported, this is what's permitted, this is where to get help. Covers DORA's four perceptions |
| 2 | Scope | All staff and contractors, all AI tools including AI features switched on inside existing SaaS, and agents run in the factory. AI inside the company's own products is governed by product processes, not this policy (file 03) |
| 3 | Roles and responsibilities | Approver, policy owner, IT and security, legal and data protection, HR, team leads, champions, every employee. Points to the RACI |
| 4 | Approved tools | Points to the register: tool, plan, highest data tier allowed, owner, admin settings, review date. A tool not on the register may only receive public information |
| 5 | Data tiers | Green, yellow, red and never, with examples and the decision tree |
| 6 | Uses | Allowed, allowed with conditions, prohibited, one example per function |
| 7 | Human oversight and accountability | A named owner for every output that leaves its author. Who checks what before it leaves the company. AI never makes the final call on a person |
| 8 | Quality rules | Short separate rules for code, numbers, documents and customer-facing content |
| 9 | IP, licensing and confidentiality | Tool terms, third-party code and licences, customer contract confidentiality, upstream projects' own AI rules, generated images |
| 10 | Security | Pointer to the hardening standard in file 04: secrets, prompt injection, agent permissions, plugins, extensions and MCP servers |
| 11 | Agents and automation | Autonomy levels, approval points, scoped permissions, an owner per agent |
| 12 | Monitoring and logging | What is logged, why, who can read it, on what trigger, how long it's kept, and what it's never used for |
| 13 | Incidents and near misses | What to report, where, how fast. Self-reports are blameless. Link to the runbook in file 04 |
| 14 | Exceptions | How to ask, maximum duration, compensating controls, exception log |
| 15 | Training and AI literacy | Core and role modules, new-hire module, records kept as Article 4 evidence |
| 16 | New tools and use cases | Intake form, triage by tier, published decision times |
| 17 | Review and improvement | Cadence, triggers, metrics, versioning, the sunset rule |
| 18 | Definitions and links | Ten terms at most |

**Data tiers with examples [inferred proposal].** Whether customer data is yellow or
red depends on the company's customer contracts and data processing agreements, so
legal sees this table before anyone else does.

| Tier | What it covers | Where it may go | Who decides a new use |
|---|---|---|---|
| Green | Public material: the company website, published case studies, public standards and docs, open-source code, generic questions | Any tool on the register | Nobody. Use it |
| Yellow | Internal and customer-confidential material without personal data: the company's source code, internal docs and ADRs, roadmaps, customer data exports and time series, proposals, prospects' tender documents | Approved company tools only: enterprise terms, no training on inputs, SSO | The team's champion. The policy owner when unsure |
| Red | Personal data (staff, candidates, customer contacts, anything linking operational data to people), data a customer contract restricts, unreleased financials, security findings about customer sites, board and investor material | Only with written approval for that use case | Legal or data protection, with the approver |
| Never | Passwords, API keys, tokens, VPN configs, remote-access details, IP addresses and network maps of customer sites, anything a customer has forbidden | No AI tool | Not open to exceptions without the approver |

**Quality rules [inferred, drawing on the sources cited].**

- **Code.** The same review and pipeline as any change. The author can explain every
  line, as LLVM requires [24]. An `Assisted-by:` trailer when an agent wrote a
  substantial part [24][25]. No agent merges to a protected branch. Reviewers may
  send back bulk output the author has not reviewed, which is LLVM's "extractive"
  idea [24].
- **Numbers.** Any figure that reaches a customer, an invoice, a savings claim or a
  decision comes from code or a checked spreadsheet, not from a chat model doing
  arithmetic. The model may write the code and the explanation. This is the "models
  propose, code decides" rule.
- **Documents.** The author owns the facts and checks every number, name and quote
  against its source. Oxide's rule on summaries applies: a summary does not replace
  reading where reading is expected [19].
- **Customer-facing text.** A person reads it before it goes out. Savings claims,
  references and technical recommendations are checked against an approved source.
  Where customers interact with an AI system directly, they are told (legal detail in
  file 03).
- **Physical systems.** No AI output changes a live setpoint, schedule or control
  configuration without the existing human check. A product's own control loop is a
  product matter and out of scope.

**Monitoring: what employees should expect to be logged [inferred].** Retention
periods and the legal basis go in after legal review (file 03).

| What is recorded | Why | Who can see it | Never used for |
|---|---|---|---|
| Which tools and seats each person has (SSO, licences) | Inventory, cost | IT, policy owner | Performance evaluation |
| Admin usage statistics (active days, volume) | Adoption metrics, cost | Policy owner, reported per team | Ranking individuals |
| Prompts and responses, where a gateway or enterprise plan keeps them | Security investigation, incident review | Security lead, only on a defined trigger, with each access logged | Browsing, performance review |
| Coding agent and CI logs | Debugging, incident review | Engineering | Same as above |
| Training records | Article 4 evidence | HR, policy owner | Not applicable |

**Incidents and near misses.** Report within one working day: personal or customer
data in the wrong tool, a secret pasted anywhere, AI output that reached a customer
with an error, an agent doing something it shouldn't, a suspected prompt injection, a
tool whose terms changed. Self-reports are blameless. The security lead and the policy
owner triage. If personal data is involved, the GDPR breach clock may already be
running (72 hours to notify the authority where notification is required, under
Article 33), so these go to data protection at once [verified, text; see file 03]. The runbook is in file 04. Count near misses. A rising count in the first months
means people trust the process. [inferred]

**Agents.** Four autonomy levels: suggest only; act in a sandbox; act with approval
for each action; act alone within scoped permissions. The fourth level needs a
decision record and a named owner, and does not touch customer systems or production
data without the approver. Every agent answers five questions: trigger, what it may
read and change, output, what checks it, who owns it when it's wrong. [inferred]

### 2.4 Operating model for a company of 50 to 100 people

**Who governs.** Not a committee. The UK Playbook describes an AI governance board
that reviews and authorises uses [13]. DORA recommends a cross-functional working
group so that engineering is not left out [3]. Australia requires accountable
officials and a use-case register [15]. Scaled to a company of this size [inferred]:

- **Approver:** the CTO, or the CEO if company policies need CEO signature.
- **Policy owner:** agree who early. If the company runs an ISO/IEC 27001 system, its
  owner is a natural choice.
- **AI working group of five:** approver, policy owner, IT or security lead, HR lead,
  and a legal or data-protection contact (possibly external). One rotating business
  representative per quarter. Thirty minutes every two weeks for three months, then
  monthly.
- **Champions:** about one per team.

**RACI [inferred].** R does the work, A answers for it (one per row), C is consulted,
I is informed.

| Activity | Approver | Policy owner | IT / security | Legal / data protection | HR | Team leads | Champions | All staff |
|---|---|---|---|---|---|---|---|---|
| Write and update the policy | A | R | C | C | C | C | C | I |
| Approve a new policy version | A, R | C | C | C | C | I | I | I |
| Maintain the tool register | A | R | R | C | I | I | I | I |
| Everyday use within the rules | | I | | | | I | C | A, R |
| New yellow use case | I | A | C | | | I | R | R (requester) |
| Red use case | A | R | C | R (sign-off) | C if staff data | I | I | R (requester) |
| Add a new tool | A | R | R | C | | C | C | I |
| Technical enforcement | A | C | R | | | I | | I |
| Training design | A | R | I | C | C | C | C | I |
| Training delivery and records | I | R | | | A | C | R | R (attend) |
| AI incident response | A | R | R | R if personal data | C | I | I | R (report) |
| Monitoring and metrics | A | R | R | C | C | I | I | I |
| Exceptions | A | R | C | C | | C | | R (requester) |
| Informing and consulting staff representatives | C | C | | C | R | I | | I |

The last row needs an accountable person, probably the CEO or COO. Estonia's
Employees' Trustee Act gives a trustee the right to information and consultation, and
employers with at least 30 employees must inform and consult on planned decisions
likely to bring significant changes in work organisation (§ 17(1) and § 20(1) of the
Act as adopted in 2006) [51, verified on the original text; current consolidated text
not re-read]. The Act doesn't name new technology or monitoring, so whether an AI
rollout or usage monitoring counts is a question for HR and legal.

**Intake for new tools and use cases [inferred].** An eight-question form:

1. What do you want to do, in which team, how often?
2. Which tool and plan? A new tool, or an AI feature inside one we already use?
3. Which data goes in? Pick the tier, give an example.
4. Who sees the output: you, colleagues, customers, the public?
5. Does the output feed a decision about a person, money or how physical equipment
   runs?
6. Does the tool act on its own (send, write, change systems) or only suggest?
7. Cost and number of seats.
8. Who owns it, and what will you do if the answer is no?

| Case | Route | Proposed decision time |
|---|---|---|
| Listed tool, green or yellow data, known kind of use | Self-serve | None |
| Listed tool, new kind of yellow use | Champion | 2 working days |
| New tool, yellow data | Policy owner, IT or security | 5 working days |
| Red data, decisions about people, or autonomous action outside engineering sandboxes | Policy owner, legal or data protection, approver | 15 working days |

Vendor criteria (detail in file 05): no training on inputs by default, retention and
deletion terms, hosting location, a data processing agreement, SSO and offboarding,
admin controls and audit logs, price, overlap with tools already paid for, a named
owner and an exit plan. Default to "yes, with conditions". Record every decision in
the register with the date and reason. Publish refusals with the reason and the
alternative. Publish the real median decision time each month.

**Exceptions [inferred].** Time-boxed to 90 days at most, with a named owner, a
written reason and a compensating control, logged and revisited at the quarterly
review. An exception renewed twice becomes a policy change or ends.

**Champions network.** Volunteers with their manager's agreement. GitHub recruits by
open call with a deadline [22]. Some practitioner guides prefer nomination followed by
opt-in [48, secondary]. GitHub suggests 30 to 60 minutes a week [22]. Plan for more
during the rollout and get the time agreed in writing: TNW reports a Zapier case
where a manager took back a champion's protected 20% within a month [48, secondary].
The job: first answer for yellow questions, collect patterns and success stories,
co-run their team's role session, report friction once a month. Support: a private
channel, a monthly 30-minute call, early access to new tools, public credit. GitHub's
champions get a private hub, monthly check-ins, recognition, and templates and
facilitation guides [22]. Ratio guides from vendors range from one champion per 15
users to one per 50 [48, secondary]; at a company of 50 to 100 people, one per team
is simpler.

**Policy as code.** Where configuration can enforce a rule, the document states the
rule and its reason and leaves the mechanics to the config. When a control blocks
someone, the message says why and where to ask. [inferred]

| Rule | Enforcement | Detail in |
|---|---|---|
| Only approved tools receive company data | SSO, licence provisioning, extension controls, a gateway if one is built | 05 |
| Coding agents don't read secrets or run without permission checks | Managed settings with deny rules and bypass mode disabled, which users and projects cannot override [47] | 04, 05 |
| AI-written code is reviewed | Protected branches, required approvals, pipeline must pass | 02 |
| Disclosure of agent-written commits | Commit-message check for `Assisted-by:`, warning rather than blocking | 02 |
| No secrets in repos or prompts | Secret scanning before commit and in CI, redaction at the gateway if there is one | 04 |
| Data location for yellow and red data | Provider region settings or gateway routing | 05 |
| Training before access | Licence granted after the core module | HR and IT process |

**Review cadence and continual improvement.** An AI programme covering security,
acceptable use, quality assurance, monitoring and incident response has the shape of
a plan, do, check, act loop. A proposed rhythm [inferred]:

- **Monthly:** register updates, new FAQ entries, a metrics snapshot, a changelog note.
  Minor versions (1.1, 1.2) for clarifications.
- **Quarterly:** the working group reviews metrics, incidents, exceptions, requests and
  survey results, and decides changes. A major version only when rules change.
  Batch changes to avoid DORA's "whiplash" [3].
- **Out-of-cycle triggers:** an incident, new regulation or guidance (the AI Board's
  Article 4 recommendations are due [7]), a new class of tool such as autonomous
  agents, an org change, a customer contract requirement.
- **Yearly:** check the whole policy against ISO 42001 A.2.4 [29] and NIST GOVERN [30].

The improvement plan itself is a dated list: gap, owner, target quarter, and the
metric that will show it closed.

A sunset rule: each rule carries its reason. If a quarterly review cannot name a risk
the rule prevented or a decision it made easier, simplify or delete it. Signs the
policy is going stale: questions the FAQ cannot answer, a growing exception log, tools
in the shadow inventory that the register lacks, a policy page nobody opened last
month. DORA suggests page views and the volume of clarification questions as metrics
[3].

### 2.5 Advising teams

**The consulting model [inferred].**

- **Office hours:** one hour a week, drop in, every question goes into the FAQ.
- **#ai-help channel:** first answer the same day, champions answer first.
- **Usage reviews:** 30 minutes per team per quarter. Tools in use, data going in, what
  works, what annoys. Output: two or three changes and one story worth sharing.
- **Pairing:** 30 to 45 minutes watching someone do a real task with AI, then
  improving it together. This finds more patterns than any survey.
- **Pattern of the week:** a short post with one risky pattern, why it matters and the
  better way.

GitHub's program lead does 1:1 support and office hours, showcases success stories,
runs the tool and policy lifecycle and tracks adoption [21]. That list is close to a
job description for an AI enablement lead.

**Pattern catalogue.** Starting examples [inferred unless cited]. Replace them with
real cases from the first month's interviews.

| Function | Risky pattern | Why it matters | Better pattern |
|---|---|---|---|
| Engineering | Merging agent output after a skim | 66% of developers in Stack Overflow's 2025 survey name "almost right" AI answers as their top frustration [40] | Small diffs, tests first, author explains the change, `Assisted-by:` trailer |
| Engineering | Agent with broad credentials on a laptop that can also reach production | One injected instruction becomes a production change | Scoped tokens, sandboxed agent, managed settings (file 04) |
| Engineering | Pasting stack traces that contain connection strings | A secret leaves the company | Redact first, secret scanning catches the rest |
| Data science | Asking a chatbot to compute savings percentages from an uploaded CSV | The number can't be reproduced or audited | Code computes, the model writes the code and the explanation |
| Data science | Customer time series in a personal notebook assistant | Customer data at an unvetted processor | Approved tools only, drop site identifiers |
| Customer success and operations | Pasting a customer's data export into a personal chatbot to decode field names | Customer-confidential data at a consumer service that may train on it | Approved enterprise tool, remove site names and addresses, keep the field names |
| Customer success and operations | Screenshot of a customer system's admin screen showing IP addresses or logins | Remote-access details | Never. Crop or describe instead |
| Customer success and operations | Acting on an AI's guess about what a data field means | A mislabelled field plus a confident guess means a wrong action on real equipment | AI proposes a mapping, the engineer checks it against the field's behaviour before use |
| Sales and marketing | AI-drafted proposal with savings figures or customer references the model supplied | Invented claims reach a prospect | Figures only from approved case studies, a person checks every number and name |
| Sales and marketing | Meeting recorder joins customer calls unannounced | Consent and confidentiality | Approved recorder, announced, the customer can say no |
| Sales and marketing | Prospect's tender documents in a consumer tool | Breaches confidentiality terms | Approved tool, or summarise it yourself |
| Product and design | AI-generated "user feedback" treated as real | Decisions without evidence. GitLab's UX guidance warns against AI in place of real user feedback [23] | AI synthesises real interview notes, quotes checked against the notes |
| Finance | Payroll or the general ledger uploaded to a chatbot | Personal and price-sensitive data | Red-tier approval, or work with aggregates |
| HR and admin | Screening or ranking CVs with AI | High-risk use under AI Act Annex III point 4 [12], and a discrimination risk | Don't, unless a red-tier approval covers a compliant system. AI may help draft job ads |
| HR and admin | AI to judge performance or "sentiment" of staff | Annex III also covers monitoring and evaluating performance [12]. Emotion recognition at work is prohibited by Article 5(1)(f), except for medical or safety reasons [verified, text; see file 03] | People decisions stay with people |
| Management | Board or investor material in a consumer tool | Red data | Approved tool after approval, or not at all |
| Management | Announcing per-person AI usage tracking | Pushes use out of sight. In Microsoft's 2024 survey, 52% of AI users were reluctant to admit using it for their most important tasks [37] | Measure teams, share stories |
| Everyone | AI features switched on by default in chat, office, video or CRM tools | Internal data processed without anyone deciding | In the inventory, an admin decides per feature |

**Changing habits instead of adding rules [inferred, with sources where cited].**

- **Supply before you ban.** A ban without a working alternative moves use out of
  sight. Mollick writes that "terrifying blanket policies" discourage experimentation
  [42]. Provide the approved tool first, then retire personal accounts.
- **Make the safe path the default.** SSO login, a pre-configured coding agent with
  managed settings, templates with the trailer already filled in.
- **Use peers.** A champion's demo of a real in-house task travels further than a
  policy page. GitHub calls AI adoption "a change management problem" [21].
- **Nudge at the moment of use.** A CI message that explains why a check failed and
  where to ask. A pinned answer in #ai-help.
- **Reward disclosure.** Thank people who report near misses, with their permission.
  Mollick recommends incentives and explicit reassurance that sharing productivity
  gains won't lead to layoffs [42]. That reassurance is management's to give, not
  the policy owner's.
- **No shaming in either direction.** Oxide names "LLM shaming" as an anti-pattern
  [19]. The same goes for shaming people who use AI less.

**Shadow AI discovery and amnesty.** Hidden use is normal. Mollick calls these users
"secret cyborgs" [42]. Lenovo's 2026 survey of 6,000 employees put use without IT
oversight at up to a third [50, secondary]. Steps [inferred, shaped by practitioner
guides 49, secondary]:

1. The CTO or the CEO announces a two-to-three-week inventory with an explicit promise
   of no penalty for past use, framed as "we are choosing which tools to pay for".
2. A short survey: tools used, for what, with which data, what you'd lose without it.
   Anonymous option.
3. A technical cross-check, agreed with HR and legal first: OAuth app grants in the
   identity provider and office suite admin consoles, browser extensions on managed
   devices, expense and card statements for AI subscriptions, AI features enabled in
   existing SaaS.
4. Per tool: adopt (register, buy seats), replace (approved equivalent) or stop (with
   the reason).
5. Publish the result in aggregate: tools found, adopted, replaced.
6. Repeat lightly every six months.

The amnesty covers use before the cut-off date. After it, the normal incident process
applies, still blameless for self-reports.

### 2.6 Role-by-role onboarding

**What Article 4 requires now.** Legal analysis is in file 03. The practical points:

- **Who is covered:** providers and deployers, for their staff and others operating AI
  systems on their behalf, which can include contractors [6]. A company is a deployer
  of every AI tool it uses, and probably a provider of any AI product it builds and
  sells (file 03).
- **The duty after the Digital Omnibus:** take measures to support AI literacy,
  considering technical knowledge, experience, education, training and context. No
  specific level is required of any individual [7][9]. Regulation (EU) 2026/1744 was
  published in the Official Journal on 24 July 2026 and entered into force on 27 July
  2026 [52, verified]. The Article 4 wording above matches the Official Journal text
  [52, verified].
- **The Commission's minimum approach** [6]: a general understanding of AI in the
  organisation; knowing whether you are provider or deployer; assessing the risks of
  the systems in use and what staff need to know; tailoring measures to staff
  knowledge and context, with legal and ethical aspects encouraged.
- **Evidence:** no certificate needed. An internal record of training and other
  initiatives is enough [6]. Measuring staff knowledge is not required [6], though it
  helps.
- **Instructions for use alone** are often ineffective, and for deployers of high-risk
  systems "not sufficient" [6].
- **Enforcement:** national market surveillance authorities from August 2026. The Q&A
  gives both 2 and 3 August [6]. Proof of an incident linked to inadequate training
  may make enforcement more likely [6].
- **Watch item:** the AI Board is to adopt recommendations on Article 4 [7].
- **The living repository** holds more than 40 initiatives from companies and public
  bodies, and copying them "does not grant presumption of compliance" [7][8].

Training record fields [inferred]: name, role, module and version, date, format,
trainer, policy version acknowledged, optional quiz score, follow-up done. HR keeps it.

**Curriculum structure [inferred].**

- **Core module, all staff:** 60 minutes live, or 30 minutes recorded plus a
  10-question scenario quiz. What AI does well and badly, the company's stance, the
  decision tree, the register, accountability, how to report.
- **Role module:** 90 minutes hands-on, cohorts of 6 to 12, with their own tasks.
- **Clinic** two to four weeks later, 45 minutes: what worked, what broke, update the
  patterns.
- **New-hire module** in onboarding week.

That is three to four hours per person in the first cycle, close to BCG's five-hour
mark once office hours and pairing are added [39]. Mollick recommends hands-on
training focused on communicating needs to the AI more than on prompting tricks [42].
Australia's foundational training covers types of AI, recognising AI inside everyday
tools, how generative AI works and how to verify its output [15].

**Per-role curriculum [inferred].** Tools are placeholders until the architecture in
file 05 is decided.

| Role | Learning goals | Controlled tools | Data rules | Real tasks | Watch-outs |
|---|---|---|---|---|---|
| Engineering | Spec-first agent work, small diffs, tests as guardrails, reviewing agent output, agent permissions | Approved coding agent under managed settings, review bot in GitLab, factory flows | Code is yellow. Never secrets, never customer production data in prompts | 1. Write a spec, let the agent implement a small issue with tests, open an MR with `Assisted-by:`. 2. Have the agent explain an unfamiliar service and turn it into a decision record. 3. Triage a failing pipeline with the agent | Unreviewed merges, broad credentials, prompt injection through issues and docs, invented package names |
| Data science | AI for exploration and code while numbers come from code. Reproducibility | Coding agent in IDE and notebooks, enterprise chat | Customer operational data yellow in approved tools if contracts allow. Data tied to people is red | 1. Agent writes a data-quality check on a sensor's behaviour, validated on known cases. 2. Summarise a paper and check five claims against it. 3. Draft a model card | Chat-computed statistics, customer identifiers in prompts, unverified literature claims |
| Product and design | Research synthesis, specs an agent can build from, prototypes, closing small backlog items through the factory | Enterprise chat, prototyping tool, factory flows | Interview notes contain personal data: anonymise or treat as red | 1. Turn interview notes into an anonymised insight summary with checked quotes. 2. Write a spec for a small change and hand it to an agent. 3. Prototype a screen | Synthetic users treated as real, invented quotes |
| Sales and marketing | Account research with citations, proposal drafts with verified figures, content in your own voice | Enterprise chat, approved CRM AI features, approved meeting recorder | Tender documents yellow. Contact data red unless handled inside an approved CRM feature | 1. Research a prospect from public sources with links. 2. Draft a proposal section, then trace every number to an approved source. 3. Draft a post and edit it into your voice | Invented savings and references, unannounced recording, unlabelled AI images of real places |
| Customer success and operations | Reading unfamiliar vendor documentation faster without exposing customer data | Approved enterprise chat with no-training terms, internal onboarding tools | Customer data exports yellow, approved tools only, site identifiers removed where possible. Credentials, IPs, VPN configs never | 1. Work out a vendor's naming convention from a sanitised sample. 2. Draft a customer email about a fault finding and check it against the data. 3. Summarise a functional description PDF and verify five claims | AI guesses about what a data field means, acted on in a live system |
| Finance | Analysis help, drafting, checking formulas | Enterprise chat, approved spreadsheet AI | Unpublished financials and payroll red, aggregates yellow | 1. Explain a budget variance with formulas you verify. 2. Build a formula with AI and test it on known values. 3. Draft an internal memo | Untraceable numbers, uploading payroll |
| HR and admin | What is prohibited and high-risk in HR. Drafting job ads, policies and FAQs. Owning training records | Enterprise chat only | Staff and candidate data red | 1. Draft a job ad and check it for biased wording. 2. Turn the AI policy into a new-hire checklist. 3. Answer a staff question from the handbook with sources | CV screening and ranking, performance or sentiment scoring, monitoring without consultation |
| Management | Setting and modelling the stance, deciding red cases, reading the metrics | Enterprise chat | Board, investor and M&A material red | 1. Use AI to argue against your own strategy memo. 2. Review your team's patterns with its champion. 3. Sponsor one use case to a measured result | Mandates without support, AI in decisions about people, per-person usage tracking |

**Formats [inferred].**

| Format | Length | Best for |
|---|---|---|
| One-page rule sheet | 5 minutes to read | Everyone, first contact |
| Core session | 60 minutes live, or 30 recorded plus quiz | Shared baseline, Article 4 record |
| Role workshop | 90 minutes hands-on | Changing how a function works |
| Clinic or office hours | 45 to 60 minutes, drop-in | Problems from real use |
| Recorded demo | 3 to 5 minutes | One pattern, shown on a real in-house task |
| Scenario quiz | 10 questions | Checking the decision tree is understood |
| Pairing | 30 to 45 minutes | Individuals stuck or sceptical |
| Showcase | 30 minutes monthly | Champions present what worked |

**Sequencing [inferred].** Order by risk and benefit, not by org chart. Assumes policy
v1 is approved around day 60 to 75 of the programme.

| Weeks from programme start | Cohort | Why then |
|---|---|---|
| 6 | Champions, train-the-trainer | They co-run every later session |
| 7 | Management, 60 minutes | Sponsors have to model the stance before staff hear it |
| 8 to 9 | Engineering and data science | Heaviest users, and the factory depends on them |
| 9 to 10 | Customer success and operations | Highest data risk: customer data and system access |
| 10 to 11 | Sales and marketing | Customer-facing claims |
| 12 | Product and design | Ties into factory flows once they exist |
| 13 to 14 | Finance, HR and admin | Smaller groups, red data, HR's high-risk boundary |
| 15 to 16 | Catch-up sessions, new-hire module, first clinics, first survey | Close gaps |

**Measuring adoption and proficiency.**

| Metric | Shows | Source |
|---|---|---|
| Share of staff with core and role module done | Coverage, Article 4 record | Training record |
| DORA's four perceptions, 1 to 5 scale | Whether the stance is understood | Survey [3] |
| Scenario quiz score | Whether the decision tree works | Quiz |
| Monthly active and engaged users of approved tools | Adoption. GitHub counts users active 10 or more days a month as "dedicated" [21] | Admin consoles |
| Tools in the shadow inventory not on the register | Shadow AI shrinking | Inventory |
| Requests: count, median decision time, approval rate | Whether the AI team is an enabler | Register |
| Questions in #ai-help, office-hour attendance | Engagement. A rise early on is good | Channel |
| Near misses and incidents by severity | Reporting culture, real risk | Incident log |
| Engineering: agent-assisted MRs, review time, change failure rate | Effect on delivery | Factory metrics, files 01 and 02 |
| Collected success stories | What to show leadership | Champions |

For proficiency, Zapier's hiring rubric has four levels, unacceptable, capable,
adoptive and transformative, and scores behaviour more than tool knowledge [46]. An
adapted internal version ("not yet" instead of "unacceptable") works for
self-assessment. Keep it out of performance reviews unless leadership and HR decide
otherwise. [secondary for the level descriptions, inferred use]

### 2.7 Change management and adoption evidence

| Source | Type | Finding | Use it for | Caveat |
|---|---|---|---|---|
| DORA 2025 [3][4][5] | Survey of nearly 5,000, plus more than 100 hours of qualitative data | A clear AI stance amplifies AI's positive effect on individual effectiveness and organisational performance and can reduce friction. AI amplifies existing strengths and weaknesses | Why the stance comes first | Correlational |
| MIT NANDA, The GenAI Divide, Jul 2025 [36] | 300+ public initiatives, interviews at 52 organisations, 153 leader surveys | 95% of organisations get zero return. Workers at over 90% of companies use personal AI tools, 40% of companies bought official subscriptions. Leading firms learn from shadow use before buying | Shadow AI is the starting point, and a source of information | Labelled "preliminary findings". A small sample for a claim this broad, so don't quote the 95% |
| Microsoft Work Trend Index 2024 [37] | Survey, 31,000 people in 31 countries | 75% use generative AI at work. 78% of AI users bring their own tools, 80% at small and mid-sized firms. 52% reluctant to admit use for important tasks. 39% got training from their employer | Hidden use, training gap | Vendor-commissioned. 75%, 78% and 39% are in Microsoft's release; 52% in press coverage; 80% only in secondary coverage [unconfirmed] |
| Microsoft Work Trend Index 2026 [38] | Survey of 20,000 AI users plus product telemetry | Organisational factors (culture, manager support, talent practices) account for about twice the impact of individual factors | Readiness is an organisational job | Secondary coverage only, vendor data |
| BCG, AI at Work 2025 [39] | Survey, 10,600+ people in 11 countries and regions | 36% feel adequately trained. Five or more hours of training goes with regular use. 72% use AI regularly; more than three-quarters of leaders and managers, but only 51% of frontline staff. 54% would use AI tools even if not authorised. Positive sentiment rises from 15% to 55% with strong leadership support | Training hours, manager role | Correlational |
| Stack Overflow Developer Survey 2025 [40] | Survey, 33,662 answers to the AI usage question | 84% use or plan to use AI. 46% distrust its accuracy, 33% trust it. 66% cite "almost right" answers | Engineers' scepticism has grounds | Self-selected sample |
| METR, Jul 2025 [41] | Randomised trial, 16 experienced developers, 246 tasks | Tasks took 19% longer with AI, while developers believed they were 20% faster | Felt speed is not a measure | Early-2025 tools. METR published newer results in Feb 2026 that I have not read |
| Mollick, "Making AI Work: Leadership, Lab, and Crowd", May 2025 [42] | Essay | Leadership sets direction and incentives, a lab builds and tests, the crowd of employees finds uses | Operating model for experimentation | Opinion |

**Mandates and how they went [secondary].** Shopify's CEO made "reflexive AI usage" a
baseline expectation and part of performance reviews in April 2025 [43]. Coinbase's
CEO said he fired engineers who had not onboarded AI coding tools within a week, and
called it heavy-handed [44]. Duolingo's "AI-first" memo said it would gradually stop
using contractors for work AI can handle; after the backlash the CEO said he "did not give
enough context" and that the company had never laid off full-time employees [45].
None of this tells you whether mandates work. It does show that the framing travels
outside the company and that people read it as a statement about their jobs.
[inferred conclusion]

**Developer resistance.** Treat it as information.

- **Quality worries are real.** The Stack Overflow and METR numbers above back them.
  Make sceptics the owners of the quality bar: they write the review checklist and
  set agent permissions.
- **Review load moves to seniors.** Pipedrive saw juniors write much more code, which
  first raised seniors' review load [1, 2025]. Small diffs, tests and AI review as a
  first pass help.
- **Seniors resist first.** In his July 2026 talk, Pipedrive's former CTO said senior
  engineers pushed back hard and juniors experimented. Seniors came round once they
  saw it work on real tasks. A five-person team led the change for 250 to 300
  engineers (talk, July 2026; the transcript is not in this pack).
- **Craft and identity.** Opt-in pilots, no mandate, results shown on the company's
  own code.
- **Job fear.** Management states the position. The policy owner does not improvise
  one.
- **Bad measures make it worse.** Pipedrive's former CTO argues in his July 2026 talk
  that "time saved" misleads and delivered product impact is what counts [2, page
  summary]. Don't publish hours-saved tallies.

**Communication plan [inferred].**

| When | Audience | Message | Channel | Owner |
|---|---|---|---|---|
| Week 1 | All staff | The AI team's job is enablement: tools, help and a few clear rules | All-hands or Slack | CTO |
| Week 2 | All staff | Interim one-page rules, #ai-help, office hours | Slack, wiki | Policy owner |
| Week 3 | All staff | Inventory with amnesty, no penalty for past use | Slack, email | CTO or CEO, with HR |
| Week 5 | All staff | What the inventory found, what we'll provide | Slack post | AI team |
| Week 8 | All staff | Policy v1 draft open for comments for two weeks | Wiki, champions | Policy owner, champions |
| Week 10 | All staff | Policy v1 published, 15-minute walk-through, FAQ | All-hands, wiki | CTO, policy owner |
| Monthly | All staff | What changed, one success story, one near-miss lesson | Slack post | AI team |
| Quarterly | Leadership, then all | Metrics, review outcome, next quarter's plan | Meeting, post | AI team |

**Positioning as enabler, not gatekeeper [inferred].**

- Call it AI enablement in channel names and announcements. Compliance is a reason,
  not the headline.
- Default to "yes, with conditions", and publish decision times and refusal reasons.
- Spend more time building what people want (an approved assistant, templates, agent
  configurations) than writing rules.
- Let champions decide most yellow cases so the AI team isn't the bottleneck.
- Show the AI team's own AI use in the open, including the mistakes caught.
- Report to leadership in adoption, decision times and incidents avoided, not in
  rules written.

## 6. Reading list

**Must read (five).**

1. The July 2026 talk by Pipedrive's former CTO, "Building an agentic software
   factory", and the Jam.dev interview about Pipedrive's AI tools [2][1].
2. DORA, "Clear and communicated AI stance" [3]. Short, and the closest thing to
   research-backed design rules for the policy.
3. GitHub, internal playbook for an AI-powered workforce, and the champions playbook
   [21][22]. The operating model nearest to what the company needs.
4. Oxide, RFD 576 [19]. The best example of a values-led policy that engineers respect.
5. European Commission, AI literacy Q&A in its post-Omnibus version [6]. What the
   training records have to show.

**Should read.**

- Mollick, "Making AI Work: Leadership, Lab, and Crowd" [42].
- LLVM AI Tool Use Policy [24], for the wording of code rules.
- Canada's generative AI guide and its do's and don'ts summary [14].
- UK AI Playbook, the principles and the AI support structure section [13].
- MIT NANDA, section 3.3 on the shadow AI economy [36].
- BCG, AI at Work 2025 [39], and Microsoft Work Trend Index 2024 [37].
- FPF internal policy considerations [35].
- METR's 2025 study and its 2026 update [41].

**Reference.**

- ISO/IEC 42001 Annex A.2 via file 03 [29]; NIST AI RMF GOVERN [30] and AI 600-1
  [31]; CSA AI Controls Matrix [32].
- Australia's policy v2.0 and its staff training guidance [15]; Sweden's guidelines
  [16]; Harvard's data-level approach [18].
- Fedora [26], Linux kernel [25], QEMU [27] and ASF [28] contribution policies.
- Claude Code managed settings and IAM docs [47].
- The Commission's living repository of AI literacy practices [8].
- Estonian State Audit Office overview [17] and the Employees' Trustee Act [51].

## 8. Sources

Accessed 2026-10-14 or 2026-10-15 unless a date is given.

1. Yash Bagal, "Pipedrive's Internal AI Tools Powering 100,000+ Customers", Jam.dev blog,
   25 Nov 2025. https://strawberryjam.ghost.io/pipedrives-internal-ai-tools-powering-100-000-customers/
2. WeAreDevelopers, Agur Jõgi, "Building an agentic software factory: How we rebuilt
   product development at Pipedrive", 10 Jul 2026 (page summary only).
   https://www.wearedevelopers.com/videos/100261-building-an-agentic-software-factory-how-we-rebuilt-product-development-at-pipedrive
3. DORA, "Clear and communicated AI stance".
   https://dora.dev/capabilities/clear-and-communicated-ai-stance/
4. Google Cloud blog, "Introducing DORA's inaugural AI Capabilities Model", 24 Sep 2025.
   https://cloud.google.com/blog/products/ai-machine-learning/introducing-doras-inaugural-ai-capabilities-model
5. DORA, State of AI-assisted Software Development 2025, report page.
   https://dora.dev/research/2025/dora-report/
6. European Commission, "AI Literacy: Questions & Answers", page updated 27 Jul 2026.
   https://digital-strategy.ec.europa.eu/en/faqs/ai-literacy-questions-answers
7. European Commission, "AI talent, skills and literacy", page updated 13 Oct 2026.
   https://digital-strategy.ec.europa.eu/en/policies/ai-talent-skills-and-literacy
8. European Commission, "Living repository to foster learning and exchange on AI
   literacy", page updated 10 Aug 2026.
   https://digital-strategy.ec.europa.eu/en/library/living-repository-foster-learning-and-exchange-ai-literacy
9. Law & Technology, "AI literacy: the Digital Omnibus rewrites Article 4 of the AI Act"
   [secondary]. https://lawandtechnology.eu/en/ai-literacy-digital-omnibus-article-4-ai-act/
10. Gibson Dunn, "EU AI Act Omnibus Agreement: Postponed High-Risk Deadlines and Other
    Key Changes", May 2026 [secondary].
    https://www.gibsondunn.com/eu-ai-act-omnibus-agreement-postponed-high-risk-deadlines-and-other-key-changes/
11. Hunton, "EU Digital Omnibus on AI enters into force", 2026 [secondary].
    https://www.hunton.com/privacy-and-cybersecurity-law-blog/eu-digital-omnibus-on-ai-enters-into-force
12. EU AI Act, Annex III (via artificialintelligenceact.eu).
    https://artificialintelligenceact.eu/annex/3/
13. UK Government, "Artificial Intelligence Playbook for the UK Government", 10 Feb 2025.
    https://www.gov.uk/government/publications/ai-playbook-for-the-uk-government/artificial-intelligence-playbook-for-the-uk-government-html
14. Treasury Board of Canada Secretariat, "Guide on the use of generative artificial
    intelligence".
    https://www.canada.ca/en/government/system/digital-government/digital-government-innovations/responsible-use-ai/guide-use-generative-ai.html
15. Australian Digital Transformation Agency, "Policy for the responsible use of AI in
    government" v2.0, in effect 15 Dec 2025, and "Guidance for staff training on AI".
    https://www.digital.gov.au/ai/ai-in-government-policy and
    https://www.digital.gov.au/ai/ai-in-government-policy/staff-training
16. IMY, "National guidelines for generative AI in public administration are launched",
    Jan 2025.
    https://www.imy.se/en/news/national-guidelines-for-generative-ai-in-public-administration-are-launched/
17. Riigikontroll, overview of AI solutions built in public sector institutions, 30 May 2025.
    https://www.riigikontroll.ee/sites/default/files/documents/2026-04/KA_80157_%C3%9Clevaade%20tehisintellekti%20lahenduste%20loomisest%20avaliku%20sektori%20asutustes_2025.pdf
18. Harvard FAS, "Administrative Staff Policy on Generative AI (GAI) Use", and HUIT, "AI
    APIs and developer tools".
    https://www.fas.harvard.edu/administrative-staff-policy-gai-use and
    https://www.huit.harvard.edu/ai-developer-tools
19. Oxide Computer Company, RFD 576 "Using LLMs at Oxide" (no date shown).
    https://rfd.shared.oxide.computer/rfd/0576
20. Simon Willison, note on RFD 576, 7 Dec 2025 [secondary].
    https://feeds.simonwillison.net/2025/Dec/7/using-llms-at-oxide/
21. GitHub, "GitHub's internal playbook for building an AI-powered workforce", Matt Nigh,
    22 Jul 2025. https://github.com/resources/insights/ai-powered-workforce-playbook
22. GitHub, "Activating your internal AI champions", Matt Nigh, 29 Aug 2025.
    https://github.com/resources/insights/activating-internal-ai-champions
23. GitLab handbook, "AI at GitLab Tips"; GitLab docs, AI guide for documentation;
    GitLab handbook, "AI in Developer Experience".
    https://handbook.gitlab.com/handbook/tools-and-tips/ai ,
    https://docs.gitlab.com/development/documentation/ai_guide/ ,
    https://handbook.gitlab.com/handbook/engineering/infrastructure-platforms/developer-experience/ai
24. LLVM, "AI Tool Use Policy". https://llvm.org/docs/AIToolPolicy.html
25. Linux kernel documentation, AI coding assistants.
    https://docs.kernel.org/process/coding-assistants.html
26. Fedora Council, "AI-Assisted Contributions Policy" v1.0, reviewed 24 Oct 2025; LWN,
    "Fedora floats AI-assisted contributions policy" [secondary].
    https://docs.fedoraproject.org/en-US/council/policy/ai-contribution-policy/ and
    https://lwn.net/Articles/1039623
27. QEMU, code provenance policy.
    https://www.qemu.org/docs/master/devel/code-provenance.html
28. Apache Software Foundation, "Generative Tooling Guidance".
    https://www.apache.org/legal/generative-tooling.html
29. ISMS.online, "ISO 42001 Annex A.2 Policies related to AI" [secondary]; AI Verify
    Foundation, crosswalk of AI Verify and ISO 42001, Jun 2024.
    https://www.isms.online/iso-42001/annex-a-controls/a-2-policies-related-to-ai/ and
    https://aiverifyfoundation.sg/wp-content/uploads/2024/06/Crosswalk-AIV-and-ISO42001-final.pdf
30. NIST AI Resource Center, AI RMF Core.
    https://airc.nist.gov/airmf-resources/airmf/5-sec-core/
31. NIST AI 600-1, Generative AI Profile (initial public draft link; final Jul 2024)
    [secondary for content]. https://airc.nist.gov/docs/NIST.AI.600-1.GenAI-Profile.ipd.pdf
32. Cloud Security Alliance, AI Controls Matrix, Jul 2025 [secondary for content].
    https://cloudsecurityalliance.org/artifacts/ai-controls-matrix/
33. SANS, "Building AI Security Confidence: SANS Unveils Toolkit to Guide Secure AI
    Adoption" [secondary, not opened].
    https://www.sans.org/press/announcements/building-ai-security-confidence-sans-unveils-toolkit-guide-secure-ai-adoption
34. Responsible AI Institute, AI Policy Template, Jun 2024 [secondary].
    https://www.responsible.ai/ai-policy-template/
35. Future of Privacy Forum, "Generative AI for Organizational Use: Internal Policy
    Considerations", updated Jun 2024.
    https://fpf.org/resource/generative-ai-for-organizational-use-internal-policy-considerations/
36. MIT NANDA (Challapally et al.), "The GenAI Divide: State of AI in Business 2025",
    Jul 2025. https://mlq.ai/media/quarterly_decks/v0.1_State_of_AI_in_Business_2025_Report.pdf
37. Microsoft, "Microsoft and LinkedIn release the 2024 Work Trend Index on the state of
    AI at work", 8 May 2024.
    https://news.microsoft.com/source/2024/05/08/microsoft-and-linkedin-release-the-2024-work-trend-index-on-the-state-of-ai-at-work/
38. The Neuron, on Microsoft's 2026 Work Trend Index, May 2026 [secondary].
    https://www.theneuron.ai/explainer-articles/microsofts-2026-work-trend-index-your-employees-figured-out-ai-your-company-didnt/
39. BCG, "AI at Work 2025: Momentum Builds, but Gaps Remain", Jun 2025, and the press
    release of 26 Jun 2025, which carries the 36% and 54% figures.
    https://www.bcg.com/publications/2025/ai-at-work-momentum-builds-but-gaps-remain and
    https://www.bcg.com/press/26june2025-beyond-ai-adoption-full-potential
40. Stack Overflow, 2025 Developer Survey, AI section.
    https://survey.stackoverflow.co/2025/ai
41. METR, "Measuring the Impact of Early-2025 AI on Experienced Open-Source Developer
    Productivity", 10 Jul 2025.
    https://metr.org/blog/2025-07-10-early-2025-ai-experienced-os-dev-study/
42. Ethan Mollick, "Making AI Work: Leadership, Lab, and Crowd", One Useful Thing,
    22 May 2025. https://www.oneusefulthing.org/p/making-ai-work-leadership-lab-and
43. Digital Commerce 360, on Shopify's internal memo, 8 Apr 2025 [secondary].
    https://www.digitalcommerce360.com/2025/04/08/internal-memo-shopify-ceo-declares-ai-non-optional/
44. Tech.co, "Coinbase CEO Fires AI-Averse Engineers After Just One Week" [secondary].
    https://tech.co/news/coinbase-ceo-fired-engineers-ai
45. Fortune, on Duolingo's CEO and the AI memo, 18 Aug 2025 [secondary].
    https://fortune.com/2025/08/18/duolingo-ceo-admits-controversial-ai-memo-did-not-give-enough-context-insists-company-never-laid-off-full-time-employees
46. Zapier, "Raising the AI fluency bar in hiring".
    https://zapier.com/blog/raising-ai-fluency-bar-in-hiring/
47. Anthropic, Claude Code docs: identity and access management, and settings.
    https://code.claude.com/docs/en/iam and https://docs.claude.com/en/docs/claude-code/settings
48. The Next Web, "'When everyone's responsible, no one is': the AI champion problem"
    [secondary]; Layer 3 Labs, AI champions program guide, updated 22 Jul 2026
    [secondary]. https://thenextweb.com/news/ai-champions-20-percent-time-zapier-windsor
    and https://www.layer3labs.io/guides/ai-champions-program
49. Compliance Podcast Network, "The 30-Day Shadow-AI Amnesty" [secondary]; First AI
    Movers, shadow AI detection and governance for European SMEs, 2026 [secondary].
    https://compliancepodcastnetwork.net/the-30-day-shadow-ai-amnesty-turning-hidden-risk-into-governance/
    and https://radar.firstaimovers.com/shadow-ai-detection-governance-european-smes-2026.md
50. Help Net Security, on Lenovo's Work Reborn research 2026, 1 May 2026 [secondary].
    https://www.helpnetsecurity.com/2026/05/01/shadow-ai-risks-it-oversight/
51. Estonia, Employees' Trustee Act (Riigi Teataja, English translation); the Act as
    adopted 13 Dec 2006, RT I 2007, 2, 6, Estonian text via ICNL; Eurofound, staff
    information and consultation on restructuring plans, Estonia, 2015 [secondary].
    https://www.riigiteataja.ee/en/eli/ee/519012015010/consolide/current ,
    https://www.icnl.org/research/library/estonia_employee_est/ and
    https://apps.eurofound.europa.eu/legislationdb/staff-information-and-consultation-on-restructuring-plans/estonia
52. Regulation (EU) 2026/1744 (Digital Omnibus on AI), Official Journal, 24 Jul 2026,
    read 15 Oct 2026.
    https://eur-lex.europa.eu/legal-content/EN/TXT/HTML/?uri=OJ:L_202601744
53. Agur Jõgi, "The CTO-CHRO Alliance: Where Human Trust Meets Algorithmic Power",
    Forbes Technology Council, 19 Feb 2026.
    https://www.forbes.com/councils/forbestechcouncil/2026/02/19/the-cto-chro-alliance-where-human-trust-meets-algorithmic-power/
