# FallLegalPaper

**Sovereign SRA-shaped document generator for UK solicitor firms.**

One HTML file. Runs entirely in the browser. Client data never leaves the device.

Part of the **falllegal** bundle (prime 757) with `falllegal` (anchor · 743), `falllegalonboard` (751) and `falllegalpractice` (761).

---

## For a 1–10-person SRA-regulated UK firm

You opened this because you spend three hours every week writing the same engagement letter, the same CFA, the same letter before action, the same s.21 notice, with the same SRA-compliant clauses pasted from your last matter.

**FallLegalPaper does that for you, on your laptop, offline, in seconds.**

Pick a client + matter + template. The letter renders in front of you with the regulatory clauses pre-locked (Legal Ombudsman block, SRA reference, file retention, PI cover, liability cap), and your firm-specific fields filled in from the firm record you set once. Edit the unlocked sections in-place. Click commit. The Document is written to IndexedDB with a SHA-256, audit-chained per SRA rule 13.5 (6-year retention), and broadcast on `BroadcastChannel('fall-law')` so the rest of the bundle (matter ledger, fee tracker, onboarding) sees it instantly.

Fifteen templates, ready out-of-the-box:

1. Engagement Letter / Client Care
2. Terms of Business
3. Costs Information & Updates (SRA Transparency Rules)
4. Conditional Fee Agreement — LASPO/CFA Order 2013-compliant, 25% PI cap baked in
5. Damages-Based Agreement — DBA Regs 2013, 25%/35%/50% caps spelled out
6. Initial Advice Letter
7. Letter Before Action — pre-action protocol shape, 14-day demand
8. Settlement Agreement — incl. COT3/s.203 ERA 1996 path
9. File-Closure / Retention Notice — 6-yr storage clause
10. No-Win-No-Fee Client Information Sheet
11. Vulnerable Client Note (internal) — MCA 2005 capacity test
12. Will (simple single-testator) — Wills Act 1837 s.9 attestation
13. LPA Property & Financial Affairs — LP1F drafting note
14. Section 21 Notice — Form 6A drafting note with all five validity pre-conditions
15. Section 8 Notice — rent arrears, Grounds 8/10/11

Plus: built-in **T0 rule pack** (12 offline regulatory answers — engagement timing, SRA Transparency, CFA/DBA caps, LeO procedure, pre-action limits, s.21 reform, will-challenge grounds, LPA fees, ATE disclosure), optional **T3 BYOK chat** to Anthropic for anything off-rule, audit chain inspector with full chain verification, export to Markdown/HTML/print, and live sync with the rest of the bundle over `fall-law`.

**You stay in charge.** Your COLP/COFA remain responsible. This is a tool that does the typing — it is not legal opinion software.

## How to use

1. Open `index.html` in Chrome 113+ (or any modern browser).
2. **Firm** tab: fill in your firm details once (name, SRA No., COLP/COFA, PI insurer, address, client account).
3. **Clients / Matters**: add or let the rest of the bundle sync them in.
4. **Generate**: pick client → pick matter → pick template. Edit anything that is not padlocked ⛯. Commit.
5. **Documents**: filter by client/matter/template/status, view, sign, export.

PWA-installable. Works offline after first load. Import/export everything as JSON.

---

## For the developer / forker

Vanilla JS, single file, no build, no dependencies, no CDN. Drop-in clone of the `fallpaper` template-engine architecture, tuned for the SRA Code instead of FCA COBS.

### Architecture

- **Storage:** IndexedDB store, KONOMI key/value shim with localStorage fallback. Stores: `firms`, `advisers`, `clients`, `matters`, `documents`, `templates`, `audit`, `state`.
- **Schema:** conforms to `LAW-BUNDLE-SHARED-SCHEMA.md` v1.0. `Matter`, `LegalClient`, `LegalAdviser` records keyed by `id`. Shared `firm` record.
- **Mesh:** `BroadcastChannel('fall-law')` for client/matter/document sync (`sync.request`, `sync.snapshot`, `client.*`, `matter.*`, `document.created`, `firm.updated`). `BroadcastChannel('fall-signal')` for the wider estate mesh (`hello`, `ping`/`pong`).
- **Audit chain:** Mansoor P3 extended. Every state-changing action appends `{i, ts, action, prevHash, payload, docHash}`. SHA-256 chained from `GENESIS`. Verifiable via the Audit tab. 6-year retention per SRA rule 13.5.
- **Template engine:** `{id, name, version, practiceArea, sections[]}` where each section is `{id, heading, body, requiredFields, locked}`. Placeholder syntax `{{client.firstName}}`, `{{matter.ref}}`, `{{firm.name}}`, `{{today}}`, `{{ctx.*}}`. Bodies are mini-Markdown (`**bold**`, `-` lists, `1.` ordered, `---` rule, `[SIGNATURE_BLOCK]` macro). Locked sections render with the ⛯ padlock and `contenteditable=false`.
- **Constants:** `TOOLNAME='falllegalpaper'`, `VERSION='1.0.0'`, `PRIME=757`.
- **Document record (per shared schema):** `{id, clientId, matterId, templateId, version, title, html, markdown, sha256, generatedAt, generatedBy, signed, signedAt, signatureHash}`.
- **Disclaimer (shared):** "Sovereign — research only … the firm's COLP/COFA remain responsible. Client data never leaves the device unless exported."
- **T0:** deterministic offline rule table (`T0_RULES`). Word-overlap heuristic in chat matches incoming query against rule `q` strings; falls back to T3 (Anthropic BYOK, browser-direct, opt-in).
- **PWA:** manifest as a data URL; theme-color set; installable.
- **Aesthetic:** oxblood/brass/cream/void. Libre Baskerville serif for document previews (paper aesthetic).
- **Forkable brand:** custom name / colour overrides in Settings.

### Verify in-browser

```js
window.FALL_LEGAL_PAPER       // {state, TOOLNAME, VERSION, PRIME, renderTemplate, renderMarkdown, sha256, T0_RULES, SEED_TEMPLATES}
window.FALL_LEGAL_PAPER.state.templates.length    // 15
window.FALL_LEGAL_PAPER.state.audit.length        // grows with every action
window.FALL_LEGAL_PAPER.T0_RULES.length           // 12
```

### Files

- `index.html` — the deliverable. Open it.
- `README.md` — this file.
- `LICENSE` — MIT.
- `.nojekyll` — disable Jekyll on GitHub Pages.

### Build / deploy

There is no build step. To host on GitHub Pages, push the four files to a repo and enable Pages (legacy build · `main` branch root). `.nojekyll` is mandatory — without it Pages will hide files starting with `_`.

### Licence

MIT. See `LICENSE`.
