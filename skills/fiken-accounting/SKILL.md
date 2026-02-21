---
name: fiken
description: |
  Fiken accounting API for Norwegian businesses. Use when:
  - Creating or sending invoices (fakturaer), credit notes (kreditnotaer), or offers (tilbud)
  - Looking up contacts or customers in Fiken
  - Checking invoice/payment status or account balances
  - Managing products/services in the product catalog
  - Any accounting or billing task via Fiken
  - User mentions "faktura", "fiken", "EHF", "kredittnota", "tilbud", or "regnskap"
license: MIT
---

# Fiken Accounting Skill

Norwegian accounting API integration for Fiken (fiken.no). Covers invoices, credit notes, offers, contacts, products, payments, and account balances.

## Setup

### Detection Order

When this skill is activated, detect the Fiken configuration in this order:

1. **Centralized** — Check if `~/.config/fiken-api/credentials.json` exists
2. **Project-local** — Check if `FIKEN_API_TOKEN` is set in the environment (via `.env`)
3. **No configuration** — Run the First-Time Setup guide below

### Using Existing Configuration

#### Centralized (`~/.config/fiken-api/`)

If `~/.config/fiken-api/credentials.json` exists:

```js
import { getFikenClient, listAccounts } from '~/.config/fiken-api/client.mjs'

// Default account
const fiken = getFikenClient()
const contacts = await fiken.getContacts()

// Specific account
const other = getFikenClient('account-name')

// List available accounts
const accounts = listAccounts()
```

The centralized client handles multi-account switching, auto-includes bank account numbers, and provides account-specific config (vatType, incomeAccount, etc.) via `fiken.config`.

#### Project-local (`.env`)

If `FIKEN_API_TOKEN` is in the environment:

```js
require('dotenv').config();
const fiken = require('./skills/fiken/scripts/client');
```

The project-local client reads from environment variables:
```
FIKEN_API_TOKEN=...          # From Fiken > Innstillinger > API
FIKEN_COMPANY_SLUG=...       # Your company slug (from dashboard URL)
```

### First-Time Setup

If no configuration is detected, guide the user through setup:

