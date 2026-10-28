# Independent validation

Give each validator the same consolidated candidate register, exact code references, and safety boundaries. Do not give them other validators' reasoning or verdicts before their own review. Each validator must independently inspect the code and return one verdict per candidate, even if no safe reproduction is possible. When there are many candidates, send the full register in ordered batches to each of the same three validators and reconcile all IDs before deciding outcomes.

## Validator 1 — Correctness and reachability

Verify that code and line references exist at the audited revision; the path is reachable; inputs are attacker-controlled; data flow is accurate; indirect authentication and authorization are accounted for; validation or sanitization does not eliminate the issue; framework defenses were considered; and deployment conditions support the claim. Search actively for evidence that disproves each candidate.

## Validator 2 — Exploitability and impact

Independently assess attacker capability, prerequisites, privileges, complexity, boundary violation, confidentiality/integrity/availability impact, user or tenant exposure, production relevance, and severity. Use safe, non-destructive checks when needed: targeted tests, static reasoning, isolated harnesses, or local reproductions. Never target external services, production infrastructure, or real users; never perform credential exfiltration or uncontrolled scans. State when the proposed exploit cannot be safely reproduced and what evidence still supports or weakens it.

## Validator 3 — Adversarial false-positive review

Assume the claim could be wrong. Seek hidden or indirect controls, unreachable paths, incorrect attacker access assumptions, authorization, sanitization, safe framework defaults, environment-specific protections, duplicate root causes, mistaken trust boundaries, unrealistic prerequisites, and overstated impact. Explain the strongest disconfirming evidence found, including when the candidate still survives that challenge.

## Per-candidate verdict

Return:

- Candidate ID and `CONFIRMED`, `REJECTED`, or `NEEDS_MANUAL_REVIEW`.
- Independent code path and exact references checked, including defensive controls and counterevidence.
- Attacker capability, preconditions, realistic impact, and recommended P0/P1/P2 severity if applicable.
- Checks performed and their actual results; distinguish observation from inference. Never fabricate a test, trace, CVE, or exploit demonstration.
- Remaining uncertainty and the minimum safe check or human decision that would resolve it.

Use `CONFIRMED` only when the security boundary crossing and meaningful impact are supported by adequate evidence. Use `REJECTED` only when the original claim is demonstrably false, duplicate of a consolidated root cause, or not a security vulnerability. Use `NEEDS_MANUAL_REVIEW` for incomplete evidence, unavailable deployment context, unsafe-to-test behavior, or unresolved material uncertainty. A single negative verdict does not automatically reject a candidate; the primary agent reconciles evidence and preserves disagreement.
