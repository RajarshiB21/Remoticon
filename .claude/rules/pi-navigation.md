# Driving pi in headless mode

How to run `pi` (the coding harness) non-interactively from a shell and read its result reliably. Read this before scripting a `pi -p` call; it caches the two things `--help` will not tell you: how to capture output without hanging, and which provider costs real money.

## Pre-flight: confirm the provider, model, and effort exist before you run

pi defaults to the `google` provider, so always name the provider explicitly. Confirm three things first, so a run fails at the check instead of mid-generation:

- **Model and provider exist, and whether the model supports thinking:** `pi --list-models <search>`. Columns are provider, model, context, max-out, thinking (yes/no), images. Example: `pi --list-models deepseek`.
- **The provider is authenticated:** `pi auth check --provider <name>` prints `ready` when credentials are live.
- **Effort is the `--thinking` level:** one of `off, minimal, low, medium, high, xhigh, max`. A model shows `thinking: yes` in `--list-models` when it accepts these.

## The call

```
pi -p --provider <name> --model <provider/id> --thinking <level> --no-session "<prompt>"
```

- `-p` (`--print`) runs once and exits. `--no-session` keeps it ephemeral (no saved session file).
- pi has no Skill tool; it activates a skill by reading its `SKILL.md` directly. To exercise a skill, run pi from the project root so it discovers `.pi/skills/`, and name the skill in the prompt.

## Capture output to a file, and background it from the start

Send pi's output straight to a file and launch it as a background job the harness owns:

```
pi -p --provider deepseek --model deepseek/deepseek-v4-flash --thinking low --no-session "<prompt>" > run.log 2>&1
```

A file on disk has no reader that can disappear, so nothing about the run depends on the foreground staying alive. Read `run.log` once the job reports done.

This is the lesson from 2026-09-04: piping a headless run through `tee | tail` to watch it live works only while the command holds the foreground. When the run overruns the shell timeout and gets detached, the pipe's reader is gone, and pi blocks forever on its next write with tokens already spent. The process stays alive, the log stays empty, and the target file never changes. Redirect to a file and background it up front, and that failure cannot happen.

## deepseek runs long

deepseek is an overthinker: it is slow to first output, and `--thinking medium` or higher makes it slower still. For a quick headless task use `--thinking low` (or `off`), give the job a generous timeout, and run it in the background rather than blocking on it.

## Provider policy

Use a first-party provider whose cost is already covered. `deepseek` (the direct provider, model ids like `deepseek/deepseek-v4-flash`) is the default choice here.

**Never use the `openrouter` provider.** It is a metered connection billed per token, so every call spends real money. When `--list-models` shows the same model under both `deepseek` and `openrouter` (for example `deepseek/deepseek-v4-flash` appears under each), pick the `deepseek` provider row. If only an `openrouter` route exists for what you need, stop and ask the user rather than spending on it.
