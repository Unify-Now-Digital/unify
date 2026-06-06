---
name: cm-revenue-tracker
description: "Churchill Memorials balance-payment tracker for orders awaiting final payment. Anchors on the Stripe deposit invoice and reconciles it against ClickUp and Gmail to produce the outstanding balance per order plus a ready-to-send balance invoice (deposit invoice duplicated, permit line removed). Trigger phrases: \"CM revenue\", \"balance payments\", \"outstanding balances\", \"who owes\", \"final invoices\", \"balance invoice\", \"payment status\", \"Churchill money\", \"CM finances\", or any mention of Churchill Memorials combined with payments or money owed."
---

# CM Balance-Payment Tracker

For Churchill Memorials orders that still owe a final balance: work out exactly what each customer owes and prepare the balance invoice. Anchored on the Stripe **deposit invoice**; adjustments verified in ClickUp comments and Gmail before anything is billed.

## Core model

CM has two billing patterns, controlled by the **`% to pay`** custom field on the task:

- **50/50 (`% to pay` = 0.5, typically new memorials):** CM bills **50% of order + full permit fee** at deposit. The balance is the **remaining 50% of the order, permit line removed**.
- **100% upfront (`% to pay` = 1.0, typically renovations):** CM bills the **full order + permit** at deposit. By the time the order reaches install, the **baseline balance is £0** — only adjustments (design changes / permit reconciliation / additional inscriptions) can produce a remaining balance.

In both cases the permit is a pass-through cost to the cemetery — it is never re-charged on the balance invoice and never counted as CM revenue.

For 50/50 orders, the simplest balance invoice is the deposit invoice **duplicated, with the permit line item deleted**. For 100% upfront orders, there is no balance invoice unless an adjustment fires.

```
50/50 case:    Balance due = remaining 50% of order   ← deposit invoice duplicated, permit line removed
                            + permit adjustment        ← required − charged   (pass-through ledger)
                            ± design-change delta      ← verified in Gmail

100% case:     Balance due = 0
                            + permit adjustment
                            ± design-change delta
                            + any additional-inscription / additional-item charges agreed post-deposit
```

**Additional lettering** (£2.40/char over the quoted allowance) is applied **manually by Arin/Aylin** — do NOT calculate or add it. Only raise a flag if a thread clearly references extra lettering, so it isn't missed.

## Scope

Run for ClickUp tasks in **CM: Orders** (List ID `901207633256`) where the task **status = `install`** — i.e. orders that have reached the install stage and so the balance is due before fitting. (Earlier proxies such as `Proof Status = Lettered` are not used; install is the operational signal that the balance must now be collected.)

## ClickUp fields

| Field | ID | Use |
|---|---|---|
| Stripe ID | `2bf78ef0-821e-44d7-9a82-2a79cd0ab53b` | Stripe customer (cus_xxx) — primary key into Stripe |
| Email (Customer) | `6b7de2eb-ac4f-4880-b8d6-59bad0998df4` | Stripe search fallback; Gmail search key |
| £ Order Value | `550c70ea-c628-45f8-aa1f-ba5ef006e928` | Quoted order value — **sanity cross-check only** |
| Permit £ | `15c1fd79-de78-443e-b793-2d2299542dea` | Expected permit fee — cross-check |
| Permit Paid? | `ca2d1c75-301b-4bd4-aa3c-9eae212ee180` | Not Yet · Paid · Paid or Not Required · Not Required — cross-check |
| % to pay | `26cbad2b-0822-4df9-95d9-66bbf1812cd0` | Billing pattern selector: 0.5 = 50/50, 1.0 = 100% upfront |
| £ Outstanding | `1d6ae285-ad5e-4568-8adc-370afb63eefa` | Formula: order − (order × % to pay). Sanity check vs Stripe |
| £ Paid Total (Stripe) | `3456af12-7ef2-4020-8680-2c6752434865` | What ClickUp thinks Stripe has received — verify against Stripe |
| Deposit Paid | `5a14dfe4-46bd-486a-93df-7be1ee8f3ab4` | Date deposit was recorded as paid |

Also extract from the task: **customer name**, **deceased name**, **burial ground/cemetery**, **status**, **tags** (`renovation` is the strong signal for 100%-upfront billing), and read **ClickUp comments** (Aylin records the actual permit fee, council emails, and any "added inscription" / scope-change notes here).

---

## The five layers

### 1. Extract (structured first)

Pull the fields above from each in-scope task. Treat ClickUp as the spine; do **not** rebuild figures from Gmail free-text — Gmail is only used to *verify adjustments*, never to construct the base number.

### 2. Validate (each figure gets a source + confidence)

