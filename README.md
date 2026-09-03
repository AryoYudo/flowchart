# NagoyaOne ERP — Controlled Development
## 03 — Roadmap and Gaps

**Purpose:** canonical milestone/gap status for M0–M15.

**Canonical status date:** 2026-09-03

---

# 1. Controlled Roadmap M0–M15

| Milestone | Status | Scope |
|---|---|---|
| M0 — Governance Foundation | ✅ CLOSED | Controlled-development governance and foundational project rules |
| M1 — Approval Security & Canonical Identity | ✅ CLOSED | maker/submitter/approver identity, self-approval prevention, security hardening |
| M2 — Canonical Procure-to-Pay E2E | ✅ CLOSED | canonical P2P/payment truth, audit, inventory and Finance Close hardening |
| M3 — Procurement Hardening | 🟡 CURRENT | MR/PO lifecycle, concurrency, revise/reapprove, supplier historical truth |
| M4 — Warehouse & Inventory | ⏳ QUEUED | partial GRN, balances, movements, pickup/return, asset handoff, warehouse controls |
| M5 — AP Invoice & Matching | ⏳ QUEUED — DISCOVERY MAPPED | invoice evidence, matching, duplicate prevention, AP concurrency, mismatch/hold |
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

`M3 → PROC-P1-002 → PurchaseOrder Optimistic Concurrency ← KITA DI SINI`

Next controlled phase:

`DISC-PROC-005A — READ-ONLY DISCOVERY`

---

# 2. Planning Progress Estimate

These are planning estimates, not contractual metrics.

Current reasonable range:

- **Overall Controlled Development:** ~30–35%
- **M2 Canonical P2P:** 100% P1 canonical closure
- **M3 Procurement Hardening:** ~35–45%

Why M3 is not closed:

- PurchaseOrder general optimistic concurrency remains
- Supplier historical snapshot boundary/enforcement remains
- M3 closure audit has not run
- residual P1 findings, if discovered, must be closed before milestone closure

Refresh estimates after each major task or milestone closure.

---

# 3. M2 P1 Gap Status — Final

| GAP | Status | Notes |
|---|---|---|
| GAP-P1-001 — Canonical Payment Truth | ✅ CLOSED | canonical supplier-payment truth locked |
| GAP-P1-002 — Workflow Identity / Cardinality | ✅ CLOSED | exact identity/cardinality through tracking |
| GAP-P1-003 — Workflow Amount Snapshot / PO Mutation Enforcement | ✅ CLOSED | DEC-026 controlled PO financial revision |
| GAP-P1-004 — Transaction + Audit Atomicity | ✅ CLOSED | A/B closed; C deferred P2 |
| GAP-P1-005 — GRN → StockMovement / Inventory Integrity | ✅ CLOSED | A/B and C1–C5 closed; C6 deferred P2 |
| GAP-P1-006 — Explicit Supplier Payment-Proof Verification | ✅ CLOSED | canonical execution/verification/evidence path enforced |
| GAP-P1-007 — Finance Close Multi-AP Allocation Completeness | ✅ CLOSED | DEC-034 + canonical multi-AP completeness enforcement |

M2 P1 remaining business gaps:

`NONE`

Canonical M2 closing head:

`9e5c2c217dcf8cf3351964e4baeddcb5e676d2b7`

M2 status:

`✅ CLOSED / CANONICAL`

---

# 4. M2 Final Closure Highlights

Closed canonical controls include:

- canonical actual payment truth
- Director/Treasury separation
- Transferred execution semantics
- persisted transfer proof
- legacy direct payment containment
- exact workflow identity/cardinality
- payment-voucher package identity
- controlled PO financial revision
- transaction + authoritative audit atomicity
- canonical GRN stock receipt
- GRN concurrency/idempotency
- manual stock mutation hardening
- direct StockBalance mutation lockdown
- canonical stock namespace reservation
- cross-path inventory concurrency protection
- explicit multi-AP allocation snapshot
- deterministic DP netting
- canonical AP Paid downstream projection
- fail-closed Finance Close completeness
- fail-closed Pelunasan tracking/completion
- historical Realized ambiguity rejection

Closing Finance validation:

`306 passed / 0 failed / 0 skipped`

No Finance migration/snapshot delta was introduced by the closing commit.

