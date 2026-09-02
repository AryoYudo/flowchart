# NagoyaOne ERP — Controlled Development
## 03 — Roadmap and Gaps

**Purpose:** canonical milestone/gap status.

**Canonical status date:** 2026-09-01

---

# 1. Controlled Roadmap M0–M15

| Milestone | Status | Scope |
|---|---|---|
| M0 — Governance Foundation | ✅ CLOSED | Controlled-development governance and foundational project rules |
| M1 — Approval Security & Canonical Identity | ✅ CLOSED | maker/submitter/approver identity, self-approval prevention, security hardening |
| M2 — Canonical Procure-to-Pay E2E | 🟡 CURRENT | canonical P2P truth, payment, audit, inventory integrity hardening |
| M3 — Procurement Hardening | ⏳ QUEUED | MR/PR/PO lifecycle, revise/reapprove, supplier/issue/cancel/change/race controls |
| M4 — Warehouse & Inventory | ⏳ QUEUED | partial GRN, balances, movements, QR/barcode, pickup, asset handoff |
| M5 — AP Invoice & Matching | ⏳ QUEUED | invoice evidence, matching, plans, mismatch/hold |
| M6 — Cash Flow & Payment Voucher | ⏳ QUEUED | weekly batch, approvals, voucher package/due-date controls |
| M7 — Treasury | ⏳ QUEUED | prepare/execute/Transferred/Realized, execution evidence |
| M8 — Reconciliation & Closing | ⏳ QUEUED | reconciliation, clearing, closing |
| M9 — Accounting | ⏳ QUEUED | subledger, account determination, automatic journals, GL/AP/GRIR/advance/bank/close |
| M10 — Tax | ⏳ QUEUED | tax calculation/compliance integration |
| M11 — Cross-Module Integration | ⏳ QUEUED | end-to-end module contracts |
| M12 — Platform Controls | ⏳ QUEUED | platform-wide control hardening |
| M13 — UI/UX | ⏳ QUEUED | final user workflow/UI hardening |
| M14 — E2E Quality Gate | ⏳ QUEUED | full regression and release quality gate |
| M15 — UAT / Release / Go-Live | ⏳ QUEUED | UAT, release, cutover, go-live |

Current location:

`M2 → P1 → GAP-P1-006 ← KITA DI SINI`

---

# 2. Progress Estimate

These are planning estimates, not contractual metrics.

Current reasonable range after closing GAP-P1-005:

- **Overall Controlled Development:** ~25–28%
- **M2 Canonical P2P:** ~85–90%

Why M2 is not closed yet:

- GAP-P1-006 has not started beyond task authorization for discovery
- GAP-P1-007 is not started
- deferred P2 items remain intentionally outside current P1 closure unless PM reprioritizes

Refresh these estimates whenever the current GAP closes or the roadmap scope changes.

---

# 3. M2 P1 Gap Status

| GAP | Status | Notes |
|---|---|---|
| GAP-P1-001 — Canonical Payment Truth | ✅ CLOSED | canonical supplier-payment truth locked |
| GAP-P1-002 — Workflow Identity / Cardinality | ✅ CLOSED | exact identity/cardinality through tracking |
| GAP-P1-003 — Workflow Amount Snapshot / PO Mutation Enforcement | ✅ CLOSED | DEC-026 controlled PO financial revision |
| GAP-P1-004 — Transaction + Audit Atomicity | ✅ CLOSED | A/B closed; C deferred P2 |
| GAP-P1-005 — GRN → StockMovement / Inventory Integrity | ✅ CLOSED | A/B and C1–C5 closed; C6 deferred P2 |
| GAP-P1-006 — Explicit Supplier Payment-Proof Verification | 🟡 CURRENT / NEXT DISCOVERY | next exact task `DISC-P2P-012A`; implementation not started |
| GAP-P1-007 — Finance Close Multi-AP Allocation Completeness | ⏳ NOT STARTED | known P1/High residual from 008F; queued after P1-006 |

---

# 4. GAP-P1-005 Final Closure

## P1-005-A — Canonical GRN StockMovement

✅ CLOSED

Canonical receipt movement is persisted per posted GRN line with exact provenance.

## P1-005-B — GRN Concurrency / Idempotency

✅ CLOSED

Canonical receipt creation and GRN stock posting were hardened and finalized in commit:

`53e36813f7c5b6ba5275916ce9464e152fd17670`

`fix(inventory): enforce canonical grn stock receipts`

## P1-005-C — Generic / Manual Stock Mutation Integrity

✅ CLOSED for P1 scope

Final state:

- C1 ✅ CLOSED
- C2 ✅ CLOSED
- C3 ✅ CLOSED
- C4 ✅ CLOSED
- C5 ✅ CLOSED
- C6 DEFERRED P2

Final pushed commit:

`caaf0add08b9b5d7745cd7ec8eb99fb2ef4a2355`

`fix(inventory): harden stock mutation integrity`

Final integrated validation:

