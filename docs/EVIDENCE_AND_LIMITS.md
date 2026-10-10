# Evidence, status and disclosure boundaries

**Public status dated 2026-10-10.** This page distinguishes observable public materials, privately inspected development work and hypotheses. It is not a substitute for an independently reproduced test.

## Claim classes

| Classification | What it means here | Public reviewability |
| --- | --- | --- |
| **PUBLICLY INSPECTABLE** | This README and three public architecture/review documents, including their revision history. | Anyone can inspect the text; it proves only what is published, not runtime behavior. |
| **PRIVATE DEV SOURCE / FIXTURE** | A Rust development codebase with authorization, durable attempt/recovery, TPM-related and thermal DEV work, plus isolated operator/CAS effect research. | Maintainer-reported and locally inspected; not reproducible from this repository. |
| **THEORETICAL DESIGN** | Endpoint-level `EffectFence` coupled with persistent `RestartFence`, execution epochs and current-authorization/revocation ordering. | Reviewable as a specification; not claimed to exist as a complete implementation. |
| **OPEN / NOT ESTABLISHED** | Production authority, trusted anchor and writer custody, complete `E_SCOPE` enumeration, crash/power-loss guarantees, formal closure and independent certification. | No positive security claim is made. |

**What private tests do not prove:** A DEV fixture reporting passing tests does not establish production isolation, hardware provenance, exclusive writer authority, absence of all bypass paths or an anti-rollback root. Source code does not prove which binary is currently running. A valid signature does not prove an authorized transition. An adapter's acknowledgement does not prove the target effect.

## Bounded progress vs global completion

The underlying private development work has examined:
- tightly scoped and signed command/admission ideas;
- durable attempt reservation and observer-only recovery;
- independent target-state readback and explicit ambiguity;
- continuous thermal gating and TPM-linked DEV evidence paths;
- lifecycle/broker integration gaps and revocation/restart counterexamples.

This list is a **research inventory**, not a claim that these features form a single verified deployment. The current proposed CAS endpoint/restart contract is **not** a passed end-to-end implementation test.

No public repository artifact currently permits an outsider to reproduce the claimed internal implementation. That is a real verification limitation. We invite specification-level review now and would need a separately reviewed public fixture plus exact run/evidence bindings for implementation-level scrutiny.

## What is deliberately not published

The private Foundry source, sensitive configuration, credentials, TPM/NV material, device identifiers, internal paths and host diagnostics are not part of this release. A critic should not need any of these to challenge the proposed invariants or supply an abstract counterexample.

## Review posture

```text
CAPABILITY != AUTHORITY
MODEL_OUTPUT != EVIDENCE
AUTHORIZED != EFFECTED != FRESHLY_OBSERVED
TEST_PASS != SYSTEM_PROOF
SYSTEM_CLOSED = NO
PRODUCTION_AUTHORITY = NOT_ESTABLISHED
CURRENT_REVOCABLE_EFFECT_ARCHITECTURE = THEORETICAL
```

No license grant for the private implementation is implied by publication of these documents. Do not infer production suitability, third-party certification, or complete open-source availability from the repository name.

To raise a public challenge, see [Review Challenges](REVIEW_CHALLENGES.md).
