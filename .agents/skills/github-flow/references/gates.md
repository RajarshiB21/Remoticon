# Checks, branch protection and review bots

Use this reference when configuring a repository's checks. The parent skill determines delegated versus learning mode; workspace AGENTS.md determines publication and merge authority.

## Continuous integration

CI runs the repository's configured commands on a runner. A green result proves those commands passed for that revision and environment. It does not prove that the assertions are sufficient: both tests and workflow files can change in a PR.

Inspect the actual workflow, runner, dependency lockfile and required check names. Reuse existing commands and supported versions rather than copying a generic setup. Local checks catch failures before push; CI also detects missing committed files and environment differences.

Keep provider secrets out of deterministic test jobs. CI can make network calls or incur charges if configured to do so; absence of paid calls is a project policy, not a property of CI. Verify current runner billing before adding paid resources. Native user acceptance remains separate from automated checks.

## Branch protection

Inspect current protection or rulesets and any bypass permissions. Configure a required PR and the actual required check names only within the user's authorization. A green optional check alone does not enforce anything.

Verify the effective rule after changing it. A branch rule is not proof that administrators or other bypass actors cannot merge. Preserve the user-only merge boundary in this workspace.

## Review bots

A review bot reports findings about code. Read its complete report and inline comments, and verify whether its status represents a completed review, a skip, a failure or a pending run on the current revision.

Availability, automatic triggers and included usage depend on the integration and account. Verify them when setting up the bot; do not promise that every PR is automatically reviewed.

Findings require engineering assessment. Apply the workspace's defect/suggestion distinction, disposition every finding, and respect the bounded local review lifecycle. Code review does not establish native appearance or responsiveness.

## Setup order

Inspect the repository and existing PR/check state, establish the workflow, verify one actual run, then configure required checks and any authorized review integration. Do not replace working infrastructure merely to match this example sequence.
