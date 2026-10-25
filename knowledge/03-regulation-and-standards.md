# Regulation and standards for governing AI

> Generic edition, built 2026-10-26: company-specific and personal content removed. See the pack README. Sections removed in this edition are omitted, and the numbering is kept so pointers resolve.

Research date: 14 to 15 October 2026. This file covers the EU and Estonian law and the standards that frame a company's AI policy. This is a learning and reference document, not legal advice. Where it interprets law, it says so.

Confidence tags used throughout:

- **[verified]** checked against a primary source during this research.
- **[verified, text]** taken from the legal text or standard as published before 2026. EUR-Lex and iso.org blocked automated access during this research, so re-read the article before quoting it to a customer or regulator.
- **[secondary]** law firm, consultancy, tracker or press source.
- **[inferred]** my own reasoning. No source states it.

Bracketed numbers such as [S7] point to the source list in section 8. Sibling files cover technical AI security and hardening, vendor enterprise plans and AI gateways, the policy text itself, employee onboarding, CI/CD and agent tooling. This file covers the legal and standards frame those sit inside.

---

## 1. What matters most

1. **The AI Act timeline moved, and most of what touches a typical company already applies.** Regulation (EU) 2026/1744 entered into force on 27 July 2026. Annex III high-risk duties now start 2 December 2027 and Annex I duties 2 August 2028 [verified, S2, S3]. Prohibitions, AI literacy, GPAI rules and Article 50 are live.
2. **Article 4 AI literacy is the obligation a company using AI certainly has.** In force since 2 February 2025 and enforceable by national authorities since 2 August 2026, it now reads as a duty to "take measures to support the development of AI literacy", with no guaranteed level per person [verified, S2, S5]. The evidence is a role-by-role training record, so role-by-role onboarding doubles as Article 4 evidence.
3. **Internal use of ChatGPT, Claude, Copilot or coding agents makes the company a deployer.** Beyond Article 4 and the Article 5 bans, the AI Act asks almost nothing of non-high-risk use. GDPR, confidentiality, contracts and security do the work.
4. **An in-house agentic development factory makes the company a provider of an AI system,** because putting a system into service for your own use counts [verified, text, S1 Art. 3(11)]. It stays minimal-risk unless someone points it at a listed use.
5. **Two internal uses could become high-risk from December 2027.** AI in hiring, and AI that monitors or evaluates individual workers, such as per-engineer agent telemetry shown to managers. Both sit in Annex III point 4, and the draft guidelines include probation periods [verified, S7 para 274]. Emotion recognition of staff is banned today.
6. **An AI product that optimises building HVAC looks outside high-risk when its customers are not CER-identified critical entities,** on the Commission's draft guidelines of 19 May 2026. Optimisation is not a safety function, and Annex III point 2 is read as covering only systems used by CER-identified critical entities [verified, S7 paras 181, 190; this is interpretation]. The guidelines are a draft. The open edge is the failure-mode limb of "safety component", which a documented control envelope for the product can close.
7. **Article 50 transparency has applied since 2 August 2026.** It bites when the company ships a chat assistant or LLM-written customer content [verified, S9].
8. **Estonia has no AI Act implementing law yet.** TTJA is the intended market surveillance authority, with designation pending final adoption on the Commission's list of 7 September 2026 [verified, S61], and there is no national penalty regime for Article 4 [secondary, S19]. AKI covers personal data.
9. **GDPR does more day-to-day work than the AI Act.** Room-level occupancy data in hotels can be personal data. EDPB Opinion 28/2024 governs models trained on personal data [verified, text, S25]. AKI wrote on 7 May 2026 that a paid business plan does not by itself make a tool GDPR-compliant [verified, S30].
10. **The EU-US Data Privacy Framework stands, with more risk than a year ago.** Trump v. Slaughter, decided 6 to 3 on 29 June 2026, overruled Humphrey's Executor and removed FTC independence, and the Latombe appeal is pending [secondary, S31]. Keep SCCs as a fallback in every US AI vendor contract.
11. **NIS2 does not reach a small enterprise by size alone, but check the size test and the exceptions.** Estonia's amended Cybersecurity Act has applied since 1 January 2026 [secondary, S34, S36, S64], and NIS2 lists electricity market participants providing aggregation, demand response or energy storage in its energy sector [verified, S38]. But energy-sector entities are in scope only if at least medium-sized under Recommendation 2003/361/EC [verified, S38 Art. 2(1)]. A small enterprise has fewer than 50 staff and a turnover or balance sheet total of no more than EUR 10 million [verified, S62]. So NIS2 likely does not apply to a small enterprise unless an exception catches it, such as identification as a critical entity or by Estonia under Article 2(2). A company whose headcount, counted the EU way, reaches 50 is no longer small [inferred]. Get counsel to confirm the size test before anyone tells a customer the company is or is not in scope.
12. **ISO/IEC 42001 shares its clause structure with ISO/IEC 27001,** so an AI management system can extend an existing ISMS instead of running beside it.
13. **AI governance areas map onto ISO/IEC 42001 and the NIST AI RMF.** Security hardening, acceptable use, quality assurance, monitoring, incident response and continual improvement each have a home (section 2.6). Use 42001 as the skeleton whether or not the company certifies.
14. **Customers will ask in writing.** Banks bring DORA clauses, NIS2-regulated customers bring supply-chain questions, and questionnaires include AI sections such as the CSA AI-CAIQ [verified, S53]. An answer bank tied to evidence pays for itself.
15. **Know when to call a lawyer.** Before "not high-risk" goes into a contract, before deciding NIS2 or CRA applicability, for any AI touching employee evaluation, and for any incident with a reporting clock. Section 2.8 has the list.

---

## 2. The frame in detail

### 2.1 Timeline of obligations

| Date | What happens | Why it matters | Tag |
|---|---|---|---|
| 1 Aug 2024 | AI Act enters into force | Starts every AI Act clock | [verified, S3] |
| 17 Jan 2025 | DORA applies to EU financial entities | Bank customers push DORA Article 30 clauses to ICT vendors | [verified, text, S42] |
| 2 Feb 2025 | AI Act Article 5 prohibitions and Article 4 AI literacy apply | Applies now to any company that uses AI | [verified, S3] |
| 2 Aug 2025 | GPAI model obligations, AI Act governance and penalty chapters apply | Binds model vendors, not a company that only builds on their models | [verified, S3, S4] |
| 3 Sep 2025 | EU General Court upholds the Data Privacy Framework, case T-553/23 | Basis for transfers to US AI vendors | [secondary, S31] |
| 12 Sep 2025 | Data Act applies | Data access rights for connected products and related services | [verified, S43] |
| 19 Nov 2025 | Commission tables digital omnibus proposals for AI and for data | Start of the AI Act delay | [verified, S3] |
| 1 Jan 2026 | Estonia's NIS2 amendments to the Cybersecurity Act (KüTS) in force | Check whether the company is in scope, size test first | [secondary, S34, S36, S64] |
| 31 Mar 2026 | Deadline to notify RIA for entities in scope on 1 Jan 2026 | If the company is in scope, check it notified | [secondary, S34, S64] |
| 7 May 2026 | Political agreement on the AI omnibus. AKI letter on general-purpose AI | | [verified, S3, S30] |
| 19 May 2026 | Commission publishes draft high-risk classification guidelines | Main basis for classifying products | [verified, S6] |
| 10 Jun 2026 | Final Code of Practice on transparency of AI-generated content | Practical route for Article 50 | [secondary, S59] |
| 29 Jun 2026 | Trump v. Slaughter decided by the US Supreme Court. Same day, the Council adopts the AI omnibus | Raises DPF risk | [secondary, S31; Council date S60] |
| Jul 2026 | CEN-CENELEC publishes EN 18286, the AI Act quality management standard. Not cited in the Official Journal | First AI Act standard | [secondary, S22] |
| Jul 2026 | EDPB draft Guidelines 02/2026 on anonymisation, consultation to 30 Oct 2026 | Affects claims that data or models are anonymous | [secondary, S26] |
| 17 Jul 2026 | CER Directive deadline for Member States to identify critical entities | Feeds the Annex III point 2 reading | [verified, text, S39] |
| 20 Jul 2026 | Commission guidelines on Article 50 transparency | | [verified, S9] |
| 24 Jul 2026 | Regulation (EU) 2026/1744 published in the Official Journal | | [verified, S2] |
| 27 Jul 2026 | Regulation (EU) 2026/1744 in force, on the third day after publication | | [verified, S2, S3] |
| 2 Aug 2026 | Article 50 applies. National authorities can enforce Article 4. Commission can fine GPAI providers | | [verified, S3, S4, S5] |
| 11 Sep 2026 | Cyber Resilience Act reporting duties start | Only if the company ships a "product with digital elements" | [verified, S41] |
| 2 Dec 2026 | Article 50(2) marking deadline for generative systems placed on the market before 2 Aug 2026. New bans in Article 5(1)(ba) and (bb) on non-consensual intimate imagery and CSAM generation | | [verified, S2 Arts. 111(4) and 113] |
| 12 Jan 2027 | Data Act: switching charges for data processing services must end | Matters if the company sells SaaS | [verified, text, S32 Art. 29] |
| 2 Aug 2027 | GPAI models placed before Aug 2025 must comply. National AI sandboxes due | | [verified, text, S1 Art. 111(3); secondary, S13] |
| 2 Dec 2027 | Annex III high-risk obligations apply | Any HR tool or product found high-risk | [verified, S2, S3] |
| 11 Dec 2027 | Cyber Resilience Act main obligations apply | | [verified, S41] |
| 2 Aug 2028 | Annex I high-risk obligations apply. Machinery delegated acts due | | [verified, S2, S3, S12] |
| 2 Aug 2030 | Deadline for high-risk systems intended for public authorities and already on the market | Only public-sector customers | [secondary, S15] |
| about 2029 | KüTS three-year compliance periods end. Start dates vary by provision | If the company is in scope | [secondary, S35] |

### 2.2 EU AI Act

