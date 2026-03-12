# Project Financial Control App Plan (MVP-First)

## 1) Product objective
Build a **web-first project financial control system** for interior fitout/contracting projects that provides one reliable, current financial truth across:
- Budget
- Commitments
- Actual cost
- Billing
- Collections
- Variations

Primary outcome: at any point, users can quickly determine whether a project is profitable, over/under-billed, and heading toward a cash or margin risk.

---

## 2) Product principles (non-negotiable)
1. **Single source of truth** per project for financial numbers.
2. **Low data-entry burden** through import templates, defaults, and workflow-linked entries.
3. **No metric ambiguity**: estimate, budget, commitment, actual, billed, certified, and received are separate fields and never mixed.
4. **Action-oriented controls**: alerts and escalation workflows, not passive dashboards.
5. **Auditability**: every edit, approval, and revision has user + timestamp + reason.

---

## 3) Scope strategy
### In scope for MVP
- Project master + role-based access
- Cost code master
- Budget upload (baseline + revisions)
- Procurement commitments (PO lifecycle)
- Vendor bill booking (actual cost)
- Client billing tracker (submitted/certified/paid)
- Receipt entry + aging
- Variation register with approval status
- Dashboard with core KPIs (10–12)
- Excel/PDF export

### Explicitly out of scope for MVP
- Full ERP replacement
- Mobile-first workflows
- Deep AI forecasting/OCR automation
- Complex external integrations (bank/GST/ERP sync)

---

## 4) User roles and accountability
- **Owner/Director:** portfolio profitability and risk visibility
- **Finance Head:** costs, billing, collections, cash flow control
- **Project Manager:** budget adherence, variation closure, forecast accountability
- **Procurement:** commitments, vendor exposure, PO discipline
- **QS/Billing:** RA billing cycle and certification status
- **Site Team:** receipts/consumption/work progress input

Role permissions should enforce both visibility and data ownership so each financial event has a clear accountable role.

---

## 5) Financial truth model (semantic layer)
For every project, maintain these canonical metrics:
- Contract Value
- Approved Variation Value
- Revised Contract Value
- Budgeted Cost
- Committed Cost
- Actual Cost Booked
- Cost to Complete
- Forecast Final Cost
- Forecast Final Revenue
- Forecast Gross Margin + Margin %
- Total Billed / Certified / Received
- Receivables Outstanding
- Unbilled Revenue
- Retention Held + Retention Due
- Net Cash Position

### Core equations
- **Revised Contract Value** = Contract Value + Approved Variations
- **Unbilled Commitment** = Committed Cost - Invoiced Against PO
- **Receivables Outstanding** = Certified (or Invoice Raised, based on policy) - Collections Received
- **Forecast Final Cost** = Actual Cost + Cost to Complete + Expected Rework Risk
- **Forecast Gross Margin** = Forecast Final Revenue - Forecast Final Cost
- **Net Cash Position** = Collections Received - (Actual Paid + Advances + Current Payables Due)

---

## 6) Data model blueprint
Entities:
- Company
- Project
- Client
- CostCode
- BudgetLine
- BudgetRevision
- Vendor
- PurchaseOrder
- PODelivery/GRN
- VendorInvoice
- ExpenseEntry
- ClientInvoice
- Receipt
- VariationOrder
- ApprovalWorkflow
- User
- Role
- AuditLog

Mandatory transaction dimensions:
- Project code
- Cost code
- Work package
- Counterparty (vendor/client)
- Date
- Amount + tax breakdown
- Status (draft/submitted/approved/posted/paid etc.)
- Supporting document link
- Approver identity

---

## 7) Module-by-module MVP design
### A) Project setup
- Project profile, contract terms, milestones, retention rules, baseline target margin.
- BOQ/cost-code template binding at project creation.

### B) Budgeting
- Baseline budget upload by cost code/work package.
- Budget revision workflow with reason code + approval trail.
- Contingency allocation and utilization tracking.

### C) Procurement & commitments
- PO entry + amendment history.
- Advance paid tracking.
- Committed vs budget by cost head.
- Vendor exposure report (ordered, invoiced, paid, open balance).

