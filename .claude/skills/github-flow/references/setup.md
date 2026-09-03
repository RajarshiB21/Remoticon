# Putting an existing folder on GitHub

An existing folder becomes a repo in place. Nothing moves, nothing is copied, no new folder appears.

The instructor stance from `SKILL.md` holds throughout, and this branch contains the session's only true one-way door: the first publish.

## Order matters

`.gitignore` comes **before** the first commit. A `.gitignore` added afterwards does not untrack what is already committed, and a commit pushed to a public repo stays public even after the file is deleted — it lives in history. Getting the order right is free; getting it wrong means rewriting history or deleting the repo.

```
1  <write .gitignore>
2  git init
3  git add .
4  git status --short          what will be committed
5  git ls-files --cached        (after add) the definitive list — read it together
6  git commit -m "Initial commit"
7  <browser or gh> create the repo and push
```

## Writing the .gitignore

Ask what the folder holds before writing it. The things that must never go up:

- **Credentials** — `auth.json`, `.env`, anything with a token or key
- **Session logs and transcripts** — private conversation data
- **Local machine settings** — paths and preferences that mean nothing to anyone else
- **Caches and installed dependencies** — `node_modules/`, build output
- **Private working notes** the user wants kept back

Write it as plain lines, one path per line, with comments saying why. The user will read this file again in six months.

## The check that matters

After `git add`, before the commit:

```
git ls-files --cached
```

That is the exact, complete list of what is about to become public. Read it with them, line by line, and ask about anything unfamiliar. This is the moment to catch a secret, and it costs ten seconds.

If a secret is already committed but not yet pushed, the fix is cheap: add it to `.gitignore`, then `git rm --cached <file>`, then commit again. If it has already been pushed, the credential is compromised — rotate it, and treat removal from history as damage control, not a fix.

## Publishing

Two ways, both fine:

**Browser** — create an empty repo at github.com/new (no README, no .gitignore, no license — the folder already has them), then run the two commands GitHub shows on the next page:

```
git remote add origin https://github.com/<user>/<repo>.git
git push -u origin main
```

**`gh` CLI** — one command:

```
gh repo create <name> --public --source=. --push
```

**Public vs private** is a real decision, and worth stating plainly: Actions minutes are unlimited and free on public repos, and most review bots have a free tier only for public ones. Private repos on the Free plan are capped at 2,000 Actions minutes a month. Public also means the code is readable by anyone from the first push.

## Naming

The folder name becomes the repo name by default, and matching them saves confusion later. If the project is a package that might be published, name it the way its ecosystem does — lowercase with hyphens, and any conventional prefix its neighbours use. Look at two or three real packages in that ecosystem before deciding; conventions are easier to read off examples than to reason out.

## Verify before calling it done

```
git remote -v      origin points at the right repo
git status -sb     shows ## main...origin/main
git ls-files       nothing unexpected
```

Then open the repo page in the browser and look at it. The file list there is the ground truth for what is now public.