#### The law in one paragraph

Regulation (EU) 2024/1689 sorts AI by risk [S1]. A short list of practices is banned (Article 5). High-risk systems, defined in Article 6 through Annexes I and III, carry heavy obligations: risk management, data governance, technical documentation, logging, human oversight, accuracy and cybersecurity, a quality management system, conformity assessment and registration. Some systems carry transparency duties (Article 50). Providers of general-purpose AI models have their own chapter. Everything else is minimal-risk, where only Article 4 and voluntary codes apply. Obligations attach to roles, not to technology. For any AI use in the company, the two questions are: what is the company's role for this system, and what is the system's intended purpose?

#### The digital omnibus: what changed

The Commission proposed the AI omnibus on 19 November 2025. Parliament and Council reached political agreement on 7 May 2026 [verified, S3]. A&O Shearman dates the trilogue deal to 6 May with COREPER confirmation on 13 May [secondary, S13]. Parliament endorsed it on 16 June and the Council adopted it on 29 June [secondary, S16; Council date S60]. It was signed on 8 July, published on 24 July and entered into force on 27 July 2026 [verified, S2, S3]. It amends the AI Act, the Machinery Regulation (EU) 2023/1230 and the civil aviation Regulation (EU) 2018/1139 [verified, S2, S12].

The first draft of this file could not render the Official Journal text. A verification pass on 15 October 2026 read it on EUR-Lex, and rows tagged S2 below were checked against it. The rest combines the Commission's own pages with law-firm summaries that agree with each other.

| Change | Before | After | What it means for the company | Tag |
|---|---|---|---|---|
| Annex III high-risk start | 2 Aug 2026 | 2 Dec 2027, a fixed date. The Commission's "wait for standards" mechanism was dropped | More time, but no further slippage to count on | [verified, S2, S3] |
| Annex I high-risk start | 2 Aug 2027 | 2 Aug 2028 | Product-embedded AI | [verified, S2, S3] |
| Article 4 AI literacy | "ensure, to their best extent, a sufficient level" | "take measures to support the development of AI literacy". No guaranteed level for any person. Commission and Member States support providers and deployers, and the Commission publishes practical examples. AI Board issues recommendations | Still binding. Evidence of measures matters more than test scores | [verified, S2, S5] |
| Article 50(2) machine-readable marking | 2 Aug 2026 | Systems placed on the market before 2 Aug 2026 get until 2 Dec 2026 (new Article 111(4)). I found no amendment to Article 50 itself in the text | Matters if the company ships generative features | [verified, S2] |
| New prohibitions, Article 5(1)(ba) and (bb) | none | Systems generating non-consensual intimate imagery, and systems generating CSAM, from 2 Dec 2026 | Belongs in the permitted-use list | [verified, S2 Art. 113] |
| "Safety component", Article 3(14) | broad | Safety function means an intended purpose of preventing or mitigating risks to health and safety. Optimisation, efficiency, automation and convenience are excluded. A system still counts if its failure or malfunction endangers health or safety | Central to classifying an AI product that optimises building systems | [verified, S2; exclusion list S8 para 37] |
| Machinery | Annex I Section A | Moved to Section B. Requirements arrive through Machinery Regulation delegated acts, applying from 2 Aug 2028 | HVAC equipment is machinery | [verified, S12] |
| Registration of Article 6(3) exemptions | Commission proposed deleting it | Kept, with simplified content | Only matters if the company uses the 6(3) filter | [verified, S2 recital 22] |
| Small mid-caps | SME relief only | SME relief extended to small mid-caps as defined in Recommendation (EU) 2025/1099: under 750 staff with turnover up to EUR 150m or balance sheet up to EUR 129m | | [definition reference verified, S2; thresholds secondary, S15] |
| Other changes | | New Article 4a allows special-category data for bias detection in high-risk systems under strict necessity [verified, S2]. Article 25 requires written agreements along high-risk value chains. The AI Office gets exclusive supervision of systems built on a GPAI model by the same provider. CRA-compliant high-risk systems are presumed to meet Article 15 | Little effect unless a product becomes high-risk | [secondary, S13, S16, except where marked] |
| Unchanged | | GPAI obligations, the prohibitions timeline, Article 50 apart from the 50(2) grace period | | [secondary, S16] |

#### Roles: provider, deployer, and where the company sits

- A **provider** develops an AI system, or has one developed, and places it on the market or puts it into service under its own name, paid or free (Article 3(3)) [verified, text, S1].
- A **deployer** uses an AI system under its authority in a professional context (Article 3(4)).
- **Putting into service** includes supplying a system for first use to the provider's own organisation (Article 3(11)). So building an internal tool on a model API makes the company the provider of that tool.
- Under **Article 25**, a deployer or other party becomes the provider of a high-risk system if it puts its name on it, substantially modifies it, or changes its purpose so that it becomes high-risk.
- A company that **fine-tunes** a general-purpose model becomes a GPAI model provider only if the modification uses more than a third of the original model's training compute. That threshold comes from the Commission's GPAI guidelines of 18 July 2025 [verified, text, S58]. Prompting, retrieval and light fine-tuning keep the company a downstream system provider.

| Activity | The company's role | Main AI Act duties today |
|---|---|---|
| AI products the company sells to customers | Provider. Customers are deployers | Article 4. Classification record. Article 50 if a feature talks to people or generates content |
| LLM features in those products built on Claude or OpenAI APIs | Provider of the AI system. Anthropic or OpenAI provide the GPAI model | As above, and request the model provider's downstream documentation |
| An agentic development factory built in-house | Provider and deployer of an AI system | Article 4. Keep it away from listed uses such as worker evaluation |
| Staff using ChatGPT, Claude, Copilot or Cursor as bought | Deployer | Article 4 and Article 5. Article 50(4) only if staff publish deepfakes or unreviewed AI text on matters of public interest |
| An AI recruitment or performance tool bought by HR | Deployer of a high-risk system from 2 Dec 2027 | Article 26 deployer duties, including informing workers' representatives and affected workers before use |

#### Prohibited practices (Article 5, in force since 2 February 2025)

The eight original bans cover: manipulative or deceptive techniques that distort behaviour and cause significant harm; exploiting vulnerabilities due to age, disability or social or economic situation; social scoring; predicting criminal offences from profiling alone; untargeted scraping of facial images to build recognition databases; emotion recognition in workplaces and education, except for medical or safety reasons; biometric categorisation that infers sensitive traits such as race, politics or sexual orientation; and real-time remote biometric identification in public spaces for law enforcement, with narrow exceptions [verified, text, S1]. The Commission published interpretive guidelines on 4 February 2025 [S57]. The omnibus adds two bans from 2 December 2026, Article 5(1)(ba) and (bb), on systems that generate non-consensual intimate imagery or child sexual abuse material [verified, S2, S3].

Two points stand out. First, emotion recognition in the workplace. Meeting tools that score attendees' sentiment from voice or video, if switched on for staff, would be a banned deployment [inferred]. Second, a building product runs in customers' buildings, which are often workplaces. A future "occupant mood" or "wellbeing" feature in a building product that infers emotions from cameras, voice or other biometric signals in an office would put both the company, as provider, and the customer, as deployer, inside the ban [inferred]. Comfort votes typed into an app are not biometric data and fall outside the ban.

Fines for Article 5 breaches reach EUR 35 million or 7% of worldwide turnover. For SMEs the lower of the two applies [verified, S4].

#### Article 4: AI literacy

Article 4 as amended requires providers and deployers to take measures to support the development of AI literacy among their staff and other persons dealing with the operation and use of AI systems on their behalf. The measures account for people's technical knowledge, experience, education and training, the context of use, and the persons on whom the systems are used. The text states that it does not require guaranteeing any specific level of AI literacy of any individual [verified, S2].

The Commission's Q&A, updated 27 July 2026 [verified, S5], says:

- It applies to staff and to others acting on the organisation's behalf, such as contractors and service providers.
- No certificate is needed. No single training format is mandated. Relying only on a system's instructions for use is usually not enough.
- Internal records of training and other initiatives are an adequate form of documentation. No AI officer or AI board is required.
- The obligation covers ordinary tool use. The Q&A's own example is a company whose staff use ChatGPT for advertising copy or translation, which should tell them about risks such as hallucinations.
- National market surveillance authorities supervise and enforce it from 2 August 2026. The page also says 3 August in one place.
- Penalties come from national law and should be proportionate. Incidents linked to inadequate staff training may weigh more.

One commentator argues the rewrite is stricter in one respect: it drops "to their best extent", and Commission guidance rather than each company's self-assessment will shape what counts as enough [secondary, S17].

Article 4 is not among the obligations with fixed fine ceilings in Article 99(4) [verified, text, S1]. Fines therefore depend on national penalty rules, and Estonia has not adopted any [secondary, S19]. The practical risk today is indirect. Poor literacy would count against the company in any incident investigation, and customers already ask for training evidence [inferred].

What good evidence looks like [inferred]:

- A role matrix covering engineers, data scientists, customer success, sales, finance and HR, and management. For each role it records which AI tools the role uses, the risks that matter for it, and the training given.
- Versioned training materials, with dates and attendance per person.
- Acknowledgement of the permitted-use policy.
- Contractor onboarding covered too.
- A yearly review date, plus a trigger to retrain when a new tool or agent capability arrives.

#### What a company must do when it merely uses ChatGPT, Claude, Copilot or coding agents

AI Act duties for plain internal use, assuming no listed use case:

1. Article 4 literacy measures with records.
2. No Article 5 practices.
3. Article 50(4) disclosure only if the company publishes deepfakes, or publishes AI-generated text informing the public on matters of public interest without human editorial review [verified, text, S1; S9].
4. Nothing else. No registration, no conformity assessment, no AI Act incident reporting.

The heavier duties for the same use come from other law. GDPR applies to any personal data typed, pasted or exposed through tools (section 2.3). Trade secrets and customer confidentiality clauses apply to customer data and to credentials for customer systems such as a BMS. NIS2 and KüTS apply if the company is in scope (section 2.4). Customers' contracts may restrict subprocessors.

