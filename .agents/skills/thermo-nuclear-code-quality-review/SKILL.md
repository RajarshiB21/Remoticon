---
name: thermo-nuclear-code-quality-review
description: Review a supplied diff for concrete maintainability regressions and unnecessary complexity, or verify fixes to an existing report.
---

# Maintainability review

Workspace AGENTS.md owns review sequencing and callback limits. Respect the supplied initial-review or finding-verification mode. Review the implementation against its approved scope.

## Initial review

Inspect the supplied diff and enough surrounding code to establish its effect. Look for:

- duplicated behavior that should share an existing owner;
- new dependencies, wrappers or abstractions without a current need;
- scattered special cases that obscure the same underlying rule;
- state or resource ownership that makes lifecycle and recovery hard to follow;
- coupling, casts or hidden fallback behavior that conceals a concrete contract;
- unnecessary file growth or fragmentation that makes this change harder to maintain.

Prefer deletion, existing helpers and direct code. A file crossing 1,000 lines is a reason to inspect cohesion, not an automatic demand for decomposition. A different architecture is not inherently better.

## Finding verification

Read the original report and correction diff. Check each original finding and maintainability regressions directly caused by its fix. Do not repeat the branch audit or search for unrelated simplifications.

## Report and completion

Name reviewed revisions/files once. Give each finding a stable ID, location, demonstrated maintenance consequence and smallest practical remedy. Tie blockers to regressions introduced by this change or violations of approved requirements.

Alternative designs, naming preferences and speculative simplifications are nonblocking suggestions. Do not require a rewrite simply because another implementation is possible. Do not reopen approved architecture unless evidence establishes that it cannot meet the contract.

Return no findings when none are supported. Reports advise the implementer; they do not authorize expanded scope, edits or another review round. Do not edit files or spawn agents.