**Step 1: Get an API token**
1. Log into [fiken.no](https://fiken.no)
2. Go to **Innstillinger > API**
3. Create a new API token (costs 99 NOK/month per company)
4. Token format: `{numeric-id}.{alphanumeric-key}`

**Step 2: Find your company slug**
- Look at your Fiken dashboard URL: `https://fiken.no/foretak/{slug}/...`
- Or call `GET https://api.fiken.no/api/v2/companies` with the token to list all accessible companies

**Step 3: Choose setup method**

Use AskUserQuestion to ask the user:
- **Question:** "How do you want to configure Fiken access?"
- **Option 1: Project-local (.env)** — "Single project, single company. Simplest setup. Credentials in your project's .env file."
- **Option 2: Centralized (~/.config/fiken-api/)** — "Multiple projects or companies. Credentials stored once in ~/.config/, used everywhere."

**If project-local (.env):**
1. Create `.env` with `FIKEN_API_TOKEN` and `FIKEN_COMPANY_SLUG`
2. Import from `scripts/client.js` (bundled with this skill)
3. Verify with `fiken.getContacts()` or `fiken.isFikenConfigured()`

**If centralized (~/.config/fiken-api/):**
1. Create `~/.config/fiken-api/credentials.json` with account(s)
2. Copy the centralized client to `~/.config/fiken-api/client.mjs`
3. Import via `getFikenClient(accountName)`
4. Verify with `fiken.getContacts()`

**Step 4: Initialize number series**
- The very first invoice, credit note, and offer of each type MUST be created manually in the Fiken web UI
- This establishes the number series — after that, the API works
- Direct link: `https://fiken.no/foretak/{slug}/fakturautkast/ny`

**Step 5: Verify**
- Call `getContacts()` to confirm the token works
- If `403 Forbidden`: API module not activated (Fiken > Foretak > Tilleggstjenester)
- If `401 Unauthorized`: Bad token (regenerate at Fiken > Innstillinger > API)

## Available Functions

| Function | Description |
|----------|-------------|
| `isFikenConfigured()` | Check if env vars are set |
| `getContacts()` | List all contacts |
| `findContactByOrgNumber(orgNr)` | Find contact by Norwegian org number |
| `createContact(input)` | Create customer/supplier — returns contactId |
| `getInvoices(page, pageSize)` | List invoices (paginated) |
| `getInvoice(id)` | Get single invoice |
| `createInvoiceDraft(input)` | Create draft — returns `{ draftId, uuid, draftUrl }` |
| `sendInvoiceDraft(draftId, sendInput)` | Two-step: finalize + dispatch — returns `{ invoiceId, saleId, invoiceUrl }` |
| `isInvoiceSettled(id)` | Check if invoice is paid |
| `getProducts()` | List products/services |
| `getAccounts()` | Chart of accounts (~475 accounts) |
| `getBankAccounts()` | Bank accounts |
| `nokToOre(nok)` / `oreToNok(ore)` | Currency conversion (1 NOK = 100 ore) |
| `delay(ms)` | Rate limit helper (default 250ms) |

## Complete Invoice Example

```js
require('dotenv').config();
const fiken = require('./skills/fiken/scripts/client');

// 1. Find or create contact
let contact = await fiken.findContactByOrgNumber('999888777');
if (!contact) {
  const contactId = await fiken.createContact({
    name: 'Example Company AS',
    email: 'post@example.no',
    organizationNumber: '999888777',
    address: {
      streetAddress: 'Storgata 1',
      city: 'Oslo',
      postCode: '0001',
      country: 'NO'
    },
    customer: true,
    supplier: false
  });
  contact = { contactId };
}

// 2. Create invoice draft
const draft = await fiken.createInvoiceDraft({
  type: 'invoice',                    // REQUIRED — see gotcha #5
  issueDate: '2026-03-01',
  daysUntilDueDate: 14,
  customerId: contact.contactId,
  ourReference: 'Your Name',
  yourReference: 'Their Contact',
  orderReference: 'PO-2026-001',      // Optional — for EHF routing
  invoiceText: 'Consulting services — March 2026',
  currency: 'NOK',
  // bankAccountNumber is auto-included by the client
  lines: [{
    description: 'Consulting — project planning, 8 hours',
    quantity: 8,
    unitPrice: fiken.nokToOre(1500),  // NOK 1,500/hr = 150000 ore
    vatType: 'HIGH',                  // 25% MVA (use 'OUTSIDE' if not MVA-registered)
    incomeAccount: '3020'             // Services, high MVA (use '3220' if not MVA-registered)
  }]
});
console.log('Draft URL:', draft.draftUrl);

// 3. Finalize + send as EHF (two-step process handled by the client)
await fiken.delay(500);
const invoice = await fiken.sendInvoiceDraft(draft.draftId, {
  method: ['ehf'],
  includeDocumentAttachments: true
});
console.log('Invoice ID:', invoice.invoiceId);   // Same as draftId
console.log('Sale ID:', invoice.saleId);          // Different — used for dashboard URLs
console.log('Invoice URL:', invoice.invoiceUrl);  // Uses saleId
```

## VAT Quick Reference

**Are you MVA-registered?** This determines which vatType and income account to use.

| Scenario | vatType | Income Account | Account Name |
|----------|---------|---------------|--------------|
| MVA-registered, standard rate | `HIGH` | `3020` | Tjenester, hoy mva-sats (25%) |
| MVA-registered, food/drink | `MEDIUM` | Account varies | 15% rate |
| MVA-registered, zero-rated | `EXEMPT` | `3120` | Tjenester, fritatt for mva |
| **Not MVA-registered** | **`OUTSIDE`** | **`3220`** | **Tjenester, unntatt for mva** |

**Critical distinction:**
- "Fritatt" (`3120`, `EXEMPT`) = zero-rated, company IS in the MVA system but rate is 0%
- "Unntatt" (`3220`, `OUTSIDE`) = exempt, company is NOT in the MVA system at all

> Full VAT reference with all types and account compatibility: see `references/vat-guide.md`

## Top 7 Critical Gotchas

### 1. VAT + Account must match
`vatType` and `incomeAccount` are linked. Using incompatible pairs returns 400. See the table above for correct combinations.

### 2. bankAccountNumber is REQUIRED on drafts
This is YOUR bank account number (the invoice sender's) — the "pay to" account printed on the invoice. It is NOT the customer's account.

The client auto-fetches it from `GET /companies/{slug}/bankAccounts` (picks first active account, caches in memory). If calling the API directly, you must include `bankAccountNumber` (Norwegian format, not IBAN). Without it: `500: "Kontonummer mangler på faktura"`. The API returns `[{ bankAccountNumber, inactive }]` — pick first where `inactive === false`.

### 3. Number series — first of each type must be manual
The very first invoice/credit note/offer MUST be created in the Fiken web UI to establish the number series. After that, the API works. Error: `400: "Missing number series for drafts of type: invoice."`.

### 4. Money is in ore (1 NOK = 100 ore)
All monetary amounts use ore. NOK 4,998 → `499800`. Use `fiken.nokToOre()` and `fiken.oreToNok()`.

### 5. `type` field is REQUIRED on drafts
Values: `invoice`, `cash_invoice`, `offer`, `order_confirmation`, `credit_note`. Omitting returns: `400: "'type' er påkrevd"`.

### 6. PUT replaces all fields (not a PATCH)
Contact and product updates via PUT replace the entire resource. Always include all fields. The only PATCH is on invoices: `newDueDate` and `sentManually` only.

### 7. Invoice lines are immutable after finalization
Once sent, you can only change `newDueDate` and `sentManually`. To fix line items, issue a credit note and create a new invoice.

### 8. Invoice sending is a TWO-STEP process (CRITICAL)
**Step 1:** `POST /invoices/drafts/{draftId}/createInvoice` — NO body, just finalizes.
**Step 2:** `POST /invoices/send` — dispatches (EHF/email/auto). Without Step 2, the invoice exists but was never sent.
If you pass send params to Step 1, they are **silently ignored**.

### 9. Three different IDs per invoice
`invoiceId` (API operations) vs `sale.saleId` (dashboard URLs) vs `sale.transactionId` (accounting).
`draftId` = `invoiceId` after finalization. The Location header from `createInvoice` returns `saleId`, NOT `invoiceId`.

### 10. Dashboard URLs use saleId, not invoiceId
Finalized invoice URL: `/foretak/{slug}/handel/salg/{saleId}` — NOT `/faktura/{invoiceId}`.

### 11. Use recipientEmail, not emailAddress
The send request field is `recipientEmail`. Using `emailAddress` is silently ignored.

> All gotchas with error messages and solutions: see `references/gotchas.md`

## Sending Methods

| Method | When to use |
|--------|------------|
| `['ehf']` | B2B to Norwegian companies (preferred) |
| `['email']` | Direct email delivery (requires `recipientEmail`) |
| `['efaktura']` | Norwegian consumer e-invoice via bank |
| `['sms']` | SMS delivery (requires `mobileNumber`) |
| `['letter']` | Physical letter (extra charge, Norway only) |
| `['auto']` | Fiken picks: EHF > eFaktura > SMS > Email |

Multiple methods = priority list: `['sms', 'email']` tries SMS first, falls back to email.

**Full sendInvoiceRequest:**
```js
{
  invoiceId: 12345,                    // Required
  method: ['ehf'],                     // Required — array of strings
  includeDocumentAttachments: true,    // Required
  recipientName: 'Name',              // Optional
  recipientEmail: 'email@x.no',       // Optional (for email method)
  emailSendOption: 'auto',            // 'document_link' | 'attachment' | 'auto'
  mergeInvoiceAndAttachments: false,   // Merge into single PDF
  organizationNumber: '999888777',     // Optional (for EHF)
  mobileNumber: '12345678',           // Optional (for SMS)
  message: 'Additional message text'  // Optional — included in email/notification
}
```

## Dashboard URL Formats

```
Draft:       https://fiken.no/foretak/{slug}/fakturautkast/{uuid}
Invoice:     https://fiken.no/foretak/{slug}/handel/salg/{saleId}
Contact:     https://fiken.no/foretak/{slug}/kontakt/{contactId}
Credit Note: https://fiken.no/foretak/{slug}/kreditnota/{creditNoteId}
```

- Domain: `fiken.no` (NOT `app.fiken.no`)
- Draft path: `fakturautkast` (ONE word)
- Drafts use **UUID**, not numeric ID (must GET the draft after creation)
- **Finalized invoices use `saleId`** (from `invoice.sale.saleId`), NOT `invoiceId`
- The API does NOT return dashboard links — construct them from `sale.saleId`

## Pagination

| Header | Description |
|--------|-------------|
| `Fiken-Api-Page` | Current page (0-indexed) |
| `Fiken-Api-Page-Size` | Items per page |
| `Fiken-Api-Page-Count` | Total pages |
| `Fiken-Api-Result-Count` | Total results |

Default: `page=0`, `pageSize=25`. Maximum: `pageSize=100`.

## API Limitations

1. **No webhooks** — must poll for changes
2. **No recurring invoices** — implement scheduling yourself
3. **No invoice reminders/dunning** via API
4. **No Vipps payment integration** via API
5. **Cannot modify invoice lines** after finalization
6. **No direct payment on invoices** — payments go through the underlying Sale object

## Safe Testing Pattern

Drafts and products can be safely created and immediately deleted (204). This enables testing without permanent changes:

```js
const draft = await fiken.createInvoiceDraft({ /* ... */ });
// inspect draft...
// DELETE /companies/{slug}/invoices/drafts/{draftId} → 204
```

## API Reference

- **Base URL:** `https://api.fiken.no/api/v2`
- **Auth:** `Authorization: Bearer {FIKEN_API_TOKEN}`
- **Rate limit:** 4 req/sec (250ms between calls)
- **Content type:** `application/json` only
- **Dates:** `yyyy-MM-dd` format
- **Attachments:** `.png`, `.jpeg`, `.jpg`, `.gif`, `.pdf` only
- **Total endpoints:** ~110
- **API cost:** 99 NOK/month per company
- **Docs:** https://api.fiken.no/api/v2/docs/

## Reference Files

| File | Contents |
|------|----------|
| `references/api-endpoints.md` | All ~110 endpoints organized by category |
| `references/workflows.md` | Multi-line invoices, credit notes, offers, products, contacts, payments |
| `references/gotchas.md` | All 14 gotchas with error messages and solutions |
| `references/vat-guide.md` | Complete VAT types, account matrix, MVA explanation |
