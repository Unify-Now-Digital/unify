---
name: cm-permit-form-sender
description: "Churchill Memorials permit form sender. Use this skill when the user asks about sending permit forms to customers, preparing permit applications, finding permit forms, downloading permit forms, or getting deposit-paid orders ready for permit submission. Trigger phrases include \"send permit forms\", \"permit applications\", \"deposit paid orders\", \"prepare permits\", \"permit forms to send\", \"find permit form\", \"get permit form\", or any mention of Churchill Memorials combined with sending forms, permit applications, or deposit-paid customers."
---

# CM Permit Form Sender

Finds deposit-paid orders, locates the correct permit form (from library or online), uploads it to Google Drive with a clean file name, and sends it to the customer.

## Order Flow Context

After a customer pays their deposit, the next step is to send them the permit application form for their burial ground. Once they complete and return it, Churchill submits it to the council/church. The full order flow is:

**invoice sent → deposit paid → FORM SENT → customer completed → pending → approved → install → closed**

## Scope

- ClickUp tasks in CM: Orders (ID: 901207633256) with status = "deposit paid"
- Can also be triggered for a specific order/burial ground (e.g. "find the permit form for Scholemoor Cemetery")

## Data Sources

**ClickUp — CM: Orders** (key fields):

| Field | ID | Purpose |
|---|---|---|
| Burial Ground | 4904f9ce-9424-47ef-a610-782f5bae6b49 | Cemetery/church name |
| Council | 082e14b1-fbfc-445e-bc7e-baf346760080 | Responsible council |
| Address | 538727b3-8b56-49aa-a948-a56a0d239afa | Cemetery address (formatted_address) |
| Permit £ | 15c1fd79-de78-443e-b793-2d2299542dea | Permit fee |
| Permit Paid? | ca2d1c75-301b-4bd4-aa3c-9eae212ee180 | 0=Not Yet, 1=Paid or Not Required, 2=Not Required |
| Email (Customer) | 6b7de2eb-ac4f-4880-b8d6-59bad0998df4 | Customer email |
| N. of Deceased | 60a6b754-8e19-47c1-9ccd-45cf20468334 | Deceased name |

**ClickUp — Permit Forms Library** (List ID: 901207634771):
Contains previously collected permit application forms as task attachments, named by council/cemetery.

**Google Drive — Permit Forms Folder:**
Central storage for all permit forms with clean, consistent file names. Upload all new forms here.

## Workflow

### Step 1: Find deposit-paid orders

Search ClickUp for all tasks with status = "deposit paid" in CM: Orders list (901207633256). Retrieve task details including all key fields above.

### Step 2: Identify the managing authority

For each order:
- Read the Burial Ground, Council, and Address fields
- Determine who manages the burial ground:
  - **Municipal cemetery** → usually the local council
  - **Churchyard** → usually the Church of England diocese (requires a Faculty application, not a council permit)
  - **Private/trust cemetery** → managed by a trust or private company (e.g. Liverpool Catholic Cemeteries, City of London Cemetery)
- If unclear, use web search: `"[burial ground name]" memorial application` or `"[burial ground name]" permit form`

### Step 3: Find the permit form

Try these sources **in order** until a form is found:

**a) ClickUp Permit Forms Library (check first):**
- Search ClickUp Permit Forms list (901207634771) using keywords from: council name, burial ground name, borough/district name
- Try multiple search terms if the first doesn't match (e.g. just "Hillingdon" not "London Borough of Hillingdon Council")
- If found → use this form, skip to Step 4

**b) Google Drive Permit Forms Folder (check second):**
- Search Google Drive for the council or burial ground name
- If found → use this form, skip to Step 5

**c) Web Search (if not in library or Drive):**
- Search for the form online using these queries (try in order):
  1. `"[council name]" memorial application form filetype:pdf`
  2. `"[burial ground name]" memorial permit application`
  3. `"[council name]" memorial regulations permit`
  4. `[council name] bereavement services memorial application`
  5. For churchyards: `"[diocese name]" faculty application memorial`
- Look for official council/church websites with downloadable PDF or DOCX forms
- Use web_fetch to access the page and locate the direct download link
- If the form is behind a web page (not a direct PDF link), provide the URL and ask the user to download manually