Uses that change the classification and need a policy rule [inferred]:

| If staff use AI to... | Then |
|---|---|
| Screen, rank or filter job applicants | Annex III point 4(a). High-risk deployment from 2 Dec 2027, plus GDPR Article 22 questions |
| Score or monitor individual employees, for example per-engineer productivity from agent telemetry sent to managers | Annex III point 4(b). The draft guidelines treat systematic monitoring "or" evaluation as enough, including during probation [verified, S7 paras 274-275]. Tools that flag issues only to the worker are not high-risk [verified, S7 para 278] |
| Infer staff emotions from voice, face or other biometrics | Banned, Article 5(1)(f) |
| Run a customer-facing chatbot | Article 50(1) disclosure duty on the company as provider |
| Publish AI-generated images of real buildings or people that look authentic | Possibly a deepfake under Article 50(4) |

#### General-purpose AI models and the code of practice

Chapter V binds providers of general-purpose AI models: documentation for the AI Office and for downstream providers, a copyright policy, and a public summary of training content. Models with systemic risk also carry evaluation, incident reporting and cybersecurity duties. These obligations have applied since 2 August 2025, models placed on the market before that date have until 2 August 2027, and Commission fines for GPAI providers apply from 2 August 2026 [verified, S3, S4; legacy date verified, text, S1 Art. 111(3)].

The GPAI Code of Practice, published 10 July 2025, has chapters on Transparency, Copyright, and Safety and Security. The Commission and AI Board found it an adequate voluntary tool. Signatories include Amazon, Anthropic, Google, IBM, Microsoft, Mistral AI and OpenAI. xAI signed only the Safety and Security chapter [verified, S10].

The code binds model providers that sign it, not downstream companies. A downstream company has two practical interests in it:

- **Downstream documentation.** As a downstream provider, the company is entitled to information that lets it understand the model's capabilities and limits (Article 53(1)(b) and Annex XII) [verified, text, S1]. Ask vendors for it and file it as evidence for customer questionnaires.
- **Vendor selection.** Signatory status is a cheap due-diligence signal for the vendor register. `05-controlled-ai-architecture.md` covers the vendors themselves.

#### Article 50 transparency (from 2 August 2026)

| Paragraph | Who | Duty | Example |
|---|---|---|---|
| 50(1) | Provider | Design systems that interact directly with people so users know it is AI, unless obvious from context | A chat assistant in the customer portal |
| 50(2) | Provider of systems generating synthetic audio, image, video or text | Machine-readable marking so outputs are detectable. Exemption for assistive editing that does not substantially alter input | LLM-drafted report text, if generated by the company's own system |
| 50(3) | Deployer of emotion recognition or biometric categorisation | Inform people exposed | |
| 50(4) | Deployer | Disclose deepfakes, and AI-generated text published to inform the public on matters of public interest unless human-reviewed | Marketing imagery, public ESG claims |

Source: [verified, text, S1; verified, S9]. The Commission published Article 50 guidelines on 20 July 2026 [verified, S9]. A Code of Practice on transparency of AI-generated content came out earlier, on 10 June 2026, with a provider section on marking and a deployer section on labelling. Anthropic, Google, Microsoft and OpenAI are among the reported provider signatories [secondary, S59, S11]. Fines for Article 50 breaches reach EUR 15 million or 3% [verified, S4].

How Article 50(2) applies to text produced by a system that sits on top of a vendor's model is not settled. The provider of the "system" carries the duty, and marking text is technically weak. Treat it as an open question for the first generative feature the company ships [inferred].

#### How high-risk classification works

There are two routes [verified, text, S1]:

1. **Article 6(1), product route.** The AI system is a safety component of a product, or is itself a product, covered by the EU harmonisation laws in Annex I, and that product must undergo third-party conformity assessment. Applies from 2 August 2028.
2. **Article 6(2), use-case route.** The system's intended purpose falls in a use case listed in Annex III. Applies from 2 December 2027.

**Safety component.** After the omnibus, a system is a safety component in either of two cases. Its intended purpose, set by the provider, may be to prevent or mitigate risks to health and safety of persons or property. Or its failure or malfunction may endanger health and safety [verified, S2; S8 paras 37-46]. The Commission's draft gives one table: the first limb is intent-based and the provider controls it, the second is consequence-based and depends on failure modes and architecture [verified, S8 para 44]. It lists "performance optimisation, service efficiency, automation, comfort, convenience" as not safety functions [verified, S8].

**Article 6(3) filter.** A system inside an Annex III use case escapes high-risk status if it performs a narrow procedural task, improves a completed human activity, detects deviations from prior decision patterns without replacing human review, or performs a preparatory task. Profiling always stays high-risk. The provider must document the assessment before placing the system on the market and register it in the EU database [verified, S7 paras 84-116]. The filter does not apply to the Article 6(1) route.

**Complex and agentic systems.** The draft says split architectures are assessed as a whole. Where linked components or agents jointly serve a high-risk purpose, an exempt module does not make the whole exempt [verified, S7 paras 75, 90].

**Human in the loop does not change classification.** Adding a human review step cannot by itself move a system out of high-risk. Only the intended purpose decides [verified, S7 para 70-71].

#### Worked example: could an HVAC optimisation product be high-risk? An analysis, not legal advice

Everything in this subsection is my interpretation [inferred] of the draft guidelines [S7, S8], which the Commission has not finalised. I found no final version as of 15 October 2026. The Commission's page, last updated 23 July 2026, still lists only the draft, and commentators expect final guidelines by the end of 2026 [verified for the page, S6; timing secondary]. Get counsel to review before anything here goes into a contract or customer answer.

**Is a building-optimisation product an "AI system" at all?** Probably yes, if it takes building data and infers setpoints that influence a physical environment, which matches Article 3(1). The Commission's February 2025 definition guidelines exclude some optimisation methods [S57], and a pure physics model with fixed control rules might sit outside. If the vendor markets the product as AI, the safer course is to assume it is one.

**Annex III point 2, critical infrastructure.** The text covers "AI systems intended to be used as safety components in the management and operation of critical digital infrastructure, road traffic, or in the supply of water, gas, heating or electricity" [verified, S1 via S7]. The draft guidelines set two conditions. First, the system must be a safety component. Second, its use must concern one of those sectors [verified, S7 section 3.2.1, the unnumbered text after para 179]. On the first condition, the draft says systems that are "merely supportive, informational, organisational or optimisation-oriented" are excluded [verified, S7 para 181]. On the second, it reads the reference to the CER Directive as limiting the use case to systems used by an entity that a Member State has identified as a critical entity [verified, S7 para 190]. For heating, that means district heating or cooling operators [verified, S7 paras 201-202]. For electricity, the list includes electricity market participants providing demand response and aggregation [verified, S7 para 203].

The CER-entity reading is narrow, and it is the Commission's draft view, not the Court's. Expect argument over it [inferred].

| Product type | Annex III point 2 | Annex I route | My reading |
|---|---|---|---|
| **HVAC optimisation**, AI that writes setpoints to HVAC systems in commercial buildings | A commercial building consumes heat rather than supplying it. Commercial building customers are usually not CER critical entities in the heating sense. Intended purpose is energy and comfort optimisation, not a safety function | Assumes the vendor does not place the product on the market as a component of an Annex I product requiring third-party assessment. The draft's own example says a smart thermostat optimising temperature is not a safety component where failure means "only discomfort or higher energy bills" [verified, S8 para 49] | Not high-risk, provided the failure limb is closed (below) |

Related product types follow the same reading [inferred]. Under point 2, maintenance detection counts as a safety function only inside critical infrastructure [verified, S7 para 184(b)], so fault detection for ordinary buildings is not high-risk; revisit it for a CER-identified district heating operator. Analytics with no safety function is minimal-risk, with Article 50 if generative features are added. Demand response sits inside the sector list when the operator is an electricity market participant, but the draft classifies an AI forecasting 15-minute grid imbalances as not high-risk for lack of a direct link to harm, and grid optimisation by demand forecasting is out too [verified, S7 para 206 examples]. Of these, demand response is the closest call.

**The failure-mode limb is the real risk for HVAC optimisation.** Ask what happens if the product writes wrong setpoints for a day. If the worst outcome is discomfort and a higher bill, the limb is not met. If the product can affect frost protection of coils, server room cooling, minimum ventilation rates, smoke control, or domestic hot water temperatures, where low temperatures raise Legionella risk, then a malfunction could endanger health or property. The draft says to consider whether "a redundant or backup system" directly protects against the harm [verified, S7 para 186]. The answer is an engineering document [inferred]:

- A **control envelope**: the exact list of BMS points the product writes, with the bounds it may write within.
- **Excluded points**: safety-relevant functions the product never touches.
- **BMS-side interlocks**: frost protection, high limits, fire and smoke modes, minimum outdoor air. These stay independent of the product, and the document says how the team knows.
- **Fallback behaviour**: what the BMS does when the product is offline or sends out-of-range values.

That document is also good engineering, and customers' security teams will ask for most of it anyway.

**What would change the answer** [inferred]:

- The product takes over a protective function, such as frost protection logic or smoke control.
- The vendor sells to district heating operators or grid operators that are CER-identified critical entities, and the AI acts on safety-relevant operation.
- A demand response product moves from forecasting and bidding to AI-driven real-time activation in frequency reserves, where it acts directly on grid stability.
- The final guidelines drop the CER-entity reading.
- A ruling or the AI Board takes a wider view of "safety component".

**Consequences of a "not high-risk" conclusion.** Being outside Annex III altogether is different from being inside and exempt under Article 6(3). Only the 6(3) route triggers documentation and registration duties. Even so, the vendor should keep a written classification record per product, dated and reviewed, because market surveillance authorities can evaluate classification under Article 80 [verified, S7 para 117] and customers will ask [inferred].

#### Estonia: national implementation

