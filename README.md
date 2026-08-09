<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>WhatsApp Form Kit — build, test and export wa.me enquiry forms</title>
<meta name="description" content="Configure a WhatsApp enquiry form, test the link with a real number, and export ready-to-paste React or HTML code.">
<link rel="preconnect" href="https://fonts.googleapis.com">
<link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
<link href="https://fonts.googleapis.com/css2?family=Space+Grotesk:wght@400;500;700&family=IBM+Plex+Mono:wght@400;500;600&display=swap" rel="stylesheet">
<style>
  :root {
    --paper:    #F2F0EC;
    --card:     #FFFFFF;
    --ink:      #14161A;
    --ink-soft: #5C6068;
    --rule:     #D9D5CD;
    --rule-soft:#E8E5DE;
    --signal:   #1B3A6B;
    --wa:       #1FA855;
    --alert:    #B4432B;
    --warn:     #A87400;
    --tint:     #FAF8F4;

    /* Overridden live by the theme picker — preview only */
    --brand:    #00396B;
    --brand-2:  #D4AF5B;
  }

  * { box-sizing: border-box; }
  html, body { margin: 0; padding: 0; }

  body {
    background: var(--paper);
    color: var(--ink);
    font-family: 'Space Grotesk', system-ui, sans-serif;
    line-height: 1.5;
    padding: 26px 18px 80px;
  }

  .shell { max-width: 1180px; margin: 0 auto; }

  /* ---------- masthead ---------- */
  .masthead { border-bottom: 2px solid var(--ink); padding-bottom: 14px; margin-bottom: 24px; }
  .eyebrow {
    font-family: 'IBM Plex Mono', monospace; font-size: 11px;
    letter-spacing: .14em; text-transform: uppercase;
    color: var(--ink-soft); margin: 0 0 6px;
  }
  .masthead h1 { font-size: clamp(25px, 5vw, 38px); font-weight: 700; letter-spacing: -.025em; margin: 0; }
  .masthead p { margin: 8px 0 0; max-width: 62ch; color: var(--ink-soft); font-size: 15px; }

  /* ---------- layout ---------- */
  .cols { display: grid; grid-template-columns: 1fr; gap: 18px; }
  @media (min-width: 980px) { .cols { grid-template-columns: 1.05fr 1fr; align-items: start; } }

  .panel { background: var(--card); border: 1px solid var(--rule); border-radius: 3px; padding: 20px; }
  .panel + .panel { margin-top: 18px; }

  .panel-label {
    font-family: 'IBM Plex Mono', monospace; font-size: 11px;
    letter-spacing: .12em; text-transform: uppercase; color: var(--ink-soft);
    padding-bottom: 10px; margin-bottom: 16px; border-bottom: 1px solid var(--rule);
    display: flex; justify-content: space-between; align-items: center; gap: 10px;
  }
  .panel-label b { color: var(--signal); font-weight: 600; }

  .sub {
    font-family: 'IBM Plex Mono', monospace; font-size: 10.5px;
    letter-spacing: .1em; text-transform: uppercase; color: var(--ink-soft);
    margin: 20px 0 9px; padding-bottom: 5px; border-bottom: 1px dotted var(--rule);
  }
  .sub:first-of-type { margin-top: 0; }

  /* ---------- fields ---------- */
  .f { margin-bottom: 13px; }
  .f > label { display: block; font-size: 13px; font-weight: 500; margin-bottom: 5px; }
  .hint { font-family: 'IBM Plex Mono', monospace; font-size: 11px; color: var(--ink-soft); margin: 4px 0 0; }

  input[type=text], input[type=tel], input[type=url], textarea, select {
    width: 100%; font-family: 'IBM Plex Mono', monospace; font-size: 13.5px;
    color: var(--ink); background: var(--paper);
    border: 1px solid var(--rule); border-radius: 2px; padding: 9px 10px;
  }
  textarea { resize: vertical; line-height: 1.55; }
  input:focus, textarea:focus, select:focus { outline: 2px solid var(--signal); outline-offset: 1px; background: #fff; }

  .row { display: flex; gap: 8px; }
  .row > * { flex: 1; min-width: 0; }

  input[type=color] {
    -webkit-appearance: none; appearance: none;
    width: 100%; height: 34px; padding: 0;
    border: 1px solid var(--rule); border-radius: 2px; background: none; cursor: pointer;
  }
  input[type=color]::-webkit-color-swatch-wrapper { padding: 3px; }
  input[type=color]::-webkit-color-swatch { border: none; border-radius: 1px; }

  .check { display: flex; align-items: center; gap: 8px; font-size: 13px; margin-bottom: 9px; cursor: pointer; }
  .check input { width: 15px; height: 15px; accent-color: var(--signal); cursor: pointer; }

  /* ---------- field builder ---------- */
  .fieldrow {
    border: 1px solid var(--rule); border-left: 3px solid var(--signal);
    border-radius: 2px; padding: 10px; margin-bottom: 8px; background: var(--tint);
  }
  .fieldrow .top { display: flex; gap: 8px; align-items: center; margin-bottom: 7px; }
  .fieldrow .tok {
    font-family: 'IBM Plex Mono', monospace; font-size: 11px;
    color: var(--signal); background: #fff; border: 1px solid var(--rule);
    padding: 3px 6px; border-radius: 2px; white-space: nowrap;
  }
  .fieldrow .grow { flex: 1; min-width: 0; }
  .mini {
    font-family: 'IBM Plex Mono', monospace; font-size: 11px;
    background: #fff; border: 1px solid var(--rule); border-radius: 2px;
    padding: 5px 7px; cursor: pointer; color: var(--ink);
  }
  .mini:hover { border-color: var(--ink); }
  .mini:disabled { opacity: .32; cursor: not-allowed; }
  .mini.danger:hover { border-color: var(--alert); color: var(--alert); }
  .mini:focus-visible { outline: 2px solid var(--signal); outline-offset: 1px; }

  .tokbar { display: flex; flex-wrap: wrap; gap: 5px; margin-top: 7px; }

  /* ---------- diagnostics ---------- */
  .chk {
    display: flex; align-items: flex-start; gap: 9px;
    font-family: 'IBM Plex Mono', monospace; font-size: 12px;
    padding: 6px 0; border-bottom: 1px dotted var(--rule);
  }
  .chk:last-child { border-bottom: 0; }
  .dot { width: 8px; height: 8px; border-radius: 50%; margin-top: 5px; flex-shrink: 0; background: var(--rule); }
  .dot.ok { background: var(--wa); } .dot.bad { background: var(--alert); } .dot.warn { background: var(--warn); }
  .chk.bad span:last-child { color: var(--alert); }
  .chk.warn span:last-child { color: var(--warn); }

  /* ---------- readout ---------- */
  .readout {
    font-family: 'IBM Plex Mono', monospace; font-size: 12px; line-height: 1.65;
    background: var(--ink); color: #E8E6E1; border-radius: 2px; padding: 12px;
    word-break: break-all; white-space: pre-wrap; margin-bottom: 12px; min-height: 46px;
  }

  /* ---------- live preview form ---------- */
  .preview {
    border: 1px solid var(--rule); border-radius: 3px; padding: 18px;
    background: linear-gradient(0deg, var(--tint), #fff);
  }
  .preview h3 {
    margin: 0 0 4px; font-size: 18px; font-weight: 700;
    letter-spacing: -.02em; color: var(--brand);
  }
  .preview .tag { margin: 0 0 14px; font-size: 13px; color: var(--ink-soft); }
  .preview label { display: block; font-size: 12.5px; font-weight: 500; margin-bottom: 4px; }
  .preview .pf { margin-bottom: 11px; }
  .preview input, .preview textarea, .preview select {
    font-family: 'Space Grotesk', sans-serif; font-size: 14px; background: #fff;
  }
  .req { color: var(--alert); }

  .send {
    width: 100%; display: flex; align-items: center; justify-content: center; gap: 9px;
    font-family: 'Space Grotesk', sans-serif; font-size: 15px; font-weight: 500;
    color: #fff; background: var(--wa); border: 0; border-radius: 3px;
    padding: 13px; cursor: pointer; margin-top: 4px;
  }
  .send:hover:not(:disabled) { filter: brightness(.92); }
  .send:disabled { background: #B8BCC0; cursor: not-allowed; }
  .send:focus-visible { outline: 2px solid var(--ink); outline-offset: 2px; }
  .send.brand { background: var(--brand); }

  .consent { font-size: 11.5px; color: var(--ink-soft); margin: 9px 0 0; line-height: 1.45; }
  .fallback { text-align: center; font-size: 13px; color: var(--ink-soft); margin: 11px 0 0; }
  .fallback a { color: var(--brand); font-weight: 500; }

  /* ---------- tabs ---------- */
  .tabs { display: flex; gap: 4px; flex-wrap: wrap; margin-bottom: 14px; }
  .tab {
    font-family: 'IBM Plex Mono', monospace; font-size: 11px;
    letter-spacing: .08em; text-transform: uppercase;
    background: transparent; border: 1px solid var(--rule); border-radius: 2px;
    padding: 7px 11px; cursor: pointer; color: var(--ink-soft);
  }
  .tab[aria-selected="true"] { background: var(--ink); border-color: var(--ink); color: #fff; }
  .tab:focus-visible { outline: 2px solid var(--signal); outline-offset: 1px; }

  pre.code {
    font-family: 'IBM Plex Mono', monospace; font-size: 11.5px; line-height: 1.6;
    background: var(--ink); color: #E8E6E1; border-radius: 2px;
    padding: 14px; overflow-x: auto; margin: 0 0 10px; max-height: 460px;
  }
  .ghost {
    font-family: 'IBM Plex Mono', monospace; font-size: 11.5px;
    background: transparent; border: 1px solid var(--rule); border-radius: 3px;
    padding: 9px 14px; cursor: pointer; color: var(--ink);
  }
  .ghost:hover { border-color: var(--ink); }
  .ghost:focus-visible { outline: 2px solid var(--signal); outline-offset: 1px; }
  .btnbar { display: flex; gap: 7px; flex-wrap: wrap; }

  /* ---------- notes ---------- */
  .notes { margin-top: 30px; border-top: 2px solid var(--ink); padding-top: 18px; }
  .notes h2 {
    font-family: 'IBM Plex Mono', monospace; font-size: 11px; letter-spacing: .14em;
    text-transform: uppercase; color: var(--ink-soft); font-weight: 500; margin: 0 0 14px;
  }
  .notes ul { margin: 0; padding-left: 18px; }
  .notes li { font-size: 14px; margin-bottom: 9px; max-width: 76ch; }
  .notes code {
    font-family: 'IBM Plex Mono', monospace; font-size: 12.5px;
    background: #E4E1DA; padding: 1px 5px; border-radius: 2px;
  }

  .foot {
    margin-top: 26px; padding-top: 14px; border-top: 1px solid var(--rule);
    font-family: 'IBM Plex Mono', monospace; font-size: 11px; color: var(--ink-soft);
    display: flex; justify-content: space-between; flex-wrap: wrap; gap: 8px;
  }

  @media (prefers-reduced-motion: reduce) { * { transition: none !important; animation: none !important; } }
</style>
</head>
<body>
<div class="shell">

  <header class="masthead">
    <p class="eyebrow">whatsapp-form-kit &nbsp;·&nbsp; v2</p>
    <h1>WhatsApp Form Kit</h1>
    <p>Configure an enquiry form, test the link against a real number, then export the code. Built for client sites where a full booking backend is overkill — the message arrives from the patient's own number, so the business just replies in the same chat.</p>
  </header>

  <div class="cols">

    <!-- ============ LEFT: configuration ============ -->
    <div>
      <section class="panel">
        <div class="panel-label"><span>Configuration</span><b id="presetName">custom</b></div>

        <p class="sub">Presets</p>
        <div class="btnbar" style="margin-bottom:4px">
          <button class="ghost" data-preset="dental">Dental clinic</button>
          <button class="ghost" data-preset="general">General clinic</button>
          <button class="ghost" data-preset="salon">Salon / spa</button>
          <button class="ghost" data-preset="minimal">Minimal</button>
        </div>

        <p class="sub">Business</p>
        <div class="f">
          <label for="cfgName">Business name</label>
          <input id="cfgName" type="text" placeholder="Dr. Arul's Tooth Care">
        </div>
        <div class="f">
          <label for="cfgTag">Form subtitle</label>
          <input id="cfgTag" type="text" placeholder="Book an appointment — we'll confirm on WhatsApp">
        </div>
        <div class="f">
          <label for="cfgNumber">WhatsApp number</label>
          <input id="cfgNumber" type="tel" placeholder="919876543210" autocomplete="off">
          <p class="hint">Country code + number. No +, spaces or dashes.</p>
        </div>
        <div class="f">
          <label for="cfgFallback">Fallback phone (shown under the button)</label>
          <input id="cfgFallback" type="tel" placeholder="+91 73065 55511">
        </div>

        <p class="sub">Form fields</p>
        <div id="fieldList"></div>
        <div class="btnbar">
          <button class="ghost" id="addField">+ Add field</button>
          <span class="hint" style="align-self:center" id="fieldCount"></span>
        </div>

        <p class="sub">Message template</p>
        <div class="f">
          <textarea id="cfgTemplate" rows="7"></textarea>
          <p class="hint">Use the tokens below. Empty optional fields are dropped from the message automatically.</p>
          <div class="tokbar" id="tokenBar"></div>
        </div>

        <p class="sub">Button &amp; consent</p>
        <div class="f">
          <label for="cfgButton">Button label</label>
          <input id="cfgButton" type="text" placeholder="Send on WhatsApp">
          <p class="hint">Say what happens. "Submit" makes people think it broke.</p>
        </div>
        <label class="check"><input type="checkbox" id="cfgConsent"> Show consent line under the button</label>
        <div class="f" id="consentWrap" hidden>
          <textarea id="cfgConsentText" rows="2"></textarea>
        </div>
        <label class="check"><input type="checkbox" id="cfgBrandBtn"> Use brand colour for the button instead of WhatsApp green</label>

        <p class="sub">Theme (preview only)</p>
        <div class="row">
          <div class="f">
            <label for="cfgBrand">Brand colour</label>
            <input id="cfgBrand" type="color" value="#00396B">
          </div>
          <div class="f">
            <label for="cfgAccent">Accent colour</label>
            <input id="cfgAccent" type="color" value="#D4AF5B">
          </div>
        </div>

        <p class="sub">Also log to Google Sheets (optional)</p>
        <label class="check"><input type="checkbox" id="cfgSheet"> Send a silent copy to an Apps Script endpoint</label>
        <div class="f" id="sheetWrap" hidden>
          <input id="cfgSheetUrl" type="url" placeholder="https://script.google.com/macros/s/.../exec">
          <p class="hint">Included in the exported code. Fires without awaiting, so the popup isn't blocked.</p>
        </div>
      </section>
    </div>

    <!-- ============ RIGHT: preview + readout ============ -->
    <div>
      <section class="panel">
        <div class="panel-label"><span>Live preview</span><b id="status">idle</b></div>
        <div class="preview" id="preview"></div>
      </section>

      <section class="panel">
        <div class="panel-label"><span>Wire readout</span><b id="charCount">0 chars</b></div>
        <div id="checks" style="margin-bottom:16px"></div>

        <p class="hint" style="margin-bottom:6px">Normalised number</p>
        <div class="readout" id="outNumber">—</div>

        <p class="hint" style="margin-bottom:6px">Message body</p>
        <div class="readout" id="outMessage">—</div>

        <p class="hint" style="margin-bottom:6px">Generated link</p>
        <div class="readout" id="outUrl">—</div>

        <div class="btnbar">
          <button class="ghost" id="copyLink">Copy link</button>
          <button class="ghost" id="openLink">Open in WhatsApp</button>
        </div>
      </section>
    </div>
  </div>

  <!-- ============ EXPORT ============ -->
  <section class="panel" style="margin-top:18px">
    <div class="panel-label"><span>Export</span><b id="exportMeta"></b></div>
    <div class="tabs" role="tablist">
      <button class="tab" role="tab" data-out="react" aria-selected="true">React + Tailwind</button>
      <button class="tab" role="tab" data-out="html" aria-selected="false">Plain HTML</button>
      <button class="tab" role="tab" data-out="config" aria-selected="false">config.js</button>
      <button class="tab" role="tab" data-out="json" aria-selected="false">Save / load JSON</button>
    </div>
    <pre class="code" id="codeOut">—</pre>
    <div class="btnbar">
      <button class="ghost" id="copyCode">Copy code</button>
      <button class="ghost" id="downloadCode">Download file</button>
      <button class="ghost" id="loadJson" hidden>Load config from JSON</button>
    </div>
  </section>

  <section class="notes">
    <h2>Things that break, and why</h2>
    <ul>
      <li><strong>Number format is the usual culprit.</strong> It must be country code + number with nothing else — <code>919876543210</code>. A leading <code>+</code>, a leading <code>0</code>, or a bare 10-digit number fails <em>silently</em>: WhatsApp opens an empty chat with no error. The readout above catches this.</li>
      <li><strong>Keep <code>window.open</code> inside the click handler.</strong> If you <code>await</code> anything before it, the browser treats it as an unsolicited popup and blocks it. That's why the Sheets call in the exported code is fired without awaiting.</li>
      <li><strong>Label the button honestly.</strong> "Send on WhatsApp", never "Submit". People who don't expect WhatsApp to open assume something crashed and close the tab.</li>
      <li><strong>Test on a real phone.</strong> Desktop opens WhatsApp Web or the desktop app; mobile jumps into the app. Nearly every patient is on mobile.</li>
      <li><strong>Always keep the phone fallback.</strong> Not everyone has WhatsApp, and a failed submit should still convert into a call.</li>
      <li><strong>The honeypot field stays.</strong> It's invisible to people and catches most bot submissions for free. Don't delete it when tidying the markup.</li>
      <li><strong>Nothing is stored by this page.</strong> Use the JSON tab to save a client's config into your repo.</li>
    </ul>
  </section>

  <div class="foot">
    <span>whatsapp-form-kit — reusable client tooling</span>
    <span>No tracking · No storage · Single file</span>
  </div>
</div>

<script>
"use strict";

/* ---------------- presets ---------------- */
const PRESETS = {
  dental: {
    name: "Dr. Arul's Tooth Care",
    tag: "Book an appointment — we'll confirm on WhatsApp",
    button: "Send on WhatsApp",
    brand: "#00396B", accent: "#D4AF5B",
    fields: [
      { key: "name",   label: "Your name",        type: "text",     required: true,  placeholder: "Ramesh Kumar" },
      { key: "phone",  label: "Phone number",     type: "tel",      required: true,  placeholder: "9876543210" },
      { key: "reason", label: "Reason for visit", type: "textarea", required: false, placeholder: "Tooth pain since 3 days" },
      { key: "slot",   label: "Preferred time",   type: "select",   required: false, options: "Morning, Afternoon, Evening" }
    ],
    template: "New appointment enquiry\n\nName: {name}\nPhone: {phone}\nReason: {reason}\nPreferred time: {slot}"
  },
  general: {
    name: "City Health Clinic",
    tag: "Request a consultation",
    button: "Send on WhatsApp",
    brand: "#12665C", accent: "#E0A100",
    fields: [
      { key: "name",    label: "Your name",       type: "text",     required: true,  placeholder: "Full name" },
      { key: "phone",   label: "Phone number",    type: "tel",      required: true,  placeholder: "9876543210" },
      { key: "doctor",  label: "Doctor",          type: "select",   required: false, options: "Any available, Dr. A, Dr. B" },
      { key: "concern", label: "What's the concern?", type: "textarea", required: false, placeholder: "Briefly describe" }
    ],
    template: "New consultation request\n\nName: {name}\nPhone: {phone}\nDoctor: {doctor}\nConcern: {concern}"
  },
  salon: {
    name: "Studio Nine",
    tag: "Book your slot",
    button: "Book on WhatsApp",
    brand: "#2E2A4F", accent: "#C86A72",
    fields: [
      { key: "name",    label: "Your name",   type: "text",   required: true,  placeholder: "Full name" },
      { key: "phone",   label: "Phone",       type: "tel",    required: true,  placeholder: "9876543210" },
      { key: "service", label: "Service",     type: "select", required: true,  options: "Haircut, Colour, Facial, Bridal" },
      { key: "when",    label: "Preferred day", type: "text", required: false, placeholder: "Saturday morning" }
    ],
    template: "New booking request\n\nName: {name}\nPhone: {phone}\nService: {service}\nPreferred: {when}"
  },
  minimal: {
    name: "Your business",
    tag: "Send us a message",
    button: "Send on WhatsApp",
    brand: "#1B3A6B", accent: "#5C6068",
    fields: [
      { key: "name",    label: "Name",    type: "text",     required: true,  placeholder: "Your name" },
      { key: "phone",   label: "Phone",   type: "tel",      required: true,  placeholder: "9876543210" },
      { key: "message", label: "Message", type: "textarea", required: false, placeholder: "How can we help?" }
    ],
    template: "New enquiry from website\n\nName: {name}\nPhone: {phone}\nMessage: {message}"
  }
};

/* ---------------- state ---------------- */
let cfg = JSON.parse(JSON.stringify(PRESETS.dental));
cfg.number = "";
cfg.fallback = "";
cfg.consent = false;
cfg.consentText = "By submitting, you agree that we may contact you regarding your enquiry.";
cfg.brandBtn = false;
cfg.sheet = false;
cfg.sheetUrl = "";

let values = {};        // live preview answers
let activeTab = "react";

const $ = id => document.getElementById(id);

/* ---------------- helpers ---------------- */
function normalise(raw) {
  let n = String(raw || "").replace(/\D/g, "");
  return n.replace(/^0+/, "");
}
function slug(s) {
  return String(s || "").toLowerCase().replace(/[^a-z0-9]+/g, "_").replace(/^_+|_+$/g, "") || "field";
}
function esc(s) {
  return String(s).replace(/&/g, "&amp;").replace(/</g, "&lt;").replace(/>/g, "&gt;");
}
function uniqueKey(base, skipIndex) {
  let k = slug(base), n = 1;
  const taken = cfg.fields.filter((_, i) => i !== skipIndex).map(f => f.key);
  while (taken.includes(k)) { k = slug(base) + "_" + (++n); }
  return k;
}

/* Build the message, dropping lines whose only token is empty */
function buildMessage(vals) {
  const lines = String(cfg.template).split("\n");
  const out = [];
  for (const line of lines) {
    const tokens = line.match(/\{(\w+)\}/g) || [];
    if (tokens.length) {
      const allEmpty = tokens.every(t => {
        const k = t.slice(1, -1);
        return !String(vals[k] || "").trim();
      });
      if (allEmpty) continue;   // drop the whole line
    }
    out.push(line.replace(/\{(\w+)\}/g, (_, k) => String(vals[k] || "").trim() || "—"));
  }
  return out.join("\n").replace(/\n{3,}/g, "\n\n").trim();
}

/* ---------------- config panel rendering ---------------- */
function renderFields() {
  const host = $("fieldList");
  host.innerHTML = "";
  cfg.fields.forEach((f, i) => {
    const row = document.createElement("div");
    row.className = "fieldrow";

    const top = document.createElement("div");
    top.className = "top";
    top.innerHTML =
      '<input class="grow" type="text" value="' + esc(f.label) + '" data-i="' + i + '" data-p="label" aria-label="Field label">' +
      '<span class="tok">{' + f.key + '}</span>';
    row.appendChild(top);

    const mid = document.createElement("div");
    mid.className = "row";
    mid.style.marginBottom = "7px";
    mid.innerHTML =
      '<select data-i="' + i + '" data-p="type" aria-label="Field type">' +
        ['text','tel','textarea','select','date','email'].map(t =>
          '<option value="' + t + '"' + (f.type === t ? ' selected' : '') + '>' + t + '</option>').join('') +
      '</select>' +
      '<input type="text" value="' + esc(f.type === 'select' ? (f.options || '') : (f.placeholder || '')) + '" ' +
        'data-i="' + i + '" data-p="' + (f.type === 'select' ? 'options' : 'placeholder') + '" ' +
        'placeholder="' + (f.type === 'select' ? 'Option A, Option B' : 'Placeholder') + '" ' +
        'aria-label="' + (f.type === 'select' ? 'Options' : 'Placeholder') + '">';
    row.appendChild(mid);

    const bot = document.createElement("div");
    bot.className = "top";
    bot.style.marginBottom = "0";
    bot.innerHTML =
      '<label class="check" style="margin:0;flex:1">' +
        '<input type="checkbox" data-i="' + i + '" data-p="required"' + (f.required ? ' checked' : '') + '> Required' +
      '</label>' +
      '<button class="mini" data-act="up" data-i="' + i + '"' + (i === 0 ? ' disabled' : '') + ' aria-label="Move up">↑</button>' +
      '<button class="mini" data-act="down" data-i="' + i + '"' + (i === cfg.fields.length - 1 ? ' disabled' : '') + ' aria-label="Move down">↓</button>' +
      '<button class="mini danger" data-act="del" data-i="' + i + '" aria-label="Remove field">Remove</button>';
    row.appendChild(bot);

    host.appendChild(row);
  });
  $("fieldCount").textContent = cfg.fields.length + " field" + (cfg.fields.length === 1 ? "" : "s");

  const bar = $("tokenBar");
  bar.innerHTML = "";
  cfg.fields.forEach(f => {
    const b = document.createElement("button");
    b.className = "mini";
    b.type = "button";
    b.textContent = "{" + f.key + "}";
    b.addEventListener("click", () => {
      const ta = $("cfgTemplate");
      const s = ta.selectionStart || ta.value.length;
      ta.value = ta.value.slice(0, s) + "{" + f.key + "}" + ta.value.slice(ta.selectionEnd || s);
      cfg.template = ta.value;
      ta.focus();
      ta.selectionStart = ta.selectionEnd = s + f.key.length + 2;
      render();
    });
    bar.appendChild(b);
  });
}

function syncConfigInputs() {
  $("cfgName").value = cfg.name;
  $("cfgTag").value = cfg.tag;
  $("cfgNumber").value = cfg.number;
  $("cfgFallback").value = cfg.fallback;
  $("cfgTemplate").value = cfg.template;
  $("cfgButton").value = cfg.button;
  $("cfgConsent").checked = cfg.consent;
  $("cfgConsentText").value = cfg.consentText;
  $("consentWrap").hidden = !cfg.consent;
  $("cfgBrandBtn").checked = cfg.brandBtn;
  $("cfgBrand").value = cfg.brand;
  $("cfgAccent").value = cfg.accent;
  $("cfgSheet").checked = cfg.sheet;
  $("cfgSheetUrl").value = cfg.sheetUrl;
  $("sheetWrap").hidden = !cfg.sheet;
}

/* ---------------- preview ---------------- */
function renderPreview() {
  const host = $("preview");
  host.innerHTML = "";

  const h = document.createElement("h3");
  h.textContent = cfg.name || "Your business";
  host.appendChild(h);

  const tag = document.createElement("p");
  tag.className = "tag";
  tag.textContent = cfg.tag || "";
  host.appendChild(tag);

  cfg.fields.forEach(f => {
    const wrap = document.createElement("div");
    wrap.className = "pf";

    const lab = document.createElement("label");
    lab.setAttribute("for", "pv_" + f.key);
    lab.innerHTML = esc(f.label) + (f.required ? ' <span class="req">*</span>' : '');
    wrap.appendChild(lab);

    let el;
    if (f.type === "textarea") {
      el = document.createElement("textarea");
      el.rows = 3;
    } else if (f.type === "select") {
      el = document.createElement("select");
      const blank = document.createElement("option");
      blank.value = ""; blank.textContent = "Select…";
      el.appendChild(blank);
      String(f.options || "").split(",").map(s => s.trim()).filter(Boolean).forEach(o => {
        const op = document.createElement("option");
        op.value = o; op.textContent = o;
        el.appendChild(op);
      });
    } else {
      el = document.createElement("input");
      el.type = f.type === "tel" ? "tel" : (f.type === "date" ? "date" : (f.type === "email" ? "email" : "text"));
    }
    el.id = "pv_" + f.key;
    if (f.placeholder && f.type !== "select") el.placeholder = f.placeholder;
    el.value = values[f.key] || "";
    el.addEventListener("input", () => { values[f.key] = el.value; render(); });
    el.addEventListener("change", () => { values[f.key] = el.value; render(); });
    wrap.appendChild(el);
    host.appendChild(wrap);
  });

  const btn = document.createElement("button");
  btn.className = "send" + (cfg.brandBtn ? " brand" : "");
  btn.id = "previewSend";
  btn.innerHTML =
    '<svg width="18" height="18" viewBox="0 0 24 24" fill="currentColor" aria-hidden="true">' +
    '<path d="M12 2A10 10 0 0 0 3.5 17.3L2 22l4.8-1.5A10 10 0 1 0 12 2zm0 18.2c-1.6 0-3.1-.4-4.4-1.2l-.3-.2-3 .9.9-2.9-.2-.3a8.2 8.2 0 1 1 7 3.7z"/>' +
    '<path d="M17.5 14.4c-.3-.2-1.7-.9-2-1-.3-.1-.5-.2-.7.1-.2.3-.7 1-.9 1.2-.2.2-.3.2-.6.1-.3-.2-1.3-.5-2.4-1.5-.9-.8-1.5-1.8-1.7-2.1-.2-.3 0-.5.1-.6l.5-.5c.1-.2.2-.3.3-.5.1-.2 0-.4 0-.5 0-.2-.7-1.6-.9-2.2-.2-.6-.5-.5-.7-.5h-.6c-.2 0-.5.1-.8.4-.3.3-1 1-1 2.5s1.1 2.9 1.2 3.1c.2.2 2.1 3.2 5.1 4.5.7.3 1.3.5 1.7.6.7.2 1.4.2 1.9.1.6-.1 1.7-.7 2-1.4.2-.7.2-1.3.2-1.4-.1-.1-.3-.2-.6-.4z"/></svg>' +
    '<span>' + esc(cfg.button || "Send on WhatsApp") + '</span>';
  btn.addEventListener("click", sendNow);
  host.appendChild(btn);

  if (cfg.consent) {
    const c = document.createElement("p");
    c.className = "consent";
    c.textContent = cfg.consentText;
    host.appendChild(c);
  }
  if (cfg.fallback) {
    const fb = document.createElement("p");
    fb.className = "fallback";
    fb.innerHTML = 'Or call us on <a href="tel:' + esc(cfg.fallback.replace(/\s/g, "")) + '">' + esc(cfg.fallback) + '</a>';
    host.appendChild(fb);
  }
}

/* ---------------- diagnostics ---------------- */
function diagnose(raw, num) {
  const c = [];
  if (!String(raw).trim()) { c.push(["idle", "Enter the WhatsApp number to begin"]); return c; }
  if (/[+\s\-()]/.test(raw)) c.push(["warn", "Stripped +, spaces and dashes — wa.me takes digits only"]);
  if (num.length < 10) c.push(["bad", "Too short — " + num.length + " digits, need at least 10"]);
  else if (num.length === 10) c.push(["bad", "Missing country code — add 91 in front for India"]);
  else if (num.length > 15) c.push(["bad", "Too long — 15 digits maximum"]);
  else {
    c.push(["ok", "Number length valid (" + num.length + " digits)"]);
    if (num.startsWith("91") && num.length === 12) c.push(["ok", "Reads as an Indian number (+91)"]);
    else c.push(["warn", "Country code is not +91 — check it is correct"]);
  }
  const missing = cfg.fields.filter(f => f.required && !String(values[f.key] || "").trim());
  if (missing.length) c.push(["warn", "Required not filled: " + missing.map(f => f.label).join(", ")]);
  else c.push(["ok", "All required fields complete"]);

  const used = (String(cfg.template).match(/\{(\w+)\}/g) || []).map(t => t.slice(1, -1));
  const keys = cfg.fields.map(f => f.key);
  const orphan = [...new Set(used)].filter(u => !keys.includes(u));
  if (orphan.length) c.push(["bad", "Template uses unknown token: " + orphan.map(o => "{" + o + "}").join(", ")]);
  const unused = keys.filter(k => !used.includes(k));
  if (unused.length) c.push(["warn", "Field not in message: " + unused.join(", ")]);
  return c;
}

/* ---------------- code generation ---------------- */
function genReact() {
  const L = [];
  const stateKeys = cfg.fields.map(f => f.key);
  L.push("import { useState } from 'react';");
  L.push("");
  L.push("const WHATSAPP = '" + normalise(cfg.number) + "';   // country code + number, digits only");
  if (cfg.sheet) L.push("const SHEET_URL = import.meta.env.VITE_APPOINTMENT_ENDPOINT;");
  L.push("");
  L.push("export default function EnquiryForm() {");
  L.push("  const [form, setForm] = useState({");
  stateKeys.forEach(k => L.push("    " + k + ": '',"));
  L.push("  });");
  L.push("  const [hp, setHp] = useState('');        // honeypot — bots fill this");
  L.push("  const [error, setError] = useState('');");
  L.push("");
  L.push("  const set = (k) => (e) => setForm({ ...form, [k]: e.target.value });");
  L.push("");
  L.push("  const handleSend = () => {");
  L.push("    if (hp) return;                        // bot caught");
  const reqs = cfg.fields.filter(f => f.required).map(f => f.key);
  if (reqs.length) {
    L.push("");
    L.push("    if (" + reqs.map(k => "!form." + k + ".trim()").join(" || ") + ") {");
    L.push("      setError('Please fill in all required fields.');");
    L.push("      return;");
    L.push("    }");
  }
  const phoneField = cfg.fields.find(f => f.type === "tel");
  if (phoneField) {
    L.push("    if (!/^\\d{10}$/.test(form." + phoneField.key + ".replace(/\\D/g, '').slice(-10))) {");
    L.push("      setError('Please enter a valid 10-digit phone number.');");
    L.push("      return;");
    L.push("    }");
  }
  L.push("    setError('');");
  L.push("");
  L.push("    const message = [");
  String(cfg.template).split("\n").forEach(line => {
    if (!line.trim()) { L.push("      '',"); return; }
    const toks = line.match(/\{(\w+)\}/g) || [];
    if (toks.length === 1 && line.trim().endsWith(toks[0])) {
      const k = toks[0].slice(1, -1);
      const label = line.slice(0, line.indexOf(toks[0]));
      L.push("      form." + k + ".trim() ? '" + label.replace(/'/g, "\\'") + "' + form." + k + ".trim() : null,");
    } else if (toks.length) {
      let expr = "'" + line.replace(/'/g, "\\'").replace(/\{(\w+)\}/g, "' + form.$1.trim() + '") + "'";
      L.push("      " + expr + ",");
    } else {
      L.push("      '" + line.replace(/'/g, "\\'") + "',");
    }
  });
  L.push("    ].filter((l) => l !== null).join('\\n');");
  L.push("");
  if (cfg.sheet) {
    L.push("    // Silent copy to Google Sheets. NOT awaited — awaiting here would");
    L.push("    // push window.open outside the click handler and get it blocked.");
    L.push("    if (SHEET_URL) {");
    L.push("      fetch(SHEET_URL, {");
    L.push("        method: 'POST',");
    L.push("        body: JSON.stringify({ ...form, website: hp }),");
    L.push("      }).catch(() => {});");
    L.push("    }");
    L.push("");
  }
  L.push("    window.open(");
  L.push("      `https://wa.me/${WHATSAPP}?text=${encodeURIComponent(message)}`,");
  L.push("      '_blank'");
  L.push("    );");
  L.push("  };");
  L.push("");
  L.push("  return (");
  L.push("    <div className=\"mx-auto w-full max-w-md rounded-lg border border-slate-200 bg-white p-6\">");
  L.push("      <h3 className=\"text-xl font-bold\" style={{ color: '" + cfg.brand + "' }}>");
  L.push("        " + (cfg.name || "Your business"));
  L.push("      </h3>");
  if (cfg.tag) L.push("      <p className=\"mb-4 text-sm text-slate-500\">" + cfg.tag + "</p>");
  L.push("");
  cfg.fields.forEach(f => {
    L.push("      <div className=\"mb-3\">");
    L.push("        <label htmlFor=\"" + f.key + "\" className=\"mb-1 block text-sm font-medium\">");
    L.push("          " + f.label + (f.required ? " <span className=\"text-red-600\">*</span>" : ""));
    L.push("        </label>");
    const cls = "w-full rounded border border-slate-300 px-3 py-2 text-sm focus:outline-none focus:ring-2";
    if (f.type === "textarea") {
      L.push("        <textarea id=\"" + f.key + "\" rows={3} value={form." + f.key + "} onChange={set('" + f.key + "')}");
      L.push("          placeholder=\"" + (f.placeholder || "") + "\" className=\"" + cls + "\" />");
    } else if (f.type === "select") {
      L.push("        <select id=\"" + f.key + "\" value={form." + f.key + "} onChange={set('" + f.key + "')} className=\"" + cls + "\">");
      L.push("          <option value=\"\">Select…</option>");
      String(f.options || "").split(",").map(s => s.trim()).filter(Boolean).forEach(o => {
        L.push("          <option value=\"" + o + "\">" + o + "</option>");
      });
      L.push("        </select>");
    } else {
      L.push("        <input id=\"" + f.key + "\" type=\"" + f.type + "\" value={form." + f.key + "} onChange={set('" + f.key + "')}");
      L.push("          placeholder=\"" + (f.placeholder || "") + "\" className=\"" + cls + "\" />");
    }
    L.push("      </div>");
  });
  L.push("");
  L.push("      {/* Honeypot — hidden from people, filled by bots */}");
  L.push("      <input");
  L.push("        type=\"text\" value={hp} onChange={(e) => setHp(e.target.value)}");
  L.push("        tabIndex={-1} autoComplete=\"off\" aria-hidden=\"true\"");
  L.push("        className=\"absolute left-[-9999px] opacity-0\"");
  L.push("      />");
  L.push("");
  L.push("      {error && <p className=\"mb-2 text-sm text-red-600\">{error}</p>}");
  L.push("");
  L.push("      <button");
  L.push("        type=\"button\"");
  L.push("        onClick={handleSend}");
  L.push("        className=\"flex w-full items-center justify-center gap-2 rounded px-4 py-3 font-medium text-white\"");
  L.push("        style={{ backgroundColor: '" + (cfg.brandBtn ? cfg.brand : "#1FA855") + "' }}");
  L.push("      >");
  L.push("        " + (cfg.button || "Send on WhatsApp"));
  L.push("      </button>");
  if (cfg.consent) {
    L.push("");
    L.push("      <p className=\"mt-2 text-xs text-slate-500\">");
    L.push("        " + cfg.consentText);
    L.push("      </p>");
  }
  if (cfg.fallback) {
    L.push("");
    L.push("      <p className=\"mt-3 text-center text-sm text-slate-500\">");
    L.push("        Or call us on{' '}");
    L.push("        <a href=\"tel:" + cfg.fallback.replace(/\s/g, "") + "\" className=\"font-medium\" style={{ color: '" + cfg.brand + "' }}>");
    L.push("          " + cfg.fallback);
    L.push("        </a>");
    L.push("      </p>");
  }
  L.push("    </div>");
  L.push("  );");
  L.push("}");
  return L.join("\n");
}

function genHtml() {
  const L = [];
  L.push('<div class="wa-form">');
  L.push('  <h3>' + (cfg.name || "Your business") + '</h3>');
  if (cfg.tag) L.push('  <p class="wa-tag">' + cfg.tag + '</p>');
  cfg.fields.forEach(f => {
    L.push('  <label for="wa_' + f.key + '">' + f.label + (f.required ? ' *' : '') + '</label>');
    if (f.type === "textarea") {
      L.push('  <textarea id="wa_' + f.key + '" rows="3" placeholder="' + (f.placeholder || '') + '"></textarea>');
    } else if (f.type === "select") {
      L.push('  <select id="wa_' + f.key + '">');
      L.push('    <option value="">Select…</option>');
      String(f.options || "").split(",").map(s => s.trim()).filter(Boolean)
        .forEach(o => L.push('    <option>' + o + '</option>'));
      L.push('  </select>');
    } else {
      L.push('  <input id="wa_' + f.key + '" type="' + f.type + '" placeholder="' + (f.placeholder || '') + '">');
    }
  });
  L.push('  <input id="wa_hp" type="text" tabindex="-1" autocomplete="off" aria-hidden="true"');
  L.push('         style="position:absolute;left:-9999px;opacity:0">');
  L.push('  <p id="wa_error" style="color:#b4432b;font-size:13px"></p>');
  L.push('  <button id="wa_send" type="button">' + (cfg.button || "Send on WhatsApp") + '</button>');
  if (cfg.consent) L.push('  <p class="wa-consent">' + cfg.consentText + '</p>');
  if (cfg.fallback) L.push('  <p class="wa-fallback">Or call us on <a href="tel:' + cfg.fallback.replace(/\s/g, "") + '">' + cfg.fallback + '</a></p>');
  L.push('</div>');
  L.push('');
  L.push('<script>');
  L.push('  var WHATSAPP = "' + normalise(cfg.number) + '";');
  if (cfg.sheet && cfg.sheetUrl) L.push('  var SHEET_URL = "' + cfg.sheetUrl + '";');
  L.push('  var FIELDS = ' + JSON.stringify(cfg.fields.map(f => f.key)) + ';');
  L.push('');
  L.push('  document.getElementById("wa_send").addEventListener("click", function () {');
  L.push('    if (document.getElementById("wa_hp").value) return;');
  L.push('');
  L.push('    var v = {};');
  L.push('    FIELDS.forEach(function (k) {');
  L.push('      v[k] = (document.getElementById("wa_" + k).value || "").trim();');
  L.push('    });');
  L.push('');
  const reqs = cfg.fields.filter(f => f.required).map(f => f.key);
  if (reqs.length) {
    L.push('    var required = ' + JSON.stringify(reqs) + ';');
    L.push('    for (var i = 0; i < required.length; i++) {');
    L.push('      if (!v[required[i]]) {');
    L.push('        document.getElementById("wa_error").textContent = "Please fill in all required fields.";');
    L.push('        return;');
    L.push('      }');
    L.push('    }');
    L.push('    document.getElementById("wa_error").textContent = "";');
    L.push('');
  }
  L.push('    var lines = [];');
  String(cfg.template).split("\n").forEach(line => {
    const toks = line.match(/\{(\w+)\}/g) || [];
    if (!toks.length) { L.push('    lines.push("' + line.replace(/"/g, '\\"') + '");'); return; }
    if (toks.length === 1 && line.trim().endsWith(toks[0])) {
      const k = toks[0].slice(1, -1);
      const label = line.slice(0, line.indexOf(toks[0]));
      L.push('    if (v.' + k + ') lines.push("' + label.replace(/"/g, '\\"') + '" + v.' + k + ');');
    } else {
      let expr = '"' + line.replace(/"/g, '\\"').replace(/\{(\w+)\}/g, '" + v.$1 + "') + '"';
      L.push('    lines.push(' + expr + ');');
    }
  });
  L.push('    var message = lines.join("\\n");');
  L.push('');
  if (cfg.sheet && cfg.sheetUrl) {
    L.push('    // Not awaited — keeps window.open inside the click handler');
    L.push('    fetch(SHEET_URL, { method: "POST", body: JSON.stringify(v) }).catch(function () {});');
    L.push('');
  }
  L.push('    window.open("https://wa.me/" + WHATSAPP + "?text=" + encodeURIComponent(message), "_blank");');
  L.push('  });');
  L.push('<\/script>');
  return L.join("\n");
}

function genConfig() {
  const o = {
    name: cfg.name,
    tagline: cfg.tag,
    whatsapp: normalise(cfg.number),
    phoneDisplay: cfg.fallback,
    phoneRaw: cfg.fallback.replace(/\s/g, ""),
    colors: { primary: cfg.brand, accent: cfg.accent },
    form: {
      buttonLabel: cfg.button,
      consent: cfg.consent ? cfg.consentText : null,
      fields: cfg.fields,
      template: cfg.template
    }
  };
  return "// Single source of truth. Import this everywhere —\n" +
         "// never hardcode the phone number or address in a component.\n\n" +
         "export const CLINIC = " + JSON.stringify(o, null, 2) + ";\n";
}

function genJson() { return JSON.stringify(cfg, null, 2); }

function currentCode() {
  if (activeTab === "react")  return genReact();
  if (activeTab === "html")   return genHtml();
  if (activeTab === "config") return genConfig();
  return genJson();
}
function currentFilename() {
  return { react: "EnquiryForm.jsx", html: "whatsapp-form.html", config: "clinic.js", json: "wa-config.json" }[activeTab];
}

/* ---------------- main render ---------------- */
function render() {
  const num = normalise(cfg.number);
  const msg = buildMessage(values);
  const url = "https://wa.me/" + num + "?text=" + encodeURIComponent(msg);
  const valid = num.length >= 11 && num.length <= 15;

  const host = $("checks");
  host.innerHTML = "";
  diagnose(cfg.number, num).forEach(([state, text]) => {
    const r = document.createElement("div");
    r.className = "chk " + state;
    r.innerHTML = '<span class="dot ' + state + '"></span><span>' + esc(text) + '</span>';
    host.appendChild(r);
  });

  $("outNumber").textContent = num || "—";
  $("outMessage").textContent = msg || "—";
  $("outUrl").textContent = valid ? url : "—";
  $("charCount").textContent = msg.length + " chars";
  $("status").textContent = valid ? "ready" : "incomplete";
  $("copyLink").disabled = !valid;
  $("openLink").disabled = !valid;

  const btn = $("previewSend");
  if (btn) btn.disabled = !valid;

  document.documentElement.style.setProperty("--brand", cfg.brand);
  document.documentElement.style.setProperty("--brand-2", cfg.accent);

  $("codeOut").textContent = currentCode();
  $("exportMeta").textContent = currentFilename();
  $("loadJson").hidden = activeTab !== "json";

  return { url, valid };
}

function renderAll() { renderFields(); renderPreview(); render(); }

function sendNow() {
  const { url, valid } = render();
  if (!valid) return;
  const missing = cfg.fields.filter(f => f.required && !String(values[f.key] || "").trim());
  if (missing.length) { $("status").textContent = "fill required"; return; }
  window.open(url, "_blank");
  $("status").textContent = "sent";
}

/* ---------------- events ---------------- */
const bind = (id, prop, ev) => {
  $(id).addEventListener(ev || "input", e => {
    cfg[prop] = e.target.type === "checkbox" ? e.target.checked : e.target.value;
    if (prop === "consent") $("consentWrap").hidden = !cfg.consent;
    if (prop === "sheet")   $("sheetWrap").hidden = !cfg.sheet;
    $("presetName").textContent = "custom";
    if (["name","tag","button","consent","consentText","brandBtn","fallback"].includes(prop)) renderPreview();
    render();
  });
};
bind("cfgName","name"); bind("cfgTag","tag"); bind("cfgNumber","number");
bind("cfgFallback","fallback"); bind("cfgTemplate","template"); bind("cfgButton","button");
bind("cfgConsent","consent","change"); bind("cfgConsentText","consentText");
bind("cfgBrandBtn","brandBtn","change"); bind("cfgBrand","brand"); bind("cfgAccent","accent");
bind("cfgSheet","sheet","change"); bind("cfgSheetUrl","sheetUrl");

$("fieldList").addEventListener("input", e => {
  const i = e.target.dataset.i, p = e.target.dataset.p;
  if (i === undefined || !p) return;
  const f = cfg.fields[+i];
  if (p === "required") { f.required = e.target.checked; }
  else if (p === "label") {
    const old = f.key;
    f.label = e.target.value;
    f.key = uniqueKey(e.target.value, +i);
    if (old !== f.key) {
      cfg.template = cfg.template.split("{" + old + "}").join("{" + f.key + "}");
      values[f.key] = values[old]; delete values[old];
      $("cfgTemplate").value = cfg.template;
    }
    renderFields(); renderPreview(); render(); return;
  }
  else if (p === "type") {
    f.type = e.target.value;
    renderFields(); renderPreview(); render(); return;
  }
  else { f[p] = e.target.value; }
  renderPreview(); render();
});
$("fieldList").addEventListener("change", e => {
  if (e.target.dataset.p === "type") return;
  if (e.target.dataset.p === "required") { renderPreview(); render(); }
});
$("fieldList").addEventListener("click", e => {
  const act = e.target.dataset.act;
  if (!act) return;
  const i = +e.target.dataset.i;
  if (act === "del") {
    const k = cfg.fields[i].key;
    cfg.fields.splice(i, 1);
    delete values[k];
  } else if (act === "up" && i > 0) {
    [cfg.fields[i - 1], cfg.fields[i]] = [cfg.fields[i], cfg.fields[i - 1]];
  } else if (act === "down" && i < cfg.fields.length - 1) {
    [cfg.fields[i + 1], cfg.fields[i]] = [cfg.fields[i], cfg.fields[i + 1]];
  }
  $("presetName").textContent = "custom";
  renderAll();
});

$("addField").addEventListener("click", () => {
  const key = uniqueKey("new_field", -1);
  cfg.fields.push({ key, label: "New field", type: "text", required: false, placeholder: "" });
  cfg.template = cfg.template.trimEnd() + "\nNew field: {" + key + "}";
  $("cfgTemplate").value = cfg.template;
  $("presetName").textContent = "custom";
  renderAll();
});

document.querySelectorAll("[data-preset]").forEach(b => {
  b.addEventListener("click", () => {
    const p = JSON.parse(JSON.stringify(PRESETS[b.dataset.preset]));
    cfg = Object.assign({}, cfg, p);
    values = {};
    $("presetName").textContent = b.dataset.preset;
    syncConfigInputs();
    renderAll();
  });
});

document.querySelectorAll(".tab").forEach(t => {
  t.addEventListener("click", () => {
    document.querySelectorAll(".tab").forEach(x => x.setAttribute("aria-selected", "false"));
    t.setAttribute("aria-selected", "true");
    activeTab = t.dataset.out;
    render();
  });
});

async function copyText(text, btn, label) {
  try {
    await navigator.clipboard.writeText(text);
    btn.textContent = "Copied";
  } catch {
    btn.textContent = "Copy blocked — select manually";
  }
  setTimeout(() => { btn.textContent = label; }, 2000);
}
$("copyLink").addEventListener("click", e => {
  const { url, valid } = render();
  if (valid) copyText(url, e.target, "Copy link");
});
$("copyCode").addEventListener("click", e => copyText(currentCode(), e.target, "Copy code"));
$("openLink").addEventListener("click", sendNow);

$("downloadCode").addEventListener("click", () => {
  const blob = new Blob([currentCode()], { type: "text/plain" });
  const a = document.createElement("a");
  a.href = URL.createObjectURL(blob);
  a.download = currentFilename();
  a.click();
  URL.revokeObjectURL(a.href);
});

$("loadJson").addEventListener("click", () => {
  const txt = prompt("Paste a saved config JSON:");
  if (!txt) return;
  try {
    const parsed = JSON.parse(txt);
    if (!Array.isArray(parsed.fields)) throw new Error("no fields array");
    cfg = parsed;
    values = {};
    $("presetName").textContent = "loaded";
    syncConfigInputs();
    renderAll();
  } catch (err) {
    alert("That isn't a valid config. Expected JSON from the Save / load JSON tab.");
  }
});

/* ---------------- boot ---------------- */
syncConfigInputs();
renderAll();
</script>
</body>
</html>
