---
name: cm-invoice-followup
description: Daily pulse-check on Churchill Memorials "invoice sent" cohort. Pulls every customer at that ClickUp status, cross-references their Gmail thread for sentiment & engagement, separates active leads from zombie pipeline, and writes an actionable summary doc to ClickUp + drops alerts on any customer who has emailed a complaint that may have gone unanswered. Use whenever the user asks for an "invoice sent pulse check", "CM follow-up audit", "who needs chasing", or asks to babysit the CM pipeline.
---

# CM Invoice-Sent Pulse Check

## What this routine is for

Churchill Memorials sells memorial stones to bereaved families. The CM: Orders ClickUp list has a `invoice sent` status — this is the **first commercial ask**, before deposit-paid. Customers sitting here are warm leads who've been asked to pay a deposit but haven't yet. Some are healthy, some are silent, and occasionally one is unhappy and at risk of cancelling. The routine's job is to **find the unhappy or about-to-lapse ones and surface them before they're lost**, while leaving the healthy ones alone.

Run it daily (or whenever requested). Silence is the wrong outcome only when something needs attention; if every customer is healthy, send no alerts.

## Inputs (known constants for this workspace)

- **ClickUp list:** `CM: Orders`, list ID `901207633256`, space ID `90122956670`, folder ID `90124525190` ("Churchill").
- **Status to filter on:** `invoice sent` (orderindex 1).
- **Custom-field IDs that matter:**
  - `6b7de2eb-ac4f-4880-b8d6-59bad0998df4` — Email (Customer)
  - `550c70ea-c628-45f8-aa1f-ba5ef006e928` — £ Order Value
  - `dc1de9bd-7c27-4da7-b309-50db5fcb4c8c` — £ Deposit (Mem)
  - `26cbad2b-0822-4df9-95d9-66bbf1812cd0` — % to pay
  - `58cabc8d-37ca-48d3-ba22-2d4144bf16f5` — Send Invoice button (its `value` is the timestamp the invoice was last sent)
  - `26c981ce-013c-4733-893e-32ef4a0f7506` — GHL ID
- **Gmail account in scope:** `info@churchillmemorials.com`.

## Steps

1. **Pull the cohort.**
   ```
   mcp__ClickUp__clickup_filter_tasks(list_ids=["901207633256"], statuses=["invoice sent"], subtasks=false, order_by="updated")
   ```

2. **Get bulk time-in-status** for every task in the cohort (up to 100):
   ```
   mcp__ClickUp__clickup_get_bulk_tasks_time_in_status(task_ids=[...])
   ```
   **Important interpretation:** sum `total_time_minutes` across the `invoice sent` entries in `status_history`. There was a bulk re-shuffle on **17 Apr 2026 (Unix ms ≈ 1776454617364)** where many tasks bounced through `deposit paid` for 4 minutes and back. Treat any `deposit paid` entry of ≤ 5 minutes as a system artifact, not real progress. Real dwell = total minutes across all `invoice sent` history slices.

3. **Bucket the cohort by real dwell time:**
   - **Zombie (≥120 days):** statistically dead. Don't deep-dive each one. Recommend bulk closure as a single line item in the summary.
   - **Stale-but-actionable (30-120 days):** the highest-value chase candidates. Deep-dive each one (steps 4-5).
   - **Recent (8-30 days):** deep-dive if value is high (>£1k deposit) or if customer has a `% to pay = 1.0` (100% upfront).
   - **Fresh (≤7 days):** skip individual analysis; let invoice cadence work.

4. **For each deep-dive target, pull full task detail** with `mcp__ClickUp__clickup_get_task(task_id, include=["custom_fields"])` to get the customer email, £ order value, £ deposit, % to pay.

5. **For each deep-dive target, sweep Gmail:**
   ```
   mcp__Gmail__search_threads(query="<customer_email> newer_than:90d", view="THREAD_VIEW_METADATA_ONLY", pageSize=10)
   ```
   - Look at message direction (`sender` field) and dates. Build an engagement timeline.
   - **Red flags to score:**
     - Inbound from customer with no Churchill reply within 48h → possible missed message.
     - Customer language hinting at frustration, paused order, "not going ahead", confusion about pricing → urgent.
     - Zero customer inbound ever, only Stripe-generated outbound → silent / possibly bad email.
   - **If a customer message looks like a complaint or cancellation signal, fetch the full thread:**
     ```
     mcp__Gmail__get_thread(threadId="<id>", messageFormat="FULL_CONTENT")
     ```
     ⚠ Stripe invoice emails balloon thread payloads (>50k chars, will overflow context). Prefer `MINIMAL` or use a subagent to extract just the plaintext_body if you need the customer's words.

6. **Score each deep-dive customer** on three axes:
   - **Engagement:** 🟢 active in last 14d / 🟡 quiet but recent touch / 🔴 silent ≥30d.
   - **Sentiment:** positive / neutral / concerned / **frustrated**.
   - **Payment readiness:** signals like "happy to proceed", asked for bank details, raised an objection.

7. **Decide the action** per customer:
   - **Phone call today** — frustrated/complaint customers. Email is too cold for these.
   - **Firm chase** — silent ≥30 days with no inbound ever.
   - **Soft nudge** — quiet but recent contact.
   - **Hold** — healthy two-way conversation in last 7 days.
   - **Mark as lost** — silent >120 days, no inbound ever.

8. **Produce the deliverable** — create or update a ClickUp doc in folder `90124525190` named `CM Invoice-Sent Pulse Check — <DATE>`. Use the document tool, not a comment, so it persists for the team. Structure:
   - **Headline:** the single most important customer needing action today.
   - **Numbers at a glance:** counts per bucket.
   - **Priority 1:** stale-but-actionable, deep details.
   - **Priority 2:** healthy & engaged (so team doesn't accidentally chase them).
   - **Priority 3:** recent — let breathe.
   - **Zombie pipeline:** the closure-cleanup recommendation with the full list.
   - **Process gaps:** anything systemic (no assignees, no Last Activity dates, GHL not wired, automation artifacts).

9. **If a customer needs urgent attention (complaint / cancellation risk), also drop a comment directly on their ClickUp task** with `mcp__ClickUp__clickup_create_comment(notify_all=true)`. Quote the customer verbatim, link the Gmail thread, recommend a concrete action. This is what triggers an alert to the team.

10. **Push notification** (when `PushNotification` is available in the session): land a one-sentence summary with the most important name + £ at risk on the user's phone. If the run found nothing actionable — every customer healthy — **send no notification**.

## What NOT to do

- Don't email the customers directly. Drafts only at most; the human decides what to send.
- Don't change ClickUp statuses unless explicitly told to. Recommend changes in the summary.
- Don't deep-dive all 45+ customers — that's hundreds of tool calls. Bucket first, deep-dive only the targets.
- Don't pull Stripe-invoice email bodies in full; they are gigantic. Use metadata view, or extract via subagent.
- Don't include speculation about why a customer is silent. Stick to what the data says.

## Known prior findings worth keeping in mind across runs

- **The 17 Apr 2026 bulk re-shuffle** corrupted time-in-status for ~27 customers. They appear "current status time 65d" but real dwell is 140-365 days. Treat the `status_history` slices as the source of truth.
- **No assignees on any CM: Orders task** — there is no human "owner" on these tasks. Flag this in the process-gaps section each time until it's fixed.
- **GHL MCP is not connected to scheduled runs** — when GHL becomes available, add a step 5b to pull GHL conversations per customer (`GHL ID` custom field 26c981ce-013c-4733-893e-32ef4a0f7506).