- Estonia has **no standalone AI law**. The AI Act applies directly. A short implementing act naming supervisors and setting the fine procedure was still in preparation in August 2026 [secondary, S19]. [unconfirmed] Its status in October 2026 could not be checked.
- **TTJA**, the Consumer Protection and Technical Regulatory Authority, is the intended market surveillance authority and single point of contact. The Commission's list, updated 7 September 2026, marks Estonia's designation as still pending final adoption [verified, S61]. A cooperation agreement between the Ministry of Justice and Digital Affairs and TTJA assigns TTJA the AI Act and Cyber Resilience Act supervision work, including an AI sandbox [secondary, S20].
- **Article 77 fundamental-rights bodies** with access rights are AKI, the Gender Equality and Equal Treatment Commissioner, and TTJA [secondary, S19].
- The **Ministry of Justice and Digital Affairs** published guidance on the Article 50 obligations around 5 August 2026 and cited fines of up to EUR 15 million or 3% [secondary, S19, S21].
- **No notifying authority** for conformity assessment bodies was identifiable in the sources I found. This matters only for high-risk products.

### 2.3 GDPR and AI

#### When building data is personal data

Temperatures, setpoints and valve positions about a 10,000 m² office floor are not personal data. They become personal data when they can be linked to an identifiable person, directly or indirectly (GDPR Article 4(1)) [verified, text, S56]. AKI's May 2026 letter makes the same point about indirect identifiability: a person can be identifiable from facts and descriptions even without a name [verified, S30]. Examples [inferred]:

- Room-level HVAC and occupancy data in hotels, joined with a property management system that knows who stayed in which room. This reveals guests' presence and habits.
- Occupancy or CO2 data for single-person offices.
- Access-control, Wi-Fi presence or meeting-room booking feeds used to forecast occupancy.
- Support tickets, emails and call notes from customer staff, and anything staff paste into an AI tool.
- Employee data in HR, payroll and the development factory's telemetry.

#### EDPB Opinion 28/2024 on AI models

Adopted 17 December 2024 [verified, text, S25], it answers three questions:

1. **When is a model trained on personal data anonymous?** Case by case. Only if extracting personal data from the model, and getting it out through queries, are both very unlikely. Controllers must document why.
2. **Can legitimate interest be the legal basis for developing or deploying a model?** It can, if the three-step test passes: a legitimate interest, necessity, and a balancing test that weighs data subjects' reasonable expectations and the mitigations in place.
3. **What if the model was developed unlawfully?** That can affect the lawfulness of later use, depending on whether the model is anonymous and on the deployer's own diligence.

For a company building on AI this matters in two places. One is any model the company trains on personal data, such as occupancy or hotel data. The other is the diligence the company does on vendors' models [inferred].

#### Later EDPB material

- **April 2025:** a Support Pool of Experts report on privacy risks and mitigations in LLMs. Not an official EDPB position, but a useful risk catalogue [verified, S27].
- **February 2026:** EDPB-EDPS Joint Opinion 2/2026 on the digital omnibus. It says the proposed AI legitimate-interest clause, Article 88c, does not clarify the law and the three-step test still applies [secondary, via S29; source document S28].
- **July 2026:** draft Guidelines 02/2026 on anonymisation, open for consultation until 30 October 2026, with a companion Guidelines 03/2026 on web scraping for generative AI [secondary, S26]. The anonymisation draft takes a relative view of identifiability: the same dataset can be anonymous for one party and personal for another. It also says AI lowers the cost of re-identification, so anonymity claims need re-checking over time [secondary, S26]. If the company tells customers that "aggregated" data is anonymous, this draft is the test to apply.

#### DPIAs for AI tools

GDPR Article 35 requires a data protection impact assessment where processing is likely to result in high risk, particularly with new technologies [verified, text, S56]. The EDPB-endorsed criteria treat innovative technology, systematic monitoring, large scale, data matching and evaluation or scoring as risk indicators. Two or more usually means a DPIA. AKI publishes Estonia's own list of processing that requires one. I did not retrieve it in this research.

Likely DPIA triggers [inferred]:

- Any AI processing of employee data for evaluation, including factory telemetry. This is also where the AI Act high-risk question arises.
- Hotel or residential occupancy analytics at room level.
- Feeding customer support or sales conversations into an LLM tool at scale.
- A new US AI vendor processing personal data, where the DPIA works together with a transfer impact assessment.

Under the omnibus, a deployer's fundamental rights impact assessment can reuse DPIA content [secondary, S13]. That only matters for high-risk deployers.

#### Employees, customers, occupants

| Data subjects | Typical AI use | Main GDPR points [inferred] |
|---|---|---|
| Employees | Coding agents, chat tools, factory telemetry, HR tools | Legal basis is usually legitimate interest or contract. Transparency to staff. Proportionality of monitoring. Estonian employment law rules on monitoring. No per-person scoring without a DPIA |
| Customer staff | Support tickets, emails, CRM notes in AI tools | The company is controller for its own CRM and processor where it handles customer data under a DPA. Check the AI vendor is a listed subprocessor |
| Building occupants and hotel guests | Occupancy-linked optimisation, analytics | Usually the company is the customer's processor. Purpose limitation: no reuse for model training without a basis and contract cover. Minimise to zone level where possible |

#### International transfers to US AI vendors

- The **EU-US Data Privacy Framework** remains valid. The General Court dismissed the Latombe challenge on 3 September 2025, and the appeal, case C-703/25 P, was pending in September 2026 [secondary, S31].
- The US Supreme Court decided **Trump v. Slaughter** on 29 June 2026, overruling Humphrey's Executor and ending the FTC's structural independence, which the adequacy decision relies on. The EDPB wrote to the Commission on 31 July 2026 asking it to assess the effect. The Commission says it is analysing the ruling [secondary, S31].
- The PCLOB, another safeguard the decision relied on, has lacked a quorum since January 2025 [secondary, S31].
- Practical stance [secondary advice in S31; application inferred]: rely on the DPF where the vendor is certified, but sign SCCs in parallel with cascade wording that switches transfers to SCCs if the DPF falls. Keep transfer impact assessments current, and prefer EU data residency for anything sensitive. Vendor-specific residency options are in `05-controlled-ai-architecture.md`.

#### AKI, the Estonian Data Protection Inspectorate

I found no comprehensive AKI guide on AI. The most useful document is a notice letter dated 7 May 2026, number 2.2-10/26/1922-1, about a healthcare provider using ChatGPT [verified, S30]. Its points carry over to any company:

- Free consumer versions and business-licensed versions must be distinguished.
- A paid business plan does not by itself guarantee GDPR compliance. Before use, check the terms, the data processing addendum, retention and deletion, access, security measures and transfers.
- The two main risks AKI names are losing control of data, including transfers outside the EU, and use of inputs for model training.
- Thorough analysis and internal rules are needed before personal data goes into ChatGPT.
- Even short-lived audio recording for transcription counts as processing.

AKI's site has an AI and data protection section covering the omnibus and AI Act deadlines. AKI recruited staff in 2026 to supervise the AI Act and the Data Governance Act [secondary, search summary only].

#### Pending GDPR changes

The data half of the digital omnibus, which would amend GDPR including a legitimate-interest clause for AI, had no Council negotiating mandate and no Parliament committee vote as of September 2026. Trilogues had not started [secondary, S29]. In early October 2026 the Council presidency put a mandate text dated 2 October to member states' ambassadors. [unconfirmed] Whether they adopted it was not known on 15 October [secondary, S66]. Do not plan around it. If it passes, it would likely apply from 2027 at the earliest [secondary, S29].

#### DPO

GDPR Article 37 requires a data protection officer where core activities involve large-scale regular and systematic monitoring of individuals, among other cases [verified, text, S56]. Whether occupancy-linked optimisation across many hotels reaches that bar is a judgement call [inferred]. Check whether a DPO is required and, if one exists, involve them.

### 2.4 Cybersecurity law

#### NIS2 and Estonia's Cybersecurity Act (KüTS)

**Status.** Estonia transposed NIS2 by amending its Cybersecurity Act, Küberturvalisuse seadus, through bill 739 SE. The amendments entered into force on 1 January 2026 [secondary, S34, S36, S64; bill record S33]. [unconfirmed] The Riigikogu adoption date of 10 December 2025 was not found in a primary source. Estonia missed the EU deadline of 17 October 2024. RIA, the Information System Authority, which runs CERT-EE, supervises. The number of covered entities rose from about 3,500 to nearly 6,500 [secondary, S34].

**Key duties** [secondary, S35, S34; they match NIS2 Articles 20, 21, 23 and 34, verified, S38]:

- Notify RIA within three months of meeting the conditions. Entities in scope on 1 January 2026 had until 31 March 2026.
- Name at least one management board member who approves and oversees cybersecurity measures, under KüTS § 6¹. Otherwise the whole board carries it.
- Run risk-based security measures under KüTS § 7, normally to E-ITS, the Estonian standard. An ISO/IEC 27001 certificate can serve as the alternative if its scope covers the systems behind the regulated service.
- Report significant incidents under KüTS § 8: early warning within 24 hours, notification within 72 hours, final report within one month.
- Fines reach EUR 10 million or 2% of turnover for essential entities, and EUR 7 million or 1.4% for important entities.
- Some requirements have three-year transition periods.

**Is the company itself in scope? Size comes first.** NIS2 applies to entities in the sectors of its Annexes I and II that are at least medium-sized under Recommendation 2003/361/EC (Article 2(1)) [verified, S38]. Under that Recommendation an enterprise is small if it has fewer than 50 staff and its turnover or its balance sheet total is no more than EUR 10 million. So it is medium-sized or larger only with 50 or more staff, or with both turnover and balance sheet above EUR 10 million [verified, S62]. What to check:

- Staff: the Recommendation counts annual work units of employees, including staff the company employs abroad, from the last approved accounting year [verified, S62]. A LinkedIn headcount can include contractors and others and is not the legal measure.
- Turnover and balance sheet total, against the EUR 10 million thresholds above.
- Ownership: check the shareholder list. A holder at 25% or more can bring partner or linked enterprise figures into the count [inferred from S62].
- A change of size class takes effect only after the thresholds are crossed in two consecutive accounting years [verified, S62 Annex Art. 4(2)].

