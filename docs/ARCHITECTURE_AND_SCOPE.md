# ODHA-T40: effect boundary, scope and proposed contracts

**Status:** public technical review draft, 2026-10-10. This is a description of research targets and privately observed DEV components, not a claim that the entire chain is operational or certified.

## 1. The question

A capable agent may identify a useful change, call a tool, or produce a plausible explanation. None of those facts provides execution authority. The research objective is a bounded technical transition:

```text
PROPOSAL
  -> BOUNDARY
  -> CLASSIFICATION + VALIDATION
  -> AUTHORITY (scoped, current and revocable)
  -> ACTUAL EFFECT
  -> FRESH OBSERVATION
```

No tool, signed command, message transport, policy document or model verdict should substitute for the missing gate.

**Scope definition:** `E_SCOPE` is the explicitly declared set of real effect paths within a particular system boundary, threat model and Trusted Computing Base (TCB). A claim of closure requires every path in that set to cross the required controls. `E_SCOPE` cannot be silently reduced to the paths for which tests happen to exist.

Out-of-scope paths must be declared; **unknown paths cannot be treated as out of scope**.

## 2. Working conceptual components

| Component | Responsibility | Must not be confused with |
| --- | --- | --- |
| Agent or model | Proposes an action and supplies candidate data | An authorization authority |
| Operator decision / broker | Authenticates a bounded decision and its origin | Permission for arbitrary downstream actions |
| Owner / kernel logic (Rust research component) | Admission, attempt consumption, current authorization, recovery contract | The operating-system kernel or a universally deployed enforcement point |
| Controlled endpoint / adapter | Carries out exactly the authorized effect within an enforceable boundary | Any unconstrained process already holding write access |
| Persistent commitment / anchor | Binds the recorded control state across failures | A trusted anti-rollback root without qualified custody |
| Independent observer | Reads fresh target state and reports uncertainty | Adapter return status alone |

These are responsibilities, not evidence that the deployment isolates them correctly. An isolated operator-CAS file effect exists as a private DEV research fixture; it does not establish an integrated production path.

## 3. Candidate invariants for one file effect

Let `op` denote an exact operation, `s` its revocation scope and `E(op)` the actual visible target replacement.

- **Binding:** the admission record identifies installation, actor/grant, resource, expected pre-state, intended operation, version/generation and relevant endpoint.
- **Durable consumption:** `C(op)`, the durable reservation/consumption event, must precede any `E(op)`.
- **Current execution permission:** a valid historical admission record is not sufficient. An execution decision needs a valid *current* grant, session/epoch and endpoint state.
- **Revocation ordering:** `F(s)` is the effective exclusion of a covered effect at its real endpoint, `D(s)` is its durable binding, and `ACK(s)` is final confirmation. `ACK(s)` requires `F(s)`, `D(s)`, and the exclusion or accounted-for completion of outstanding affected writers.
- **Exclusivity:** no in-scope writer may produce an equivalent target effect without crossing the same effective gate.
- **Restart safety:** previously consumed attempts do not gain new execution tickets during recovery; stale process instances and capabilities must be retired or fenced.
- **Observation:** a fresh read of the target supports only the state actually observed. It does not necessarily establish whether an earlier transient effect occurred before a crash or rollback.

An acknowledged revocation is therefore stronger than a request received by a broker. That distinction must be explicit rather than hidden in a response named `cancelled`.

## 4. The revocation race

```text
Case A: revoke wins endpoint ordering
   F(s) -> later attempt to E(op)
   Expected: E(op) must be denied.

Case B: effect wins endpoint ordering
   E(op) -> F(s) -> D(s) -> ACK(s)
   Expected: effect may have happened; ACK must not claim to undo it.

Case C: revoke request arrives while effect is inside a long critical phase
   request_received -> [effect critical phase] -> E(op) -> F(s)
   Expected: no claim that request receipt alone guaranteed prevention.
```

Case C is an important unresolved distinction between a **final-ACK guarantee** and the stronger demand that every revoke received before the target effect must prevail. A critical section, OS boundary, endpoint interface, or protocol redesign must be evaluated against whichever property is actually required.

The candidate `EffectFence` concept locates the final revocation/effect ordering at the controlled endpoint, not merely at an upstream broker.

## 5. Crash and replay

A proposed `RestartFence` couples persistent attempt state with proof that old execution contexts can no longer write. A new owner generation or a released store lock alone is insufficient: an orphaned adapter might still hold the target write capability.

Proposed closed restart sequence:

1. Start with no implicit continuation or reusable effect ticket.
2. Identify the bound endpoint set and relevant old executors.
3. Fence or wait out **all** affected old effect capabilities.
4. Reconcile persistent state, anchor and open checkpoints.
5. Preserve consumed attempts; perform metadata-only recovery.
6. Bind a fresh execution epoch to the endpoint-fence evidence.
7. Obtain fresh target observations before admitting new effects.

This is a **theoretical protocol**, not current source-level proof. Its feasibility depends on qualified endpoint exclusivity, persistence ordering and an anti-rollback reference that survives the modeled failure.

If all local state and its purported anchor can be restored together, the system may be unable to distinguish "never executed" from "executed and rolled back." A local hash chain alone does not solve that problem.

## 6. Limits of current research

The private research code and DEV fixtures are not a public artifact in this repository. In particular, there is no publicly verified guarantee that:

- the runtime executes the latest candidate Owner code;
- only the controlled writer can create a protected effect;
- a TPM/anchor provider has production custody;
- the OS service, file, IPC and device paths have complete `E_SCOPE` coverage;
- end-to-end revocation and restart are already implemented; or
- every effect is definitively attributable under hostile crashes, process death or power loss.

**Review challenge:** show a trace, an alternate path, or a missing TCB assumption that defeats any invariant above. See [the falsification cases](REVIEW_CHALLENGES.md).
