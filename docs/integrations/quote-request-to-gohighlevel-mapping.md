# Request-a-Quote → GoHighLevel field mapping

**Business:** Churchill Memorials
**Source:** Website "Request a Quote" email — sender `no-reply@churchillmemorials.co.uk`, subject `[Request a quote]` (WooCommerce *Request a Quote* plugin, HTML body).
**Target:** GoHighLevel (GHL) — one **Contact** + one **Opportunity** per request.

This document is the human-readable companion to the machine-readable map in
[`quote-request-to-ghl-fieldmap.json`](./quote-request-to-ghl-fieldmap.json).

---

## 1. What the email contains

Every request email carries the same blocks:

```
Request a Quote #<number>
  <Product name> - <size> (#<SKU>)
    Pick A Memorial Colour: <value>   + £ x
    Pick Lettering Colour:  <value>   + £ x
    Flower Container:       <value>   + £ x
    Photo Plaque:           <value>   + £ x
    Garden Kerbset:         <value>   + £ x
    Choose infill:          <value>   + £ x
    Inscription:            <free text, multi-line>  + £ x   (letter overage)
  Total: £ <order value>
Customer's message
  <free text>            (optional — often absent)
Customer's details
  First Name : ...
  Last Name  : ...
  Email      : ...
  Phone      : ...
  Grave Location : ...   (free text — often blank)
  Grave Number   : ...   (often blank)
```

## 2. The nine classification fields

| # | Classified field | Where it comes from in the email | Notes |
|---|---|---|---|
| 1 | **First name** | `First Name :` | Direct |
| 2 | **Last name** | `Last Name :` | Direct — **can be blank** |
| 3 | **Order value** | `Total: £ …` | WooCommerce configured total; a **starting figure**, not the final quote, and excludes any permit fee |
| 4 | **Order type** | derived from the product name | Keyword rules: KERB→Kerb Set, HEADSTONE→Headstone, BOOK/DESK/TABLET→Cremation Memorial, RENOVAT/ADD NAME→Renovation |
| 5 | **Cemetery** | `Grave Location :` | ⚠️ Free text — sometimes a postcode (`SG5 4NY`), an address, a cemetery name, or blank. Needs normalisation |
| 6 | **Occasion** | inferred from inscription / message | ⚠️ **Not captured by the form.** Defaults to *Memorial (bereavement)* from cues like "in loving memory of" |
| 7 | **Inscription** | `Inscription:` | Preserve line breaks; strip the trailing `+ £` letter-overage charge |
| 8 | **Additional notes** | `Customer's message` + all product options (colour, lettering, plaque, infill, kerbset, flower container) | Concatenate so nothing configured is lost |
| 9 | *(bonus)* **Deceased name** | parsed from the inscription | The **requester is often not the deceased** — keep them distinct |

## 3. Proposed GoHighLevel mapping

### 3a. Contact (the person requesting the quote)

| GHL field | Type | Source |
|---|---|---|
| `firstName` | standard | First Name |
| `lastName` | standard | Last Name |
| `email` | standard | Email |
| `phone` | standard | Phone |
| `source` | standard | static → `Website – Request a Quote` |
| `tags` | standard | `quote-request`, `<order type>` |
| `cemetery_grave_location` | **custom** | Grave Location |
| `grave_number` | **custom** | Grave Number |

Upsert on **email** (fallback phone) so repeat enquirers don't duplicate.

### 3b. Opportunity (the potential order)

| GHL field | Type | Source |
|---|---|---|
| `name` | standard | `{First} {Last} — {Order Type} (Req #{number})` |
| `monetaryValue` | standard | Order value (Total) |
| `pipelineId` | standard | Churchill **Sales** pipeline |
| `pipelineStageId` | standard | **New Enquiry** stage |
| `status` | standard | `open` |
| `source` | standard | `Website – Request a Quote` |
| `contactId` | standard | id from the Contact upsert |
| `quote_request_number` | **custom** | Request # |
| `order_type` | **custom** | derived |
| `product_name` / `product_sku` | **custom** | product row |
| `memorial_colour` / `lettering_colour` / `photo_plaque` | **custom** | options |
| `occasion` | **custom** | inferred |
| `inscription` | **custom** | inscription |
| `deceased_name` | **custom** | parsed |
| `additional_notes` | **custom** | message + remaining options |