NIS2 would still apply regardless of size if the company were identified as a critical entity under the CER Directive (Article 2(3)), or if Estonia identified it under Article 2(2), for example as the sole provider of an essential service or because its disruption could cause significant systemic risk [verified, S38]. Counsel should confirm whether either applies. Estonian practice also looks at group size where IT governance is shared [secondary, S34]. A company that crosses a threshold in two consecutive accounting years changes size class, and the routes below can then apply.

Three routes into scope once a company passes the size test, each a question for counsel [inferred]:

1. **Energy sector, electricity.** NIS2 Annex I lists "Market participants as defined in Article 2, point (25), of Regulation (EU) 2019/943" providing aggregation, demand response or energy storage services as defined in Directive (EU) 2019/944 [verified, S38]. The same entities appear in the CER list the Commission quotes [verified, S7 para 203]. If a demand response operator bids flexibility into balancing or electricity markets as an independent aggregator or market participant, that operator sits in this sector. A software vendor that only supplies a partner aggregator is a supplier instead.
2. **ICT service management, B2B, as a managed service provider.** NIS2 defines an MSP as an entity providing installation, management, operation or maintenance of ICT products, networks, infrastructure, applications or other network and information systems, through assistance or active administration on customers' premises or remotely (Article 6(39)) [verified, S38]. Take a product that writes setpoints into customers' building automation systems around the clock. Whether that counts as "active administration" of a "network and information system" is arguable.
3. **Digital infrastructure, as a cloud computing service.** NIS2 recital 33 names SaaS as a cloud service model [verified, text, S38]. Most readings do not treat every SaaS product as a cloud computing service provider, but some national authorities read the term broadly.

If the company falls in routes 2 or 3, Commission Implementing Regulation (EU) 2024/2690 adds detailed technical requirements, including on supply chain security [verified, text, S23]. An ISO 27001 certificate is a strong start for any of the three routes. Its scope must cover the production systems behind the regulated service [inferred].

**Customers in scope and what flows to their suppliers.** Customers that are NIS2 entities, such as energy companies, district heating operators, research organisations and public bodies, must address "supply chain security" with their direct suppliers (Article 21(2)(d)). They must also consider each supplier's vulnerabilities, product quality and secure development practices (Article 21(3)) [verified, text, S38]. Under KüTS, outsourcing does not move responsibility to the supplier, and contracts should set contacts, escalation, evidence preservation and notice periods shorter than the statutory ones [secondary, S35]. Expect such customers to ask the company about remote BMS access, credentials, secure development including AI-assisted development, and incident notification within hours [inferred]. Hotels and property companies are not NIS2 sectors as such. Banks fall under DORA instead.

**CER.** The Critical Entities Resilience Directive, (EU) 2022/2557, required Member States to identify critical entities by 17 July 2026 [verified, text, S39]. Estonia transposed it through its Emergency Act [secondary, S36]. CER status matters here mainly through the AI Act reading in section 2.2.

#### Cyber Resilience Act

Regulation (EU) 2024/2847 entered into force on 10 December 2024. Reporting obligations for actively exploited vulnerabilities and severe incidents apply from 11 September 2026, and the main obligations from 11 December 2027 [verified, S41]. Reporting runs through a single platform. Actively exploited vulnerabilities require an early warning within 24 hours, a notification within 72 hours, and a final report within 14 days of a fix being available. Severe incidents require 24 hours, 72 hours and one month [verified, text, S40]. The reporting duty also covers products placed on the market before December 2027 [verified, text, S40].

**Does the company make a "product with digital elements"?** The CRA does not cover pure SaaS. It does cover "remote data processing solutions": cloud processing designed by the manufacturer without which a product cannot perform one of its functions (Article 3(2), recital 12) [verified, text, S40]. The status of a SaaS product that writes to building systems therefore depends on how it connects to buildings [inferred]:

- If the company ships an on-site gateway, an edge box, or installable connector software, that is likely a product with digital elements. The product's cloud may then be its remote data processing solution, which brings both into scope, with reporting duties live since 11 September 2026.
- If the product reaches buildings only through cloud-to-cloud BMS APIs or customer-operated VPNs, with none of the company's software placed on site, it is more likely out of scope.

Check which applies, and get counsel to confirm. The omnibus links CRA compliance to AI Act Article 15 for high-risk systems [secondary, S13], which matters only if a product is ever high-risk.

#### DORA, as far as bank customers push it to suppliers

DORA, Regulation (EU) 2022/2554, has applied to EU financial entities since 17 January 2025 [verified, text, S42]. It does not regulate the company. It reaches the company through contracts [verified, text, S42; application inferred]:

- Banks must keep a register of all ICT third-party arrangements under Article 28(3). A SaaS product is an "ICT service", so a bank customer's register will likely include the company.
- Article 30(2) contract terms apply to all ICT services. They cover a description of the service, data processing locations, data protection, data access and return on exit, service levels, assistance during ICT incidents, cooperation with authorities, termination rights, and participation in the bank's security awareness programmes.
- Article 30(3) adds audit rights, exit plans and more for services supporting "critical or important functions". Building HVAC optimisation is unlikely to qualify, but the bank decides.

Expect bank questionnaires to ask how the company uses AI in development and operations, where data goes, and how AI vendors appear in its subcontracting chain [inferred].

#### Data Act, in one paragraph

The Data Act has applied since 12 September 2025 [verified, S43]. It gives users of connected products and related services, which arguably includes building owners using a building-optimisation service with their BMS, rights to access and share the data generated. Its switching rules for data processing services, SaaS included in my reading, require ending switching charges by 12 January 2027 [verified, text, S32; SaaS reading inferred]. It is outside this file's scope, but it will surface in the same customer contracts.

### 2.5 Standards

#### ISO/IEC 42001:2023, AI management system

Published December 2023 [verified, text, S44]. It is a certifiable management system standard. It tells an organisation how to run AI responsibly, as a repeatable system. It does not specify technical model requirements. It uses the same harmonized clause structure as ISO/IEC 27001 and ISO 9001, so most of clauses 4 to 10 will look familiar to whoever runs an ISMS. iso.org blocked automated access during this research. The clause and control content below comes from the standard as published [verified, text].

**Clauses 4 to 10**

| Clause | What it requires | What it looks like in practice [inferred] |
|---|---|---|
| 4 Context | 4.1 internal and external issues, including the organisation's AI roles. 4.2 interested parties and their requirements. 4.3 scope of the AIMS. 4.4 establish the system | Scope statement: the company's AI products, the factory, internal AI tool use. Interested parties: customers, AKI, TTJA, RIA, employees, partners |
| 5 Leadership | 5.1 top management commitment. 5.2 AI policy. 5.3 roles, responsibilities, authorities | CEO or CTO signs the AI policy. Owner named. Clear decision rights for approving new AI uses |
| 6 Planning | 6.1.2 AI risk assessment. 6.1.3 AI risk treatment and a Statement of Applicability against Annex A. 6.1.4 AI system impact assessment on individuals, groups and society. 6.2 measurable AI objectives. 6.3 planning changes | AI risk register extending the ISMS register. SoA listing all 38 Annex A controls. Impact assessments for each AI product and the factory |
| 7 Support | 7.1 resources. 7.2 competence. 7.3 awareness. 7.4 communication. 7.5 documented information | The Article 4 literacy record is the competence evidence. Policy acknowledgements. Document control |
| 8 Operation | 8.1 operational planning and control. 8.2 risk assessments at planned intervals and on change. 8.3 implement risk treatment. 8.4 impact assessments at planned intervals and on change | Change gates in CI/CD for AI features. Model or vendor change triggers reassessment |
| 9 Performance evaluation | 9.1 monitoring, measurement, analysis. 9.2 internal audit. 9.3 management review | AI metrics dashboard. Yearly internal audit. Quarterly or half-yearly review with leadership |
| 10 Improvement | 10.1 continual improvement. 10.2 nonconformity and corrective action | Incident and near-miss log feeding the improvement plan |

**Annex A controls.** There are 38 controls under 9 objectives. Annex A is normative, and Clause 6.1.3 requires the organisation to decide which controls apply and to justify exclusions in the Statement of Applicability.

| Objective | Controls | Plain meaning, with an example [inferred] |
|---|---|---|
| A.2 Policies related to AI | A.2.2 AI policy. A.2.3 alignment with other policies. A.2.4 review of the AI policy | One AI policy, consistent with the ISMS, privacy and HR policies, reviewed yearly |
| A.3 Internal organization | A.3.2 AI roles and responsibilities. A.3.3 reporting of concerns | RACI for AI decisions. A channel for staff to report AI concerns without blame |
| A.4 Resources for AI systems | A.4.2 resource documentation. A.4.3 data resources. A.4.4 tooling resources. A.4.5 system and computing resources. A.4.6 human resources | Inventory of models, datasets, tools, compute and the people who run them. This is the AI system inventory |
| A.5 Assessing impacts of AI systems | A.5.2 impact assessment process. A.5.3 documentation of assessments. A.5.4 impact on individuals or groups. A.5.5 societal impacts | Impact assessment template. A building optimiser's occupant comfort and energy impacts. The factory's effect on staff |
| A.6 AI system life cycle | A.6.1.2 objectives for responsible development. A.6.1.3 processes for responsible design and development. A.6.2.2 requirements and specification. A.6.2.3 design and development documentation. A.6.2.4 verification and validation. A.6.2.5 deployment. A.6.2.6 operation and monitoring. A.6.2.7 technical documentation. A.6.2.8 event logs | Engineering practice for AI features: specs, evals, release gates, monitoring, logs. The factory's CI/CD carries most of this |
| A.7 Data for AI systems | A.7.2 data for development and enhancement. A.7.3 acquisition of data. A.7.4 quality of data. A.7.5 data provenance. A.7.6 data preparation | BMS data quality (sensor readings, point labels), mislabeled datapoints, lineage, rights to use customer data for training |
| A.8 Information for interested parties | A.8.2 system documentation and information for users. A.8.3 external reporting. A.8.4 communication of incidents. A.8.5 information for interested parties | Customer-facing docs on what each AI product does and its limits. A way for customers to report AI problems. Incident notices |
| A.9 Use of AI systems | A.9.2 processes for responsible use. A.9.3 objectives for responsible use. A.9.4 intended use | The permitted-use rules for staff tools and agents. Intended-use statements per system |
| A.10 Third-party and customer relationships | A.10.2 allocating responsibilities. A.10.3 suppliers. A.10.4 customers | AI vendor due diligence and contracts. Customer contract clauses on AI |