**d) Contact the authority directly:**
- If no form can be found online, propose contacting the council/church:
  - Draft an email or provide a phone number
  - Ask them to send the memorial application form and confirm the permit fee
  - Note this in the output as "Form not available — contact required"

### Step 4: Upload to Google Drive

Upload the form to Google Drive with a **clean, consistent file name** following this format:

```
[Council or Authority Name] - Memorial Permit Application.[ext]
```

**Naming rules:**
- Use the official council/authority name (not the cemetery name, unless it's a private cemetery with its own form)
- Title case
- Remove years, version numbers, and unnecessary words
- Keep the original file extension (.pdf, .docx, etc.)

**Examples of clean names:**
| Original file name | Clean name |
|---|---|
| `birmingham-memorial-app-2024-v3.pdf` | `Birmingham City Council - Memorial Permit Application.pdf` |
| `LCC_Yewtree_form.docx` | `Liverpool Catholic Cemeteries - Memorial Permit Application.docx` |
| `diocese-of-chester-faculty-form-NEW.pdf` | `Diocese of Chester - Faculty Application for Memorial.pdf` |
| `SBC Memorial Permit Form & Regulations 2024.docx` | `Stevenage Borough Council - Memorial Permit Application.docx` |
| `hammersmith_fulham_memorial_app.pdf` | `Hammersmith and Fulham Council - Memorial Permit Application.pdf` |

For churchyard/diocese forms, use "Faculty Application for Memorial" instead of "Memorial Permit Application".

### Step 5: Flag blockers before proceeding


### Step 6: Check if already sent

Before sending to the customer:
- Search Gmail for `"[burial ground]" permit` or `"[council name]" permit` combined with the customer name or email
- If a permit form has already been sent for this order, skip it and note "Already sent on [date]"

### Step 7: Send to customer

Draft the email as a **reply in the customer's existing Gmail thread** — do not create a new email. Search Gmail for the customer's name or email to find the right thread, then reply to it.

Use the template below and include the Google Drive share link directly in the body. Always get user confirmation before sending.

## Email Template

**To:** [customer email]
**Subject:** Memorial Permit Form — [Customer Name]

Hi [Customer First Name],

I hope you are keeping well.

Please find below the link to your memorial permit application form for [Cemetery Name]:
[Google Drive link]

When completing the form, please fill in the applicant section with your details — this will include your full name, address, grave number, and signature. 

The grave owner section must be completed by the registered grave owner of the plot. 

Once completed, please return the form to us and we will submit it to the cemetery on your behalf, completing the memorial details.

If you have any questions, please don't hesitate to get in touch.

Many thanks,
Arin
Churchill Memorials

## Output Format

```
Permit Forms — Ready to Send
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

1. [Customer Name] — deposit paid [date]
   Burial Ground: [name]
   Managing Authority: [council/church name]
   Permit Fee: £[amount]

   Form Source: [Library / Drive / Found Online / Not Found]
   Drive Link: [shareable Google Drive link]
   Already Sent: Yes (sent [date]) / Not yet
   Action: [Send to customer / Already handled / Form not found — contact needed / Blocked — see note]

---
```

**Summary line at the end:**
```
Ready to send: [X] orders
New forms found & uploaded: [X]
Forms not found — need manual action: [X]
Already sent — skipped: [X]
Blocked (missing data): [X]
```

## Important Notes
- Always be certain the URL of the correct form is in the email body
- Always verify the 'to' address is the customer in question
- Always verify the managing authority. Some cemeteries are managed by churches/dioceses rather than councils. Churchyards = usually Church of England diocese. Municipal cemeteries = usually local council.
- The Permit Forms library names are not always exact matches — try partial searches (e.g. just "Hillingdon" not "London Borough of Hillingdon Council")
- When uploading to Drive, check if a form for this authority already exists to avoid duplicates.
- Share the form via a Google Drive link in the email body — do not attach files to the email.
- Always reply in the customer's existing Gmail thread. Search by customer name/email to find it first.
- Permit work is sensitive — customers are dealing with bereavement. Keep all communications warm and respectful.
- Always confirm with the user before sending any emails.
- If Google Drive tools are not connected, prompt the user to connect Google Drive in their Claude settings, or offer to save the form locally instead.
- Use OCR vision tool to validate the document or to search if unsure.
