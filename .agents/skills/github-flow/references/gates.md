# Gates: checks, branch protection, review bots

A **gate** is something that stands between a branch and main. Three exist, they stack, and they are worth understanding as one idea before wiring any of them up.

## What a check is

A **check** is any automated thing that reports pass or fail on a pull request. It appears in one place — a strip on the PR page saying "All checks have passed" or "Some checks haven't completed yet" — no matter what produced it.

The teaching point, and the reason any of this is worth setting up: **a check is a receipt the agent did not write.** An agent can tell the user the tests passed. A green tick on the PR page was produced by a machine outside the project, running only what was committed, and cannot be edited from inside the repo. For a user who has been burned by an agent editing tests to make them pass, this is the whole value — say it in those terms.

## GitHub Actions

A rented, empty Linux machine that runs commands listed in a file in the repo. It is not a testing tool; it runs whatever is written down.

```
.github/workflows/ci.yml
```

Two properties decide everything about how it is used: **it has no screen, and it has no money.** So it can never look at pixels, and it must never make a paid API call. Anything visual, and anything that costs, stays on the user's machine and never gates a merge.

A minimal workflow for a Node/TypeScript project:

```yaml
name: CI
on: [push, pull_request]
jobs:
  check:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-node@v4
        with:
          node-version: '22'
      - run: npm ci
      - run: npm run check
```

Two things to explain when it first runs:

- **`npm ci` not `npm install`** — installs exactly what the lockfile says, so the run is reproducible.
- **Why the same commands run twice.** The user already ran them locally. Actions runs them on a machine holding *only what was committed*. That is the point: a forgotten file passes locally, because the file is sitting on their disk, and fails here. Same for a stale build or an environment variable only they have. Local cannot catch these by definition — local is where the contamination lives.

Free without limit on public repos, standard runners. On private repos the Free plan includes 2,000 minutes a month.

## Branch protection

The switch that turns a written workflow into a rule. Settings → Branches → Add rule on `main`:

- **Require a pull request before merging** — no more pushing straight to main
- **Require status checks to pass** — then pick the checks by name (they only appear in the list after they have run at least once, so open one PR first)

Before this, "don't merge until it's green" is a promise. After it, the Merge button is disabled until it is green. Frame it that way — it is the difference between a rule and a locked door, and it is one checkbox.

## Review bots

A bot that reads the diff and leaves inline comments on the PR, as a colleague would. CodeRabbit, Greptile, Copilot review and Qodo all do this; most have a free tier for public repos. Connected once on the bot's own website, then it comments on every PR by itself — nothing is installed locally.

Three things to be clear about when one is set up:

- **It registers as a check**, so it holds the merge button while it thinks. That surprises people the first time.
- **It reads code, not pixels.** It cannot say whether something looks right. Anything visual is still the user's eyes.
- **Its comments are advice, not tasks.** Acting on all of them is a choice, not an obligation — and auto-fixing every nitpick is how a project drowns in churn.

## The order to add them

Each one is only useful once the one before it exists.

1. **A PR** — nothing to gate without one.
2. **Actions** — something for the gate to read.
3. **Branch protection** — pointing at a check that has already run once.
4. **A review bot** — optional, and last.

Adding branch protection before any check exists produces a rule that blocks everything and points at nothing.
