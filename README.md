# WhatsApp Link Lab

Test a WhatsApp number, build an enquiry form around it, export the code.

**[Open the tool](https://msubham06.github.io/wa-link-lab/)** — single file, no build step.

## Use

1. Enter the WhatsApp number (country code + number, digits only — `919876543210`)
2. Scan the QR with your phone to confirm it opens correctly on a real device
3. Pick a preset, edit the fields and message
4. Export as React, HTML, or JSON

## Features

- Field builder — text, tel, email, date, textarea, dropdown
- Token message template (`{name}`, `{phone}`) — empty optional fields drop out automatically
- Live diagnostics on the number format
- QR code for phone testing
- Presets: contact, appointment, booking, quote
- Exports: `EnquiryForm.jsx`, `whatsapp-form.html`, `wa-config.json`

## Gotchas

**Number format** — a leading `+`, a leading `0`, or a bare 10-digit number fails *silently*. WhatsApp just opens an empty chat with no error. The readout catches this.

**Popup blocking** — keep `window.open` inside the click handler. `await` anything first and the browser blocks it.

**Test on a real phone** — desktop opens WhatsApp Web, which is a different code path.

## Notes

Nothing is stored. Use the JSON tab to save a setup.

MIT