---

# 5. M3 — Procurement Hardening Full Roadmap

M3 is the current milestone.

## M3.0 — Procurement Baseline Discovery

`DISC-PROC-001A`

✅ COMPLETE

Known P1 register produced from the baseline:

- `PROC-P1-001 — ApprovalRequest Canonical Uniqueness`
- `PROC-P1-002 — Procurement Optimistic Concurrency`
- `PROC-P1-003 — Supplier Historical Snapshots`

---

## M3.1 — PROC-P1-001 ApprovalRequest Canonical Uniqueness

✅ CLOSED

Canonical commit:

`6b603b5dc49cffb381ae30d26e63c61fb300e8c5`

`fix(approvals): enforce canonical request uniqueness`

Locked decision:

`DEC-033`

Canonical identity:

`(ModuleCode, EntityType, EntityId)`

One canonical ApprovalRequest row is reused/reset for resubmission; submission attempts remain historical records.

---

## M3.2 — PROC-P1-002 Procurement Optimistic Concurrency

Split into two controlled slices.

### M3.2A — IMP-PROC-002A MaterialRequest Optimistic Concurrency

✅ CLOSED / CANONICAL

Commit:

`ef415bee053e1cb72721442ad758ccf064288621`

`fix(procurement): enforce material request optimistic concurrency`

Verified controls:

- SQL Server RowVersion
- client token exposure/requirement
- EF OriginalValue usage
- stale-write HTTP 409
- child-only parent concurrency participation
- frontend token propagation/refetch/no automatic retry
- test-only SQLite compatibility
- true SQL Server concurrency proof

Canonical migration:

`20260903025559_AddMaterialRequestRowVersion`

### M3.2B — PurchaseOrder Optimistic Concurrency

⏳ NEXT

Implementation is **not authorized yet**.

First controlled task:

`DISC-PROC-005A — PurchaseOrder Optimistic Concurrency Scope / Interaction Discovery`

Discovery must map at minimum:

- active PO mutation paths
- edit/submit/revise/reapprove/issue/cancel concurrency boundaries
- relationship with existing `ReceiptConcurrencyVersion`
- GRN/receipt interaction
- financial-revision interaction with DEC-026
- frontend token contract
- migration requirement
- true SQL Server race-test matrix
- known canonical `CS8602` warning in `CreatePurchaseOrdersFromMaterialRequestCommandHandler.cs(43,46)`

Do not copy the MaterialRequest design blindly onto PurchaseOrder.

Expected implementation task after discovery/decision:

`IMP-PROC-002B`

---

## M3.3 — PROC-P1-003 Supplier Historical Snapshots

⏳ QUEUED

### M3.3A — Discovery / Boundary

Planned task:

`DISC-PROC-003A — Supplier Historical Snapshot Boundary Discovery`

Must identify which procurement-relevant Supplier fields are:

- live master references
- already persisted transaction snapshots
- required historical truth for PO/procurement
- explicitly deferred to Treasury/Accounting/Tax milestones

Do not automatically snapshot every Supplier field.

Bank/payment identity belongs to Finance/Treasury boundaries where applicable.

Tax identity behavior must respect M10 boundary.

### M3.3B — Implementation

Planned task:

`IMP-PROC-003 — Supplier Historical Snapshot Enforcement`

Target principle:

`later Supplier master changes must not silently rewrite historical procurement truth`

Exact fields and revision semantics require discovery/PM decision first.

---

## M3.4 — M3 Closure Audit

⏳ QUEUED

Planned task:

`DISC-PROC-CLOSE-001A — M3 Procurement Hardening Closure Audit`

Must re-check:

- MR lifecycle
- PO lifecycle
- approval identity
- revise/reapprove
- issue/cancel
- stale writes/races
- supplier historical truth
- authorization
- audit durability
- schema/DB invariants
- frontend/backend contract
- active bypasses

M3 does not close merely because known tasks are implemented.

---

## M3.5 — Residual P1 Fixes

`CONDITIONAL`

Create additional `PROC-P1-00X` only when closure discovery proves a real residual P1/high production gap.

Do not invent tasks just to extend the roadmap.

P2/medium findings may be deferred only through explicit PM governance.

---

## M3.6 — M3 Final Closure Gate

M3 becomes ✅ CLOSED only when:

