# Review challenges: please try to break the effect contract

**Public discussion artifact.** The traces below are minimal threat-model/correctness counterexamples. They are not claims of successful exploitation of an actual ODHA deployment, and they are not executable tests in this repository.

## How to review

For each counterexample, identify (1) the claimed invariant, (2) the minimum initial state and trust assumptions, (3) the exact event order, (4) the effect that becomes possible, and (5) a measurement that would falsify the proposed fix.

A counterexample to the specification is welcome even without private source access. Implementation-level security assessment remains impossible until a suitable reproducible public fixture exists.

| ID | Challenge | Minimal sequence | What a correct design must establish |
| --- | --- | --- | --- |
| R1 | **Queued revocation** | Effect preparation blocks a serial worker; revoke waits behind it; adapter performs effect. | Define revoke acceptance/linearization and whether final ACK or receipt has precedence. |
| R2 | **Adapter retains old grant** | Adapter reads authorization at startup; a grant is revoked; old instance uses a cached decision. | Endpoint checks current authority or loses the ability to write. |
| R3 | **Ack before durable fence** | Revoke is reported final while RAM-only state is set; power fails; old attempt resumes. | Final ACK must depend on both effective and durable end-of-authority evidence. |
| C1 | **Orphan writer after restart** | Owner crashes and releases its store lock; old adapter survives; new Owner completes metadata recovery; old adapter writes. | Restart must fence all affected old writer capabilities before reopening admission. |
| C2 | **Commit response lost** | Durable attempt or actual effect happened; acknowledgement disappears; caller retries same request. | No second effect; observer-only recovery and explicit uncertainty. |
| C3 | **Complete rollback** | Target, journal and local consumption records are all restored to an earlier snapshot; request is replayed. | An independently protected monotonic reference is required for anti-replay under that threat model. |
| C4 | **Crash across the effect boundary** | Process crashes during staging, replacement, sync or observation. | Distinguish never attempted, may have happened, current target state and independently attributable effect. |
| T1 | **Alternate writer** | A helper, inherited descriptor, privileged IPC path or other actor writes the target without entering the gate. | Enumerate and exclude every equivalent in-scope effect path; else `SYSTEM_CLOSED = NO`. |
| A1 | **Authenticated but unbound** | A valid signer approves a different resource, pre-state or operation generation; payload is reused. | Cryptographic authenticity *and* exact state/operation binding, not signature validity alone. |
| O1 | **Observer trusts executor** | Adapter reports success but target is unchanged, replaced by another writer, or briefly changed and restored. | Independently observed current state; attribution and history left UNKNOWN when not provable. |
| L1 | **Fail-closed forever** | Every request is denied, including a correctly authorized request on a healthy endpoint. | Positive liveness test: one admitted operation produces exactly one bounded effect and fresh observation under declared progress assumptions. |

## Especially useful objections

**1. Where is the real linearization point?**  
An API call named `before_effect` may precede file access, synchronization and process creation. The actual irreversible boundary is adapter-specific. What precisely is still revocable at each point?

**2. How do we prove write exclusivity?**  
A mutex shared by two cooperative components does not prevent an uncooperative process with direct filesystem rights. Which OS/endpoint mechanism denies bypass writes, and who verifies it?

**3. Can the revoke channel make progress while the effect is slow?**  
An otherwise correct lock does not help if revocation messages cannot be processed until preparation completes.

**4. What exactly did "cancelled" mean?**  
Request received, endpoint fenced, durable fence bound, attempt not executed, and target state observed are distinct claims. Which are supported by the acknowledgement?

**5. Can a fresh owner accidentally resurrect the old one?**  
A new generation value does not withdraw inherited descriptors or queued capabilities. How are old contexts fenced across a process restart?

**6. What is outside the rollback domain?**  
If the target and journal are both restored, what independently protected state makes the prior effect distinguishable?

**7. Are we mistaking state for history?**  
Seeing the old target bytes after a restart does not prove the new bytes were never visible. What attribution or temporal claim is actually possible?

**8. Is `E_SCOPE` honestly enumerated?**  
If a privileged helper or alternative IPC path can produce the same effect without the contract, the claimed closure is false regardless of passing tests on the documented path.

## Suggested issue format

```text
Claim / invariant challenged:
Effect path and scope:
Assumed initial state:
Events in exact order:
Observed or possible counterexample:
Why the current contract does not exclude it:
Minimal additional test / proof obligation:
Source or reproducible evidence (if available):
```

[Open a public issue](https://github.com/andreaslaube2639-ship-it/odha-t40-public/issues/new).

Please avoid posting secrets, operational account details or sensitive real-host evidence. This project seeks falsifiable criticism, not disclosure of credentials.