**Annex B** gives implementation guidance for each Annex A control. Annex B is where to look when a control's one-line wording is unclear. **Annex C** lists potential AI objectives, such as accountability, fairness, privacy, robustness, safety, security, transparency and explainability, and risk sources, such as level of automation, lack of transparency, machine learning specifics, life cycle issues and technology readiness. **Annex D** covers use across sectors and integration with other management systems [verified, text, S44].

**Certification.** An accredited certification body audits in two stages. Stage 1 reviews documentation and readiness. Stage 2 checks that the system operates, with records. A certificate lasts three years, with yearly surveillance audits. ISO/IEC 42006:2025 sets the extra requirements for bodies certifying 42001, including auditor competence and audit time [verified, text, S47]. Several large certification bodies offer 42001 audits. Check each one's accreditation for 42001 specifically [inferred]. If the company already has an ISO 27001 certification body, that body is a natural first call.

**Effort and cost.** I found no reliable public figure for a company of 50 to 100 people. With an ISO 27001 ISMS already running, my planning estimate is [inferred]: a 2 to 3 week gap assessment; 4 to 8 months of part-time implementation by one owner plus engineering input; at least one internal audit and one management review cycle before Stage 2; a certificate 9 to 12 months after starting. Audit fees depend on audit days, which ISO/IEC 42006 ties to headcount and complexity. Get two or three quotes, and ask for an integrated 27001 and 42001 audit.

**Integration with ISO/IEC 27001.** Clauses 4 to 10 match, so a company can run one integrated management system: one management review, one internal audit programme, one risk methodology with an AI extension, one document control system [verified, text]. 27001's Annex A controls cover security of the systems around AI, and 42001's cover AI-specific concerns: impact, data provenance, intended use, life cycle. Many controls interlock. Logging appears in both, as does supplier management [inferred]. The cheapest path for a company with an ISMS is to extend the ISMS scope document and risk register rather than build a parallel system.

EN ISO/IEC 42001:2026 has reportedly been published as a European standard [secondary, S22]. It is not harmonised under the AI Act and gives no presumption of conformity.

#### Related ISO/IEC standards

| Standard | What it is | When to open it |
|---|---|---|
| ISO/IEC 22989:2022 | AI concepts and terminology, including stakeholder roles such as AI provider, producer, customer, partner and subject [verified, text, S48] | When writing definitions in the policy |
| ISO/IEC 23894:2023 | Guidance on AI risk management, adapting ISO 31000 [verified, text, S45] | When extending the ISMS risk method to AI |
| ISO/IEC 42005:2025 | Guidance on AI system impact assessment, supporting 42001 clause 6.1.4. Published May 2025, not certifiable [verified, text, S46; date secondary] | When designing the impact assessment template |
| ISO/IEC 42006:2025 | Requirements for bodies that audit and certify 42001. Published July 2025 [verified, text, S47; date secondary] | When choosing and challenging an auditor |

#### NIST AI RMF and companions

- **NIST AI RMF 1.0**, NIST AI 100-1, released 26 January 2023 [verified, S49, S50]. Voluntary. Four functions: Govern, the policies, roles and culture that run across everything; Map, the context, intended purpose and risks of a system; Measure, testing and metrics; Manage, prioritising, treating, monitoring and responding. NIST's page says the framework "is being revised as part of the White House AI Action Plan". No new version or date was given as of October 2026 [verified, S49].
- **Generative AI Profile**, NIST AI 600-1, released 26 July 2024 [verified, S49, S51]. Lists risks specific to generative AI, among them confabulation, information security, data privacy, intellectual property and value chain and component integration. It suggests actions mapped to the RMF subcategories. It is useful as a checklist for coding agents.
- **NIST SP 800-218A**, final 26 July 2024 [verified, S52]. Extends the Secure Software Development Framework with practices for producing AI models and systems that use them. Relevant to the factory's pipeline.
- **AI RMF profile for critical infrastructure.** NIST released a concept note on 7 April 2026 for a profile on trustworthy AI in critical infrastructure [verified, S49]. Worth watching for AI that sits close to energy systems, such as building optimisation and demand response.

NIST is US and voluntary. In the EU it is a crosswalk and shared vocabulary, not an obligation. Some US-headquartered customers, and the CSA AICM, map to it [verified, S53].

#### AI Act harmonised standards

CEN-CENELEC JTC 21 is writing the European standards that will give a presumption of conformity for high-risk systems once cited in the Official Journal. As of early September 2026 none was cited [secondary, S22].

- **EN 18286:2026**, quality management system for AI Act purposes, was published in July 2026 [secondary, S22]. It includes clause-level mappings to ISO 9001 and ISO/IEC 42001 [secondary, search summary].
- Other work items reported in 2025 cover AI risk management, a trustworthiness framework, cybersecurity, bias, data quality and conformity assessment. Their numbers were reported as prEN 18228, 18229, 18282, 18283, 18284 and 18285. These remained drafts [secondary; numbers not confirmed in this research].
- A draft Commission decision notified on 30 July 2026 would set 28 February 2027 as the deadline for delivering the standards and move the standardisation request's expiry to 31 August 2028 [secondary, S22].
- No Article 41 common specification exists [secondary, S22].

These matter only if a product becomes high-risk. EN 18286 is still worth skimming, because it shows what an EU regulator considers a good AI quality system.

### 2.6 Mapping AI governance areas to the frameworks

An AI governance programme reads like a management system. Here is the mapping. AI Act references assume the company's systems are not high-risk. High-risk articles are listed where they would apply, marked "if high-risk".

| Area | ISO/IEC 42001 | NIST AI RMF | AI Act and other law |
|---|---|---|---|
| **Security hardening** | 6.1.3, 8.3 risk treatment. A.4.4 tooling. A.4.5 computing resources. A.6.2.4 verification and validation. A.6.2.8 event logs. A.10.3 suppliers. Plus 27001 Annex A technological controls | GOVERN 6.1 third-party policies. MAP 4.1 third-party component risks. MEASURE 2.7 security and resilience. MANAGE 3.1 third-party risk monitoring | Art. 15 if high-risk. GDPR Art. 32 security. NIS2 Art. 21 and KüTS § 7 if in scope. CRA Annex I if the company ships a product. NIST SP 800-218A for the dev pipeline |
| **Acceptable use** | 5.2 and A.2.2 AI policy. A.9.2 responsible-use processes. A.9.4 intended use. A.3.2 roles | GOVERN 1.1 legal requirements understood. GOVERN 1.2 trustworthiness in policy. MAP 1.1 intended purpose | Art. 4 literacy. Art. 5 bans. Art. 50(4) deployer disclosures. Annex III point 4 boundaries for HR and worker monitoring. GDPR Arts. 5, 6, 28, 35 and Chapter V transfers |
| **Quality assurance** | 6.2 AI objectives. 8.1 operational control. A.6.1.2, A.6.2.2 to A.6.2.5 life cycle. A.7.4 data quality | MEASURE 1 metrics. MEASURE 2 trustworthiness evaluation. MAP 3 capabilities and costs | Art. 17 QMS and EN 18286 if high-risk. Contractual SLAs |
| **Monitoring** | 9.1 monitoring and measurement. A.6.2.6 operation and monitoring. A.6.2.8 event logs | MEASURE 2.4 production monitoring. MEASURE 3 tracking risks over time. MANAGE 4.1 post-deployment monitoring | Art. 12 logging, Art. 26(5) deployer monitoring, Art. 72 post-market monitoring, all if high-risk. GDPR accountability, Art. 5(2) |
| **Incident response** | 10.2 nonconformity and corrective action. A.3.3 reporting of concerns. A.8.4 communication of incidents | GOVERN 4.3 incident identification and information sharing. MANAGE 4.3 incidents communicated, response and recovery documented | Art. 73 serious incidents if high-risk. GDPR Arts. 33 and 34, 72 hours. KüTS § 8, 24 h, 72 h, 1 month, if in scope. CRA Art. 14, 24 h and 72 h, if the company ships a product. DORA assistance clauses for bank customers |
| **Continual improvement** | 9.2 internal audit. 9.3 management review. 10.1 continual improvement | GOVERN 1.5 periodic review. MANAGE 4.2 continual improvement | No general AI Act duty. Art. 72 if high-risk |
| Guidance for teams | 7.4 communication. A.3.2 roles. A.8.2 user information | GOVERN 2.1 roles. MAP 1 context | Art. 4. Art. 26 if high-risk deployment |
| Role-based training | 7.2 competence. 7.3 awareness. A.4.6 human resources | GOVERN 2.2 AI risk training | Art. 4, with records per the Commission Q&A |
| Documentation | 7.5 documented information. A.6.2.7 technical documentation. A.4.2 resource documentation | GOVERN 1.4 transparent policies. GOVERN 1.6 AI system inventory | Classification record per system. Art. 6(4) documentation and Art. 11 technical documentation if relevant |

NIST subcategory numbers are from AI RMF 1.0 [verified, text, S50]. Check them against the revised version when NIST publishes it.

### 2.7 Customer due diligence

**What customers ask.** Security questionnaires now carry AI sections. Common structures [verified for CSA, S53; others secondary or inferred]:

