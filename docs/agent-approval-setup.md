# Customer insight agent and brief approval setup

This guide extends the existing ARI-01/02 setup with ARI-03 through ARI-07. These public exports require configuration before use. Original node expressions and routing are preserved; cosmetic workflow names and private configuration have been sanitized. The screenshots show the original configured instance, not a fresh import of these public copies.

## 1. Prepare Google Sheets

Use the same spreadsheet as the review processing system. Select it in every Google Sheets node after replacing `YOUR_GOOGLE_SHEET_ID`.

`Review_Analysis` needs these exact headers for ARI-03:

```text
asin, review_id, rating, primary_theme, customer_issue, positive_signal, severity, listing_opportunity, creative_opportunity, recommended_action
```

Use `asin` with no leading space. The packaging code tolerates a legacy ` asin` property, but the Sheets filter targets the exact `asin` column.

Create `Brief_Drafts` with:

```text
brief_id, created_at, asin, review_count, brief_text, status, reviewer_notes
```

Keep `brief_id` unique. Draft records are appended; approval nodes match `brief_id` and update status. The timestamp is created during drafting. Reviewer notes are stored but there is no automatic notes-collection step in the approval workflow.

## 2. Import the extension

Import in this order so dependencies can be selected: **ARI-03, ARI-06, ARI-07, ARI-05, ARI-04**. The files are inactive by default. Reconnect Google Sheets, Gmail, and OpenAI credentials in the relevant nodes.

Choose an OpenAI model available in your account in ARI-04 and ARI-05. The exported model selection reflects the original instance and is not a guarantee of model availability.

## 3. Select your imported workflows

Replace each placeholder by selecting the imported workflow in the node's workflow picker. IDs differ between n8n instances.

| Workflow | Caller node | Select |
| --- | --- | --- |
| ARI-04 | Review Lookup Tool | ARI-03 |
| ARI-04 | Draft Improvement Brief Tool | ARI-05 |
| ARI-04 | Call ARI-07 tool | ARI-07 |
| ARI-05 | Call ARI-03 | ARI-03 |
| ARI-05 | Call ARI-06 | ARI-06 |

Recheck mapped inputs after selection: review and draft tools take `asin`; status lookup takes `brief_id`; approval takes `brief_id`, `asin`, and `brief_text`. Keep ARI-05's approval call set to **waitForSubWorkflow: false**, so the chat does not wait for the reviewer.

## 4. Configure sheet nodes and email

Select `Review_Analysis` for ARI-03 and `Brief_Drafts` for ARI-05/06/07. Refresh column mappings if the node asks for them.

In ARI-06, replace `reviewer@example.com` with your reviewer. Confirm both decision branches match the `brief_id` from **Approval Input**. Ensure the n8n instance has a reachable public URL for approval callbacks, then publish or activate workflows as required by your instance.

In ARI-07, keep the filter column fixed to `brief_id`, with value `={{ $json.brief_id }}`. Keep **Always Output Data** enabled and the Code node in **Run Once for Each Item** mode. An unfiltered lookup can return an unrelated row.

## 5. Run functional checks

1. Call ARI-03 with an ASIN that has known analyzed rows. Verify matching IDs and review_count.
2. Ask ARI-04 for an analysis of that ASIN; check that findings cite returned review IDs.
3. Explicitly request a listing improvement brief. Confirm a new Draft row and note its generated brief ID.
4. Verify the approval email arrives. Approve it and confirm that the same row becomes Approved.
5. Ask the agent for that brief's status. Compare the response with the sheet.
6. Ask for a brief ID absent from the sheet. It should report not found, never another brief's status.
7. Create a separate draft and decline it to validate Rejected independently.

Use your newly generated ID. **BRIEF-1530 is an identifier from the documented demonstration, not seeded data supplied by this repository.**

## Known limitations and follow-up improvements

- ARI-05's false branch for zero reviews is unconnected. It does not save a draft, but may return no usable tool response. Connect an explicit no-reviews response before relying on this path.
- Approval starts asynchronously. A successful draft response confirms neither email delivery nor a reviewer decision. Check ARI-06's execution and the stored status.
- The demonstrated chat response itself cautioned that approval submission was not confirmed. The email and subsequent sheet update provide the separate evidence that approval proceeded.
- The original ARI-05 fields include expression names `=asin` and `=text`, string review_count, and literal reviewer_notes `Leave empty`. They are preserved to avoid silently changing tested behavior. Inspect resolved output after import; for a maintained version, normalize names/types and use an empty notes field, then retest.
- The agent's prompts encourage grounded answers, but model output still requires human review. Approval of a brief does not automatically publish listing changes or validate product/safety claims.
- These exports do not add retries, approval reminders, expiration handling, or monitoring. Reconnect expired OAuth credentials and investigate failed executions.
