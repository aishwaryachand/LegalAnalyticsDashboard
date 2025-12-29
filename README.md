# LegalAnalyticsDashboard

# Legal Collections Workflow Intelligence Dashboard (Power BI)

A Power BI dashboard that simulates a legal collections workflow and provides end-to-end visibility from **billing (invoices)** to **collections (payments)** .
Built as a personal project to demonstrate KPI design, dimensional modeling, DAX measures, and stakeholder-focused reporting.

---

## What this dashboard answers (stakeholder questions)

**Billing & Payments**
- How much was invoiced vs collected?
- What is the current Open AR (accounts receivable)?
- What is the recovery rate over time and by client?
- What is the invoice status mix (Open / Paid / Partial)?

**Workflow Funnel & Bottlenecks**
- Where are cases getting stuck (stage-level drop-offs)?
- What is the validation rate and closed case volume?
- What is the disposition mix (Paid / Dispute / Promise to Pay / Unreachable)?

---

## Pages (Report Tabs)

### 1) Billing & Payments Overview
**Visuals used**
- KPI Cards: Total Invoiced, Total Payments Posted, Open AR, Recovery Rate %
- Table: Client scorecard (ClientName, Open AR, Recovery Rate %, Total Payments Posted)
- Bar Chart: Open AR by ClientName
- Line Chart: Total Payments Posted by YearMonth
- Donut Chart: Invoice Status Mix (Invoices Count by InvoiceStatus)

**Screenshot**
- Add image here: `screenshots/billing_payments_overview.png`

---

### 2) Workflow Funnel & Bottlenecks
**Visuals used**
- KPI Cards: Cases Count, Validation Rate %, Closed Cases, (optional: Dispute %)
- Line Chart: Cases Count by Month (Intake)
- Funnel Chart: Cases Count by WorkflowStage
- Donut Chart: Cases Count by CaseDisposition (Closed only recommended)
- Table: Case listing (CaseID, ClientName, AccountName, CaseStatus, WorkflowStage)

**Screenshot**
- Add image here: `screenshots/workflow_funnel_bottlenecks.png`

---

## Data Model (Star Schema)

## Tables

### Dimensions
- DimDate: DateKey, Date, YearMonth, MonthName, Quarter
- DimClient: ClientID, ClientName
- DimAccount: AccountID, AccountName, ClientID, Portfolio, Region

### Facts
- FactCase: CaseID, AccountID, IntakeDateKey, ValidationDateKey, CaseStatus, WorkflowStage, CaseDisposition
- FactInvoice: InvoiceID, CaseID, AccountID, InvoiceDateKey, DueDateKey, InvoiceAmount, InvoiceStatus
- FactPayment: PaymentID, InvoiceID, PaymentDateKey, PaymentAmount, PaymentStatus

## Relationships (Power BI Model view)
- DimClient (ClientID) 1-to-many DimAccount (ClientID)
- DimAccount (AccountID) 1-to-many FactCase (AccountID)
- FactCase (CaseID) 1-to-many FactInvoice (CaseID)
- FactInvoice (InvoiceID) 1-to-many FactPayment (InvoiceID)

### Date relationships
- DimDate (DateKey) 1-to-many FactInvoice (InvoiceDateKey)
- DimDate (DateKey) 1-to-many FactPayment (PaymentDateKey)
- DimDate (DateKey) 1-to-many FactCase (IntakeDateKey)


> Note: Multiple date keys exist (InvoiceDate, DueDate, PaymentDate, IntakeDate, ValidationDate).  


---

## DAX Measures Used

## Measures (DAX) Used 

### Billing & Payments
- **Total Invoiced** — Total invoice dollars in the current filter context (Client/Portfolio/Region/Date).
- **Total Payments Posted** — Total successful (posted) payment dollars; excludes returned payments.
- **Open AR** — Outstanding receivables = Total Invoiced minus Total Payments Posted.
- **Recovery Rate %** — Collection efficiency = Total Payments Posted divided by Total Invoiced.
- **Invoices Count** — Number of unique invoices (distinct InvoiceID).

### Workflow Funnel & Bottlenecks
- **Cases Count** — Number of unique cases (distinct CaseID).
- **Validated Cases** — Number of cases that have a validation date (i.e., passed validation).
- **Validation Rate %** — Validated Cases divided by total Cases Count.
- **Closed Cases** — Number of cases with CaseStatus = Closed.
- **Dispute Cases** — Number of cases with CaseDisposition = Dispute.
- **Dispute %** — Dispute Cases divided by Cases Count.

Recovery Rate % = DIVIDE([Total Payments Posted], [Total Invoiced])

