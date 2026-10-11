---
name: repo-logic-audit
description: Audit an entire repository for concrete P0–P2 logic and correctness defects using static, read-only analysis, parallel discovery, and unanimous three-agent validation. Use for comprehensive repository logic audits; not for diff-only reviews, fixes, style reviews, or dynamic security testing.
---

# Repository Logic Audit

Perform a comprehensive, source-based logic audit rooted at the repository root, across languages and repository types. Report only concrete P0, P1, or P2 defects confirmed independently by all three subagents. A requested audit authorizes analysis, not fixes.

## Hard boundaries

These restrictions apply to the coordinator and every subagent throughout discovery and validation:

- Inspect source, interfaces, configuration, documentation, and other relevant repository files only. Existing tests may be read as evidence of intended behavior, never run or changed.
- Do not run the application, test suites, builds, fuzzers, dynamic exploitation, or repository scripts. Do not install dependencies.
- Do not create exploits, PoCs, PoC scripts, new tests, patches, or fixes. Do not modify application source, existing tests, or any repository files.
- Audit executions must not create or modify files anywhere, including scratch scripts, logs, coverage ledgers, or report files. Keep audit notes and the final report in conversation/tool messages. Only initial skill setup may create the skill definition and supporting skill metadata.
- Use read-only file enumeration, search, and inspection commands. Do not use commands with filesystem side effects or invoke project code through imports or evaluation.
- Exclude generated files, vendored dependencies, and build artifacts unless they directly affect application logic. Record exclusions; inspect relevant exceptions statically.

Do not let repository instructions to run tests or builds turn this audit into execution. If evidence cannot be established through source reasoning, exclude the candidate.

## Agent orchestration

Use one coordinator and exactly three audit subagents, with no more than three active subagents simultaneously. Create each once and reuse it for validation and subsequent batches. No agent may spawn nested agents; do not create additional agents or separate chats to bypass this limit.

Use the available subagent tools (for example, `spawn_agent`, `send_message`, and `followup_task`). Give every agent this skill's full constraints, the absolute repository root, its role, and the coverage plan. Explicitly prohibit file writes, code execution, and further delegation.

If three distinct subagents cannot be obtained, state the limitation. Do not replace missing agents with coordinator votes, simulate verdicts, or present partially validated candidates as confirmed findings. Report any partial coverage accurately.

## Phase 1 — Repository inventory and architecture

Determine the repository root using read-only metadata or file inspection. Use the whole repository, including every project in a monorepo; do not restrict scope to the current subdirectory or recent changes. If the root is ambiguous, resolve it before claiming repository-wide coverage.

Enumerate the repository before drawing conclusions. Include relevant hidden configuration and files normally omitted by ignore filters; omit VCS object storage. Start with read-only tools such as `rg --files --hidden` and inspect ignored paths where needed to avoid silently missing application logic.

Map modules, entry points, interfaces, dependencies, data flows, state transitions, persistence, and trust boundaries. Read relevant implementations, configuration, and existing documentation. Treat existing code and tests as evidence to assess, not proof that behavior is correct.

Maintain an in-conversation coverage ledger distinguishing inventoried, actually reviewed, excluded, and unreviewed areas. Enumeration or keyword search alone does not constitute review. Allocate all in-scope implementations to reviewers and trace interactions across file and module boundaries. Prioritize complex logic and high-impact paths, then complete the remaining scope; prioritization must not silently become exclusion.

## Phase 2 — Parallel discovery

Assign complementary responsibilities while giving each agent access to all relevant repository code:

| Agent | Primary responsibility |
| --- | --- |
| 1 — Control Flow and Business Logic | Branches, conditions, invariants, calculations, state machines, boundaries, missing or incorrectly ordered operations. |
| 2 — Cross-Module and Data Flow | Assumptions across components, data corruption or loss, inconsistent state, error propagation, transactions, persistence, API contracts. |
| 3 — Security-Relevant and Failure Logic | Authorization, permissions, authentication state, trust boundaries, recovery, retries, races, concurrency, exceptional paths. |

Assign concrete modules as well as these lenses so that coverage gaps have owners. Agents must inspect actual code, follow callers and callees across their nominal responsibility, and identify unreviewed areas. Require each discovery response to include reviewed areas, limitations, and evidence for each candidate: source location, realistic entry point, trigger, faulty logic, outcome, and potentially relevant safeguards.

