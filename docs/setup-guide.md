# Setup Guide

## 1. Requirements

- An n8n account or self-hosted n8n instance
- OpenAI API access
- Google Sheets OAuth credentials in n8n
- Gmail OAuth credentials in n8n
- A Google Sheet with the tabs described below

## 2. Import the workflow

1. Download `workflow/amazon-review-intelligence-workflow.json`.
2. In n8n, create a new workflow.
3. Choose **Import from File** and select the JSON file.
4. Keep the workflow inactive while configuring it.

## 3. Configure credentials

Reconnect these node types inside n8n:

- Google Sheets
- Gmail
- OpenAI Chat Model

No credentials are included in this repository.

## 4. Configure Google Sheets

Replace every `YOUR_GOOGLE_SHEET_ID` value with the ID of your spreadsheet.

Create these tabs:

- `Review_Input`
- `Review_Analysis`
- `System_Summary`

Use the field names documented in [data-schema.md](data-schema.md). Keep header spelling consistent and remove accidental leading or trailing spaces.

## 5. Configure alerts

Open **11 — Send Priority Review Alert** and replace:

`alerts@example.com`

with the email address that should receive urgent review notifications.

## 6. Test safely

1. Add one synthetic review to `Review_Input`.
2. Set `processing_status` to `NEW`.
3. Make sure every node is unpinned.
4. Execute the workflow manually.
5. Confirm that:
   - One review passes validation.
   - AI analysis is saved.
   - Priority is calculated.
   - An alert is sent when required.
   - The source status becomes `ANALYZED`.
   - The summary is updated.

## 7. Connect Looker Studio

Connect Looker Studio to the analysis and summary tabs. Build views for executive KPIs, customer themes, action queues, and content opportunities.

## Important

Use synthetic or properly authorized review data. Do not commit API keys, OAuth credentials, private spreadsheet links, customer email addresses, or personal customer information.
