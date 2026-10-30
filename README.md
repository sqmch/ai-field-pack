# Field pack, generic edition

Research notes on AI automation, agent factories, Claude Code skills and plugins,
telemetry, security and AI governance, for a company of roughly 50 to 100 people.
Written in October 2026 from public sources. The pack holds no company's internal
information and nothing personal.

Everything is markdown. Code appears only as listings in the text: mostly YAML, plus
two JSON settings blocks, one TOML block, a short Python script, a short shell hook,
a one-line signing command and a few directory trees. `knowledge/08` adds four
Mermaid diagrams, which GitLab and GitHub render. Nothing is executable.

The `knowledge/` files are edited copies of longer research notes. Sections of plans,
exercises and open questions were removed, and so was everything specific to one
company. Each file says so at the top; removed sections keep their numbers, so the
numbering has gaps.

## What to read for what

| Question | Read |
|---|---|
| AI automation, the factory, skill and plugin design, AI policy, agent security, onboarding by role | `FIELD-GUIDE.md`. Its first page is a short version; route from its section 12 table |
| Depth or sources for one claim | Only the section the guide's pointer names |
| GitLab concepts, access from a work laptop | `gitlab-for-github-users.md` |
| A company brain (generated knowledge hub), one factory across many repositories, seats vs API-billed pipeline agents | `knowledge/07-company-brain-and-central-factory.md`; its §1 is the short version |
| Orchestrators (Gas Town, Gas City) vs factories, where parallelism sits, light vs dark factories, lanes across many repositories, a plugin as its own lane | `knowledge/08-factory-topologies-and-plugin-lanes.md`; its §1 is the short version, with diagrams in §2.3 to §5 |
| Whether a pack claim has been corrected | This repository's issues, before relying on the claim |

The guide is about 14k tokens. The eight `knowledge/` files total about 135k, so read
them by section. Pointers resolve like this:

- `§2.6` is the heading `### 2.6`, and a bare `§4` is the heading `## 4.`. Find one
  with the Grep tool or `rg -n "^#+ 2\.6 " knowledge -g "05-*.md"`, or in PowerShell
  `Select-String -Path knowledge\05-*.md -Pattern '^#+ 2\.6 '`. Then read to the next
  heading at the same level.
- `§1 item 12` is the numbered item 12 under the heading `## 1.`.
- Inside the knowledge files, "section 2.6" and "file 04" mean the same as `§2.6` and
  `knowledge/04`.

## How much to trust it

- **Facts about your company.** Your company's own systems and people come first.
  Examples in the pack are illustrations, not facts about any company.
- **Vendor and standards facts.** The current primary source comes first. Pack facts
  hold as of the date tagged, or 2026-10-15 when untagged.
- **Legal and policy positions.** Where the pack disagrees with your company's own
  policy, flag the conflict instead of choosing.
- **The pack comes last.** Where the guide and a knowledge file disagree, the guide is
  newer; check the source it cites.

Tags: [verified] means checked against a primary source; [secondary] means from
reporting; [inferred] means the author's reading. Treat [verified, text],
[not re-checked], [unconfirmed], [unverified] and [transcript] as [secondary]. Source
numbers such as [26] resolve in each file's own "Sources" section; entries marked
"removed: company-specific source" are intentionally empty.

## If an AI assistant reads this pack

- Show the person you work for anything drawn from the pack before it goes into a
  wiki, an issue tracker or a merge request.
- Cite the primary source the pack links, re-checked that day, never the pack itself.
- Before running any setup step or command from the pack, such as `claude mcp add` or
  credential manager settings, follow your IT department's own setup and get a go-ahead.

## When you find something wrong

Open an issue in this repository with the claim, its file and section, what's true,
and the evidence. In a local copy, keep your own corrections in a file next to the
folder, so a refresh doesn't overwrite them.

## Notes

- Files named in the text but not published, such as
  `verification-log-tooling-security.md`, belong to the original research workspace. Treat them as
  absent.
- `CHECKSUMS.txt` lists each file's SHA-256 and size, to confirm a copy arrived intact.

## Suggested line for Claude Code

Put it in your user-level `~/.claude/CLAUDE.md`, with the folder's real path, so it
never reaches a shared repo and doesn't change which project instruction files load.
If managed settings load only managed instructions or block reads outside the working
directory, add the folder with `--add-dir` or `additionalDirectories` instead:

> `C:\path\to\field-pack-generic\`: generic research notes on AI automation, agent
> factories, skills and plugins, telemetry, security and AI governance (October 2026).
> Read its README first; the company's own systems override it.