### D) Cost capture
- Vendor bill booking against PO or direct cost head.
- Labour/site overhead/petty cash expense capture.
- Blocking rules for cost without cost code or approval path.

### E) Billing
- RA bill creation linked to BOQ/progress.
- Status states: draft → submitted → certified → invoiced → due → received.
- Debit/credit note handling.

### F) Receivables
- Aging buckets and follow-up log.
- Retention outstanding tracking.
- Collection forecast by week.

### G) Variations
- Change request intake and internal cost impact.
- Quote submitted/approval pending/approved/rejected states.
- Flag when work has started before commercial approval.

### H) Dashboard
- Financial truth widgets + risk alerts + traffic-light health status.

---

## 8) Controls and mandatory alerts
1. Cost head >90% of budget
2. PO issued without approved budget
3. Vendor bill exceeds PO ceiling
4. Margin forecast below threshold
5. Client payment overdue (X days)
6. Variation pending approval while execution started
7. Unbilled completed work above threshold
8. Forecast loss project
9. Retention eligible but unreleased
10. Site overhead % exceeds allowed band

All alerts should carry owner, SLA, escalation path, and closure notes.

---

## 9) KPI set for MVP dashboard (10–12)
1. Revised contract value
2. Budgeted cost
3. Committed cost
4. Actual cost
5. Forecast final cost
6. Forecast margin %
7. Billed amount
8. Certified amount
9. Collections received
10. Receivables outstanding
11. Unbilled revenue
12. Net cash position

---

## 10) Workflow map (approved-event driven)
- **Budget:** estimate → review → approve baseline
- **Procurement:** budget check → RFQ/rate approval → PO issue → commitment update
- **Cost:** bill submission → site/QS verification → finance posting → actual cost update
- **Billing:** progress verify → RA generate → submit → certify → invoice due
- **Collections:** follow-up log → receipt booking → receivable reduction
- **Variations:** request → costing → quote → approval decision → billing linkage

Rule: dashboard numbers refresh only from approved/posted transactions.

---

## 11) Technical plan (MVP)
- **Frontend:** React/Next.js web app
- **Backend:** Node.js (NestJS) or Django REST (choose one stack early)
- **Database:** PostgreSQL
- **Storage:** S3-compatible documents
- **Auth:** RBAC + audit trail + session security controls

Architecture preference: modular monolith for MVP (faster delivery, easier governance), with clear service boundaries for later extraction.

---

## 12) Delivery roadmap
### Phase 0 (1–2 weeks): Discovery + design freeze
- Process mapping and current reports inventory
- Cost code structure finalization
- Approval matrix and escalation rules
- KPI dictionary and formulas sign-off
- UAT scenarios + data migration templates

### Phase 1 (4–6 weeks): MVP build
- Build modules in this order: master → budget → commitments → cost → billing → receipts → variations → dashboard/reports
- Implement role permissions, exports, and core alerts

### Phase 2 (2–4 weeks): Pilot (1–2 live projects)
- Daily data quality checks
- Formula and workflow validation
- Process friction fixes
- Team training and SOP refinement

### Phase 3 (3–6 weeks): Scale
- Portfolio rollout
- Report scheduler and enhanced controls
- Selected integrations (ERP/GST/bank) based on ROI

---

## 13) Implementation governance
- Nominate one **Data Owner** per project (PM + Finance co-ownership model).
- Weekly project finance review must use app outputs as system of record.
- Enforce entry timeliness SLAs (PO before issue, bill within X days, receipts same day).
- Create exception queue for bypassed approvals and untagged costs.

---

## 14) Acceptance criteria (60–90 days post pilot)
- Live budget vs commitment vs actual visible on all active pilot projects
- ≥90% POs entered before execution start
- Major variations logged with status and expected recovery
- Weekly reviews run from app data (not parallel spreadsheets)
- Early warning of margin leakage before month-close
- Project-wise billing and collection delays clearly tracked

---

## 15) Immediate next actions (this week)
1. Freeze standardized cost code structure and work package taxonomy.
2. Finalize 5 templates: budget, PO, vendor bill, client bill, variation register.
3. Approve KPI dictionary with formula ownership (Finance sign-off mandatory).
4. Define approval matrix by role and threshold.
5. Select one pilot project with clean historical data for controlled rollout.

