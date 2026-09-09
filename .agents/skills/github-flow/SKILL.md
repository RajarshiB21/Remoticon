---
name: github-flow
description: Carry out delegated Git and GitHub work, or teach one command at a time when the user asks to learn. Inspect repository state and preserve the user's publication and merge boundaries.
---

# Git and GitHub workflow

## Choose the requested mode

For delegated work such as "commit this", "push this" or an approved implementation workflow, run authorized commands yourself and verify the result. Do not make the user type commands or repeat approval already given.

For an explicit learning request, explain one command, let the user run it, then verify before introducing the next. Teach terms where they appear rather than presenting a glossary.

Use the terminal and GitHub CLI or browser. Follow workspace AGENTS.md for review, publication and merge sequencing; this skill adds no approval gates.

## Before changing state

Check the exact repository, branch, worktree, remote and intended diff. Preserve unrelated edits. Inspect tracked/staged content for private notes or credentials before publication. Use an explicit working directory, especially when planning and product repositories differ.

Commit only intended files. Verify each mutation succeeded before dependent operations. Never infer that a commit, push, check or merge succeeded from an attempted command.

## Pull requests and cleanup

Use a feature branch and the project's existing checks. Write PR descriptions around the final behavior and actual validation. Keep multiline descriptions in a body file or structured tool argument.

Apply the workspace's review limits. A PR is the proposed merge, not authorization to merge it. The user-only merge rule remains in force unless explicitly changed.

After the user merges, verify remote state, update local main safely, remove the completed local branch and prune stale remote references. Check any separately installed runtime against the approved source.

## References

Read only the relevant reference when needed:

- Putting a local project on GitHub: [setup](references/setup.md).
- Configuring checks or branch protection: [gates](references/gates.md).
- Recovering a Git mistake: [recovery](references/recovery.md).

Reference teaching prompts apply in learning mode. Delegated execution follows the user's existing authorization.
