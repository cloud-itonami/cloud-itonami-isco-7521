# cloud-itonami-isco-7521

Open Occupation Blueprint for **ISCO-08 7521**: Wood Treaters.

This repository designs a forkable OSS business for a treatment-facility
scheduling and logistics coordination practice: a treatment-facility
scheduling and supply-coordination robot manages crew/task records under a
governor-gated actor, so a wood-treatment crew keeps its own operating
records instead of renting a closed workforce-management SaaS.

**Maturity: `:implemented`.** `src/woodtreatcoord/` implements the
`WoodTreatCoordActor` as a `langgraph.graph/state-graph`
(`woodtreatcoord.actor`) wired to a `Wood Treatment Facility Coordination
Advisor` (`woodtreatcoord.advisor`) and an independent
`WoodTreatCoordGovernor` (`woodtreatcoord.governor`), following the
itonami actor pattern (ADR-2607121000): `:intake -> :advise -> :govern
-> :decide -+-> :commit (:ok? true) +-> :request-approval (:escalate? true,
human-in-the-loop interrupt) +-> :hold (:hard? true)`. HARD invariants
(always hold, never overridable): treater provenance, facility provenance,
no-actuation (`:effect` must be `:propose`), a closed op-allowlist
(`:log-work-record`, `:schedule-crew-operation`, `:flag-safety-concern`,
`:coordinate-supply-order` — nothing else may ever be proposed), and a
permanent, unconditional block on any proposal that would directly
finalize a treatment-execution decision (e.g. deciding to proceed with a
specific wood-treatment run) or a chemical-safety-clearance decision (e.g.
declaring a treated batch safe for handling or shipment), or that would
override a shop safety officer's judgment. Always-escalate paths (human
sign-off regardless of confidence, mapping this repo's Trust Controls in
[`docs/business-model.md`](docs/business-model.md)): `:flag-safety-concern`
(always) and `:coordinate-supply-order` above the registered cost
threshold.

## Robotics premise

All cloud-itonami verticals are designed on the premise that a **robot performs
the physical domain work**. Here a treatment-facility scheduling/logistics
coordination robot performs crew scheduling, batch/inventory/progress-record
logging and preservative-chemicals/timber-stock supply-order coordination
for a wood-treatment crew, under an actor that proposes actions and an
independent **Wood Treatment Facility Coordination Governor** that gates
them. The governor never dispatches hardware itself, never performs wood
treatment work on the facility floor, and never finalizes a
treatment-execution decision or a chemical-safety-clearance decision, and
never overrides a shop safety officer's judgment; `:high`/`:safety-critical`
actions (such as a flagged chemical-exposure/ventilation/equipment-condition
concern, or an above-threshold supply order) require human sign-off. **This
actor coordinates TREATMENT-FACILITY SCHEDULING/LOGISTICS ONLY — it never
performs wood treatment work itself, and it never makes a chemical-safety-
clearance decision itself.**

## Core Contract

```text
crew roster + facility registration + safety-reporting policy
        |
        v
Wood Treatment Facility Coordination Advisor -> WoodTreatCoordGovernor -> log/schedule/coordinate, or human sign-off
        |
        v
robot actions (gated) + operating records + audit ledger
```

No automated advice can dispatch a robot action the governor refuses,
finalize a treatment-execution decision, finalize a chemical-safety-
clearance decision, override a shop safety officer's judgment, suppress an
operating record, or disclose sensitive data without governor approval and
audit evidence.

## Capability layer

Resolves via [`kotoba-lang/occupation`](https://github.com/kotoba-lang/occupation)
(ISCO-08 `7521`). Required capabilities:

- :robotics
- :identity
- :audit-ledger

See [`docs/business-model.md`](docs/business-model.md) and
[`docs/operator-guide.md`](docs/operator-guide.md).

## License

AGPL-3.0-or-later.