> **Custom fields to create in GHL first** (Settings → Custom Fields): the two Contact
> fields and the Opportunity fields listed above. Everything else is a standard GHL field.

## 4. Known gaps / decisions for Arin

1. **No cemetery field on the website form.** `Grave Location` is free text and frequently blank. Either add a proper "Cemetery" field to the WooCommerce form, or accept that cemetery is captured/cleaned manually in GHL.
2. **No occasion field on the form.** We infer *Memorial (bereavement)* — fine as a default for a memorial-masonry business, but it is a guess, not captured data.
3. **Order value is not the quote.** It's the customer's self-configured WooCommerce total; the real quote (and permit fee) is set later. Store it as "Lead Value", not the agreed price.
4. **Last name / deceased name** can be blank or differ from the requester — mapping tolerates both.

## 5. Smoke test — a real email mapped end-to-end

**Source:** Request a Quote **#9984** (received 2026-07-21, `no-reply@churchillmemorials.co.uk`).

Parsed classification:

| Field | Value |
|---|---|
| First name | Latara |
| Last name | Leigh |
| Order value | £5,010.00 |
| Order type | **Kerb Set** (product: "STUNNING KERBSET WITH PATHWAY AND STEPS … CARVED ROSES … ITALIAN WHITE MARBLE", SKU `AR001-01`) |
| Cemetery | `SG5 4NY` (postcode only — flagged for normalisation) |
| Occasion | Memorial (bereavement) — inferred from "In loving memory of" |
| Inscription | *In loving memory of / Alison Natalie Wynter / Sunrise 24/5/1970 / Sunset 19/8/2018 / Loving Daughter, Sister & Auntie / Forever in our hearts / Rest peacefully* |
| Additional notes | Colour: Black; Lettering: Gold; Flower Container: Two Vases; Infill: White (+£250); Photo Plaque: Oval (+£160) |
| Deceased name | Alison Natalie Wynter (≠ requester Latara Leigh) |

Resulting GHL payloads:

```json
// POST /contacts/  (upsert on email)
{
  "firstName": "Latara",
  "lastName": "Leigh",
  "email": "mperryp88@gmail.com",
  "phone": "07490889838",
  "source": "Website – Request a Quote",
  "tags": ["quote-request", "kerb-set"],
  "customField": {
    "cemetery_grave_location": "SG5 4NY",
    "grave_number": ""
  }
}
```

```json
// POST /opportunities/
{
  "name": "Latara Leigh — Kerb Set (Req #9984)",
  "pipelineId": "<churchill_sales_pipeline_id>",
  "pipelineStageId": "<new_enquiry_stage_id>",
  "status": "open",
  "monetaryValue": 5010.00,
  "source": "Website – Request a Quote",
  "contactId": "<id_from_contact_upsert>",
  "customField": {
    "quote_request_number": "9984",
    "order_type": "Kerb Set",
    "product_name": "STUNNING KERBSET WITH PATHWAY AND STEPS FINISHED WITH CARVED ROSES - ITALIAN WHITE MARBLE - 6FT2X2FT6 - 3FT HIGH",
    "product_sku": "AR001-01",
    "memorial_colour": "Standard - Black",
    "lettering_colour": "Gold",
    "photo_plaque": "Oval plaque",
    "occasion": "Memorial (bereavement)",
    "inscription": "In loving memory of\nAlison Natalie Wynter\nSunrise 24/5/1970\nSunset 19/8/2018\nLoving Daughter, Sister & Auntie\nForever in our hearts\nRest peacefully",
    "deceased_name": "Alison Natalie Wynter",
    "additional_notes": "Colour: Standard - Black; Lettering: Gold; Flower Container: Two Vases; Infill: White (+£250); Photo Plaque: Oval plaque (+£160)"
  }
}
```

