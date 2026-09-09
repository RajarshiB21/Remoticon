---
name: thermo-nuclear-review
description: Review a supplied diff for demonstrated correctness, security, compatibility and developer-experience regressions, or verify fixes to an existing report.
---

# Security and correctness review

Workspace AGENTS.md owns review sequencing and callback limits. Respect the supplied initial-review or finding-verification mode. This skill does not authorize additional agents or review rounds.

## Initial review

1. Establish the supplied repository, base and candidate revisions, intended uncommitted files and relevant requirements. Review that complete change once.
2. Trace changed behavior through its callers and dependencies. Look for incorrect results, data loss, credential exposure, broken error/recovery paths, compatibility changes, build/setup regressions and feature-gate leaks.
3. Establish a concrete trigger and consequence before reporting. Inspect accessible related code instead of presenting an unresolved hypothesis as a defect.
4. Report only defects introduced or exposed by the supplied change. Approved intentional behavior is not a regression. Existing unrelated defects and speculative platform support are outside scope.
5. If a PR exists and a material finding needs reconciliation with existing discussion, read that discussion after your own inspection. Attribute duplicate or externally reported findings.

## Finding verification

Use the original report and correction diff. Check the original finding IDs and direct regressions introduced by their fixes. Preserve previously settled scope. Do not repeat the initial audit.

## Report

Name reviewed revisions/files once. For each finding provide ID, priority, file/line, concrete trigger, consequence and evidence. Distinguish defects from nonblocking suggestions. A concern without an established failure path is a question or limitation, not a blocker.

Return no findings when none are supported. State verification limits honestly; a review is not a guarantee that no defects remain. Do not edit files or run another agent.
