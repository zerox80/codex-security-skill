# Report format

Write `security-audit-report.md` in the audited repository root. Keep evidence concise but independently checkable. Use repository-relative file links and line ranges tied to the recorded revision. Never include secret values or dangerous live exploit instructions.

## Executive Summary

State repository and revision, dirty-worktree state if relevant, audit scope, methodology (three discovery and three validation roles, or exact limitations), coverage limitations, counts of confirmed P0/P1/P2 findings, count requiring manual review, and overall security risk assessment. Make the risk assessment proportional to observed evidence and gaps.

## Confirmed Findings

Sort by severity, then exploitability and impact. For each finding include:

1. Unique finding ID; P0/P1/P2; clear title; CWE where applicable; final confidence.
2. Exact file path and line range, vulnerable function/component, and audited revision.
3. Root cause, attacker-controlled entry point, source-to-sink or exploit path with concrete code evidence, and violated security boundary.
4. Required attacker privileges, prerequisites, deployment assumptions, realistic reachability, and security impact.
5. Safe reproduction procedure if feasible; otherwise state why it was not performed. Never imply an unrun check succeeded.
6. Three separate validation verdicts and their key evidence, including any revised verdict after disagreement resolution.
7. Recommended remediation and a suggested regression test that would fail before the fix and pass after it.

Label observed evidence, inference, and unresolved assumptions distinctly. Contextualize any CVSS v4.0 score used.

## Manual Review Required

For each uncertain or disputed candidate, use the same evidence fields as for a confirmed finding, marking unknown or unverified fields explicitly rather than inventing them. Include all available independent verdicts, the exact uncertainty or disagreement, and the safe next step needed to decide it. Include plausible candidates that could not receive all three independent verdicts; do not count them as confirmed.

## Rejected Candidates

Provide a concise table with candidate ID, original claim, and code-backed rejection reason. Record duplicate root-cause grouping here if relevant. Do not include secret values.

## Coverage Summary

Document the repository inventory and coverage map: reviewed components, entry points and trust boundaries, discovery assignments, excluded areas and reasons, uninspected surfaces, and tool, environment, agent-availability, or context limitations. If batching was needed, report batch coverage and remaining gaps. Do not claim complete coverage without evidence.

## Remediation Priorities

Give an actionable fix order for confirmed findings, prioritizing root causes that eliminate multiple exploit paths. For manual-review candidates, recommend the safe verification needed before remediation decisions. Do not mix speculative hardening suggestions into the findings.
