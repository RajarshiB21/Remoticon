---
name: readme-update
description: Update a project's README to reflect newly added skills, subagents, or tools, with author credits and licenses verified against the original source, and prose run through the unslop then humanizer pipeline. Use whenever the user adds a skill, subagent, or borrowed tool and wants the README or its attributions brought up to date, or says the README is stale. Also use when the user asks to credit an author, verify a license, or document a project's .agents/ or .pi/ folder. Works with Codex and Pi projects, including Pi-only projects.
---

# README update

Bring a project's README into line with what the project actually contains: newly added skills, subagents, and borrowed tools, each credited to the right author under a license you confirmed rather than assumed. The README is the front door of a public repo, so a wrong license or a missing credit is a real problem, not a cosmetic one. This skill is deliberately general: it reads the project in front of it and works from what it finds, so it behaves the same in any repo without hardcoded names.

Two things make this skill worth using instead of freehand editing: it never states a license or an author it has not verified, and it runs every word of new prose through the unslop then humanizer pipeline so the README does not read like it was written by a machine.

## Two gates before any writing

These come first because getting them wrong is expensive. Do not skip ahead to drafting.

### Gate 1: the project must have a `.agents/`, `.codex/`, or `.pi/` folder

A project can be a Codex project, a Pi project, or both. Codex loads skills from `.agents/skills/` and subagents from `.codex/agents/` as TOML files. When `.codex/agents` links to `.agents/agents`, inventory the canonical targets once. In another project, `.codex/agents` may be a real folder, so inventory it directly. Pi loads skills from `.pi/skills/` and `.agents/skills/`; this setup gives Pi skills only. Either layout tells you what the README should describe, so check the project root for all three:

- **If `.agents/` or `.codex/` exists**, inventory `.agents/skills/` and `.pi/skills/` when present, then `.codex/agents/` and `.agents/agents/` when present. Resolve links before counting so the same TOML agent is listed once. A `.pi/skills` link that points at `.agents/skills` is the same content seen twice, so count each skill once.
- **If neither `.agents/` nor `.codex/` exists but `.pi/` does** (with a real, non-linked `.pi/skills/`), this is a Pi-only project. Do not create an `.agents/` folder. Confirm with the user that this is the Pi layout to document, then inventory `.pi/skills/`. Pi has no dedicated subagent folder, so expect skills only.
- **If none of `.agents/`, `.codex/`, or `.pi/` exists**, stop. Do not guess what the project contains or invent a structure. Tell the user plainly that you found none of these folders, and ask where the skills, subagents, or tools live and what they want documented. Wait for their answer before doing anything else.

### Gate 2: never state a license or author you have not verified

A license and a copyright holder are legal facts. Guessing one, or copying a claim the old README already makes without checking it, is how a repo ends up asserting something false. If at any point you are unsure of a skill's origin, its author, or its license, stop and ask the user for the link to the original repository they downloaded it from. An honest "I could not confirm this" is always better than a confident guess.

## Workflow

### 1. Inventory what the project has, and what the README is missing

Read every `SKILL.md` under `.agents/skills/` and `.pi/skills/` when present, resolving links and counting each canonical file once. Read every TOML subagent in `.codex/agents/` and `.agents/agents/` when present, resolving links and deduplicating files that resolve to the same path. Read the current README. The gap between the inventory and the README is your work: skills or subagents that exist but are undocumented, credits that are missing, and claims that no longer match the files.

Separate the user's own work from borrowed work as you go. The user's own skills and subagents are credited as theirs; only work from someone else needs third-party attribution. Look for signals of origin: a `LICENSE` file inside the skill folder, a lock or manifest file at the project root (a `skills-lock.json` or similar) that names the source repo and path, or a note in the existing README.

### 2. Verify each borrowed skill's origin and license against the source

For every skill or tool that is not the user's own, confirm three facts before you write a single word of credit: the author, the license type, and the copyright year.

- Start from what the project already tells you: any `LICENSE` file in the folder, and any lock or manifest file that names the source repository.
- Then confirm it at the source. Search the web for the skill and its author, then open the original repository's `LICENSE` file and skill folder to read the license type, copyright holder, and year directly. Verify, do not trust: read the actual license text rather than repeating a claim.
- If the origin or license is unclear, or web research does not settle it, **stop and ask the user for the link to the original repo** they got the skill from. Do not fill the gap with a plausible-sounding license.

Check the project's own convention too. If every borrowed skill folder is supposed to carry its own `LICENSE` file and one is missing, flag it and offer to copy the correct license text into that folder, so the README's claim stays true.

### 3. Present your findings and get confirmation

Before writing, show the user what you found: each borrowed item, its author, its verified license and year, the source URL, and anything you could not confirm or any license file that is missing. This is the moment for the user to correct a wrong author or point you at the right repo. Wait for their confirmation.

### 4. Draft the README changes

Write the additions and fixes: a short entry for each new skill and subagent under whatever "what's here" section the README uses, updated credits with the verified license lines, and a corrected license note. Match the README's existing voice and structure rather than imposing a new one. Keep the user's own opinions and phrasing; you are updating their document, not rewriting it.

Describe each item by what it does, plainly. The user's own subagents and skills are marked as theirs.

### 5. Run the prose through the pipeline: unslop, then humanizer

Every piece of new or changed prose goes through two passes, in order: **unslop** first to strip the machine tells (em dashes, colon-connectors, forced triples, puffery, inflated importance, wrong counts), then **humanizer** to make what remains read like the writer. Both passes are mandatory; silently skipping one defeats the purpose of the skill.

Before running either pass, locate both [unslop](../unslop/SKILL.md) and [humanizer](../humanizer/SKILL.md). These relative links reach sibling skills through the canonical folder or a harness junction. If a linked skill is absent, check the skills the harness exposes for the same name. If either skill cannot be located and read, report the missing dependency and stop; do not imitate its effect.

Read and follow unslop on the draft first. Then read and follow humanizer on the resulting prose. Automatic invocation policy does not prevent this workflow from reading the named skill files.

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
- It will not proceed without a `.agents/`, `.codex/`, or `.pi/` folder, or a clear answer from the user about where things live, and it will not create an `.agents/` folder in a Pi-only project.
- It will not add a mermaid diagram the user did not request or approve.
- It will not run the unslop then humanizer pipeline when either skill is unavailable; it stops and flags instead.