- PROC-P1-001 closed
- IMP-PROC-002A closed
- IMP-PROC-002B closed
- PROC-P1-003 closed
- no active M3 P1 gap remains
- closure audit passes
- required SQL Server concurrency proof passes
- migrations/snapshot are clean and canonical
- relevant frontend/backend validation passes or explicit environment block is accepted
- PM actual code/diff review completed
- all closure commits are integrated to `dev`
- canonical governance sources are updated

---

# 6. M5 Discovery Register - Pre-Mapped, Not Active

`DISC-AP-001A` COMPLETE / PM ACCEPTED

M5 implementation remains queued. This discovery must be reused as the baseline when M5 opens; do not re-run the same broad discovery from zero unless M3/M4 or later canonical changes materially alter AP behavior. At M5 entry, perform targeted delta-validation only.

## Discovery maturity cache

| Area | Discovery result |
|---|---|
| AP lifecycle | PARTIAL |
| Three-way matching | PARTIAL |
| Partial GRN/AP control | PARTIAL |
| Duplicate supplier invoice DB protection | NO |
| AP receipt evidence | PARTIAL |
| AP payment handoff | YES / CANONICAL |
| AP optimistic concurrency | NO |
| Active AP payment bypass | NO |
| M4 blocks all M5 | NO |

## Reusable technical findings

- AP active path: Draft -> Matched/Hold -> Posted -> Paid downstream projection.
- no active AP edit endpoint was found.
- AP creation requires existing PO/GRN and posted GRN.
- Match enforces PO/GRN line relationship, exact received/invoiced quantity alignment, and price alignment after 2-decimal rounding.
- Draft creation can currently accept mismatching quantity/price; Match later fails closed to Hold.
- one GRN maps to one AP through unique `GoodReceiveNoteId`.
- generic AP does not own an authoritative aggregate PO-level invoiced-quantity guard.
- internal `InvoiceNumber` and `GoodReceiveNoteId` are unique; `SupplierInvoiceNo` is not DB-unique.
- receipt-log duplicate prevention is application-only and not authoritative against concurrency.
- receipt log supports create/verify/list; no active cancellation endpoint was found.
- receipt create and verify currently use the same permission, so independent verifier SoD is not enforced.
- AP Paid remains downstream projection from canonical realized Finance payment; no active AP payment bypass was found.
- AP has no general optimistic concurrency token.
- `Posted` is currently operational AP state; accounting journal/ledger/tax recognition remains M9/M10 scope.

## AP-P1-001 - Supplier Invoice Number Uniqueness & DB Enforcement

Severity: `P1 / critical financial integrity`

Risk:

- duplicate supplier invoice can create duplicate payable candidates
- concurrent creation can race past application checks
- case/normalization identity is not authoritative at DB level

Current protection:

- `InvoiceNumber` unique
- `GoodReceiveNoteId` unique
- `SupplierInvoiceNo` NOT unique

Migration: YES.

M4 dependency: NO for core duplicate enforcement.

Canonical uniqueness scope is NOT YET LOCKED. No `DEC-035` exists. Before implementation, PM must resolve the exact identity/normalization scope and require duplicate-data audit/fail-closed migration behavior.

Recommended implementation candidate after the decision: `IMP-AP-001A - Supplier Invoice Canonical Uniqueness Enforcement`.

## AP-P1-002 - AP Invoice Optimistic Concurrency & Terminal-State Conflict Handling

Severity: `P1 / high`

Risk:

- concurrent Match/Post/Hold/payment projection can operate from stale AP state
- receipt verification/attachment also lacks a general optimistic token

Current state: no AP RowVersion/timestamp concurrency token.

Migration likely required if RowVersion is selected.

M4 dependency: NO for core AP concurrency design.

## AP-P1-003 - Receipt Evidence Source, Verification SoD & Cancellation Control

Severity: `P1 conditional / governance-dependent`

Known issues:

- receipt log stores PO/supplier but not `GoodReceiveNoteId`
- duplicate protection is app-only/exact-case
- create and verify share `finance.invoice_receipt_log.create`
- cancellation exists at entity level but no active cancellation endpoint was found

Migration: NO for permission/API-only completion; POSSIBLY YES if GRN linkage becomes canonical.