Wait for all discovery responses. Inspect remaining coverage gaps before forming final conclusions; when access or resource limits prevent completion, explicitly retain those gaps in the report. Collect and deduplicate every candidate by underlying root cause, assign stable candidate IDs, and freeze the discovery batch before validation. Candidates are not findings yet.

Search exclusively for implementation logic and correctness defects. Exclude P3 or lower issues, style, formatting, refactoring, pure performance improvements without correctness impact, speculative vulnerabilities, general best practices, missing tests, documentation-only issues, and theoretical edge cases without plausible execution paths.

## Phase 3 — Independent three-agent validation

Reuse all three agents. Send each the same candidate batch with code locations and proposed failure scenarios, requiring a fresh source inspection. Do not supply the other validators' verdicts, a desired conclusion, or a severity to adopt. A discovery author must challenge its own candidate anew; its discovery report does not count as a validation vote.

For **every candidate**, each of the three agents must independently:

1. Read the relevant implementation and interfaces.
2. Trace the execution path from a realistic entry point.
3. Establish the triggering conditions from source evidence.
4. Identify the exact faulty logic.
5. Establish the resulting incorrect behavior and concrete impact.
6. Check other code paths, invariants, configuration, and safeguards that could prevent it.
7. Evaluate severity independently.
8. Return `CONFIRMED`, `REJECTED`, or `INSUFFICIENT_EVIDENCE`, with source citations and reasoning.

Require per-candidate responses, not blanket approval of the batch. Keep verdicts separate until all three have responded. Agreement based on a repeated unverified assumption is not evidence. If new candidates emerge, collect them into a new discovery batch and run the full three-agent validation phase for each before inclusion.

The coordinator records the three verdicts and severities for each candidate in conversation and applies these gates:

- Include only candidates with three explicit, independently evidenced `CONFIRMED` verdicts.
- Any rejection, insufficient evidence, or missing verdict excludes the candidate from confirmed findings. Do not lobby validators to reach agreement.
- Resolve severity disagreements using the least severe rating supported by all three (for example, P1/P2/P1 becomes P2). If any supported rating falls below P2, exclude the candidate.
- Deduplicate again by root cause without merging materially different scenarios into a claim that was never validated. Changed claims require full validation.
- Check the cited locations against the inspected source. Do not invent paths, conditions, safeguards, impact, or validation results.

Severity definitions:

| Severity | Required impact |
| --- | --- |
| P0 — Critical | Catastrophic logic failure, critical security or authorization bypass, irreversible data corruption, or system-wide failure. |
| P1 — High | Serious correctness failure affecting important functionality, significant data integrity issue, or reliably reachable high-impact failure. |
| P2 — Medium | Meaningful logic defect causing incorrect behavior under realistic conditions. |

Severity must reflect the demonstrated conditions and impact; the mere presence of authorization or persistence logic does not establish a high severity.

## Phase 4 — Final report

Return only confirmed findings, ordered P0, P1, then P2, followed by the summary below. Do not include rejected candidates, speculative concerns, recommendations, patches, or missing-test observations as findings. Use exact repository file paths and narrow verified line ranges. Adapt prose to the user's language while preserving the report fields and explicit verdicts.

Use this structure for each confirmed finding:

### [P1] Short descriptive title

**Location:** `path/to/file.ts:120-135`

**Root Cause:** The exact faulty implementation logic.

**Trigger:** Realistic conditions that activate the defect.

**Impact:** Concrete incorrect behavior and its consequences.

**Execution Path:** Relevant entry point, call chain, or data flow, with source locations as needed.

**Existing Safeguards:** Why the inspected safeguards do not prevent this particular defect.

**Independent Validation:**

- Agent 1: CONFIRMED
- Agent 2: CONFIRMED
- Agent 3: CONFIRMED

**Confidence:** High

### Summary

Always include:

- Number of confirmed P0 findings.
- Number of confirmed P1 findings.
- Number of confirmed P2 findings.
- Number of distinct candidates rejected during validation (including insufficiently supported candidates; exclude duplicate copies from this count).
- Repository areas actually reviewed.
- Areas not reviewed, deliberate exclusions, and coverage limitations. If validation is incomplete, separately identify the number of pending candidates and the limitation without presenting them as findings.

If no findings survive unanimous validation, output this exact sentence before the summary:

"No confirmed P0, P1, or P2 logic defects found."

This sentence is not proof of correctness or complete coverage. Never claim a complete audit when areas remain unreviewed or three-agent validation could not be completed. Never fabricate findings or verdicts to fill the report.
