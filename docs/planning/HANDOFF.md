# Implementation Handoff Contract

Use this structure at the end of every substantive implementation work package.

The purpose is to separate implementation claims from evidence so the next reviewer can independently reconcile repository truth.

```text
MILESTONE:
LAYER(S):
BASE SHA:
HEAD SHA:

STATUS:
PASS | PARTIAL | BLOCKED | FAIL

CLAIMS:
- Exact behavior or structure implemented.
- Do not include claims that are only planned or inferred.

CHANGED FILES:
- path — purpose

VALIDATION EVIDENCE:
- command:
  result: PASS | FAIL
  evidence: concise exact result relevant to the claimed property

NEGATIVE / FAILURE EVIDENCE:
- invalid/failure case:
  expected:
  observed:

ARCHITECTURAL DECISIONS:
- decision:
  rationale:
  authority/source:

DEPENDENCIES / CONTRACTS CONSUMED:
- existing contract or dependency relied upon by this work

UNPROVEN:
- property not demonstrated
- validation not run
- environment/path not exercised

KNOWN RISKS:
- demonstrated or credible remaining risk; distinguish evidence from concern

SCOPE DEVIATIONS:
- NONE
or
- exact deviation and reason

COMMITS:
- <sha> <subject>

NEXT DEPENDENCY:
- what must be reviewed, accepted, or become true before subsequent work starts

STOP CONDITION:
- whether the roadmap requires the agent to stop here
```

## Evidence rules

- Include exact commands, not paraphrases such as "ran tests".
- A summary produced by the implementing agent is not independent verification.
- Do not report a property as proven when only a proxy was tested.
- Negative architecture/failure requirements require observed rejection evidence when the roadmap calls for it.
- If a required tool, service, environment, or validation path is unavailable, list it under `UNPROVEN`; do not convert absence of evidence into PASS.
- If repository state differs from the expected starting state, report the discrepancy before claiming completion.

## Mandatory M1 stop

The initial M0 + M1 run must stop after producing this handoff. M2 is not authorized by successful M1 validation. The repository must be reviewed/reconciled and explicitly re-authorized first.
