# When something is wrong

Read this when the user is stuck, confused about where they are, or has done something they want back.

## Diagnose before prescribing

A description of a git problem is a description of a symptom, and several different states produce the same symptom. "I committed to the wrong branch" and "I branched from the wrong place" look identical from the outside and need different fixes.

So the first move is always the same: run the read-only commands, work out what state they are actually in, and **show them the output** before proposing anything. The evidence teaches them to read their own situation next time — which is worth more than the fix.

```
git status -sb          branch, tracking, uncommitted work
git log --oneline -8    what's actually in history
git branch -a           what branches exist, local and remote
git log --oneline main..HEAD    commits on this branch that main doesn't have
```

That last one answers the question behind most confusion: *what have I actually got that main hasn't?*

## Reversible or not

Sort every situation into one of two piles before touching anything, and tell the user which pile they are in — the anxiety is usually worse than the problem.

**Local and unpushed** — almost everything is recoverable, cheaply. Nobody else has seen it.

**Already pushed** — history is shared. Fixes that rewrite it (`push --force`, rebasing published commits) can destroy other people's work and are rarely the right answer for a learner. Prefer a new commit that corrects the old one; the ugly history is a fair price.

## Common states

**Uncommitted work on the wrong branch.** Nothing is lost — uncommitted changes are not attached to a branch. They just switch:

```
git checkout -b <right-branch>
```

**Committed to main by mistake, not pushed.** Inspect status, remote history and worktrees first. Preserve all uncommitted work and identify the exact pre-change commit. Once the working tree is clean and the intended correction is authorized, keep the commit on a new branch and move the unoccupied local main pointer back:

```
git switch -c <new-branch>
git branch -f main <verified-pre-change-commit>
```

Verify both branch pointers afterward. Do not assume origin/main is current or discard unrelated working changes. If main is checked out elsewhere or the commit was published, stop this recipe and establish the appropriate recovery from that evidence.

**Want to undo the last commit but keep the changes.**

```
git reset --soft HEAD~1
```

`--soft` keeps the files exactly as they are and only removes the commit. `HEAD~1` means one commit back.

**A file was committed that shouldn't have been, not yet pushed.**

```
<add it to .gitignore>
git rm --cached <file>
git commit -m "Remove <file> from tracking"
```

`--cached` removes it from git while leaving it on disk. If it was a credential and it has been pushed, say plainly: the credential is compromised and must be rotated. Removing it from history is damage control, not a fix.

**Merge conflict on pull.** Two people changed the same lines. `git status` lists the conflicted files. Inside each, git has written both versions between `<<<<<<<` and `>>>>>>>` markers. They edit the file to what it should be, delete the markers, then:

```
git add <file>
git commit
```

Inspect the conflicting commits to explain the actual cause; do not assume where the edits came from.

**"I don't know what I did."** Inspect `git reflog` for recorded earlier HEAD positions, including commits no branch currently references. Recovery depends on retained reflog entries and objects; do not promise a fixed retention window or recoverability of uncommitted files.

## After any recovery

Verify and show them:

```
git status -sb
git log --oneline -5
```

Then say in one line what state they are in now and what the next move is. Ending a recovery without re-orienting them leaves them afraid to type anything.
