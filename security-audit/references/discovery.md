# Discovery assignments and candidate register

Each discovery agent receives the same repository revision, scope, coverage map, and safety boundaries from `SKILL.md`. Assign the listed security-sensitive components to at least one role. The roles define a primary focus, not a reason to ignore a cross-cutting vulnerability encountered elsewhere. Agents inspect independently and return candidates only after tracing the relevant code and controls. Report an empty candidate list when no evidence-backed candidate is found, along with inspected components and gaps.

## Agent 1 — Authentication, authorization, and business logic

Inspect identity and access paths, including authentication bypass; broken access control; IDOR/BOLA; privilege escalation; ownership and function-level checks; session and token handling; JWT validation; OAuth/SSO; account takeover and password reset; tenant isolation; security-relevant races; and business logic abuse. Trace identity establishment through every authorization decision to the protected operation. Verify actual caller reachability and whether indirect middleware or service checks apply.

## Agent 2 — Injection, APIs, and untrusted input

Inspect input handling and trust-boundary crossings, including SQL/NoSQL and command injection; SSRF; XSS; template injection; path traversal and arbitrary file access; deserialization and XML external entities; prototype pollution; unsafe uploads; request smuggling; security-significant open redirects; GraphQL/REST authorization; and other unsafe input-to-sink flows. Identify where data becomes trusted, transformed, validated, or escaped. Inspect framework behavior before claiming a bypass.

## Agent 3 — Infrastructure, secrets, cryptography, and supply chain

Inspect hardcoded or exposed credentials; insecure defaults; dependencies and vulnerable usage; cryptography and random generation; cloud IAM; containers and Kubernetes; CI/CD or workflow injection; deployment configuration; sensitive-data disclosure and logging; security-critical caching; and production debug settings. Check whether a configuration or dependency issue is actually present and exploitable in this repository's relevant deployment path. Do not print secret values.

## Candidate format

Return one record per candidate with these fields:

| Field | Required content |
| --- | --- |
| `candidate_id` | Stable local ID for consolidation. |
| `title`, `provisional_severity`, `confidence` | Concrete claim, P0/P1/P2 estimate, and confidence with reason. |
| `location` | Exact repository-relative path, line range, function/component, and revision. |
| `entry_point` | Attacker-controlled input and how the attacker reaches it. |
| `privileges`, `preconditions` | Required access, configuration, timing, and deployment assumptions. |
| `path` | Source-to-sink or attacker-to-impact trace with intermediate calls and code references. |
| `controls` | Nearby and indirect authentication, authorization, validation, sanitization, and framework controls inspected. |
| `boundary_impact` | Violated trust/security boundary and concrete confidentiality, integrity, or availability impact. |
| `reachability` | Why the path is realistically reachable, or the remaining uncertainty. |
| `reproduction` | Minimal conceptual, safe reproduction scenario; no production targeting or destructive exploit. |
| `counterevidence` | Evidence that may weaken or disprove the claim. |

For each assigned component, also return inspected paths, principal entry points and trust boundaries, excluded paths with reasons, uninspected paths, and tool or environment limitations. Do not turn a grep match, scanner alert, hypothetical bad deployment, or a generic best practice into a candidate without a concrete exploit path.