- **CSA AI Controls Matrix (AICM)**, released 9 July 2025, with v1.1 the current version. It has 243 control objectives across 18 domains, building on CSA's Cloud Controls Matrix. Its questionnaire, the **AI-CAIQ**, is used for self-assessment and vendor review. It maps to ISO 42001, ISO 27001, NIST AI RMF, NIST AI 600-1, BSI AIC4 and the EU AI Act. Under "STAR for AI Level 1", a vendor can publish its AI-CAIQ self-assessment on the CSA STAR Registry [verified, S53].
- **Shared Assessments SIG.** Recent versions include AI content. The site requires sign-in, and I could not verify the current AI question set [unverified, S54].
- **Bespoke bank and DORA questionnaires**, and university procurement forms, which often add AI Act and data-residency questions [inferred].

**Questions and the evidence that answers them** [inferred]:

| They ask | What they want to know | Evidence that answers it |
|---|---|---|
| Do you use AI in the product or service? Which models and vendors? | Hidden AI subprocessors | AI system inventory. Subprocessor list naming AI vendors. Architecture diagram |
| Is our data used to train models, yours or a vendor's? | Data reuse | Data flow diagram. Vendor terms with no-training clauses. The company's own training-data policy. DPA wording |
| Where is data processed and stored? | Transfers, DORA Art. 30 | Data location table per subprocessor. DPF certification and SCCs. TIA summary |
| How do you classify your AI under the EU AI Act? | Their own deployer risk | One-page classification record per product, counsel-reviewed |
| Do you have an AI policy and governance? | Maturity | AI policy, roles, literacy records, AIMS scope. ISO 42001 certificate if obtained |
| How do you test, monitor and change AI behaviour? | Quality, drift | Evaluation reports, release gates, monitoring dashboards, change log |
| What human oversight exists? Can we override the AI? | Control | Control envelope document. Override and fallback design |
| How do you handle AI incidents and notify us? | Incident clocks | Incident procedure with notification times. Past incident summary |
| Do developers use AI coding tools on our data or code? | Supply chain, secure development | Permitted-use policy for coding agents. Secret-scanning and repository controls. SP 800-218A alignment |
| Certifications? | Shortcut to trust | ISO/IEC 27001:2022. ISO/IEC 42001 if pursued. CSA STAR entry |

**Draft answer lines, needing counsel sign-off before use** [inferred]:

- "Based on our classification assessment dated [date], made against Article 6 of the AI Act and the Commission's draft guidelines of 19 May 2026, we consider [product] not a high-risk AI system. Its intended purpose is energy and comfort optimisation. Safety functions remain with the building automation system's own interlocks, which [product] does not write to. We will review this when the Commission finalises its guidelines."
- "Customer data is not used to train third-party models. Our AI vendors are contractually barred from training on it. [Describe the company's own use honestly.]"

### 2.8 When to involve a lawyer

The pattern: call counsel when a statement becomes a commitment to a third party, when a reporting clock may be running, or when employees' rights are involved [inferred].

- Before the company states in a contract, tender or questionnaire that a product is or is not high-risk.
- To confirm the NIS2 and KüTS size test (staff in annual work units, group figures, the two-year rule), then whether the company is in scope through demand response, managed services or cloud. If it is, confirm the RIA notification.
- To decide whether any on-site component the company ships is a CRA product.
- Before any AI use that evaluates, ranks or monitors employees or candidates, including factory telemetry.
- When negotiating AI clauses in customer contracts: DORA Article 30 terms, liability for AI output, training rights over customer data.
- When the DPF falls or a vendor changes its transfer basis.
- Within hours of any incident that may trigger GDPR, KüTS, CRA or contractual notification.
- Before launching an AI feature that interacts with the public or generates content at scale.

---

## 6. Reading list

**Must read, five items**

1. The AI Act as amended by Regulation (EU) 2026/1744, Articles 3, 4, 5, 6, 25, 26, 50, 99 and Annex III. Use a consolidated version from the AI Act Service Desk or EUR-Lex [S1, S2].
2. The Commission's draft guidelines on high-risk classification, 19 May 2026: the Annex I part, paras 37-49 on safety components, and the Annex III part, sections 2 and 3.2 on critical infrastructure and 3.4 on employment [S6, S7, S8].
3. The Commission's AI literacy Q&A, updated 27 July 2026 [S5].
4. ISO/IEC 42001:2023 itself: clauses 4 to 10, Annex A and Annex B. Buy it through EVS, the Estonian Centre for Standardisation and Accreditation, or ISO [S44].
5. EDPB Opinion 28/2024 on AI models [S25].

**Should read**

- Commission Article 50 guidelines, 20 July 2026 [S9], and the transparency Code of Practice [S11, S59].
- AKI letter of 7 May 2026 on general-purpose AI [S30].
- A&O Shearman, "Digital Omnibus on AI: the trilogue deal is done", June 2026, the clearest summary of what changed [S13, secondary].
- NIST AI RMF 1.0 and the Generative AI Profile [S50, S51].
- KüTS consolidated text on riigiteataja.ee, and Legiscope's 29 September 2026 overview [S35, secondary].
- CSA AI Controls Matrix and AI-CAIQ [S53].
- Faegre Drinker on Trump v. Slaughter and the DPF [S31, secondary].

**Reference**

- ISO/IEC 22989, 23894, 42005, 42006 [S45 to S48].
- NIST SP 800-218A [S52].
- NIS2 Directive, CER Directive, Implementing Regulation (EU) 2024/2690 [S38, S39, S23].
- Cyber Resilience Act and the Commission's CRA page [S40, S41]. Data Act [S32, S43].
- DORA Articles 28 to 30 [S42].
- EDPB draft Guidelines 02/2026 and 03/2026 [S26]. EDPB LLM risk report [S27].
- GPAI Code of Practice and GPAI guidelines [S10, S58].
- Commission guidelines on the AI system definition and on prohibited practices [S57].
- aiactinsight.eu harmonised standards tracker [S22, secondary].

---

## 8. Sources

Accessed 14 to 15 October 2026 unless stated. "Not reachable" means the host blocked automated access during this research.

