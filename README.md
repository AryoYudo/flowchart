# NagoyaOne ERP — Controlled Development
## M5 — AP Invoice & Matching — Meeting Status Update

**Purpose:** current executive/PM status for M5 and surrounding roadmap.  
**Status date:** 2026-09-07  
**Branch:** `dev`  
**Current canonical source HEAD:** `f9f34f3ab59359491a3245a9aef87a197ae7e578`  
**Current milestone:** `M5 — AP Invoice & Matching`  
**Current gate:** `M5.6 — Cancellation / Recovery`  
**Governance state:** `GOV-AP-006C = PM VERIFIED`, governance commit/push still pending before final M5.6 closure audit.

---

# 1. Controlled Roadmap M0–M15

| Milestone | Status | Scope |
|---|---|---|
| M0 — Governance Foundation | ✅ CLOSED | Controlled-development governance, task gates, PM verification, single-writer/direct-dev rules |
| M1 — Approval Security & Canonical Identity | ✅ CLOSED | maker/submitter/approver identity, approval security, self-approval protection |
| M2 — Canonical Procure-to-Pay E2E | ✅ CLOSED | canonical P2P/payment truth, audit, Finance Close, inventory/payment integrity |
| M3 — Procurement Hardening | ✅ CLOSED | MR/PO lifecycle, approval identity, optimistic concurrency, supplier historical truth |
| M4 — Warehouse & Inventory | ✅ CLOSED / CANONICAL | GRN, StockBalance, StockMovement, pickup/return, warehouse concurrency and controls |
| M5 — AP Invoice & Matching | 🟡 CURRENT | duplicate invoice protection, AP concurrency, receipt evidence, SoD, cancellation/recovery, payment handoff, AP UX |
| M6 — Cash Flow & Payment Voucher | ⏳ QUEUED | weekly batch, approvals, voucher package/due-date controls |
| M7 — Treasury | ⏳ QUEUED | prepare/execute/Transferred/Realized, execution evidence |
| M8 — Reconciliation & Closing | ⏳ QUEUED | reconciliation, clearing, closing |
| M9 — Accounting | ⏳ QUEUED | subledger, account determination, automatic journals, GL/AP/GRIR/advance/bank/close |
| M10 — Tax | ⏳ QUEUED | tax calculation/compliance integration |
| M11 — Cross-Module Integration | ⏳ QUEUED | end-to-end module contracts |
| M12 — Platform Controls | ⏳ QUEUED | platform-wide control hardening |
| M13 — UI/UX | ⏳ QUEUED | final cross-module user workflow/UI hardening |
| M14 — E2E Quality Gate | ⏳ QUEUED | full regression and release quality gate |
| M15 — UAT / Release / Go-Live | ⏳ QUEUED | UAT, release, cutover, go-live |

Current location:

`M5 → M5.6 Cancellation / Recovery → governance reconciliation PM VERIFIED → governance commit/push → Final M5.6 Closure Audit`

---

# 2. M5 — Current Detailed Status

