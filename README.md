# WhatsApp Form Kit

A single-file tool for building, testing and exporting WhatsApp enquiry forms for client websites.

Small business sites rarely need a booking backend. A form that opens WhatsApp with the message pre-filled costs nothing to run, stores no personal data, and lands in an inbox the client already checks all day. This tool configures that form, tests the link against a real number, and exports the code.

**[Open the tool](https://msubham06.github.io/whatsapp-form-kit/)** — no build step, no dependencies, no install.

---

## Why this instead of a form backend

| | WhatsApp deep link | Form → database |
|---|---|---|
| Monthly cost | Nothing | Hosting + database |
| Personal data stored | None | Name, phone, message |
| Client training needed | None — they already use WhatsApp | Dashboard walkthrough |
| Reply time | Instant, in the same thread | Whenever someone checks |
| Maintenance calls to you | Rare | Guaranteed |

The message arrives **from the patient's own number**, so the business replies in the same chat instead of copying numbers out of a spreadsheet. That single detail is why this converts better than a form that vanishes into a sheet.

Trade-off worth knowing: there's no automatic record of enquiries. If the client wants one, tick **Also log to Google Sheets** and the exported code sends a silent copy alongside the WhatsApp message.

---

## What it does

- **Field builder** — add, remove, reorder and retype fields. Text, tel, email, date, textarea, dropdown.
- **Message template** — token-based (`{name}`, `{phone}`). Lines whose fields are empty drop out of the message automatically, so optional fields never leave `Reason: —` dangling.
- **Live diagnostics** — catches the mistakes that fail *silently*, especially number format.
- **Real send test** — fires an actual `wa.me` link so you can confirm delivery before shipping.
- **Theme preview** — set brand and accent colours to check the form against a client's palette.
- **Four exports** — React + Tailwind, plain HTML, a `clinic.js` config object, and JSON to save the whole setup.
- **Presets** — dental, general clinic, salon, minimal.

---

## Exports

**React + Tailwind** → `EnquiryForm.jsx`
Drop-in component. Includes required-field validation, 10-digit phone validation, honeypot, and the optional Sheets call.

**Plain HTML** → `whatsapp-form.html`
No framework. For static sites and landing pages.

**config.js** → `clinic.js`
Single source of truth for name, phone, WhatsApp number and colours. Import it everywhere rather than hardcoding a phone number into six components — it's also how you keep NAP details consistent, which matters for local search.

**JSON** → `wa-config.json`
Save a client's whole setup. Commit it to their project repo and reload it here later.

---

## Three things that will bite you

**Number format.** Country code + number, digits only — `919876543210`. A leading `+`, a leading `0`, or a bare 10-digit number fails *silently*: WhatsApp opens an empty chat with no error at all. The diagnostics panel exists specifically for this.

**Popup blocking.** `window.open` must stay inside the click handler. `await` anything before it and the browser treats it as an unsolicited popup and blocks it. That's why the Sheets request in the exported code is fired without awaiting — it's deliberate, not sloppy.

**Button label.** "Send on WhatsApp", never "Submit". Users who don't expect WhatsApp to open assume the site crashed and close the tab.

---

## Google Sheets logging (optional)

If the client wants a record, create the Sheet **on their own Google account** and keep it private. Their Google login is the access control — that's the point.

Extensions → Apps Script:

```js
function doPost(e) {
  try {
    const sheet = SpreadsheetApp.getActiveSpreadsheet()
      .getSheetByName('Enquiries');
    const data = JSON.parse(e.postData.contents);

    if (data.website) return json({ result: 'ok' });   // honeypot
    if (!data.name || !data.phone) {
      return json({ result: 'error', message: 'Missing fields' });
    }

    sheet.appendRow([
      new Date(),
      String(data.name).slice(0, 100),
      String(data.phone).slice(0, 20),
      String(data.reason || '').slice(0, 500)
    ]);

    MailApp.sendEmail({
      to: 'CLIENT_EMAIL_HERE',
      subject: 'New website enquiry',
      body: 'Name: ' + data.name + '\nPhone: ' + data.phone
    });

    return json({ result: 'success' });
  } catch (err) {
    return json({ result: 'error' });
  }
}

function json(obj) {
  return ContentService
    .createTextOutput(JSON.stringify(obj))
    .setMimeType(ContentService.MimeType.JSON);
}
```

Deploy → New deployment → Web app. Execute as **Me**, access **Anyone**. The URL only accepts writes — it can't read the sheet back.

Put it in `.env` as `VITE_APPOINTMENT_ENDPOINT`, and confirm `.env` is gitignored **before** your first commit.

> Don't set a `Content-Type` header on the fetch. It triggers a CORS preflight that Apps Script doesn't handle.

---

## Before shipping to a client

- [ ] Tested on a real Android phone and a real iPhone, not just DevTools
- [ ] Button says what it does
- [ ] Phone fallback shown under the form for people without WhatsApp
- [ ] Honeypot field still present in the markup
- [ ] Consent line included if the form collects a phone number
- [ ] WhatsApp number is the one reception actually monitors
- [ ] Sheet (if used) is private and owned by the client
- [ ] No credentials in the repo — check git history, not just current files

---

## Notes

Single file, no dependencies, no build step. Fonts load from Google Fonts; everything else is inline. Nothing is stored or transmitted anywhere — use the JSON tab to save configs.

Works offline once loaded, apart from the webfonts.

## Licence

MIT — use it on client work freely.