**Deposit invoice — Stripe (this is the anchor)**
- List the customer's Stripe invoices (by cus_xxx, fallback email).
- **Multiple Stripe customers for one email is common** — searching by email can return 2–5 `cus_xxx`. Prefer the `Stripe ID` field on the task. If missing, match by invoice **total = `£ Inv Total`** AND **created date ≈ `Deposit Paid` date**.
- **50/50 orders:** identify the **deposit invoice** by a **line-item description containing `*0.5`** (the 50% deposit marker). Balance baseline = that invoice duplicated, permit line removed = remaining 50%.
- **100%-upfront orders (`% to pay` = 1):** the deposit invoice IS the full-amount invoice (order + permit). Baseline balance = **£0**.
- If the apparent deposit invoice total ≠ expected (`order × % to pay` + permit), inspect line items — there may be additional items or the order/permit may have changed.
- Watch out for **stale draft / voided / orphan invoices** on sibling Stripe customers under the same email — `void` or `open` invoices on a *different* `cus_xxx` than the paid deposit usually need separate review, not silently rolled into this order's balance.
- If there is **no invoice at all** → flag `No invoice — manual check`.

**Permit charged to customer** = the permit line item on the deposit invoice (£). 0 if no permit line.
- If `Permit Paid? = "Paid or Not Required"` but **no permit line appears on any Stripe invoice**, the permit was likely settled out-of-band (cash/transfer/separately raised invoice). Flag for confirmation — don't assume it's been collected.

**Permit required (actual cemetery fee)** — resolve in this order:
1. **ClickUp comments** (Aylin records it as e.g. `"Added inscription £116.00 (council.contact@...gov.uk)"` or `"Paid 122"`) — primary
2. **Gmail** thread (deceased/customer + cemetery, "permit/fee/payment") — fallback/corroboration
3. **ClickUp `Permit £` / `Permit Paid?`** — structured cross-check
Resolve to a figure, or **not required (£0)**, or **unknown → flag**. When the comment quotes a council figure that **differs from `Permit £`**, the comment wins — `Permit £` is the original quote, not the reconciled actual.

**Payments received** = sum of Stripe payment intents with status `succeeded`, **net of refunds**, GBP (pence → £). Confirms the deposit landed and whether any balance has already been paid.

**Design changes** = scan the customer + burial-ground Gmail thread for **agreed price changes after the deposit** (size, colour, photo plaque added/removed, scope changes). Capture as a signed delta **plus the evidencing email**. Never auto-apply. If a design change altered the order *price itself*, bill the **full difference** on the balance (do not re-split 50/50).

### 3. Reconcile (two separate ledgers — never merged)

- **Revenue ledger** (what CM keeps): remaining 50% ± design-change delta.
- **Permit pass-through ledger**: permit adjustment = `required − charged`
  - required > charged → **underpaid** → add the shortfall to the balance
  - required < charged → **overpaid** → credit / refund (reduce the balance)
  - equal, or correctly paid → no change (already handled by stripping the line)
- **Sanity checks:** typical order value ≈ £2,113 (AOV) — flag wild outliers. Cross-check the baseline against ClickUp `£ Order Value`; a material mismatch → flag.

### 4. Decide (gate by confidence + state)

| State | Decision |
|---|---|
| Received ≥ expected, balance ≤ £0 | **Settled** — no action |
| Deposit invoice found, permit reconciles, no design-change emails, no extra-lettering mention | **Clean** — eligible to auto-draft balance invoice |
| Any adjustment triggered, or any unknown/ambiguity | **Review queue** — summarise, do not draft a send |
| Balance invoice already raised/sent in Stripe | **Skip** (idempotency — do not duplicate) |

### 5. Act (gated, plan-first, idempotent)

- **Clean orders:** prepare the balance invoice = deposit invoice **duplicated, permit line removed**. Present the full list to Arin for review. **Never auto-send externally** — show the plan, send only on confirmation.
- **Review-queue orders:** present the discrepancy, the proposed adjustment, and the evidencing comment/email, for Arin to decide.
- Before acting on any order, confirm no balance invoice already exists in Stripe.

---

## Flags

| Flag | Condition |
|---|---|
| 🔴 Balance outstanding | Clean balance > £0 — ready to invoice |
| 🟠 No deposit invoice | No `*0.5` invoice (and no full invoice) found in Stripe |
| 🟡 Permit unreconciled | Required fee unknown, or charged ≠ required |
| 🟣 Design change unconfirmed | Gmail shows a post-deposit price change |
| 🔵 Extra lettering mentioned | Thread references additional lettering — apply £2.40/char **manually** |
| ⚪ Overpaid | Stripe received > expected total |
| ✅ Settled | Balance ≤ £0 |

## Report format

Concise. Header summary:

```
💰 CM Balance Check — [date]
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
Orders reviewed:        [X]
Ready to invoice:       [A]
In review (adjusted):   [B]
Settled:                [C]
Total outstanding:      £X,XXX
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
```

Then one short block per order:

```
[Customer] — [Deceased] — [Cemetery]
  Baseline (remaining 50%):  £X
  Permit:  charged £X / required £X  → adj £X   [source: ClickUp comment / Gmail dd-mm]
  Design change:  none | +£X — see email dd-mm
  Lettering:  none | mentioned, apply manually
  ▶ Balance due: £X
  Action: Ready to send balance invoice  |  Hold — [reason]
```

## Principles

- The Stripe deposit invoice is the source of truth. ClickUp `£ Order Value` and Gmail figures corroborate it; they never replace it.
- Permit fees are pass-through to the cemetery — never folded into CM revenue, never re-charged on the balance.
- Every customer is bereaved. No invoice goes out without Arin's sign-off; any uncertainty holds for review rather than billing a grieving customer in error.