M4 dependency: only where receipt-source/reversal authority depends on warehouse decisions.

## Missing tests to add when relevant slices open

- duplicate supplier invoice across GRNs/POs
- concurrent duplicate AP creation
- concurrent Match/Post/Hold/payment projection
- direct GRN quantity versus AP quantity mismatch case
- aggregate AP quantity over PO
- receipt duplicate race and normalization
- receipt cancellation and independent verifier
- controlled AP concurrency conflict response
- due-date behavior only when its business decision is opened

## Cross-milestone boundary

Do not fold these into M5 without explicit PM authorization:

- M4: posted-GRN reversal/correction and warehouse receipt authority
- M9: journal/ledger/accounting recognition
- M10: tax behavior
- supplier tax/bank snapshot policy: separate cross-milestone decision

Controlled roadmap sequencing remains:

`finish M3 -> M4 -> M5 implementation`

unless PM explicitly changes roadmap order.

---

# 7. Deferred / Follow-Up Items

Existing deferred controls include:

- `P1-004-C` external evidence/file orphan handling — DEFERRED P2 / Medium
- `P1-005-C6` permission provisioning / role ownership — DEFERRED P2
- posted-GRN reversal/correction policy
- explicit opening-stock workflow
- historical inventory reconciliation/repair policy
- selected defense-in-depth rollback hardening

Do not silently absorb deferred work into unrelated tasks.

---

# 8. Important Verified Commit Chain

Recent controlled history:

- `52d2f6baf4cba040dfaaae7fb1821641168de783` — `fix(p2p): harden transaction and audit atomicity`
- `53e36813f7c5b6ba5275916ce9464e152fd17670` — `fix(inventory): enforce canonical grn stock receipts`
- `caaf0add08b9b5d7745cd7ec8eb99fb2ef4a2355` — `fix(inventory): harden stock mutation integrity`
- `d34994fa9a7539b3a91a6e610b159ad3eb1c6aaa` — `fix(finance): enforce transfer execution verification`
- `691cc20dc75c84bc8534e79afed3c6d45b304cad` — platform/model snapshot repair
- `6b603b5dc49cffb381ae30d26e63c61fb300e8c5` — `fix(approvals): enforce canonical request uniqueness`
- `31b5bd447d983bf41c63efc0f2377f106c48ce7b` — `test(finance): fix sqlite rowversion compatibility`
- `ef415bee053e1cb72721442ad758ccf064288621` — `fix(procurement): enforce material request optimistic concurrency`
- `9e5c2c217dcf8cf3351964e4baeddcb5e676d2b7` — `fix(finance): enforce multi-ap finance close completeness`

Historical pre-rebase Finance SHA:

`8d3cfe154ff3ab52e785770aff17af0fb014e746`

Do not describe it as the current `dev` SHA.

---

# 9. Known Current Residuals

## Canonical API warning

`CS8602` — `CreatePurchaseOrdersFromMaterialRequestCommandHandler.cs(43,46)`

Classification:

`EXISTING CANONICAL PROC-P1-002A WARNING / NON-FINANCE REGRESSION`

Disposition:

inspect during the next procurement boundary; do not fix under unrelated work.

## Latest broader backend baseline

`1057 passed / 10 failed`

Known failure classes are baseline/environment/shared DB schema drift; no Finance regression was identified at M2 closure.

Do not use unrelated-task scope to normalize these failures.

---

# 10. Current Sequence

Immediate controlled sequence:

```text
M2 ✅ CLOSED
↓
M3 CURRENT
↓
DISC-PROC-005A
PurchaseOrder concurrency discovery
↓
PM decision if required
↓
IMP-PROC-002B
↓
PM actual diff review
↓
VERIFIED + commit/push/integrate
↓
DISC-PROC-003A
Supplier snapshot boundary
↓
IMP-PROC-003
↓
M3 closure audit
↓
residual P1 only if proven
↓
M3 ✅ CLOSED
↓
M4
↓
M5
↓
M6 ... M15
```

---

# 11. Progress Bar Snapshot

Overall Controlled Development:

`███████░░░░░░░░░░░░░  ~30–35%`

M2:

`████████████████████  100% CLOSED`

M3:

`████████░░░░░░░░░░░░  ~35–45%`

Current next task:

`DISC-PROC-005A — not started`
