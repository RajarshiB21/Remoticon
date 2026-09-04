---
name: readme-update
description: Update a project's README to reflect newly added skills, subagents, or tools, with author credits and licenses that were verified against the original source, and the prose run through the unslop then humanizer pipeline. Use whenever the user adds a skill, subagent, or borrowed tool to a project and wants the README, its credits, or its license attributions brought up to date, or says the README is stale or out of date, even if they don't say the word "README". Also use when the user asks to credit an author, verify a license, or write up what lives in a project's .claude/ or .pi/ folder. Works on Claude Code projects (.claude/) and pi projects (.pi/) alike.
---

# README update

Bring a project's README into line with what the project actually contains: newly added skills, subagents, and borrowed tools, each credited to the right author under a license you confirmed rather than assumed. The README is the front door of a public repo, so a wrong license or a missing credit is a real problem, not a cosmetic one. This skill is deliberately general: it reads the project in front of it and works from what it finds, so it behaves the same in any repo without hardcoded names.

Two things make this skill worth using instead of freehand editing: it never states a license or an author it has not verified, and it runs every word of new prose through the unslop then humanizer pipeline so the README does not read like it was written by a machine.

## Two gates before any writing

These come first because getting them wrong is expensive. Do not skip ahead to drafting.

### Gate 1: the project must have a `.claude/` or a `.pi/` folder

A project can be a Claude Code project, a pi project, or both. Claude Code keeps its skills in `.claude/skills/` and its subagents in `.claude/agents/`. pi keeps its skills in `.pi/skills/` and has no dedicated subagent folder. Either layout tells you what the README should describe, so check the project root for both:

- **If `.claude/` exists**, treat it as the source of truth and continue to the inventory step. A `.pi/skills` that is only a link (a symlink or junction) pointing back at `.claude/skills` is the same content seen twice, not a second set: follow it to `.claude/skills` and count each skill once.
- **If `.claude/` does not exist but `.pi/` does** (with a real, non-linked `.pi/skills/`), this is a pi-specific project. Do not create a `.claude/` folder the project has no use for. First confirm with the user that this is a pi project you should document from `.pi/`, then inventory `.pi/skills/`. Remember pi has no subagent system, so expect skills only, no agents.
- **If neither `.claude/` nor `.pi/` exists**, stop. Do not guess what the project contains or invent a structure. Tell the user plainly that you found neither folder, and ask them where the skills, subagents, or tools live and what they want documented. Wait for their answer before doing anything else.

### Gate 2: never state a license or author you have not verified

A license and a copyright holder are legal facts. Guessing one, or copying a claim the old README already makes without checking it, is how a repo ends up asserting something false. If at any point you are unsure of a skill's origin, its author, or its license, stop and ask the user for the link to the original repository they downloaded it from. An honest "I could not confirm this" is always better than a confident guess.

## Workflow

### 1. Inventory what the project has, and what the README is missing

Read every `SKILL.md` in the project's skills folder (`.claude/skills/*/SKILL.md`, or `.pi/skills/*/SKILL.md` in a pi project) and, for a Claude Code project, every `.claude/agents/*.md`, to list every skill and subagent. Read the current README. The gap between the two is your work: skills or subagents that exist but are undocumented, credits that are missing, and any claim in the README that no longer matches the files (for example, a line saying "two skills" when there are now four, or "a LICENSE rides along" when a folder has none).

Separate the user's own work from borrowed work as you go. The user's own skills and subagents are credited as theirs; only work from someone else needs third-party attribution. Look for signals of origin: a `LICENSE` file inside the skill folder, a lock or manifest file at the project root (a `skills-lock.json` or similar) that names the source repo and path, or a note in the existing README.

### 2. Verify each borrowed skill's origin and license against the source

For every skill or tool that is not the user's own, confirm three facts before you write a single word of credit: the author, the license type, and the copyright year.

