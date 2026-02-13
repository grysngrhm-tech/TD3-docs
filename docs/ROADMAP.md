# TD3 Development Roadmap

**Last Updated:** February 2026

---

## Table of Contents

1. [Platform Status](#platform-status)
2. [Upcoming Features](#upcoming-features)
3. [AI Intelligence](#ai-intelligence)
4. [Feature Timeline](#feature-timeline)
5. [Related Documentation](#related-documentation)

---

## Platform Status

TD3's core platform is live and operational:

- Loan lifecycle management (origination through payoff)
- Budget import with AI-powered categorization to industry-standard cost codes
- Draw request processing with automated validation and flagging
- Wire batch funding with consolidated builder payments and audit trail
- Invoice AI extraction and deterministic matching with learning system
- Passwordless authentication with pre-authorized access and stackable permissions
- Row-level database security on all business tables
- Interactive dashboards with portfolio analytics and polymorphic reports
- User preferences (theme, font size, reduced motion, default dashboard)
- Activity tracking with device metadata and complete audit history
- Installable progressive web app with offline-capable assets

---

## Upcoming Features

### DocuSign Integration

Streamline loan origination by integrating electronic signature workflows directly into the platform. When a new loan is created, TD3 will automatically populate document templates with loan data and send them for signature. Once all parties have signed, the executed documents are stored in the loan record and the loan status advances automatically. For details on the loan lifecycle this integrates with, see [Technical Architecture: Loan Lifecycle Management](ARCHITECTURE.md#loan-lifecycle-management).

**Document types templated:**
- **Deed of Trust** -- Property-specific collateral agreement populated with parcel number, legal description, and loan terms
- **Promissory Note** -- Loan amount, interest rate, maturity date, and payment schedule pre-filled from the loan record
- **Personal Guaranty** -- Guarantor information and guarantee terms linked to the borrower and loan profiles
- Additional templates can be configured per lender or loan product type

**Template variable population:** Each document template contains merge fields that map directly to TD3's loan data model. When an envelope is created, fields like borrower name, property address, loan amount, interest rate, and maturity date are automatically populated---eliminating the manual copy-paste process that introduces errors.

**Expiration and reminder workflows:** Unsigned envelopes trigger automated reminders on a configurable schedule (e.g., 3 days, 7 days). Envelopes that remain unsigned past a deadline are flagged in the loan record as requiring follow-up. The processor can resend or void envelopes directly from the loan detail page.

**Completion tracking:** The loan detail view shows real-time signature status for every document in the envelope---which parties have signed, which are pending, and whether any have declined. Loan status cannot advance from Pending to Active until all required documents are fully executed.

```mermaid
sequenceDiagram
    participant TD3 as TD3
    participant DS as E-Signature Service
    participant User as Signers

    TD3->>DS: Create envelope with loan data
    DS->>User: Send documents for signature
    User->>DS: Sign documents
    DS->>TD3: Notification: documents executed
    TD3->>TD3: Store signed PDF, activate loan
```

This eliminates manual document preparation, reduces turnaround time, and ensures every loan has a complete, tamper-evident document trail.

### Microsoft Adaptive Cards

**What they are:** Adaptive Cards are a JSON-based interactive card format developed by Microsoft that renders natively in Teams, Outlook, and other Microsoft 365 surfaces. Instead of a plain notification email with a link, the recipient sees a rich, interactive card---with formatted data, action buttons, and input fields---rendered directly in their existing workflow tool.

**Why they reduce context-switching:** The core value is eliminating the "notification → click link → wait for page load → find the right section → take action" loop. With Adaptive Cards, the relevant data and available actions are embedded directly in the notification. A bookkeeper can input a funding date without ever opening the TD3 interface. A processor can approve a draw from their Teams chat. The action travels back to TD3 via a webhook callback, and the platform state updates in real time.

Enable workflow notifications and approvals directly within the team's existing communication tools. For details on the draw processing workflow these cards interact with, see [Technical Architecture: Draw Processing Workflow](ARCHITECTURE.md#draw-processing-workflow).

**Key use cases:**

- **Funding requests** -- Bookkeeper receives a card with builder name, amount, and account details. They input the funding date directly in the card, which feeds back to TD3.
- **Payoff approvals** -- Approver reviews the payoff breakdown and approves or rejects with comments, without leaving Outlook or Teams.
- **Draw notifications** -- Processor receives a summary card with flags and a quick-approve button, with a deep link to the full review page for complex cases.
- **Status updates** -- Builders receive read-only cards showing draw progress, wire confirmation, and next steps---reducing inbound status inquiries.

**Integration mechanics:** When a triggering event occurs in TD3 (e.g., a draw moves to `pending_wire`), the system constructs an Adaptive Card JSON payload with the relevant data and sends it to a Microsoft webhook endpoint. The card renders in the recipient's Teams channel or Outlook inbox. When the user takes an action (clicks a button, inputs a date), the response is sent back to a TD3 API callback route, which validates the action, applies it to the database, and logs the activity.

### Builder & Lender Portals

**Why self-service matters:** Builders and lenders currently rely on phone calls, emails, and manual report generation to get updates on their loans. Every "what's the status of my draw?" inquiry requires a processor to look up the information and relay it---a pattern that scales linearly with portfolio size. Self-service portals eliminate this overhead by giving external stakeholders real-time visibility into their own data, reducing support requests and freeing the internal team to focus on processing work.

Provide external stakeholders with secure, limited-access views into their relevant data. Both portals build on TD3's existing [security model](SECURITY.md#permissions-what-each-user-can-do) and [role-based UI adaptations](DESIGN_LANGUAGE.md#75-role-based-adaptations-future).

**Builder Portal:**
- View their own loans and current balances
- Review approved budgets by category with remaining amounts
- Submit draw requests and upload invoices directly
- Track draw status through the funding pipeline in real time
- Download funded draw confirmations and wire details

**Lender Portal:**
- Read-only portfolio view filtered to their funded loans
- Loan summaries with current balances, draw progress, and LTV metrics
- Aggregate portfolio statistics and performance metrics
- Exportable reports for internal lending reviews and regulatory compliance
- Historical trend views showing portfolio health over time

**Portal authentication model:** Both portals use a separate login flow distinct from the internal TD3 interface. External users authenticate via email OTP (the same scanner-safe code verification used internally) but receive scoped access tokens that restrict database queries to their own records via row-level security. A builder sees only their own loans; a lender sees only loans they fund. The internal permission system (`processor`, `fund_draws`, etc.) does not apply to portal users---their access is governed entirely by data ownership.

**Design adaptation per role:** Each portal presents a simplified interface tailored to the stakeholder's needs. Builders see a construction-focused view emphasizing budget progress and draw submission. Lenders see a finance-focused view emphasizing balances, risk metrics, and portfolio performance. Both share the same underlying data model and design language, adapted for audience.

### Mobile Inspection App

**The problem:** Construction site inspections today rely on paper checklists, standalone photo apps, and manual data entry. Inspectors capture photos on their phones, take notes on paper or in a separate app, then return to the office to manually enter findings into the loan management system. Photos are disconnected from the specific budget lines they document. Notes lack structured data that the system can act on. The gap between field observation and system record introduces delays, errors, and lost context.

**Technology approach:** The mobile inspection app will be built as a Progressive Web App (PWA), leveraging TD3's existing installable PWA infrastructure. This approach delivers a native app experience---home screen icon, offline support, camera access, GPS---without requiring App Store distribution. The same codebase and design language used in the main TD3 interface extend to the mobile experience, ensuring visual and behavioral consistency.

**Core capabilities:**

- **Map view** showing all active project locations with navigation and distance-based sorting
- **Inspection checklists** comparing draw request items against visible construction progress, with per-item pass/fail/partial status
- **Photo capture** with annotations tied to specific projects and budget categories---every photo links to the budget line it documents
- **GPS tagging** for automatic location verification, confirming the inspector is on-site
- **Offline support** for areas with poor connectivity, with automatic sync when connection is restored
- **Integration with TD3** -- inspection records, photos, and notes appear in the project detail view

**Budget-linked evidence:** The key differentiator is direct integration with TD3's budget structure. When an inspector photographs framing progress, that photo is linked to the specific framing budget line and its associated draw request. This creates a visual evidence trail that connects field observations to financial decisions---inspectors verify construction progress, and that verification flows directly into the draw approval process.

**Progress tracking against draw schedules:** Inspection data feeds into draw processing by providing field-verified evidence of construction progress. When a builder submits a draw for framing work, the processor can review inspection photos and checklist results for that specific budget category---reducing the need for separate progress verification steps.

---

## AI Intelligence

TD3's current AI capabilities---budget categorization, invoice extraction, and invoice-to-budget matching---are documented in the [Artificial Intelligence](ARTIFICIAL_INTELLIGENCE.md) guide. The features below build on that foundation, extending the platform's intelligence from individual-transaction decisions to portfolio-wide learning and prediction.

A critical enabler for these features is TD3's [standardized cost code system](ARTIFICIAL_INTELLIGENCE.md#td3s-standardized-cost-code-system). Because every builder's budget is mapped to a single canonical set of 89 subcategories, data from different projects and builders becomes directly comparable---the prerequisite for meaningful cross-loan analytics. Without standardization, a builder's "Mechanical Rough-in" cannot be compared to another builder's "HVAC Labor"---with it, both map to the same cost code and become data points in the same analysis.

### Portfolio Intelligence (RAG Chatbot)

**The problem:** Answering questions about portfolio data today requires either manual SQL queries, navigating through multiple pages and filters, or exporting data to spreadsheets for ad-hoc analysis. Simple questions like "which loans are approaching maturity?" involve visiting several screens and mentally assembling the answer. Complex cross-project queries---comparing budget utilization across builders, for instance---are impractical without technical skills.

**The solution:** A natural-language query interface that allows users to ask questions about their portfolio data conversationally:

- "What's the total outstanding balance across all active loans?"
- "Show me draws funded this month for Builder X"
- "Which loans are approaching maturity?"
- "Compare budget utilization across active projects"

**How RAG works:** Retrieval-Augmented Generation (RAG) combines database retrieval with AI reasoning. When a user asks a question, the system first retrieves the relevant data context---loan records, budget figures, draw history---then sends that context alongside the question to an AI model. The model reasons about the data and returns a formatted, human-readable answer with tables, summaries, and supporting detail.

**Why RAG over a raw LLM:** Grounding the AI's response in actual retrieved data prevents hallucination---the model cannot fabricate loan balances or draw amounts because it only works with real records from the database. All data stays within the platform's security boundary; nothing is sent externally without the user's query context. Row-level security ensures users only see data they're authorized to access.

**Why cost codes are the key:** TD3's [standardized cost code system](ARTIFICIAL_INTELLIGENCE.md#td3s-standardized-cost-code-system) is what makes cross-project queries possible. Because every builder's budget maps to the same 89 canonical subcategories, the chatbot can compare framing costs across five different builders---even though each builder originally labeled the line item differently. Without standardization, cross-project analytics would require manual normalization for every query.

The chatbot will translate questions into database queries, present results in formatted tables and summaries, and support multi-step analytical reasoning for complex questions.

### Self-Improving Invoice Matching

TD3 already captures structured [training data](ARTIFICIAL_INTELLIGENCE.md#training-data-and-the-path-to-self-improvement) every time a draw is funded: vendor-to-category associations, human corrections, confidence calibration data, and amount patterns. The data collection infrastructure is in place; this feature activates the feedback loop.

**How it works:**

- **Vendor history as context** -- When the AI evaluates a new invoice, it receives the vendor's complete match history as few-shot examples in the prompt. A vendor matched to HVAC categories across five previous projects carries strong prior evidence for the next match.
- **Human corrections weighted heavily** -- When a reviewer overrides an AI suggestion, that correction carries more weight than an accepted auto-match. Corrections represent the cases where the AI was wrong, making them the most valuable signal for improvement.
- **Confidence recalibration** -- Comparing the AI's reported confidence against actual outcomes over time reveals systematic miscalibration. If the model reports 80% confidence on a certain class of invoices but is actually correct 95% of the time, thresholds can be refined to reduce unnecessary human review.
- **Few-shot learning** -- Concrete examples of past matches---including the reasoning and outcome---are more effective than abstract instructions. The AI sees "last time you matched a Pacific Insulation invoice, you correctly assigned it to Insulation/Drywall/Paint" rather than a generic rule.

The result is a virtuous cycle: more funded draws produce more training data, which produces more accurate matching, which requires less human intervention. Early loans require the most human review; as the vendor knowledge base grows, the ratio of auto-approved to manually-reviewed invoices shifts steadily toward automation. See the [self-improvement loop](ARTIFICIAL_INTELLIGENCE.md#the-self-improvement-loop) for a detailed diagram of this feedback cycle.

### Builder Performance Intelligence

As historical data accumulates across completed loans, TD3 can analyze builder performance at a depth that individual spreadsheets cannot achieve. This is made possible by the [standardized cost code system](ARTIFICIAL_INTELLIGENCE.md#td3s-standardized-cost-code-system)---because every builder's budget maps to the same 89 subcategories, comparison across builders and projects is meaningful rather than approximate.

**Builder benefits:**

- Self-assessment on timeline adherence, budget accuracy, and draw frequency patterns
- Category-level analysis showing where a builder consistently comes in over or under budget
- Trend tracking across sequential projects to measure operational improvement
- Vendor performance insights---which subcontractors deliver on budget, which consistently exceed estimates

**Lender benefits:**

- Risk scoring based on historical budget accuracy, draw patterns, and project completion timelines
- Side-by-side builder comparison using standardized metrics across the same cost categories
- Early warning indicators when a current project deviates from a builder's historical baseline
- Portfolio concentration analysis---exposure by builder, geographic area, and project size

The key insight is that standardized cost codes transform builder data from isolated project records into a connected performance history. A builder's framing costs across ten projects tell a story that no single project budget can.

### Predictive Analytics

Using the historical performance data assembled by builder intelligence, TD3 can project forward to forecast outcomes on active and prospective loans.

**Forecasting capabilities:**

- **Cash flow projection** -- Based on a builder's historical draw cadence and the current project's budget structure, forecast when draws are likely to be submitted and funded.
- **Budget risk modeling** -- Identify cost categories where the builder has historically exceeded budget, and flag those categories on new projects before construction begins.
- **Completion timeline estimation** -- Use the builder's track record on similar-sized projects to estimate realistic completion dates, accounting for seasonal patterns and historical delays.
- **Portfolio-level planning** -- Aggregate individual project forecasts into portfolio-wide cash flow projections, helping lenders plan capital allocation and liquidity.
- **Market condition signals** -- Track cost trends across the portfolio to identify material price shifts, labor market tightness, or regional cost pressures before they impact individual loan performance.

These capabilities are enabled by the [standardized cost code system](ARTIFICIAL_INTELLIGENCE.md#td3s-standardized-cost-code-system) creating a unified data model. When every project uses the same category structure, historical patterns from completed loans can be directly applied to forecast outcomes on new ones.

The accuracy of these predictions improves with portfolio size. Early forecasts rely on industry benchmarks and the builder's limited history. As a builder completes more projects through TD3, the predictions become increasingly precise---calibrated to that specific builder's patterns, preferred subcontractors, and typical project profile. See the [training data](ARTIFICIAL_INTELLIGENCE.md#what-td3-captures-today) documentation for details on the data foundation.

---

## Feature Timeline

```mermaid
gantt
    title TD3 Feature Roadmap
    dateFormat YYYY-MM-DD

    section Integrations
    DocuSign Integration           :docusign, 2026-04-01, 21d
    Microsoft Adaptive Cards       :cards, 2026-04-15, 28d

    section Portals
    Builder Portal                 :builder, 2026-07-01, 28d
    Lender Portal                  :lender, 2026-08-01, 28d

    section Mobile
    Mobile Inspection App          :mobile, 2026-10-15, 42d

    section AI Intelligence
    Portfolio RAG Chatbot          :rag, 2026-10-01, 35d
    Self-Improving Matching        :matching, 2026-11-01, 28d
    Builder Performance Analytics  :builder-perf, 2026-12-01, 35d
    Predictive Analytics           :predictive, 2027-01-05, 42d
```

---

## Related Documentation

| Document | Contents |
|----------|----------|
| [Artificial Intelligence](ARTIFICIAL_INTELLIGENCE.md) | AI pipeline, cost code system, confidence model, and training data |
| [Technical Architecture](ARCHITECTURE.md) | System architecture, data model, and deployment |
| [Security](SECURITY.md) | Authentication, permissions, data-level enforcement, and audit trail |
| [Design Language](DESIGN_LANGUAGE.md) | Design philosophy, color system, polymorphic behaviors, and accessibility |
| [Glossary](GLOSSARY.md) | Definitions of key construction lending, financial, and platform terms |
| [README](../README.md) | Project overview, workflow summary, and documentation index |

---

*© 2024-2026 TD3, built by Grayson Graham. All rights reserved.*
