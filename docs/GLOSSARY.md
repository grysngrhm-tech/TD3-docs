# Glossary

> Key terms used throughout TD3's documentation, defined for quick reference.

---

## Table of Contents

- [Adaptive Card](#adaptive-card)
- [Amortization](#amortization)
- [Audience-Scoped Dedup Key](#audience-scoped-dedup-key)
- [Auto-Approved](#auto-approved)
- [Builder](#builder)
- [builder_member](#builder_member)
- [builder_portal](#builder_portal)
- [Budget / Budget Line](#budget--budget-line)
- [CARD_BUILDERS](#card_builders)
- [Confidence Score](#confidence-score)
- [Construction Loan](#construction-loan)
- [Cost Code](#cost-code)
- [Disbursement](#disbursement)
- [Dispatcher](#dispatcher)
- [Draw Request](#draw-request)
- [Draw Status](#draw-status)
- [Extraction](#extraction)
- [Fee Escalation](#fee-escalation)
- [Funded](#funded)
- [InternalOnly](#internalonly)
- [IRR (Internal Rate of Return)](#irr-internal-rate-of-return)
- [Lender](#lender)
- [Lifecycle Stage](#lifecycle-stage)
- [LTV (Loan-to-Value)](#ltv-loan-to-value)
- [OTP (One-Time Password)](#otp-one-time-password)
- [Outbox](#outbox)
- [Payoff](#payoff)
- [Payoff Adjustments](#payoff-adjustments)
- [Per Diem](#per-diem)
- [Principal Paydown](#principal-paydown)
- [Project](#project)
- [Retainage](#retainage)
- [Row-Level Security](#row-level-security)
- [Semantic Matching](#semantic-matching)
- [STAFF_PERMISSIONS](#staff_permissions)
- [Subcategory](#subcategory)
- [Training Data](#training-data)
- [useStaffOnlyRedirect](#usestaffonlyredirect)
- [Vendor Association](#vendor-association)
- [Wire Batch](#wire-batch)

---

### Adaptive Card

Outlook-renderable interactive email card. Recipients act inline (approve a payoff, mark a wire funded, review a builder's draw) without opening TD3. One channel of the V2 notification pipeline; sent only to internal staff for events that have a `card_builder` registered.


---

### Amortization

A schedule of periodic interest payments over the life of a construction loan. TD3 calculates compound interest with accrual at two trigger points: the last day of each calendar month, and the funding date of each new draw. Interest is charged on the total balance (principal plus all previously accrued interest), producing a real-time view of the loan's interest position. The amortization schedule feeds into payoff calculations, fee escalation tracking, and projection charts.

> See [Architecture: Payoff Workflow](ARCHITECTURE.md#payoff-workflow) for details on financial calculations.

---

### Audience-Scoped Dedup Key

Format `{event_code}:{entity_id}:audience={label}` used in `notification_deliveries` and `user_queue_items`. The audience suffix ensures cascade resolution (e.g. `wire.funded` clearing `wire.pending`) does not cross audience boundaries — a builder reading their queue copy doesn't clear the processor copy and vice versa. The label comes from the matched `notification_rules` row (permission code for `permission` rules; fixed label per entity type for `entity_member` rules).

> See [Permissions & Notifications V2](PERMISSIONS_NOTIFICATIONS_V2.md) §9.6.

---

### Auto-Approved

An AI invoice match with 95% or higher confidence that is applied automatically without human review. Auto-approved matches are skipped in the review queue entirely and logged in the audit trail as automatic decisions. This tier handles clear, unambiguous vendor-to-category alignments where the AI's certainty is comparable to a human expert's.

> See [Artificial Intelligence](ARTIFICIAL_INTELLIGENCE.md#confidence-gated-automation) for details.

---

### Builder

The contractor or construction company performing the work on a project. Each builder record in TD3 carries banking information used for wire transfers, and is linked to one or more projects. Builders submit budget spreadsheets and draw requests throughout the construction process.

> See [Architecture](ARCHITECTURE.md#data-architecture) for details.

---

### builder_member

A row in the `builder_members` table linking a `builder_portal` user to a builder. Many-to-many — one user can be on multiple builder teams; one builder can have multiple users. Drives the disjunctive RLS pattern that scopes builder users to their own data without a parallel app.

> See [Permissions & Notifications V2](PERMISSIONS_NOTIFICATIONS_V2.md) §4.1.

---

### builder_portal

Permission code marking a user as an external builder team member rather than TD3 staff. Always combined with at least one `builder_members` row. Builder users only see the three pages their `builder_members` rows scope them to (`/builders/[id]`, `/projects/[id]`, `/draws/[id]`) plus `/account*`; every other top-level page wraps with `useStaffOnlyRedirect`.

---

### Budget / Budget Line

Categorized line items that define the expected cost structure of a construction build, organized against TD3's standardized cost codes. Each budget line tracks an original amount, a current approved amount, and the amount already spent through funded draws. Once a draw has been funded against a budget line, that line is protected from deletion during re-imports.

> See [Architecture](ARCHITECTURE.md#budget-intelligence) for details.

---

### CARD_BUILDERS

Registry in `lib/adaptive-cards/index.ts` mapping `notification_events.card_builder` keys (`funding_date_card`, `payoff_verification_card`, `draw_review_card`) to async card-building functions. The V2 dispatcher's email channel looks up the builder by key, calls it with the event payload, and hands the resulting card JSON to `sendAdaptiveCardEmail`.

---

### Confidence Score

A numerical measure (0--100%) of the AI's certainty in a match or classification decision. Confidence scores drive TD3's three-tier automation system: 95% or above triggers auto-approval, 70--94% prompts a one-click human confirmation, and below 70% routes the decision to full manual review. Every confidence score is recorded in the audit trail alongside the final outcome.

> See [Artificial Intelligence](ARTIFICIAL_INTELLIGENCE.md#confidence-gated-automation) for details.

---

### Construction Loan

A short-term loan that finances the construction of a residential property, disbursed in stages as work progresses. In TD3, each construction loan is represented as a project that links a builder, lender, property, and all associated budgets, draws, and wire batches.

---

### Cost Code

TD3's proprietary classification system for construction costs, inspired by the National Association of Home Builders (NAHB) framework. The system organizes all costs into 12 parent categories and 89 subcategories, numbered in 100-point increments following the chronological order of construction. Cost codes enable cross-project comparison, builder performance tracking, and portfolio-level analytics by mapping every builder's unique terminology to a single canonical structure.

> See [Artificial Intelligence](ARTIFICIAL_INTELLIGENCE.md#td3s-standardized-cost-code-system) for details.

---

### Disbursement

A release of funds from the construction loan to pay for completed work. In TD3, disbursements are tracked through draw requests and wire batches, with budget spent amounts updated atomically when funding is confirmed.

---

### Dispatcher

[`lib/notifications/dispatcher.ts`](../lib/notifications/dispatcher.ts) plus the cron at `/api/cron/dispatch-notifications`. Drains `notification_outbox` every minute and fans events out to recipients across channels (in-app + email). Idempotent via `notification_deliveries` source-of-truth check before any handler call. Replaces the deleted `lib/notifications.ts` direct-dispatch orchestrator.

> See [Permissions & Notifications V2](PERMISSIONS_NOTIFICATIONS_V2.md) §9.

---

### Draw Request

A builder's formal request for funds, containing individual line items matched to budget categories. Each draw request progresses through a structured workflow: review, staged, pending wire, and funded. Invoices are uploaded alongside draw requests to support the funding amounts, and AI assists with matching invoices to the correct budget lines.

> See [Architecture](ARCHITECTURE.md#draw-processing-workflow) for details.

---

### Draw Status

The current stage of a draw request in the approval pipeline. TD3 uses four statuses: **review** (initial submission, under processor review), **staged** (approved and grouped by builder, ready for wire transfer), **pending wire** (wire batch created, awaiting funding confirmation), and **funded** (funds released, records locked as immutable).

> See [Architecture](ARCHITECTURE.md#draw-processing-workflow) for details.

---

### Extraction

The AI process of reading an uploaded invoice PDF and converting it into structured data---vendor name, amount, invoice number, date, line item descriptions, trade classification, and work context. TD3 uses a model optimized for high-volume document parsing, processing invoices asynchronously so the user can continue working while extraction runs in the background.

> See [Artificial Intelligence](ARTIFICIAL_INTELLIGENCE.md#invoice-data-extraction) for details.

---

### Fee Escalation

TD3's tiered fee structure that increases over the loan term to incentivize timely project completion. The fee starts at 2% for months 1--6, increases by 0.25% per month from months 7--12, jumps to 5.9% at month 13 (the extension fee), and increases by 0.4% per month thereafter. The fee is calculated on the total principal drawn (not the committed loan amount). Fee escalation is visualized in the Payoff tab's Fee Escalation Timeline chart.

> See [Architecture: Payoff Workflow](ARCHITECTURE.md#payoff-workflow) for details.

---

### Funded

The final status of a draw request or wire batch, indicating that funds have been released to the builder. Once a record reaches funded status, it becomes immutable---budget amounts, draw line items, wire batch details, and invoice matches are locked as a permanent financial record. The funding action also triggers training data capture for the AI learning system.

> See [Security](SECURITY.md#audit-trail) for details on immutability.

---

### InternalOnly

`<InternalOnly>` component in [`app/components/auth/InternalOnly.tsx`](../app/components/auth/InternalOnly.tsx). Sugar over `<PermissionGate>` with `STAFF_PERMISSIONS`. The canonical wrapper for any internal data (IRR, lender financials, processor notes, AI confidence scores, performance comparisons) that appears on shared pages where builder users land.

---

### IRR (Internal Rate of Return)

The annualized effective rate of return on a construction loan, accounting for the timing and amounts of all cash flows. TD3 calculates IRR using the Newton-Raphson iterative method on the loan's actual disbursement schedule and payment history, providing an accurate measure of realized yield.

---

### Lender

The financial institution funding the construction loan. Each lender is linked to one or more projects in TD3, and lender-specific data includes loan terms, interest rates, and reporting requirements. The upcoming lender portal will provide read-only access to portfolio views, loan summaries, and exportable reports.

> See [Roadmap](ROADMAP.md#builder--lender-portals) for details on the planned lender portal.

---

### Lifecycle Stage

A project's current phase in TD3. Every construction loan progresses through three stages: **Pending** (loan origination, documents being prepared), **Active** (loan funded and in progress, budgets tracked and draws processed), and **Historic** (loan paid off, all data preserved as an immutable archive for performance analysis and auditing).

> See [Architecture](ARCHITECTURE.md#loan-lifecycle-management) for details.

---

### LTV (Loan-to-Value)

The ratio of the loan amount to the appraised property value, expressed as a percentage. TD3 uses color-coded risk indicators to surface LTV at a glance: green at 65% or below (low risk), yellow from 66--74% (moderate risk), and red at 75% or above (elevated risk). LTV is displayed on dashboards and loan detail views to support lending decisions.

---

### OTP (One-Time Password)

An 8-digit numeric code sent via email for passwordless authentication. Each code expires after a short window and can only be used once. TD3 uses OTP codes rather than email links because numeric codes are immune to the link-scanning behavior of enterprise email security systems that can inadvertently consume magic links before the user clicks them.

> See [Security](SECURITY.md#authentication-who-can-sign-in) for details.

---

### Outbox

`notification_outbox` table. SQL triggers write rows here inside the same transaction that mutates the entity (transactional outbox pattern), so notifications survive transaction rollback — if the entity write rolls back, so does the outbox row, and no phantom notification ever fires. The dispatcher drains the outbox in batches via `claim_outbox_batch` (`FOR UPDATE SKIP LOCKED`).

> See [Permissions & Notifications V2](PERMISSIONS_NOTIFICATIONS_V2.md) §4.5.

---

### Payoff

The process of closing out a completed construction loan through a three-step approval workflow. First, a loan processor approves the calculated payoff statement---which includes principal balance, accrued compound interest, fee escalation charges, document fees, and any credits or debits. Second, a user with payoff approval authority independently verifies the statement, transitioning it from DRAFT to APPROVED status. Third, the processor records the actual payoff date and amount received, completing the loan and moving it to Historic status.

The payoff statement is a formal financial document that includes wire instructions, a per diem rate for date adjustments, and an approval audit trail. It can be downloaded as a PDF at any point after approval.

> See [Architecture: Payoff Workflow](ARCHITECTURE.md#payoff-workflow) for the full approval pipeline.

---

### Payoff Adjustments

Credits and debits applied to the payoff statement that modify the final balance due. Credits (such as builder rebates or escrow refunds) reduce the payoff amount; debits (such as late fees or inspection charges) increase it. Adjustments are editable before approval, then locked into a persistent JSONB record once the payoff is approved. The net adjustment amount appears as a line item on the formal payoff statement.

---

### Per Diem

The daily interest charge on a construction loan, calculated as the total balance (principal plus accrued compound interest) multiplied by the annual interest rate divided by 365. The per diem rate is shown on the payoff statement so that if funds are received after the statement's good-through date, each additional day's cost is clearly documented.

---

### Principal Paydown

A partial principal repayment from borrower to lender made before final loan payoff. Paydowns reduce the outstanding principal balance going forward, which lowers subsequent compound interest accrual; they do **not** reduce the finance fee base, which is calculated on the maximum cumulative principal drawn over the life of the loan (the high-water mark). For IRR calculations, paydowns count as positive cash inflows on their actual paydown date.

Paydowns are recorded through a "Record Paydown" action available to processors on active loans. Each paydown captures a date, amount, optional wire reference, and optional notes. Paydowns appear interleaved with draws in the loan's draw history (rendered with a green inflow chip), as line items beneath the Principal Balance row in the payoff statement, and as a footnote on the Income Breakdown card explaining the actual interest reduction. Paydowns are immutable once created — corrections happen by deleting and re-recording. Both create and delete are locked once a payoff statement reaches `payoff_approved` status, requiring the payoff to be revised first.

> See [Architecture: Payoff Workflow](ARCHITECTURE.md#payoff-workflow) for how paydowns flow through interest, fee, and IRR calculations.

---

### Project

A single construction loan in TD3, serving as the central entity that links a builder, lender, property address, and all associated budgets, draw requests, invoices, and wire batches. Projects progress through lifecycle stages from Pending through Active to Historic, and each project's data is accessible through dashboards, detail views, and reports.

> See [Architecture](ARCHITECTURE.md#data-architecture) for details.

---

### Retainage

A percentage of payment withheld from a builder until project completion, ensuring work quality and contract fulfillment. Retainage is a standard practice in construction lending that protects the lender's interest by holding back a portion of each draw until the builder has satisfied all contractual obligations.

---

### Row-Level Security

Database-enforced access control that evaluates permissions on every individual query, independent of the application interface. In TD3, row-level security ensures that even if the application layer were bypassed, the database would independently reject unauthorized operations. Every read, write, and delete is checked against the user's authenticated identity and permission set before any data is returned or modified.

> See [Security](SECURITY.md#data-level-enforcement) for details.

---

### Semantic Matching

AI-powered invoice-to-budget matching that uses contextual understanding of construction terminology rather than rigid lookup tables. The AI evaluates vendor names, line item descriptions, work context, trade classification, and amount relationships to identify the correct budget line. This approach handles the real-world variation in how construction work is described---recognizing, for example, that an invoice from a vendor named "Pacific Insulation Supply" with line items about "R-38 blown-in attic insulation" belongs to the insulation budget category, even without an exact keyword match.

> See [Artificial Intelligence](ARTIFICIAL_INTELLIGENCE.md#semantic-matching-how-the-ai-reasons) for details.

---

### STAFF_PERMISSIONS

Canonical TD3 staff permission set: `['processor', 'fund_draws', 'approve_payoffs', 'users.manage']`. Exported from [`lib/supabase.ts`](../lib/supabase.ts) and consumed by `<InternalOnly>`, `useStaffOnlyRedirect`, and the navigation manifest. Single source of truth for "is this user staff?" — adding or removing staff codes is a one-line edit that propagates to every gating surface.

---

### Subcategory

One of 89 specific cost classifications within TD3's cost code system. Each subcategory falls under one of 12 parent categories and represents a distinct type of construction work (e.g., "Rough Plumbing" under the Plumbing parent category, or "Insulation" under Insulation/Drywall/Paint). Subcategories may be flagged as fundable (eligible for draw funding) or auto-matchable (suitable for AI-assisted matching).

> See [Artificial Intelligence](ARTIFICIAL_INTELLIGENCE.md#td3s-standardized-cost-code-system) for details.

---

### Training Data

Structured records captured from every funded draw that feed into TD3's self-improving AI system. Training data includes vendor-to-category associations, AI-versus-human decision comparisons, confidence calibration data, amount patterns, and match method records. This data forms the foundation for future improvements including vendor history context in AI prompts, confidence recalibration, and correction-driven learning.

> See [Artificial Intelligence](ARTIFICIAL_INTELLIGENCE.md#training-data-and-the-path-to-self-improvement) for details.

---

### useStaffOnlyRedirect

Hook in [`app/components/auth/PermissionGate.tsx`](../app/components/auth/PermissionGate.tsx). The reverse of `usePermissionRedirect` — bounces builder portal users (those holding `builder_portal` and no `STAFF_PERMISSIONS`) away from staff-only pages back to their builder home. Apply to every staff-only top-level page so URL-guessing builders can't reach internal surfaces. Returns `{ checking, allowed }` to handle the `permissionsLoaded` race the same way `usePermissionRedirect` does.

---

### Vendor Association

A learned relationship between a vendor and a cost code category, strengthened each time the same vendor is matched to the same category across projects. Each association tracks match count (frequency), cumulative dollar amount (distinguishing primary trades from one-off work), and recency (when the last match occurred). Over time, vendor associations build a comprehensive map that reinforces AI matching decisions---a regional HVAC company that appears on every project from a given builder becomes a near-certain match.

> See [Artificial Intelligence](ARTIFICIAL_INTELLIGENCE.md#vendor-intelligence) for details.

---

### Wire Batch

A consolidated payment that groups multiple approved draw requests for the same builder into a single wire transfer. Wire batches reduce banking fees and simplify bookkeeping by combining draws that would otherwise require separate payments. Each batch includes a funding report with per-draw breakdowns, builder banking information, and a complete audit trail from creation through funding confirmation.

> See [Architecture](ARCHITECTURE.md#wire-batch-funding) for details.

---

## Related Documentation

| Document | Contents |
|----------|----------|
| [README](../README.md) | Project overview, workflow summary, and documentation index |
| [Architecture](ARCHITECTURE.md) | System architecture, data model, and deployment |
| [Security](SECURITY.md) | Authentication, permissions, data-level enforcement, and audit trail |
| [Artificial Intelligence](ARTIFICIAL_INTELLIGENCE.md) | AI pipeline, cost code system, confidence model, and training data |
| [Design Language](DESIGN_LANGUAGE.md) | Design philosophy, color system, polymorphic behaviors, and accessibility |
| [Roadmap](ROADMAP.md) | Upcoming features, timeline, and development priorities |

---

*TD3 Glossary -- &copy; 2024-2026 TD3, built by Grayson Graham -- Last updated: February 2026*