- Start from what the project already tells you: any `LICENSE` file in the folder, and any lock or manifest file that names the source repository.
- Then confirm it at the source. Do a **WebSearch** for the skill and its author, then **WebFetch** the original repository (its `LICENSE` file and the skill's folder) to read the license type, copyright holder, and year directly. Verify, do not trust: read the actual license text rather than repeating a claim.
- If the origin or license is unclear, or a WebSearch and WebFetch do not settle it, **stop and ask the user for the link to the original repo** they got the skill from. Do not fill the gap with a plausible-sounding license.

Check the project's own convention too. If every borrowed skill folder is supposed to carry its own `LICENSE` file and one is missing, flag it and offer to copy the correct license text into that folder, so the README's claim stays true.

### 3. Present your findings and get confirmation

Before writing, show the user what you found: each borrowed item, its author, its verified license and year, the source URL, and anything you could not confirm or any license file that is missing. This is the moment for the user to correct a wrong author or point you at the right repo. Wait for their confirmation.

### 4. Draft the README changes

Write the additions and fixes: a short entry for each new skill and subagent under whatever "what's here" section the README uses, updated credits with the verified license lines, and a corrected license note. Match the README's existing voice and structure rather than imposing a new one. Keep the user's own opinions and phrasing; you are updating their document, not rewriting it.

Describe each item by what it does, plainly. The user's own subagents and skills are marked as theirs.

### 5. Run the prose through the pipeline: unslop, then humanizer

Every piece of new or changed prose goes through two passes, in order: **unslop** first to strip the machine tells (em dashes, colon-connectors, forced triples, puffery, inflated importance, wrong counts), then **humanizer** to make what remains read like the writer. Both passes are mandatory; silently skipping one defeats the purpose of the skill.

Before running them, preflight that both skills are actually available in this project, because neither is guaranteed and neither ships inside this skill. Run the two checks identically:

- **Does an invocable `unslop` skill exist?** (a `SKILL.md` in the project's skills folder, or a skill the harness lists.) If no: flag to the user that unslop is missing and stop. Do not run the pipeline or imitate its effect.
- **Does an invocable `humanizer` skill exist?** Same check. Note that humanizer is often shipped as a plugin, and some harnesses (pi, for one) load only skills, not plugins, so a humanizer plugin can be invisible. It ships a standalone `SKILL.md` (MIT), so the fix is to vendor that one file into the project's skills folder (`.claude/skills/humanizer/` or `.pi/skills/humanizer/`). If no invocable humanizer skill exists: flag to the user and stop.

Proceed only when both checks answer yes. `unslop` may be reserved for explicit user invocation (a `disable-model-invocation` skill); if you cannot invoke it yourself, ask the user to run `/unslop` on the draft, then continue.

Run the passes on prose only. Leave license text, code blocks, YAML frontmatter, and link targets exactly as they are.

### 6. Mermaid diagrams: only on request or with approval

A mermaid flow diagram can make a subagent's or a tool's behavior much clearer than prose. But it is a large visual addition, so it is never added silently.

- Add one when the user **explicitly asks** for it.
- Otherwise you may **offer** it as a recommendation, in one line, and add it only after the user says yes.
- Never insert a diagram the user did not request or approve.

When you do add one, draw the actual behavior (the real steps and branches), keep the syntax valid for GitHub's renderer, and avoid characters that break rendering (write ranges as "1 to 5", not with a dash inside a node label).

### 7. Present for approval, do not commit

Write the final README, do a plain grammar and spelling pass, and confirm the mermaid block (if any) is well-formed. Then present it for the user's approval. Do not commit, push, or open a pull request unless the user asks. The README is theirs to sign off on.

## What this skill will not do

- It will not state a license or author it did not verify at the source.
- It will not proceed without a `.claude/` or `.pi/` folder, or a clear answer from the user about where things live, and it will not create a `.claude/` folder in a pi-only project.
- It will not add a mermaid diagram the user did not request or approve.
- It will not run the unslop then humanizer pipeline when either skill is unavailable; it stops and flags instead.
