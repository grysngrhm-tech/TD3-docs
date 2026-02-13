# Glossary

> Key terms used throughout TD3's documentation, defined for quick reference.

---

## Table of Contents

- [Amortization](#amortization)
- [Auto-Approved](#auto-approved)
- [Builder](#builder)
- [Budget / Budget Line](#budget--budget-line)
- [Confidence Score](#confidence-score)
- [Construction Loan](#construction-loan)
- [Cost Code](#cost-code)
- [Disbursement](#disbursement)
- [Draw Request](#draw-request)
- [Draw Status](#draw-status)
- [Extraction](#extraction)
- [Fee Escalation](#fee-escalation)
- [Funded](#funded)
- [IRR (Internal Rate of Return)](#irr-internal-rate-of-return)
- [Lender](#lender)
- [Lifecycle Stage](#lifecycle-stage)
- [LTV (Loan-to-Value)](#ltv-loan-to-value)
- [OTP (One-Time Password)](#otp-one-time-password)
- [Payoff](#payoff)
- [Project](#project)
- [Retainage](#retainage)
- [Row-Level Security](#row-level-security)
- [Semantic Matching](#semantic-matching)
- [Subcategory](#subcategory)
- [Training Data](#training-data)
- [Vendor Association](#vendor-association)
- [Wire Batch](#wire-batch)

---

### Amortization

A schedule of periodic interest payments over the life of a construction loan. TD3 calculates compound interest with monthly accrual at month-end and at each draw funding event, giving a real-time view of the loan's interest position throughout the construction period.

> See [Architecture: Data Architecture](ARCHITECTURE.md#data-architecture) for details on financial calculations.

---

### Auto-Approved

An AI invoice match with 95% or higher confidence that is applied automatically without human review. Auto-approved matches are skipped in the review queue entirely and logged in the audit trail as automatic decisions. This tier handles clear, unambiguous vendor-to-category alignments where the AI's certainty is comparable to a human expert's.

> See [Artificial Intelligence](ARTIFICIAL_INTELLIGENCE.md#confidence-gated-automation) for details.

---

### Builder

The contractor or construction company performing the work on a project. Each builder record in TD3 carries banking information used for wire transfers, and is linked to one or more projects. Builders submit budget spreadsheets and draw requests throughout the construction process.

> See [Architecture](ARCHITECTURE.md#data-architecture) for details.

---

### Budget / Budget Line

Categorized line items that define the expected cost structure of a construction build, organized against TD3's standardized cost codes. Each budget line tracks an original amount, a current approved amount, and the amount already spent through funded draws. Once a draw has been funded against a budget line, that line is protected from deletion during re-imports.

> See [Architecture](ARCHITECTURE.md#budget-intelligence) for details.

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

TD3's tiered fee structure that increases over the loan term to incentivize timely project completion. The fee starts at 2% for months 1--6, increases by 0.25% per month from months 7--12, jumps to 5.9% at month 13, and increases by 0.4% per month thereafter. These calculations are performed server-side using auditable formulas.

---

### Funded

The final status of a draw request or wire batch, indicating that funds have been released to the builder. Once a record reaches funded status, it becomes immutable---budget amounts, draw line items, wire batch details, and invoice matches are locked as a permanent financial record. The funding action also triggers training data capture for the AI learning system.

> See [Security](SECURITY.md#audit-trail) for details on immutability.

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

### Payoff

The process of closing out a completed construction loan, including final interest calculation and balance settlement. Payoff actions require the Approve Payoffs permission, enforced at both the application and database levels. Once a payoff is completed, the project transitions to Historic status.

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

### Subcategory

One of 89 specific cost classifications within TD3's cost code system. Each subcategory falls under one of 12 parent categories and represents a distinct type of construction work (e.g., "Rough Plumbing" under the Plumbing parent category, or "Insulation" under Insulation/Drywall/Paint). Subcategories may be flagged as fundable (eligible for draw funding) or auto-matchable (suitable for AI-assisted matching).

> See [Artificial Intelligence](ARTIFICIAL_INTELLIGENCE.md#td3s-standardized-cost-code-system) for details.

---

### Training Data

Structured records captured from every funded draw that feed into TD3's self-improving AI system. Training data includes vendor-to-category associations, AI-versus-human decision comparisons, confidence calibration data, amount patterns, and match method records. This data forms the foundation for future improvements including vendor history context in AI prompts, confidence recalibration, and correction-driven learning.

> See [Artificial Intelligence](ARTIFICIAL_INTELLIGENCE.md#training-data-and-the-path-to-self-improvement) for details.

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