- reviewed task scope: 11 files
- inventory regression: `60 passed / 0 failed / 0 skipped`
- API build: SUCCESS, `0 warnings / 0 errors`
- provider-origin SQLite contention proof preserved
- no unrelated teammate content absorbed into the task commit
- normal fast-forward push completed
- `dev == origin/dev == caaf0ad`
- working tree clean

`P1-005-C6` remains deferred and must not be silently implemented as part of another task.

---

# 5. Important Verified Commit Chain

Recent controlled chain:

- `306b61e` — `fix(finance): enforce payment voucher package identity`
- `a13e9ef` — `fix(finance): enforce exact tracking payment identity`
- `3621a92` — `fix(finance): enforce controlled po financial revision`
- `52d2f6b` — `fix(p2p): harden transaction and audit atomicity`
- `53e3681` — `fix(inventory): enforce canonical grn stock receipts`
- `caaf0ad` — `fix(inventory): harden stock mutation integrity`

Earlier verified 007B–008F chain:

- `c080937`
- `42b9e15`
- `32b20d7`
- `ccfd173`
- `8f317e8`
- `849790f`
- `4f92315`
- `6d8f562`

Do not replace these with old pre-cherry-pick hashes when describing current `dev` history.

---

# 6. Closed Work Highlights

## M0

Governance foundation complete.

## M1

Approval security / canonical identity complete.

Historical security controls include creator cannot approve own transaction, maker/submitter/approver separation, controlled non-fabricating remediation, and unknown historical actor remains unknown rather than being invented.

## M2 completed control areas

- canonical actual payment truth
- Director/Treasury separation
- Transferred semantics
- legacy direct payment containment
- explicit AP allocation
- canonical Pelunasan truth
- FullPayment reconciliation ownership
- persisted transfer-evidence gate
- exact workflow identity/cardinality
- Payment Voucher package identity
- tracking cardinality
- controlled PO financial revision
- transaction + audit atomicity
- canonical GRN StockMovement
- GRN concurrency/idempotency
- manual stock adjustment integrity
- direct StockBalance mutation lockdown
- canonical stock namespace reservation
- cross-path StockBalance concurrency protection for ManualAdjustment / GRN / Pickup / Return

---

# 7. Deferred Items

## P1-004-C — External Evidence/File Orphan Handling

DEFERRED P2 / Medium.

Do not reopen under unrelated work unless separately authorized.

## P1-005-C6 — Permission Provisioning / Role Ownership

DEFERRED P2.

Existing permission gate stays. Do not invent role seeding.

Other intentionally non-current topics:

- posted-GRN reversal/correction policy
- opening-stock workflow
- historical inventory rewrite/reconciliation
- tracked-state rollback defense-in-depth hardening

---

# 8. External / Baseline Repository Residuals

These are tracked separately and do not reopen GAP-P1-005.

## SupplierBankAccount EF model drift

Status:

`REPOSITORY / MIGRATION CONSISTENCY DEFECT, WAITING PM DECISION.`

First state:

`03ef423bcd3a9adcdc1f7a438f54a4edbfde55f1`

Ownership:

remote baseline / teammate change

Exact mismatch:

runtime ordinary `SupplierId` index vs filtered unique `UX_SupplierBankAccounts_DefaultPerSupplier` in migration/model snapshot.

`caaf0ad` / 011E model delta: NO.

## Baseline seeder failures

Not caused by 011E:

- `MasterFlowSeederRunnerTests`: 3 failures
- `AdminUserSeederTests`: 2 failures

## Tracked-state residual

`DEFENSE-IN-DEPTH RISK ONLY`

Not a current blocker.

## SQL Server provider caveat

SQLite executable proof does not claim exact SQL Server lock acquisition/key-range/deadlock behavior.

---

# 9. Current GAP — GAP-P1-006

## GAP-P1-006 — Explicit Supplier Payment-Proof Verification

🟡 CURRENT / NEXT DISCOVERY

Current task:

`DISC-P2P-012A`

Status:

`⏳ NEXT / NOT STARTED`

Required next phase:

`DISCOVERY`

Do not jump directly to implementation.

Discovery should establish actual current payment-proof/evidence behavior, ownership, identity, lifecycle gates, replay/catch-up behavior, audit/transaction boundary, and any ambiguity before governance or implementation is authorized.

---

# 10. Next Sequence

Immediate:

`DISC-P2P-012A — GAP-P1-006 Explicit Supplier Payment-Proof Verification`

Then, only after discovery and any required governance:

`GOVERNANCE/DECISION → IMPLEMENTATION → TEST → ACTUAL CODE/DIFF REVIEW → VERIFIED → COMMIT → PUSH`

After GAP-P1-006:

`GAP-P1-007 — Finance Close Multi-AP Allocation Completeness`

unless PM explicitly reprioritizes.

After M2 closes, continue M3–M15 in roadmap order unless governance changes priority.

---

# 11. Progress Bar Snapshot

Overall:

`█████░░░░░░░░░░░░░░░  ~25–28%`

M2:

`█████████████████░░░  ~85–90%`

GAP-P1-005:

`████████████████████  100% P1 scope CLOSED`

Current GAP-P1-006:

`░░░░░░░░░░░░░░░░░░░░  discovery not started`
