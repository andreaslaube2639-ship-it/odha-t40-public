# ODHA-T40 | Controlling agent-caused effects

**Research-stage Rust security/authorization architecture. Not a deployed security product.**

> **CAPABILITY != AUTHORITY**  
> Being able to invoke a tool, obtain a valid signature, or produce convincing model output is not permission to change the world.

**ODHA-T40 asks a specific engineering question:** Can an execution system make the boundary between an AI/agent proposal and a real, state-changing effect explicit, narrow, durable, revocable, and independently observable?

We are looking for **technical criticism and counterexamples**, not endorsements. In particular, we want to know where an effect could still happen despite the declared authorization boundary.

**Deutsch:** ODHA-T40 untersucht, wie die Wirkungen autonomer Agenten technisch begrenzt werden können. Nicht das Modell entscheidet, was ausgeführt werden darf. Der technische Ausführungspfad muss Berechtigung, Vorzustand, Widerruf und tatsächliche Wirkung verbindlich prüfen. Das Projekt befindet sich in der Entwicklung; produktive Sicherheit oder vollständige Kontrolle aller Effektpfade sind **nicht nachgewiesen**.

## A concrete example

Imagine an agent proposes replacing a bounded state file. The relevant question is not only "did the model receive an approval?" but also:

1. **Which exact effect?** Bind target resource, expected previous state, proposed change, operation identity, generation, and the authorized actor.
2. **Who granted authority?** Establish an authentic, explicitly scoped operator grant. A model-generated instruction or a session credential alone is not a grant.
3. **Can it be revoked?** A revoke that is merely queued while an old executor still has write access is not effective cancellation.
4. **What survives a crash?** A consumed operation must not be replayed just because a process restarted or its response was lost.
5. **Did the effect happen?** An adapter returning "success" is not independent observation of the target state.
6. **Could another path do the same thing?** An alternate writer, inherited descriptor, privileged helper or other route can defeat an otherwise correct gate.

The example is a **review scenario**, not an assertion that these guarantees have all been implemented.

```text
Agent proposal (capability, no authority)
    |
Bound operation + preconditions
    |
Authenticated, scoped operator decision
    |
Admission / persistent attempt / current grant check
    |
Controlled effect boundary  <--- revocation must be ordered here
    |
Actual target effect
    |
Fresh, independent observation / fail-closed recovery
```

A useful critique would show a specific trace that breaks this sequence or demonstrate an effect path that bypasses it.

## What exists, and what does not

This repository is a **documentation-only public interface** to a private development project. The private Rust source has DEV work around authorization, attempts, persistence/recovery, TPM-related evidence, thermal gates, and isolated effect fixtures. These are **developer-reported, privately inspected artifacts**, *not publicly reproducible proofs*. The current research direction includes a revocable endpoint-level effect fence coupled to a restart fence; that design is **not a verified implementation**.

**Not established:** production authority, complete enforcement of all in-scope effect paths, protected writer/anchor custody, full rollback resistance, formally verified revocation, universal effect attribution, or independent certification. `SYSTEM_CLOSED = NO`.

We deliberately do **not** publish private implementation internals, keys, operational endpoints, or raw audit logs here. There is currently no published executable reference implementation, and the repository is not a claim of an open-source code release.

## Read and challenge the design

- [Architecture, effect scope and trust boundaries](docs/ARCHITECTURE_AND_SCOPE.md): proposed control topology, invariants and explicit limits.
- [Falsification and review challenges](docs/REVIEW_CHALLENGES.md): concrete concurrency, crash/restart, replay, and bypass counterexamples.
- [Evidence and claim boundaries](docs/EVIDENCE_AND_LIMITS.md): what is observed privately, proposed theoretically, and still unknown.

**How to challenge:** [Open a GitHub issue](https://github.com/andreaslaube2639-ship-it/odha-t40-public/issues/new). State the violated invariant, affected effect path, initial state, event ordering, and observable counterexample. You do not need to agree with the premise. A well-formed failure trace is more useful than praise.

The [issue tracker](https://github.com/andreaslaube2639-ship-it/odha-t40-public/issues) is for public technical review; please do not post credentials, production endpoints, private documents or exploit-sensitive host data.

## Project boundaries

- **Foundation layer, not a complete agent platform:** T40 concerns whether a given effect may occur. Broader normative decisions about whether an effect *should* be requested remain separate.
- **DEV != production:** Test fixtures, simulated conditions, passing tests and source inspection cannot prove deployment or closure.
- **MODEL_OUTPUT != EVIDENCE:** Independent observation, source provenance and the exact claim scope matter.
- **Authorized != effected != observed:** These must remain distinguishable, including after partial failures.

**Maintainer:** [Andreas Laube on LinkedIn](https://www.linkedin.com/in/andreas-laube-bb54ba245/).

*Public review revision: 2026-10-10. This page invites critique of a bounded specification; it does not represent a security guarantee.*