**Result:** all nine target fields populate cleanly from a real email; the only
non-direct values are `order_type` (rule-derived), `occasion` (inferred), and the
cemetery (postcode needing normalisation) — exactly the gaps noted in §4.

> This mapping was validated against real request emails #9977, #9978, #9979, #9982
> and #9984. The `customField` key form above is illustrative; when wiring this into
> GHL via API/Make/n8n, custom fields are sent as an array of `{ id, field_value }`
> using each field's GHL id.

---

## 6. Preferred data source — WooCommerce REST API (not email HTML)

Sections 1–5 describe parsing the notification **email**. In practice the same
request also lands as a **WooCommerce order** reachable over the REST API
(`GET /wp-json/wc/v3/orders?search=<name|email>`), and that is the source you
should map from where possible: the fields are already structured, so there is
no HTML/regex parsing and no entity-decoding of the human-readable email.

Each Request-a-Quote order carries these `meta_data` keys at the order level:

| Order meta key | Contains |
|---|---|
| `_raq_customer_name` | Requester full name |
| `_raq_customer_email` | Requester email |
| `_raq_customer_message` | Free-text message (e.g. `"2 foot 6"`) |
| `_raq_status` | Lifecycle flag (`new`, …) — useful for dedupe / "unprocessed" filtering |
| `_raq_request` | **Structured object** with every form field (see below) |

`_raq_request` holds one entry per form field, each `{ id, type, label, value }`:

| `_raq_request` field | Maps to |
|---|---|
| `first_name.value` | Contact `firstName` |
| `last_name.value` | Contact `lastName` |
| `email.value` | Contact `email` (upsert key) |
| `Phone.value` | Contact `phone` |
| `Grave_Location.value` | `cemetery_grave_location` (still free text — same normalisation gap as §4 item 1) |
| `Grave_Number.value` | `grave_number` |
| `message.value` | folded into `additional_notes` |

The order's own fields cover the rest:

| Order / line-item field | Maps to |
|---|---|
| `id` / `number` | `quote_request_number` |
| `total` | `monetaryValue` / Order value (already numeric — no `£` to strip) |
| `date_created` | Opportunity created date |
| `line_items[].name` | `product_name` (→ `order_type` via the §2 keyword rules) |
| `line_items[].sku` | `product_sku` |
| `line_items[].meta_data[]` | product options — `Pick A Memorial Colour`, `Pick Lettering Colour`, `Flower Container`, `Photo Plaque`, `Garden Kerbset`, `Inscription`, `overall-height` |

**One decode step remains:** line-item option `value`s are stored with HTML
entities and a trailing price token, e.g.
`"Gold&nbsp;&nbsp;+&pound;0.00"` or the inscription
`"In loving memory of … &nbsp;&nbsp;+&pound;24.50"`. Before mapping, decode
entities (`&nbsp;`→space, `&pound;`→£) and strip the trailing `+ £<amount>`
overage token — the same inscription/overage handling as the §2 inscription
row, just applied to the structured value instead of an email line. Ignore the `_nbo_option_price`
and `_ga_*` internal keys.

`order_type`, `occasion`, and `deceased_name` are still **derived** exactly as in
§2 (keyword rule / inference / inscription parse) — the REST source removes the
*parsing* work, not the *inference* gaps of §4.

> Validated against live orders #8649–#8675 via the `CM: Lookup WooCommerce
> Orders` Make tool. Example: order #8675 → Susan GIBNEY, `sgibney85@gmail.com`,
> `07483245456`, Grave Location `Holy Trinity Church, Hatfield Heath CM22 7EA`,
> product `Dark Grey Ogee with Gilded rose design - 2ft 6"` (SKU `16019-01`,
> £1,399.50), inscription for Peter Chester — all read directly from
> `_raq_request` and `line_items`, no email parse required.
