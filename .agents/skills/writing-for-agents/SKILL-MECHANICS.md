# Skill mechanics

The skill-specific branch of [`writing-for-agents`](SKILL.md): what changes when the document is a skill (frontmatter, the invocation choice, and router skills). Everything else about writing it is the universal reference in `SKILL.md`.

## Invocation

Every skill keeps a `name` and `description` in `SKILL.md`. Invocation policy controls automatic discovery; it does not remove the file or make its instructions inaccessible to another skill.

Two choices, trading the two loads:

- An **automatically selected** skill has a model-facing description carrying its trigger branches. The harness offers it during skill selection, and the user can still request it explicitly. This spends context load for discoverability. In Codex, omit `policy.allow_implicit_invocation` from `agents/openai.yaml` or set it to `true`. In Pi, omit `disable-model-invocation` from the skill frontmatter or set it to `false`.
- An **explicit-only** skill is selected when the user requests it, rather than automatically from a matching task. This spends cognitive load: the user must remember the skill exists. In Codex, set `policy.allow_implicit_invocation: false` in `agents/openai.yaml`; the user can invoke `$skill-name`. In Pi, set `disable-model-invocation: true` in the skill frontmatter; the user can invoke `/skill:skill-name`. For a shared explicit-only skill, keep both settings aligned.

Preserve an existing invocation policy. For a new skill, use automatic selection when the agent needs to discover it from the task; use explicit-only selection when the user chooses that boundary. Keep each description concise and describe the capability accurately under either policy.

A workflow that needs another skill can point directly to its `SKILL.md`: read it and follow its instructions when that step is reached. Use a relative path between shared skills so the same pointer works through either harness's skill folder. If the required skill cannot be found or read, report the missing dependency rather than imitate its contents. A pointer loads instructions; it does not authorize actions beyond the user's request.

## Splitting by invocation

The invocation cut of splitting (the sequence cut lives in `SKILL.md`): split off a skill when a distinct capability needs independent invocation or another workflow needs to reach it. Automatic selection also pays context load for the description, so independent discovery has to be worth it. Shared reference without an independent invocation need can remain a plain file reached by a pointer.

## Router skills

When explicit-only skills multiply past what the user can remember, a **router skill** can name the workflows and when to reach each. The user invokes one entrypoint; its instructions select the relevant branch and read the linked skill or reference. Keep the router's own invocation policy separate from its ability to read a named file.

## Harness references

When changing these mechanics, check the current [Codex skill documentation](https://developers.openai.com/codex/skills/) and [Pi skill documentation](https://pi.dev/docs/latest/skills). `agents/openai.yaml` holds Codex-specific metadata and policy; Pi's explicit-only switch remains in `SKILL.md` frontmatter.
