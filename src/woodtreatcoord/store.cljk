(ns woodtreatcoord.store
  "SSoT for the ISCO-08 7521 wood treaters treatment-facility
  scheduling/logistics coordination actor (itonami actor pattern,
  ADR-2607121000 / CLAUDE.md Actors section; README's 'Robotics
  premise' — a treatment-facility scheduling/logistics coordination
  robot performs crew scheduling, batch/inventory/progress-record
  logging and preservative-chemicals/timber-stock supply-order
  coordination for a wood-treatment crew under this advisor/governor
  pair, which never dispatches hardware itself, never performs wood
  treatment work itself, and never finalizes a treatment-execution
  decision or a chemical-safety-clearance decision, and never overrides
  a shop safety officer's judgment — those remain the shop safety
  officer's exclusive judgment). Modeled closely on
  cloud-itonami-isco-7514's preservecoord.store.

  Domain:

    treater — a registered wood-treatment crew member (:treater-id,
              :name)
    facility — a registered treatment-facility site {:facility-id :name
              :max-supply-cost number}. `:max-supply-cost` is an
              informational registered ceiling used only to decide
              whether a `:coordinate-supply-order` proposal escalates
              to human sign-off (the governor never blocks a
              within-threshold order outright; it only decides
              commit vs. escalate).
    record  — a committed operating record (a logged batch/inventory/
              progress entry, a scheduled crew/treatment-cycle
              operation, a flagged safety concern, or a coordinated
              preservative-chemicals/timber-stock supply order) —
              written ONLY via commit-record!.
    ledger  — append-only audit trail, commit or hold.")

(defprotocol Store
  (treater [s treater-id])
  (facility [s facility-id])
  (records-of [s treater-id])
  (ledger [s])
  (register-treater! [s treater])
  (register-facility! [s facility])
  (commit-record! [s record])
  (append-ledger! [s fact]))

(defrecord MemStore [a]
  Store
  (treater [_ treater-id] (get-in @a [:treaters treater-id]))
  (facility [_ facility-id] (get-in @a [:facilities facility-id]))
  (records-of [_ treater-id] (filter #(= treater-id (:treater-id %)) (:records @a)))
  (ledger [_] (:ledger @a))
  (register-treater! [s t]
    (swap! a assoc-in [:treaters (:treater-id t)] t) s)
  (register-facility! [s f]
    (swap! a assoc-in [:facilities (:facility-id f)] f) s)
  (commit-record! [s record]
    (swap! a update :records (fnil conj []) record) s)
  (append-ledger! [s fact]
    (swap! a update :ledger (fnil conj []) fact) s))

(defn mem-store
  ([] (mem-store {}))
  ([seed] (->MemStore (atom (merge {:treaters {} :facilities {} :records [] :ledger []}
                                    seed)))))
