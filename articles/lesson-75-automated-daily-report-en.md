# Lesson 75: Automated Daily Reports with OpenClaw

An automated daily report should not dump numbers. It should collect reliably, calculate consistently, highlight anomalies, explain changes, and require approval when needed.

## Skills Used

Search [skills.lc](https://skills.lc/) for `automation`, `report`, `schedule`, or `dashboard`. In this environment, combine scheduled tasks, `browser` for web data, `spreadsheets` for metrics, and document or email skills for report generation and delivery.

## Workflow

Define the template: core metrics, sources, update time, calculation rules, anomaly thresholds, and recipients.

Define collection methods: API, web page, file, database, or manual sheet. Every source needs a failure path.

Analyze changes: day-over-day, week-over-week, target gap, anomalies, and possible reasons.

Send automatically only when the risk is low. Sensitive reports should be generated as drafts for approval.

## Human Review

Finance, customer, outage, incident, and public-disclosure information should not be sent without confirmation.

## Exercise

Design a product operations daily report that collects visits, signups, conversions, and errors, then generates an email draft.

The value is a shared set of trusted facts every day.