| Tahap | Fokus | Buat apa | Status |
|---|---|---|---|
| **M5.0** | Startup & Discovery | Memetakan kondisi AP sebenarnya sebelum coding: flow, schema, permission, lifecycle, test, payment boundary, dan gap yang masih reachable. | ✅ **DONE** |
| **M5.1** | AP-P1-001 Decision & Data Gate | Mengunci aturan identitas `SupplierInvoiceNo`, normalization, duplicate behavior, dan mengecek existing duplicate data sebelum DB enforcement. | ✅ **DONE / DECISION LOCKED** — collision existing data tetap menjadi **deployment gate**, bukan source blocker |
| **M5.2** | SupplierInvoiceNo Integrity Hardening | Mengimplementasikan proteksi duplicate supplier invoice di database + API, canonicalization, filtered unique index, migration guard, conflict `409`, dan SQL Server validation. | ✅ **CANONICAL / COMPLETE** |
| **M5.3** | AP Optimistic Concurrency | Mencegah dua user/proses mengubah AP yang sama berdasarkan state stale pada Match/Post/Pay dengan RowVersion, `409 CONCURRENCY_CONFLICT`, fresh token, dan no auto retry. | ✅ **CANONICAL / COMPLETE** |
| **M5.4** | Receipt Evidence Integrity | Memastikan receipt/evidence AP tidak duplicate, verification aman, independent verifier, RowVersion, DB identity authority, dan race protection. | ✅ **CANONICAL / COMPLETE** |
| **M5.5** | Permission & SoD | Memisahkan permission dan actor duties: Receipt Create/Verify, AP Matcher/Poster, payment executor/uploader/realizer, serta memblokir impersonation pada financial mutation. | ✅ **CLOSED / CANONICAL** |
| **M5.6** | Cancellation / Recovery Policy | Mengimplementasikan AP/receipt cancellation yang historis dan aman, RowVersion, Serializable concurrency, identity retention/release, payment dependency fail-closed, audit, dan eligibility safety. | 🟡 **CURRENT — SOURCE IMPLEMENTATION COMPLETE**. B1/B2/B3 canonical, combined regression accepted, governance reconciliation **PM VERIFIED**; menunggu governance commit/push lalu final closure audit |
| **M5.7** | Payment Handoff Validation | Memastikan AP yang sudah masuk lifecycle pembayaran benar-benar menggunakan canonical payment workflow tanpa duplicate projection atau bypass. | ⏳ **PENDING FORMAL GATE** — fondasi utamanya sudah banyak divalidasi di M5.5–M5.6, tetapi M5.7 belum formal closed |
| **M5.8** | AP Frontend Operational UX | Membuat UI AP jujur terhadap conflict, evidence, status, permission, cancellation, refresh, error mapping, dan action availability. | ⏳ **PENDING** — AP/Receipt cancellation frontend sengaja dibawa ke sini |
| **M5.9** | Full AP Regression | Menjalankan regression AP besar setelah seluruh hardening utama selesai agar perubahan satu area tidak merusak area AP lain. | ⏳ **PENDING** — regression besar sudah dilakukan per gate, tetapi M5.9 belum formal complete |
| **M5.10** | M5 Final Closure Audit | Mengecek seluruh M5: runtime, schema, security, SoD, DB invariants, payment handoff, frontend boundary, regression, dan deployment gaps. | ⏳ **PENDING** |
| **M5.11** | M5 Closure | PM menutup M5 resmi, governance final canonical, repo clean, lalu membuka milestone berikutnya. | ⏳ **PENDING** |

---

# 3. M5.6 — What Is Already Canonical

## B1 — AP Cancellation Status / Domain / Schema

✅ `IMP-AP-006B1 — CANONICAL`

Implemented:

- `ApInvoiceStatus.Cancelled = 5`
- AP cancellation metadata: `CancelledAt`, `CancelledByUserId`, `CancellationReason`
- only `Draft/Hold -> Cancelled`
- `Matched/Posted/Paid/Cancelled -> Cancelled` rejected
- Cancelled is terminal
- SupplierInvoiceNo identity retained
- GoodReceiveNoteId identity retained
- no hard delete
- forward migration and DB invariant

Canonical commit:

`3aada3ac9f1397e755de20190f6950de9641df92`

## B2 — Receipt Cancellation Runtime

✅ `IMP-AP-006B2 — CANONICAL`

Implemented:

- `POST /api/ap-invoices/receipt-logs/{id}/cancel`
- permission `finance.invoice_receipt_log.cancel`
- initial business role: `finance.ap`
- RowVersion concurrency
- explicit Serializable transaction
- attached receipt hard-block
- Cancel-vs-Verify safety
- Cancel-vs-Attach safety
- replacement receipt identity safety
- transactional audit
- impersonation forbidden

Canonical commit:

`767d72247c95b94d26a86222b91a415393db19a9`

## B3 — AP Cancellation Runtime

✅ `IMP-AP-006B3 — CANONICAL`

Implemented:

- `POST /api/ap-invoices/{id}/cancel`
- permission `finance.ap_invoice.cancel`
- initial business role: `finance.ap`
- AP RowVersion concurrency
- explicit Serializable transaction
- payment dependency fail-closed
- Cancel-vs-Match safety
- Cancel-vs-Allocation protection
- Cancelled excluded from Match/re-Match, Post, legacy `/pay`, canonical Paid projection, new allocation, Realize, reconciliation, cash-flow candidates, readiness, pickup, and active AP workflow semantics
- transactional audit
- impersonation forbidden
- no payment reversal
- no receipt detach
- no SupplierInvoiceNo/GRN identity release

Canonical commit:

`f9f34f3ab59359491a3245a9aef87a197ae7e578`

---

# 4. M5.6 Combined Validation Evidence

