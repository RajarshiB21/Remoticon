---
name: systematic-debugging
description: Diagnose a bug, failed check or unexpected behavior before fixing its cause. Use the smallest reproduction and preserve the failed-attempt count.
---

# Systematic debugging

Use this process for concrete failures. Workspace AGENTS.md owns scope checkpoints, approvals and retry limits. Scale investigation to the failure; this skill does not require a new diagnostic framework.

## 1. Establish the failure

Read the complete relevant error and inspect recent changes. Reproduce with the smallest existing test or local observation. Record expected and actual behavior.

Trace the affected operation through its callers to the first point where behavior diverges. Compare a working path when one exists. Read the relevant implementation fully enough to establish its contract; unrelated files do not become audit scope.

Use existing logs and diagnostics first. Add temporary instrumentation only to answer a specific unresolved question. Record presence or sanitized metadata, never credentials, prompts, full environment dumps or private session content. Keep diagnostics out of an active terminal UI.

If the failure cannot be reproduced, report the evidence gap and the next bounded observation. Do not guess a fix or claim a cause is known.

## 2. Test one explanation

State one hypothesis and the observation that would disprove it. Change one variable in a minimal experiment. Check the result before applying another change.

A failed experiment updates the hypothesis; a failed attempted fix increments the issue's counter. Keep the counter and evidence across sessions. Do not rename the symptom to reset it.

## 3. Fix the shared cause

Confirm a failing regression test or independent reproduction before the fix. Use the cheapest useful layer: pure logic, disposable filesystem or existing integration flow. No additional test framework or terminal boot unless that independent failure requires it.

Inspect callers before changing a shared function. Correct the cause without weakening a valid product assertion, suppressing errors, or adding fixture-specific exceptions. Keep unrelated cleanup out of the fix.

For independent CI and review findings, diagnose causes separately, then batch their proven corrections into one coherent revision as the delivery workflow requires.

## 4. Verify and finish

Run affected checks and report actual outputs. Run the complete suite when the coherent executable revision requires it. Repeat broader checks only for new relevant changes or unresolved failures.

Confirm the original failure is gone; a green unrelated test is not proof. Remove task-owned diagnostic changes once evidence is retained.

After three failed fixes to one issue, stop before a fourth. Report attempted explanations, evidence, the suspected architectural obstacle and the smallest proposed correction. Continue independent authorized work; the user should decide scope, not design the debugger.

## References

Read only when the specific failure needs it:

- [Root-cause tracing](root-cause-tracing.md): follow a failure backward through callers.
- [Defense in depth](defense-in-depth.md): decide where additional validation is justified after establishing the cause.
- [Condition-based waiting](condition-based-waiting.md): replace an observed arbitrary-wait failure with a meaningful condition.

Examples in references are illustrative, not permission to add every layer, use unsupported platform commands, expose secrets or expand scope.
