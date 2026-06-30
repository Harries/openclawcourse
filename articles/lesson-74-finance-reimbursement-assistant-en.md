# Lesson 74: Finance Reimbursement Assistant

Expense automation must be traceable and policy-aware. OpenClaw can organize receipts, summarize spreadsheets, and flag anomalies, but it cannot approve expenses.

## Skills Used

Search [skills.lc](https://skills.lc/) for `finance`, `invoice`, `expense`, or `spreadsheet`. In this environment, use `pdf:pdf` for receipt PDFs, `spreadsheets` for reimbursement tables, `documents` for explanations, and `browser` for company policy or public rules.

## Workflow

Collect receipts and reimbursement rules first: category, limit, approver, date range, and attachment requirements.

Extract fields: date, merchant, amount, tax, currency, category, project, payer, and invoice number.

Generate the reimbursement spreadsheet and flag anomalies: duplicates, over-limit items, missing attachments, out-of-range dates, or mismatched amounts.

Produce a submission package: table, attachment list, anomaly explanation, and items needing confirmation.

## Human Review

Approval, tax judgment, and payment must remain human-controlled.

## Exercise

Give OpenClaw ten receipts and ask for an expense workbook, anomaly list, and submission note.

The assistant makes the process clearer, not looser.