`VAL-AP-006C = COMPLETE / PM ACCEPTED`

Focused combined validation:

`228 PASS / 0 FAIL / 0 SKIP`

Full backend regression:

`1395 PASS / 14 accepted existing/environment FAIL / 0 SKIP / 1409 TOTAL`

Build:

`0 errors / 5 existing warnings`

EF model validation:

`No changes have been made to the model since the last migration.`

Conclusion:

- no M5.6-specific regression blocker
- no current P1 cancellation production defect found
- M5.6 backend source implementation is complete
- source closure now depends on governance/final closure sequencing, not new coding

---

# 5. Current M5.6 Governance Gate

`GOV-AP-006C = PM VERIFIED`

Governance reconciliation already passed actual-diff review.

It reconciles:

- B1/B2/B3 canonical status
- VAL-AP-006C accepted evidence
- current M5.6 status
- historical/superseded roadmap entries
- recovery/correction deferment
- frontend M5.8 ownership
- source-vs-deployment distinction

Current action:

`GOV-AP-006C verified governance patch -> commit/push -> clean baseline`

After that:

`M5.6 Final Closure Audit`

M5.6 is **NOT yet formally CLOSED**.

---

# 6. What Cancellation Means

Canonical policy:

`CANCEL = TERMINATE / QUARANTINE HISTORY`

It does **not** mean:

- DELETE
- VOID PAYMENT
- PAYMENT REVERSAL
- GRN REVERSAL
- CORRECT
- AMEND
- REPLACE
- RECREATE

## AP cancellation

Allowed:

- `Draft -> Cancelled`
- `Hold -> Cancelled`

Forbidden:

- `Matched -> Cancelled`
- `Posted -> Cancelled`
- `Paid -> Cancelled`

A Cancelled AP continues occupying:

- `SupplierInvoiceNo` identity
- `GoodReceiveNoteId` identity

## Receipt cancellation

Allowed only when receipt is unattached:

- `Logged -> Cancelled`
- `Verified -> Cancelled`

A cancelled receipt releases only its active receipt identity so a replacement receipt can be created while the historical record remains.

---

# 7. Deferred Capability

## Recovery / Correction

`CORRECT / AMEND / REPLACE = DEFERRED`

Current known limitation:

- Cancelled AP cannot be recreated using the same `SupplierInvoiceNo`
- Cancelled AP cannot reuse the same `GoodReceiveNoteId`
- Draft/Hold correction/edit workflow is not yet exposed
- Posted/Paid reversal is not implemented

This is intentional under the current M5.6 boundary and does **not** block M5.6 source closure.

## Frontend

Cancellation frontend is deferred to:

`M5.8 — AP Frontend Operational UX`

Still pending:

- explicit AP `Cancelled` frontend typing
- AP Cancel API/client action
- Receipt Cancel action
- cancellation reason modal
- terminal-state visual treatment
- operational conflict/error UX

Backend cancellation is complete; frontend invocation is not yet implemented.

---

# 8. Source Closure vs Deployment Readiness

These are intentionally separated.

| Area | Status |
|---|---|
| M5.6 backend/source implementation | ✅ COMPLETE |
| M5.6 combined regression | ✅ ACCEPTED |
| M5.6 governance reconciliation | ✅ PM VERIFIED — commit/push pending |
| M5.6 final closure audit | ⏳ NEXT AFTER GOVERNANCE COMMIT |
| M5.6 source closure | ⏳ NOT YET CLOSED |
| Configured Development DB deployment | ⚠️ NOT READY |

Deployment not ready does **not** mean source is incomplete.

---

# 9. Current Deployment / Data Gates

## M5.2 SupplierInvoiceNo Collision

Existing Development data collision remains:

- Supplier: `SUP-000003 / PT. Sanford Batam`
- `AP26080006` — `Posted` — SupplierInvoiceNo `321321`
- `AP26080002` — `Draft` — SupplierInvoiceNo `321321`

Important:

- cancellation does **not** solve this collision
- a Cancelled AP still occupies SupplierInvoiceNo identity
- remediation needs a separate controlled data disposition
- source closure is not blocked by this collision
- migration/deployment readiness is blocked until disposition is resolved

## Configured Development DB

Development DB is still behind canonical migration state.

Required future deployment ordering includes:

1. resolve M5.2 data collision disposition
2. apply canonical pending migrations in controlled order
3. validate receipt/AP schema alignment
4. validate permission/role seed alignment
5. deploy compatible B2/B3 runtime

