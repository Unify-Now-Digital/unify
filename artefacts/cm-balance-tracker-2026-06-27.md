# CM Balance Tracker — 27 June 2026

**Scope:** all Churchill Memorials orders with `Proof Status = Lettered` and `Paid in Full ≠ Yes/Sent` (per screenshot — 26 customers).

**Method per customer:** Stripe deposit invoice → ClickUp custom fields → ClickUp comments (Aylin's permit log) → Gmail (post-deposit design changes / extra lettering). Each row shows the reconciled balance and a confidence level.

**GHL not loaded into this session** — design-change signal comes from Gmail + ClickUp comments only. If a customer thread lives only in GHL, the design-change flag may be a false negative.

---

## TL;DR — action queue

### 🟢 Ready to draft balance invoice (clean — duplicate deposit, remove permit line)

| # | Customer | Deposit inv | Balance £ | Stripe customer | Notes |
|---|---|---|---|---|---|
| 1 | **Janette Etheridge** | `-0416` (paid £938.45, permit £110) | **£828.45** | `cus_U39kGnet8iFBJr` | clean |
| 2 | **Leonard Lewis** | `-0293` (paid £1,481.75, permit £588) | **£893.75** | `cus_TPPGXhA2TZc18u` | clean — permit perfectly reconciled |
| 3 | **Sheara Carl Singh** | `-0305` (paid £1,109.25, permit £207) | **£902.25** | `cus_TS5grAyDsbUnZv` | clean |
| 4 | **Sharmine Goni** | `-0358` (paid £974.75, permit £240) | **£734.75** | `cus_TkQYr8Vra6yUYG` | clean — £5 permit shortfall vs ClickUp £245 (negligible, CM absorbs) |
| 5 | **Helen Fricker** | `-0245` (paid £875, no permit) | **£875.00** | `cus_T8wjU4ALkHGCSH` | clean — renovation, no permit by agreement. 4 duplicate Stripe customers exist (cleanup) |
| 6 | **Leanne Smith** | `-0220` (paid £2,180, permit £550) | **£1,630.00** | `cus_T2fxh1Gh5lbK29` | clean BUT customer asked for concrete landing addition — flag scope/price decision before invoicing |
| 7 | **Binil Baby** | `-0386` (paid £3,043, permit £538) | **£2,505.00** | `cus_TuADU2rACNyZc2` | clean BUT (a) cemetery actually charged £739 (£201 shortfall — absorb or recover?), (b) customer wants cross etched — scope change |
| | **Subtotal** | | **£8,369.20** | | |

### 🟡 Ready, but with a permit-credit decision

| # | Customer | Balance baseline | Adjustment | Net | Reason |
|---|---|---|---|---|---|
| 8 | **Helen Allen** | £830.95 | −£25.90 | **£805.05** | Charged £191.70 permit; Aylin paid £165.80 — refund / credit £25.90 to customer |
| 9 | **Keely Whailin** | £658.30 | −£235.00 | **£423.30** | Charged £305 permit; Aylin paid £70 — large overcharge, credit £235 |
| | **Subtotal** | | | **£1,228.35** | |

### 🟠 Existing balance invoice needs revision

| # | Customer | Existing inv | Should be | Why |
|---|---|---|---|---|
| 10 | **Shannon Gorton** | `-0333` open £627.50 (no permit) | **£542.50** | Apply −£200 credit (Stevie Stones paid on her behalf) + £115 permit shortfall (charged £160, cemetery £275). Net: 627.50 − 200 + 115 |

### 🔴 Already paid — update ClickUp only

| # | Customer | What happened | Action |
|---|---|---|---|
| 11 | **Philip Lunn** | Balance `-0602` £1,017.50 already paid 24 Jun | Update ClickUp `Paid in Full → Yes`. £20 permit absorbed (charged £175, paid £195) |

### ⏸ Hold — needs a call from you before invoicing

| # | Customer | Why it's on hold |
|---|---|---|
| 12 | **Steph Carter** | Aylin: "Add permit fee to 2nd invoice when we know" — Polborder Methodist still hasn't confirmed permit fee. Hold balance £713.45 until permit is known. |
| 13 | **Zulfiqar Qazi** | (a) Deposit `-0350` £633.70 **still OPEN/unpaid** — chase first. (b) Aylin paid Edinburgh £512.40 but deposit charged £0 permit — likely combined permit for multiple memorials? Need clarification. |
| 14 | **Sherriden Lewis** | Two deposit invoices on file: `-0334` open £925 (untouched) and `-0387` paid £685 (with −£240 lettering credit). Decide: void `-0334`, and does the £240 credit apply to balance too (so balance = £685, not £925)? |
| 15 | **Ingrid Ransome** | Deposit `-0284` £1,012.50 **still OPEN/unpaid** despite "install" status — solicitors Ellisons supposed to pay. Chase before balance. |
| 16 | **Terry Ford** | Spec changed mid-flight. Two paid deposits of £1,155.15 each (`-0272` and `-0603`) = full Stripe order £2,310.30 already collected. ClickUp says order is £3,070.80 → £760.50 gap. Either ClickUp is the revised total and balance is due, or Stripe is correct and no balance. Voided invoices `-0308/0309/0310` reference revised spec (Heart photo plaque + 160 letters + 2 pots). Needs your call. |
| 17 | **Erol Hasan** | Deposit `-0260` £920 **still OPEN/unpaid**. Permit £332 paid separately and confirmed. Chase deposit, then balance £920. |
| 18 | **Cindy Tearle** | Eaton Bray Parish Council permit unconfirmed (`£60 Res / £220 non-Res`). Deposit `-0306` £839 paid with £0 permit charged. Decide whether to hold balance until permit confirmed, or invoice £839 now and add permit later. |

### ✅ Out of scope (already paid in full at deposit — `pct_to_pay = 1`)

Dale Humphreys, Julie Willis, Aysha Khatun, Tessa Newson — no balance owed; suggest you flip ClickUp `Paid in Full → Yes`.

### ❓ Data anomalies — manual investigation

| Customer | Issue |
|---|---|
| **Dale Ramdeen** | `cus_SGGuU3kWopLlYY` exists, no Stripe invoices, but £1,496.33 in PaymentIntents (paid outside invoice system). ClickUp order value missing. |
| **Damali Raymond** | `cus_SomBH7NxnvRW02`, no invoices, £1,770 PI. ClickUp order value missing. |
| **Shaun Houlahan** | `cus_SweNF5lpd4RKrx`, no Stripe-native invoice, £875 one-off charge tied to external invoice 000380. |
| **Angela Sadler** | Not found in Stripe by email or stripe_id. |
| **Rachel Burkey** | Not found in Stripe by email or stripe_id. |

---

## Totals

| | £ |
|---|---|
| Clean & ready to draft (7 customers) | 8,369.20 |
| Ready w/ permit credit (2 customers) | 1,228.35 |
| Existing draft to revise (Shannon Gorton) | 542.50 |
| Already paid (Philip Lunn — no new £) | 0 |
| On hold (7 customers) — potential | up to ~6,365 |
| **Realistic next 30 days (clean + adjusted)** | **~£10,140** |

---

## Per-customer detail

Each block: Stripe invoice anchor → ClickUp money fields → ClickUp comments (Aylin) → Gmail signal → reconciled balance + confidence.

### 🟢 1. Janette Etheridge — Dorothy Lodge

| Source | Data |
|---|---|
| ClickUp task | [`869c9hg6b`](https://app.clickup.com/t/869c9hg6b) — status `install` |
| Stripe customer | `cus_U39kGnet8iFBJr` |
| Email | jetheridge@hotmail.co.uk |
| Deposit invoice | `-0416` PAID 2026-02-26 — total £938.45 |
| Line: permit | £110 |
| Line: 50% deposit marker | £828.45 (`Total: 1656.9*0.5`) |
| ClickUp order / permit | £1,656.90 / £110 |
| ClickUp permit paid? | Not Yet (CM hasn't paid cemetery yet — doesn't affect balance) |
| ClickUp comments | Approval received; "Also paid by card" |
| Gmail design change | none |
| **Balance baseline** | **£828.45** (deposit total − permit line) |
| Adjustments | none |
| **Final balance** | **£828.45** |
| Confidence | **HIGH** |
| Action | Draft `BALANCE` = duplicate `-0416`, remove permit line |

---

### 🟢 2. Leonard Lewis — Lynnette Ebanks

| Source | Data |
|---|---|
| ClickUp task | [`869b4yd2f`](https://app.clickup.com/t/869b4yd2f) — status `install` |
| Stripe customer | `cus_TPPGXhA2TZc18u` |
| Email | leonardlewis1@hotmail.com |
| Deposit invoice | `-0293` PAID 2025-11-12 — total £1,481.75 |
| Line: permit | £588 |
| Line: 50% deposit marker | £893.75 (`Total: 1787.5*0.5`) |
| ClickUp order / permit | £1,787.50 / £588 |
| ClickUp permit paid? | Paid or Not Required |
| ClickUp comments | Aylin: "Paid 588. Ref 3465" ✓ perfectly reconciled |
| Gmail design change | none |
| **Balance baseline** | **£893.75** |
| Adjustments | none |
| **Final balance** | **£893.75** |
| Confidence | **HIGH** |
| Action | Draft `BALANCE` = duplicate `-0293`, remove permit line |

---

### 🟢 3. Sheara Carl Singh — TBC

| Source | Data |
|---|---|
| ClickUp task | [`869b7n13n`](https://app.clickup.com/t/869b7n13n) — status `install` |
| Stripe customer | `cus_TS5grAyDsbUnZv` |
| Email | sheara_singh@yahoo.co.uk |
| Deposit invoice | `-0305` PAID 2025-11-19 — total £1,109.25 |
| Line: permit | £207 |
| Line: 50% deposit marker | £902.25 (`Total: 1804.5*0.5`) |
| ClickUp order / permit | £1,804.50 / £207 |
| ClickUp permit paid? | Not Yet (CM hasn't paid cemetery — no balance impact) |
| ClickUp comments | Approval received; no permit amount confirmed yet |
| Gmail design change | none |
| **Balance baseline** | **£902.25** |
| Adjustments | none |
| **Final balance** | **£902.25** |
| Confidence | **HIGH** |
| Action | Draft `BALANCE` = duplicate `-0305`, remove permit line. Confirm deceased name before sending. |

---

### 🟢 4. Sharmine Goni — Jasmine Sulthana Goni

| Source | Data |
|---|---|
| ClickUp task | [`869bp5x2j`](https://app.clickup.com/t/869bp5x2j) — status `Closed` |
| Stripe customer | `cus_TkQYr8Vra6yUYG` |
| Email | sharmine273@gmail.com |
| Deposit invoice | `-0358` PAID 2026-01-05 — total £974.75 |
| Line: permit | £240 |
| Line: 50% deposit marker | £734.75 (`Total: 1469.5*0.5`) |
| ClickUp order / permit | £1,469.50 / £245 |
| ClickUp permit paid? | Paid or Not Required |
| ClickUp comments | Aylin: "Paid 245 - Approval & Receipt incoming" (9 Mar 2026) |
| Gmail design change | none |
| **Balance baseline** | **£734.75** |
| Adjustments | £5 permit shortfall (charged £240, paid £245) — CM absorbs, no customer impact |
| **Final balance** | **£734.75** |
| Confidence | **HIGH** |
| Action | Draft `BALANCE` = duplicate `-0358`, remove permit line |

---

### 🟢 5. Helen Fricker — Ronald Frank Mouland

| Source | Data |
|---|---|
| ClickUp task | [`869a77e38`](https://app.clickup.com/t/869a77e38) — status `Closed`, renovation |
| Stripe customer | `cus_T8wjU4ALkHGCSH` (4 other duplicate cus_ exist — cleanup needed) |
| Email | helenshields64@btinternet.com |
| Deposit invoice | `-0245` PAID 2025-09-29 — total £875 |
| Line: permit | £0 |
| Line: 50% deposit marker | £875 (`Total: 1750*0.5`) |
| ClickUp order / permit | £1,750 (renovation) / £0 |
| ClickUp permit paid? | Not Yet (going ahead without permit) |
| ClickUp comments | Aylin: "Going ahead without permit — customer happy too" |
| Gmail design change | none |
| **Balance baseline** | **£875** |
| Adjustments | none |
| **Final balance** | **£875.00** |
| Confidence | **HIGH** |
| Action | Draft `BALANCE` = duplicate `-0245`, remove permit line (£0, so technically just duplicate). Flag the 4 duplicate Stripe customers for cleanup. |

---

### 🟢 6. Leanne Smith — TBC (⚠️ scope addition flag)

| Source | Data |
|---|---|
| ClickUp task | [`869afc1na`](https://app.clickup.com/t/869afc1na) — status `Closed` |
| Stripe customer | `cus_T2fxh1Gh5lbK29` |
| Email | leannehsmith@outlook.com |
| Deposit invoice | `-0220` PAID 2025-09-12 — total £2,180 |
| Line: permit | £550 |
| Line: 50% deposit marker | £1,630 (`Total: 3260*0.5`) |
| ClickUp order / permit | £3,260 / £550 |
| ClickUp permit paid? | Paid or Not Required |
| ClickUp comments | Aylin: "Paid 550 online ref FS827591258" ✓ ; **customer wants concrete landing added** (not yet priced) |
| Gmail design change | none in email |
| **Balance baseline** | **£1,630** |
| Adjustments | **Pending decision** — concrete landing scope change. Not priced. |
| **Final balance** | **£1,630 + landing TBC** |
| Confidence | HIGH on baseline, but the landing add-on needs a price |
| Action | EITHER draft `BALANCE` £1,630 and bill landing separately later, OR price the landing now and roll into the balance. Your call. |

---

### 🟢 7. Binil Baby — Neha George (⚠️ permit shortfall + scope addition)

| Source | Data |
|---|---|
| ClickUp task | [`869c030bv`](https://app.clickup.com/t/869c030bv) — status `Closed` |
| Stripe customer | `cus_TuADU2rACNyZc2` |
| Email | binilbaby91@hotmail.com |
| Deposit invoice | `-0386` PAID 2025-12-02 — total £3,043 |
| Line: permit | £538 |
| Line: 50% deposit marker | £2,505 (`Total: 5010*0.5`) |
| ClickUp order / permit | £5,010 / £538 |
| ClickUp permit paid? | Paid or Not Required |
| ClickUp comments | Aylin: "Paid £739.00 online cmg website" — **£201 shortfall vs charged**; customer also wants **cross etched** onto memorial |
| Gmail design change | none in email |
| **Balance baseline** | **£2,505** |
| Adjustments | (a) Permit shortfall £201 (decide: absorb or add to balance). (b) Cross etching scope add (not priced). |
| **Final balance** | **£2,505** (or £2,706 if you bill the permit shortfall) — plus cross etch TBC |
| Confidence | HIGH on baseline |
| Action | Draft `BALANCE` £2,505. Decide separately on the £201 permit shortfall and cross etch price. |

---

### 🟡 8. Helen Allen — Bernard Allen (permit credit £25.90)

| Source | Data |
|---|---|
| ClickUp task | [`869bem90w`](https://app.clickup.com/t/869bem90w) — status `install` |
| Stripe customer | `cus_TZwlKkdQIKzeJ9` |
| Email | helenmallen286@gmail.com |
| Deposit invoice | `-0340` PAID 2025-12-10 — total £1,022.65 |
| Line: permit | **£191.70** charged |
| Line: 50% deposit marker | £830.95 (`Total: 1661.9*0.5`) |
| ClickUp order / permit | £1,661.90 / £165.80 |
| ClickUp permit paid? | Paid or Not Required |
| ClickUp comments | Aylin: **"Paid 165.80 Invoice 7410442. wiv Rev."** — cemetery only charged £165.80 |
| Gmail design change | Discussed mixed-colour infill (black & grey, silver lettering) — no money impact |
| **Balance baseline** | **£830.95** |
| Adjustments | **Customer overcharged £25.90 on permit** (191.70 − 165.80) — credit on balance |
| **Final balance** | **£805.05** (or £830.95 + separate £25.90 refund) |
| Confidence | **HIGH** on the overcharge; **MEDIUM** on the decision (credit vs refund) |
| Action | Draft `BALANCE` £805.05 — duplicate `-0340`, remove permit line, add `Permit overpayment credit` line at −£25.90. (Or balance £830.95 + separate refund of £25.90.) |

---

### 🟡 9. Keely Whailin — Irene Whailin (permit credit £235)

| Source | Data |
|---|---|
| ClickUp task | [`869ajftqz`](https://app.clickup.com/t/869ajftqz) — status `install` |
| Stripe customer | `cus_T6Kpzhor2JYrRo` |
| Email | keelywhailin74@gmail.com |
| Deposit invoice | `-0226` PAID 2025-09-22 — total £963.30 |
| Line: permit | **£305** charged |
| Line: 50% deposit marker | £658.30 (`Total: 1316.6*0.5`) |
| ClickUp order / permit | £1,316.60 / £305 |
| ClickUp permit paid? | Paid or Not Required |
| ClickUp comments | Aylin: **"Paid 70 on Rev. Approval incoming"** — Armthorpe Parish Council only £70 |
| Gmail design change | none |
| **Balance baseline** | **£658.30** |
| Adjustments | **Customer overcharged £235 on permit** (305 − 70) — large credit |
| **Final balance** | **£423.30** (or £658.30 + separate £235 refund) |
| Confidence | **HIGH** on the overcharge; **MEDIUM** on credit-vs-refund decision |
| Action | Draft `BALANCE` £423.30 (duplicate `-0226`, remove permit, add credit line). Confirm the £305 vs £70 isn't a data error first — that's a big swing. |

---

### 🟠 10. Shannon Gorton — Lukas Gorton (existing balance invoice needs revision)

| Source | Data |
|---|---|
| ClickUp task | [`869axydrv`](https://app.clickup.com/t/869axydrv) — status `install` |
| Stripe customer | `cus_THwt18bAIiTyIi` |
| Email | shannonpenny6@gmail.com |
| Deposit invoice | `-0271` PAID 2025-10-23 — total £787.50 (£627.50 mem + £160 permit) |
| **Existing balance invoice** | **`-0333` open £627.50** (no permit line) — needs revision |
| ClickUp order / permit | £1,255 / £275 |
| ClickUp permit paid? | Paid or Not Required |
| ClickUp comments | Aylin: **"Paid 275 22 Mar"** (Stevenage Weston Rd) ; **"NB: Stevie Stones is supposed 2 have paid bout 200 on her behalf. Edit 2nd invoice accordingly"** |
| Gmail design change | none |
| **Balance baseline** | **£627.50** |
| Adjustments | (a) **−£200 credit** (Stevie Stones paid on customer's behalf), (b) **+£115 permit shortfall** (charged £160, cemetery £275) |
| **Final balance** | **£542.50** |
| Confidence | **HIGH** on both adjustments (both explicitly in Aylin's comments) |
| Action | **VOID `-0333`**, create new balance with the −£200 credit line, +£115 permit-shortfall line, net £542.50. (Or edit `-0333` in place if Stripe lets you while it's open.) |

---

### 🔴 11. Philip Lunn — Joyce Mary Lunn (already settled)

| Source | Data |
|---|---|
| ClickUp task | [`869btnfvv`](https://app.clickup.com/t/869btnfvv) — status `Closed` |
| Stripe customer | `cus_TnS2ddi9Qbr6XE` |
| Email | phil.lunn@evotra.co.uk (note: ClickUp comment says use philiplunn@hotmail.com going forward) |
| Deposit invoice | `-0366` PAID 2025-12-15 — total £1,192.50 (£1,017.50 mem + £175 permit) |
| **Balance invoice** | **`-0602` PAID 2026-06-24 — £1,017.50** |
| ClickUp comments | Aylin: "Paid 195 fee" — CM paid £195 cemetery (£20 absorbed vs £175 charged) |
| Gmail design change | none |
| **Status** | **Fully paid** |
| Action | Flip ClickUp `Paid in Full → Yes` on task `869btnfvv`. No invoice needed. |

---

### ⏸ 12. Steph Carter — Natalie Regine Allen

| Source | Data |
|---|---|
| ClickUp task | [`869c9m1ca`](https://app.clickup.com/t/869c9m1ca) — status `install` |
| Stripe customer | `cus_U3C1pE6wS3RYw2` |
| Deposit invoice | `-0418` PAID 2026-02-26 — total £713.45 (£713.45 mem + £0 permit) |
| ClickUp order / permit | £1,426.90 / £0 |
| ClickUp permit paid? | Not Yet |
| ClickUp comments | Aylin: "Add permit fee to 2nd invoice when we know" — Polborder Methodist still TBC |
| **Hold reason** | Permit fee unknown — invoicing balance now means a follow-up permit invoice later |
| If you invoice now anyway | **£713.45** + later permit add-on |
| Action | **HOLD** — chase Polborder Methodist for permit fee, then invoice combined balance + permit |

---

### ⏸ 13. Zulfiqar Qazi — Kanza Chohan (deposit unpaid + huge permit discrepancy)

| Source | Data |
|---|---|
| ClickUp task | [`869bj0qcb`](https://app.clickup.com/t/869bj0qcb) — status `install`, priority urgent |
| Stripe customer | `cus_TdQ5IZR60Cs0bT` |
| Deposit invoice | `-0350` **OPEN, unpaid** since 2025-12-19 — £633.70 (£633.70 mem + £0 permit) |
| ClickUp order / permit | £1,267.40 / £164.40 |
| ClickUp permit paid? | Paid or Not Required |
| ClickUp comments | Aylin: **"Paid 512.40 Ref 50137"** (Edinburgh, May 2026) — customer charged £0, cemetery £512.40. Likely combined permit for multiple memorials? |
| Gmail | inscription wording edits only |
| **Hold reason** | (a) Deposit unpaid 6 months, (b) £512.40 cemetery cost vs £0 charged customer — massive shortfall |
| Action | (1) Chase deposit `-0350` £633.70. (2) Confirm whether £512.40 is for one memorial or several. (3) If single memorial, balance = £633.70 + £512.40 = £1,146.10. |

---

### ⏸ 14. Sherriden Lewis — Leo Aaron Sutherland (two deposit invoices)

| Source | Data |
|---|---|
| ClickUp task | [`869bcqxeq`](https://app.clickup.com/t/869bcqxeq) — status `install` |
| Stripe customer | `cus_TXf5yCnSYrrglp` |
| Deposit invoice A | `-0334` OPEN £925 (permit £0) — earlier |
| Deposit invoice B | `-0387` PAID £685 (£925 mem − £240 "Lettering credit adjustment") |
| ClickUp order / permit | £1,850 / £0 (no permit) |
| ClickUp comments | Aylin: "No Permit fee" "No Patch on Bear". **Surname mismatch — Birmingham permit issued to 'Sherriden Sutherland' (Sutton New Hall)** — verify |
| Gmail design change | Patch on bear removed + step inscription added — explains the −£240 credit |
| **Hold reason** | Need to decide: (a) void `-0334`, (b) does the £240 credit apply to balance (→ balance = £685) or was it deposit-only (→ balance = £925)? |
| Action | Decide on credit, then void `-0334` and draft new balance |

---

### ⏸ 15. Ingrid Ransome — James Leslie Ransome (deposit unpaid)

| Source | Data |
|---|---|
| ClickUp task | [`869b2hrzy`](https://app.clickup.com/t/869b2hrzy) — status `install` |
| Stripe customer | `cus_TMojeZUiNVsEVG` |
| Deposit invoice | `-0284` **OPEN, unpaid** since 2025-11-05 — £1,012.50 (£852.50 mem + £160 permit) |
| ClickUp order / permit | £1,705 / £160 |
| ClickUp permit paid? | Not Yet |
| ClickUp comments | Solicitors Ellisons paying — fast-track requested. No permit confirmation. |
| Gmail | none relevant |
| **Hold reason** | Deposit `-0284` £1,012.50 still open 7+ months |
| Action | Chase Ellisons solicitors first. Once deposit paid, balance = £852.50 (clean). |

---

### ⏸ 16. Terry Ford — Ellie Elizabeth Ford (£760 mystery)

| Source | Data |
|---|---|
| ClickUp task | [`869axykj7`](https://app.clickup.com/t/869axykj7) — status `install` |
| Stripe customer | `cus_THwuBGDyP6g4m6` |
| Deposit invoice A | `-0272` PAID — £1,155.15 (Total `2310.3*0.5`, no permit) |
| Deposit invoice B | `-0603` PAID 2026-06-24 — £1,155.15 (Total `2310.3*0.5`, no permit) |
| Voided | `-0308`, `-0309`, `-0310` (revised spec attempts) |
| Open | revised spec invoices at £1,915.65 / £2,113.65 |
| ClickUp order / permit | £3,070.80 / £0 |
| Permit paid? | Not Required (baby grave waiver) |
| ClickUp comments | Aylin: "Spoke 2 cemetery — baby grave so No Fee" ✓ ; Arin: "Final Invoice is prepared in stripe with permit and adjusted amount at 160 letters" (Nov 2025) |
| Gmail | Spec revised — Heart photo plaque, 2 pots, 160 letters (60 over standard 100 × £2.40 = +£144) — invoice `-0311` for £1,915.65, voided |
| Payments received | £2,310.30 total |
| **Hold reason** | Customer has paid the full Stripe order value (£2,310.30) over two deposits. ClickUp says order is £3,070.80 — £760.50 gap. Two readings: (a) Stripe is current — customer owes £0. (b) ClickUp is current — customer owes £760.50 for the spec increase (extra lettering + plaque). |
| Action | Tell me which reading is correct. If (b), draft balance £760.50. |

---

### ⏸ 17. Erol Hasan — Arif Halil Hasan (deposit unpaid)

| Source | Data |
|---|---|
| ClickUp task | [`869ardq70`](https://app.clickup.com/t/869ardq70) — status `Closed` |
| Stripe customer | `cus_TEyMb49Q8oeW7a` |
| Deposit invoice | `-0260` **OPEN, unpaid** since 2025-10-15 — £920 (£920 mem + £0 permit) |
| Separate permit invoice | `in_1SrzLU…` — £332 PAID |
| ClickUp order / permit | £1,840 / £332 |
| ClickUp permit paid? | Paid or Not Required |
| ClickUp comments | Aylin: "Paid 332 (664 Total — together with Sherko Permit)" — £332 for this memorial ✓ |
| Gmail | inscription longer than permit form box — possible extra lettering, low signal |
| **Hold reason** | Deposit `-0260` £920 still open 8+ months |
| Action | Chase deposit. Once paid, balance = £920 (no permit on balance — already collected separately). Verify lettering count. |

---

### ⏸ 18. Cindy Tearle — Thomas Luke Tearle (permit unconfirmed)

| Source | Data |
|---|---|
| ClickUp task | [`869b7nmg9`](https://app.clickup.com/t/869b7nmg9) — status `Closed` |
| Stripe customer | `cus_TS69vbicBzNjKe` |
| Deposit invoice | `-0306` PAID 2025-11-19 — £839 (£839 mem + £0 permit) |
| ClickUp order / permit | £1,678 / £0 |
| ClickUp permit paid? | Not Yet |
| ClickUp comments | Eaton Bray Parish Council; permit "£60 Res / £220 non-Res" — actual not yet confirmed; approved needs BACS |
| Gmail | none relevant |
| **Hold reason** | Permit fee not yet confirmed (could be £60 or £220) |
| Action | EITHER chase Eaton Bray confirmation and combine, OR invoice balance £839 now and permit separately later |

---

### ✅ Out of scope — paid in full at deposit (`pct_to_pay = 1`)

| Customer | Deposit invoice | Status | Action |
|---|---|---|---|
| Dale Humphreys | (no Stripe lookup needed — `paid_total` confirms) | full upfront | ClickUp `Paid in Full → Yes` |
| Julie Willis | (renovation, full upfront) | paid | ClickUp `Paid in Full → Yes` |
| Aysha Khatun | (paid £1,456.19 total) | paid | ClickUp `Paid in Full → Yes` |
| Tessa Newson | Stripe `-0512` PAID £1,659 (full, no `*0.5` marker) | paid | ClickUp `Paid in Full → Yes`. Note 3 duplicate Stripe customers exist. |

---

### ❓ Data anomalies — manual investigation

| Customer | Stripe customer | Stripe invoices | Payments | What to check |
|---|---|---|---|---|
| **Dale Ramdeen** | `cus_SGGuU3kWopLlYY` | none | £895.90 + £600.43 = £1,496.33 across 2 succeeded PIs | Why no invoice on file? Were these manual/Square payments? Reconstruct order value. |
| **Damali Raymond** | `cus_SomBH7NxnvRW02` | none | £1,770 single PI | Same — no Stripe invoice. ClickUp `paid_total_stripe = £1,770` mirrors this. Order value unknown — needs sales record. |
| **Shaun Houlahan** | `cus_SweNF5lpd4RKrx` | none Stripe-native | £875 charge tagged "invoice 000380" | External invoice number — likely a manual/legacy invoice. Reconstruct order. |
| **Angela Sadler** | not found | — | — | Email `angelasadler2000@yahoo.co.uk` and ClickUp `Stripe ID` empty. Maybe different email used in Stripe? |
| **Rachel Burkey** | not found | — | — | Email `rachelburkey3@icloud.com` not in Stripe. ClickUp permit £600 noted but no order value. |

---

## Methodology & confidence notes

- **Anchor:** Stripe deposit invoice's `*0.5` line item.
- **Permit reconciliation:** ClickUp comments (Aylin's "Paid X Ref Y" entries) are the cemetery-actual figure; Stripe's permit line is the customer-charged figure. Difference = adjustment.
- **Design changes:** Gmail thread scan per customer email + ClickUp comment review. **No GHL access** — if there's a customer who only talks via GHL/SMS, design-change signal could be missed.
- **Confidence levels:**
  - HIGH = Stripe & ClickUp & Aylin's comment all agree, no Gmail noise
  - MEDIUM = small permit discrepancy, or Gmail signal that may or may not be material
  - LOW = open data questions (Terry Ford, Zulfiqar Qazi, Sherriden Lewis)
- **Idempotency check:** Stripe was scanned for existing balance invoices — Shannon Gorton (`-0333`) and Philip Lunn (`-0602`) already exist; everything else genuinely needs drafting.

---

## What I'd like you to confirm before I create Stripe drafts

For the 🟢 group, I'd duplicate each deposit invoice in Stripe (`status=draft`, not finalized/sent) and remove the permit line. For 🟡 I'd also add a negative credit line. For 🟠 Shannon I'd void `-0333` and create a fresh draft.

Three calls from you before I push:

1. **Helen Allen £25.90 credit** and **Keely Whailin £235 credit** — apply as a negative line on the balance invoice, or as separate Stripe refunds against the original deposit? (Skill default: credit line.)
2. **Binil Baby £201 permit shortfall** — absorb or add to balance?
3. **Leanne Smith concrete landing** + **Binil cross etch** — draft balance now without them (you bill scope add separately), or hold until priced?

Once you answer, I'll create the Stripe drafts.