1. Regulation (EU) 2024/1689, Artificial Intelligence Act. OJ L 12 July 2024. https://eur-lex.europa.eu/eli/reg/2024/1689/oj. Not reachable.
2. Regulation (EU) 2026/1744 of 8 July 2026, Digital Omnibus on AI. OJ 24 July 2026. https://eur-lex.europa.eu/eli/reg/2026/1744/oj. Did not render on 14 October; read on 15 October through https://eur-lex.europa.eu/legal-content/EN/TXT/HTML/?uri=OJ:L_202601744
3. European Commission, "AI Act" regulatory framework page, updated 3 August 2026. https://digital-strategy.ec.europa.eu/en/policies/regulatory-framework-ai
4. European Commission, "Navigating the AI Act" FAQ, updated 7 August 2026. https://digital-strategy.ec.europa.eu/en/faqs/navigating-ai-act
5. European Commission, "AI literacy: questions and answers", updated 27 July 2026. https://digital-strategy.ec.europa.eu/en/faqs/ai-literacy-questions-answers
6. European Commission, "Draft Commission guidelines on the classification of high-risk AI systems", 19 May 2026, updated 23 July 2026. https://digital-strategy.ec.europa.eu/en/library/draft-commission-guidelines-classification-high-risk-ai-systems
7. Draft guidelines, Annex III part, PDF. https://ec.europa.eu/newsroom/dae/redirection/document/128561. Read in full for sections 2, 3.2 and 3.4.
8. Draft guidelines, Annex I part, PDF. https://ec.europa.eu/newsroom/dae/redirection/document/128560. Read paras 37-50.
9. European Commission, "Commission publishes guidelines on transparency obligations...", 20 July 2026. https://digital-strategy.ec.europa.eu/en/news/commission-publishes-guidelines-transparency-obligations-providers-and-deployers-certain-ai-systems
10. European Commission, General-Purpose AI Code of Practice page. Code published 10 July 2025. https://digital-strategy.ec.europa.eu/en/policies/contents-code-gpai
11. European Commission, "Strong backing for Code of Practice on transparency of AI-generated content", about end of July 2026. https://digital-strategy.ec.europa.eu/en/news/strong-backing-code-practice-transparency-AI-generated-content. Seen through a search summary only.
12. UK Department for Business and Trade, Explanatory Memorandum on Regulation (EU) 2026/1744, 3 August 2026. https://www.niassembly.gov.uk/globalassets/committee-blocks/windsor-framework-democratic-scrutiny-committee/reg-eu-20261744/regulation-eu-2026_1744-em.pdf
13. A&O Shearman, "Digital Omnibus on AI: The trilogue deal is done", June 2026 [secondary]. https://edge.sitecorecloud.io/allenoveryllp1-aoshearmanwe0db-production-ecf3/media/project/aoshearman/pdf-downloads/insights/2026/06/ealert---digital-omnibus-on-ai---the-trilogue-deal-is-done.pdf
14. Gibson Dunn, "EU AI Act Omnibus Agreement", 27 May 2026 [secondary]. https://www.gibsondunn.com/eu-ai-act-omnibus-agreement-postponed-high-risk-deadlines-and-other-key-changes/
15. Orrick, "EU AI Act Update: Digital Omnibus Finalizes 8 Compliance Changes", 29 July 2026 [secondary]. https://www.orrick.com/en/Insights/2026/07/EU-AI-Act-Update-Digital-Omnibus-Finalizes-8-Compliance-Changes
16. K&L Gates, Cyber Law Watch, "EU Digital Omnibus on AI Enters Into Force", 31 July 2026 [secondary]. https://www.cyberlawwatch.com/2026/07/31/eu-digital-omnibus-on-ai-enters-into-force/
17. IAPP, "EU AI Act literacy changes may complicate more than simplify compliance", 23 September 2026 [secondary]. https://iapp.org/news/a/eu-ai-act-literacy-changes-may-complicate-more-than-simplify-compliance
18. artificialintelligenceact.eu, Article 4 page, amended text [secondary]. https://www.artificialintelligenceact.eu/article/4/
19. Praxikon, "AI Act enforcement in Estonia", last verified 10 August 2026 [secondary]. https://www.praxikon.com/en/enforcement/estonia
20. Ministry of Justice and Digital Affairs and TTJA, cooperation agreement amendment, on a third-party document host [secondary, seen through a search summary]. https://adr-docs.karlerss.com/VFB94aolL18mEPj8yd9KQfCoc78VLYNY/Lepingu_muutmise_kokkulepe_JDM-TTJA%20RUKT.pdf
21. ERR, "Euroopa Liit kehtestas nõuded tehisaru läbipaistvuse suurendamiseks", August 2026 [secondary, seen through a search summary]. https://www.err.ee/1610105020/euroopa-liit-kehtestas-nouded-tehisaru-labipaistvuse-suurendamiseks
22. aiactinsight.eu, "AI Act harmonised standards: where the presumption stands", updated 4 September 2026 [secondary]. https://aiactinsight.eu/en/standards/harmonised-standards/
23. Commission Implementing Regulation (EU) 2024/2690 on NIS2 technical and methodological requirements for digital infrastructure and ICT service providers. https://eur-lex.europa.eu/eli/reg_impl/2024/2690/oj. Not reachable.
24. Cloud Security Alliance, research note on the AI Act high-risk deadline, 1 August 2026 [secondary]. https://labs.cloudsecurityalliance.org/research/csa-research-note-eu-ai-act-high-risk-deadline-omnibus-20260/
25. EDPB, Opinion 28/2024 on certain data protection aspects related to the processing of personal data in the context of AI models, 17 December 2024. https://www.edpb.europa.eu/our-work-tools/our-documents/opinion-board-art-64/opinion-282024-certain-data-protection-aspects_en
26. EDPB, "EDPB sheds light on anonymisation and web scraping for generative AI...", July 2026. https://www.edpb.europa.eu/news/edpb-sheds-light-on-anonymisation-and-web-scraping-for-generative-ai-and-adopts-final-version_en. Content via search summaries and Sidley [secondary]: https://datamatters.sidley.com/2026/07/27/edpb-publishes-draft-guidelines-on-anonymisation/
27. EDPB Support Pool of Experts, "AI Privacy Risks & Mitigations: Large Language Models", April 2025. https://www.edpb.europa.eu/system/files/2025-04/ai-privacy-risks-and-mitigations-in-llms.pdf
28. EDPB-EDPS Joint Opinion 2/2026 on the Digital Omnibus proposal, February 2026. https://www.edpb.europa.eu/system/files/documents/2026-02/edpb_edps_jointopinion_202602_digitalomnibus_en.pdf. Not opened. Content via S29.
29. Acompli, "Digital Omnibus GDPR and cookie reforms stall without a Council mandate", September 2026 [secondary]. https://acompli.ie/news/digital-omnibus-gdpr-cookies-status-september-2026/
30. Andmekaitse Inspektsioon, "Tähelepanu juhtimine", letter 2.2-10/26/1922-1, 7 May 2026, read in full. https://www.aki.ee/sites/default/files/documents/2026-06/Tahelepanu%20juhtimine.%20%C3%9Cldotstarbelise%20tehisintellekti%20kasutamine%20tervishoius_0.pdf
31. Faegre Drinker, "Trump v. Slaughter: Implications... for EU-US Data Transfers and the Data Privacy Framework", 17 September 2026 [secondary]. https://www.faegredrinker.com/en/insights/publications/2026/9/trump-v-slaughter-implications-of-the-us-supreme-court-ruling-for-eu-us-data-transfers-and-the-data-privacy-framework
32. Regulation (EU) 2023/2854, Data Act. https://eur-lex.europa.eu/eli/reg/2023/2854/oj. Not reachable.
33. Riigikogu, bill page "Küberturvalisuse seaduse ja teiste seaduste muutmise seadus". https://www.riigikogu.ee/en/eelnoud/4429a2b9-e6e2-41cf-991d-f6955c6c4a69/kuberturvalisuse-seaduse-ja-teiste-seaduste-muutmise-seadus-kuberturvalisuse-2.-direktiivi-ulevotmine/. Draft-stage record.
34. Grant Thornton Baltic, "The Cybersecurity Act is in force, but the market is still looking for answers", 19 May 2026 [secondary]. https://www.grantthornton.ee/en/insights1/the-cybersecurity-act-is-in-force-but-the-market-is-still-looking-for-answers/
35. Legiscope, "NIS2 Eestis 2026: küberturvalisuse seadus ja RIA järelevalve", 29 September 2026 [secondary]. https://www.legiscope.com/blog/nis2-eestis.html
36. KPMG Estonia, "Küberturvalisuse ja hädaolukorra seaduse muudatused", February 2026 [secondary]. https://kpmg.com/ee/et/sundmused/2026/02/kueberturvalisuse-ja-haedaolukorra-seaduse-muudatused.html
37. OIXIO, "Uus KüTS (üle võetud NIS2 direktiiv)" [secondary, seen through a search summary]. https://oixio.eu/et/uudised/uus-kuts-ule-voetud-nis2-direktiiv-kaua-oodatud-vahe-igatsetud-kuid-nuud-valtimatu/
38. Directive (EU) 2022/2555, NIS2. https://eur-lex.europa.eu/eli/dir/2022/2555/oj. Not reachable on 14 October; Articles 2, 3, 6, 23, 34, recital 16 and Annex I read on 15 October through https://eur-lex.europa.eu/legal-content/EN/TXT/HTML/?uri=CELEX:32022L2555
39. Directive (EU) 2022/2557, CER. https://eur-lex.europa.eu/eli/dir/2022/2557/oj. Not reachable.
40. Regulation (EU) 2024/2847, Cyber Resilience Act. https://eur-lex.europa.eu/eli/reg/2024/2847/oj. Not reachable.
41. European Commission, Cyber Resilience Act page, updated 7 September 2026. https://digital-strategy.ec.europa.eu/en/policies/cyber-resilience-act
42. Regulation (EU) 2022/2554, DORA. https://eur-lex.europa.eu/eli/reg/2022/2554/oj. Not reachable.
43. European Commission, Data Act page, updated 2 July 2026. https://digital-strategy.ec.europa.eu/en/policies/data-act
44. ISO/IEC 42001:2023. https://www.iso.org/standard/81230.html. Not reachable.
45. ISO/IEC 23894:2023. https://www.iso.org/standard/77304.html. Not reachable.
46. ISO/IEC 42005:2025. https://www.iso.org/standard/42005. Returned 403.
47. ISO/IEC 42006:2025. https://www.iso.org/standard/42006. Returned 403.
48. ISO/IEC 22989:2022. https://www.iso.org/standard/74296.html. Not reachable.
49. NIST, AI Risk Management Framework page. https://www.nist.gov/itl/ai-risk-management-framework
50. NIST AI 100-1, AI RMF 1.0, 26 January 2023. https://doi.org/10.6028/NIST.AI.100-1
51. NIST AI 600-1, Generative AI Profile, 26 July 2024. https://doi.org/10.6028/NIST.AI.600-1
52. NIST SP 800-218A, 26 July 2024. https://csrc.nist.gov/pubs/sp/800/218/a/final
53. Cloud Security Alliance, AI Controls Matrix, released 9 July 2025, updated 30 October 2025. https://cloudsecurityalliance.org/artifacts/ai-controls-matrix
54. Shared Assessments, SIG. https://sharedassessments.org/sig/. Sign-in wall.
55. [removed: company-specific source]
56. Regulation (EU) 2016/679, GDPR. https://eur-lex.europa.eu/eli/reg/2016/679/oj. Not reachable.
57. European Commission, guidelines on the AI system definition, 6 February 2025, and on prohibited AI practices, 4 February 2025. https://digital-strategy.ec.europa.eu/en/library/commission-publishes-guidelines-ai-system-definition-facilitate-first-ai-acts-rules-application and https://digital-strategy.ec.europa.eu/en/library/commission-publishes-guidelines-prohibited-artificial-intelligence-ai-practices-defined-ai-act. Not re-opened.
58. European Commission, guidelines on the scope of obligations for providers of general-purpose AI models, 18 July 2025. https://digital-strategy.ec.europa.eu/en/library/guidelines-scope-obligations-providers-general-purpose-ai-models-under-ai-act. Not re-opened.
59. Jones Day, "European Commission publishes final Code of Practice on marking and labelling AI-generated content", June 2026 [secondary]. https://www.jonesday.com/en/insights/2026/06/european-commission-publishes-final-code-of-practice-on-marking-and-labelling-aigenerated-content
60. Council of the EU, timeline of simplification omnibus packages, entry "Council gives final green light to simplify and streamline AI rules", 29 June 2026. https://www.consilium.europa.eu/en/policies/simplification/timeline-simplification-omnibus-packages/. Seen through a search summary.
61. European Commission, "Market surveillance authorities under the AI Act", updated 7 September 2026. https://digital-strategy.ec.europa.eu/en/policies/market-surveillance-authorities-under-ai-act
62. Commission Recommendation 2003/361/EC on the definition of micro, small and medium-sized enterprises, Annex Articles 2 to 5. Read through the HMRC reproduction, https://www.gov.uk/hmrc-internal-manuals/corporate-intangibles-research-and-development-manual/cird92800 ; official text https://eur-lex.europa.eu/eli/reco/2003/361/oj
63. [removed: company-specific source]
64. WIDEN, "Kas sinu ettevõte läheb uue küberturvalisuse seaduse alla?", February 2026 [secondary]. https://widen.legal/wp-content/uploads/2026/02/KuTSi-kohaldumine-ettevotetele.pdf
65. [removed: company-specific source]
66. PPC Land, "EU Council draft cuts cookie re-prompt wait from 6 months to 4", October 2026 [secondary, seen through a search summary]. https://ppc.land/eu-council-draft-cuts-cookie-re-prompt-wait-from-6-months-to-4/