No configured Development DB migration/write is authorized by the current source-closure work.

## Treasury Drift

Known configured-DB drift:

`finance.treasury.execute`

Canonical source seeder remains authority.

Classification:

`DEPLOYMENT / CONFIGURATION GATE`

This does not block M5.6 source closure.

---

# 10. Key Management Talking Points

| Area | Current Condition |
|---|---|
| Duplicate supplier invoice protection | ✅ DB + API canonical protection |
| AP stale/concurrent update | ✅ RowVersion + 409 conflict protection |
| Receipt/evidence duplicate & race | ✅ DB identity + concurrency protection |
| Independent receipt verification | ✅ Permission and actor controls |
| AP Matcher vs Poster SoD | ✅ Enforced |
| Payment actor SoD | ✅ Enforced |
| Receipt cancellation | ✅ Backend canonical |
| AP Draft/Hold cancellation | ✅ Backend canonical |
| Cancelled AP entering payment | ✅ Blocked |
| Posted/Paid reversal | ⏳ Future controlled policy, not part of M5.6 |
| Financial history hard-delete | 🚫 Not allowed |
| AP identity release after cancellation | 🚫 Not allowed |
| Receipt active identity release after cancellation | ✅ Allowed when unattached |
| Frontend cancellation | ⏳ M5.8 |
| Development DB deployment | ⚠️ Not ready |
| Existing AP `321321` collision | ⚠️ Still requires controlled remediation |
| M5.6 source implementation | ✅ Complete |
| M5.6 formal closure | ⏳ Final closure audit still pending |

---

# 11. Progress Bar Snapshot

Planning estimate only, not contractual.

Overall Controlled Development:

`█████████░░░░░░░░░░░  ~40–45%`

M2:

`████████████████████  100% CLOSED`

M3:

`████████████████████  100% CLOSED`

M4:

`████████████████████  100% CLOSED / CANONICAL`

M5 backend hardening core:

`███████████████░░░░░  ~70–80% COMPLETE`

M5 overall milestone:

`████████████░░░░░░░░  ~60% ACTIVE`

Current next task:

`GOV-AP-006C commit/push → M5.6 Final Closure Audit`

---

# 12. Current Controlled Sequence

```text
M3 ✅ CLOSED
↓
M4 ✅ CLOSED / CANONICAL
↓
M5 CURRENT
↓
M5.0 ✅ Discovery
↓
M5.1 ✅ Decision & Data Gate
↓
M5.2 ✅ SupplierInvoiceNo Integrity
↓
M5.3 ✅ AP Optimistic Concurrency
↓
M5.4 ✅ Receipt Evidence Integrity
↓
M5.5 ✅ Permission & SoD CLOSED
↓
M5.6 CURRENT
   ├─ B1 ✅ CANONICAL
   ├─ B2 ✅ CANONICAL
   ├─ B3 ✅ CANONICAL
   ├─ VAL-AP-006C ✅ PM ACCEPTED
   └─ GOV-AP-006C ✅ PM VERIFIED
↓
GOV-AP-006C commit/push
↓
M5.6 Final Closure Audit
↓
M5.6 source closure decision
↓
M5.7 Payment Handoff Validation
↓
M5.8 AP Frontend Operational UX
↓
M5.9 Full AP Regression
↓
M5.10 M5 Final Closure Audit
↓
M5.11 M5 Closure
```

---

# 13. Planning Progress Estimate

Planning estimate only, not contractual.

- **M3:** `100% CLOSED`
- **M4:** `100% CLOSED / CANONICAL`
- **M5 backend hardening core:** approximately `70–80% complete`
- **M5 overall milestone:** approximately `~60% active progress`
- **Overall Controlled Development:** approximately `~40–45%`, but milestone closure remains the primary management indicator

Recommended management statement:

> **NagoyaOne sudah melewati Procurement dan Warehouse hardening. Saat ini fokus ada di AP Invoice & Matching (M5). Core backend hardening AP—duplicate prevention, concurrency, receipt integrity, permission/SoD, serta AP/receipt cancellation—sudah complete secara source. M5.6 tinggal governance commit dan final closure audit. Setelah itu fokus bergerak ke payment handoff, frontend operational UX, full regression, dan M5 closure. Deployment Development DB masih ditahan oleh migration/data gate yang dikontrol terpisah.**
