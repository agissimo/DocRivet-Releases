# Release Notes — DocRivet

## Version 3.9.0 — Full Feature List
**Release Date:** 2026-05-27
**Editions:** Standard (free) · Premium (license key)
**Platform:** Windows 10/11 · Single self-contained EXE (no installer, no admin rights)

DocRivet assembles multi-source PDF packages — redacted, normalized, OCR-searchable, and PII-scanned — entirely offline. No files leave the machine at any point. No cloud API, no subscription, no internet connection required.

---

### Document Assembly

- **PDF merge** — combine any number of PDFs into a single output file; sources are never modified
- **Image ingest** — JPG, PNG, TIFF (including multi-page), GIF, WebP, BMP, PNM, PGM, PBM, PPM — all convert to PDF pages automatically
- **Folder import** — recursive folder scan adds every supported file in one step
- **Drag-and-drop** — drag files or folders from Explorer directly onto the file list
- **Per-source page selection** — click any file to reveal a thumbnail strip; pick individual pages or ranges; empty selection means "all pages"
- **Reorder by drag or keyboard** — Alt+↑ / Alt+↓ moves rows; merge order follows list order
- **Check / uncheck sources** — unchecked files stay in the list but are excluded from the merge; toolbar buttons for All / None / Invert
- **Password-protected PDFs** — click the lock icon on any encrypted file to enter its password; the password lives in memory only, never on disk
- **PDF Split & Extract** — extract chosen pages from any PDF into a new file; or split into one PDF per page (or custom ranges) into a chosen output folder

---

### Page Normalization ★ Premium

- Mixed page sizes (Letter, A4, Legal, native image dimensions) are unified to a single output size
- **Standard (free):** output at native size — images embedded at their natural dimensions
- **Premium:** choose A4, Letter, or Legal from the Output tab; all sources scaled to fit

---

### Manual Redaction

- **Draw-to-redact** — switch to the Redact tab, drag a rectangle over any sensitive region on any page; the region turns solid black immediately in the preview
- **Forensic burn-in** — redaction destroys content at the PDF content-stream level via PyMuPDF; no overlay, no hidden data; old bytes purged with `garbage=4`
- **Image source redaction** — works on JPG, PNG, TIFF, and other image inputs; the image is converted to PDF in memory, redacted, then merged
- **Per-source, per-page scope** — redaction zones tracked independently per source file and page; nothing bleeds between sources
- **Right-click to clear** — removes all marks on the current page without affecting other pages
- **Applied at merge time only** — source files are never touched

---

### PII Pattern Redaction (Auto-Redact) ★ Premium

Automated PII scan pass inside the Redact tab. Switch the palette to **AUTO** mode, select pattern categories, scan all loaded files in a background thread, review each match, and commit approved ones to the redaction layer in one click. All processing is local — pure Python `re`, no cloud API, no ML model.

#### Pattern library (V1 — US / CA + Universal)

| Jurisdiction | Patterns |
|---|---|
| Universal | Email address, Credit / debit card (Luhn-validated), IBAN |
| 🇺🇸 US | SSN, Phone (NANP), EIN, ABA routing (checksum), ITIN, NPI (Luhn), DEA number, Passport |
| 🇨🇦 CA | SIN (Luhn-validated), OHIP health card, RAMQ (Québec), BC PHN, AB PHN, Postal code, Phone, Passport, CRA Business Number |
| Tier 2 (opt-in) | Date of Birth (ISO format) — unchecked by default; high false-positive risk on non-PHI dates |

#### Workflow

1. Redact tab → palette → **AUTO** toggle
2. Check jurisdictions and patterns (selections persist across MANUAL/AUTO toggles within the session)
3. **Scan** — background thread with per-file progress in the status bar; cancellable at any point
4. Review match list: category chip · matched text · filename + page · click row to navigate preview
5. Check / uncheck individual matches; **Mark All** / **Clear All** for bulk selection
6. **Commit** — approved rects written to redaction layer; audit log updated; palette returns to MANUAL

Staged (pending) matches show an **orange dashed outline** on the canvas; committed marks are solid red.

Re-scanning and re-committing the same file never creates duplicate redaction marks or audit entries.

---

### Search & Text Redact ★ Premium

- **Full-text search** — search a term across all loaded files simultaneously; results highlight in the preview
- **Step through matches** — ↑ / ↓ navigate match by match; the preview jumps to each occurrence
- **Mark All for Redact** — converts every match in every file into a redaction zone in one click
- **Works on OCR'd sources** — if a scanned PDF has been OCR-processed, search finds text in the invisible layer

---

### OCR — Searchable Text Layer ★ Premium

- **OCR on Add** — as each file is added, OCR runs in the background; the text layer is ready before merge
- **OCR at merge** — the merged output gets an invisible text layer on image-only pages, making the final PDF fully searchable
- **18 languages** — English default; custom language selectable per session via the Output tab
- **Fully offline** — Tesseract is bundled in the EXE; no internet connection, no cloud upload
- **Semaphore-throttled concurrency** — max 2 concurrent Tesseract jobs; prevents CPU thrash on multi-file drops

---

### Redaction Audit Log ★ Premium

- Every redaction action is logged automatically: file path, page number, bounding coordinates, reason code, and timestamp
- **CSV export** — audit log saved alongside the merged PDF on every merge; filename matches the output
- **In-session view** — audit panel in the Redact tab shows all entries for the current project with a **Clear** button in the header
- **Project persistence** — audit log is saved into the `.docrivet` project file and restored on open

---

### Reason Codes ★ Premium

- Reason dropdown in the Redact toolbar: FOIA Exemption, HIPAA PHI, Attorney-Client Privilege, Confidential Business Information, Private or Sensitive, PII, Court Order, Custom
- Each redaction mark carries its reason code into the audit log
- Codes follow FOIA and HIPAA compliance conventions

---

### Stamp / Watermark ★ Premium

- Add a text overlay to every page of the merged output
- **Tokens:** CONFIDENTIAL, project name, date, page numbers — combinable freely
- **Position:** Footer / Header / Diagonal watermark
- **Colour:** Black, Red, or custom
- Stamp settings saved to the `.docrivet` project file

---

### Compress Output ★ Premium

- **"Compress output PDF"** checkbox in the Output tab → PDF Tools section
- Applies maximum fitz deflate compression to the merged output as a post-merge step
- Setting persisted to `.docrivet` preferences across sessions

---

### Output Tab

- **3-column layout** — Destination (left), Options (centre), Metadata (right)
- **Review card** — live summary: file count, total pages, combined source size, estimated output size, redaction mark count, OCR-needed page count, free disk space
- **Metadata** — "Clean metadata" strips Title, Author, Creator from output (on by default); when off, edit all five fields inline
- **Atomic write** — output is written to a `.tmp` staging file then renamed; no partial file on failure or cancellation

---

### Project Save / Load

- `.docrivet` files (plain JSON) capture the complete session: file list, check states, page selections, redaction zones, reason codes, audit log, output path, OCR language, page size choice, and stamp settings
- **New Project** resets every subsystem

---

### License Management

- **PRO badge** always visible in the title bar — gold when Premium is active, grey when Standard or expired
- Click the badge to open the license dialog and enter or renew a key
- License stored at `%APPDATA%\DocRivet\license.key`; never transmitted

---

### Themes

- **Parchment** (light), **Lights Out** (dark), **Slate** — switchable at runtime; preference persisted

---

### Keyboard Shortcuts

| Shortcut | Action |
|----------|--------|
| Ctrl+O | Add files |
| Ctrl+Shift+O | Add folder |
| Ctrl+S | Save project |
| Ctrl+N | New project |
| Ctrl+M | Merge |
| Ctrl+F | Switch to Search tab |
| Ctrl+1–4 | Switch to Files / Redact / Search / Output tab |
| Delete | Remove selected file |
| Alt+↑ / Alt+↓ | Move file up / down |
| Ctrl+ / Ctrl− / Ctrl+0 | Zoom font in / out / reset (75%–200%) |
| F1 | About |

---

## Version History

### 3.9.5 (2026-06-07)
Memory & resource leak hardening: search UI lock-up fix (missing `as exc` left `_searching=True` permanently after any search error); Leptonica thread-safety fix (search path now acquires `_leptonica_lock` before calling `get_textpage_ocr()`); merge worker no longer crashes on app-close mid-merge; fitz document handles wrapped in `try/finally` throughout OCR and normalize paths; pixmaps explicitly released after `tobytes()` (up to ~89 MB saved at max zoom); `_meta_cache` now evicted on file removal. No new user-visible features. 564 tests.

### 3.9.0 (2026-05-27)
Redaction integrity fixes: audit log reconciliation on project load (orphaned marks re-added automatically); Search "Mark All for Redact" now immediately updates the Redact tab badge and file list; tab-switch mark refresh; merge progress now reports per-file redaction counts and ends with a counter-validation summary confirming how many marks were applied.

### 3.7.0 (2026-05-26)
Security hardening: CI security checks (Bandit + pip-audit on every PR); shell injection fix in keygen GUI; explicit SSL context for license time checks; NTP response validation (length + stratum); silent exception swallowing replaced with logger warnings; project file path validation. 548 tests (up from 475).

### 3.6.0 (2026-05-22)
PII Pattern Redaction (Premium): MANUAL/AUTO palette toggle; US, CA, Universal pattern library (SSN, SIN, credit card, IBAN, phone, email, 20+ patterns); Luhn/checksum validation; background scan with cancel; staged orange-outline preview; dedup commit to redaction layer; 475 tests.

### 3.5.0 (2026-05-20)
Premium feature gating (PRO chip in Redact & Search tabs for non-premium users); theme consistency fixes; UI cleanup.

### 3.4.1 (2026-05-20)
OCR-on-add deduplication — folders with both `foo.pdf` and `foo_ocr_….pdf` no longer produce duplicate file list entries.

### 3.4.0 (2026-05-17)
PDF Split & Extract; Compress Output PDF ★; semaphore-throttled OCR concurrency; toolbar tooltips.

### 3.3.0 (2026-05-16)
Stable baseline with all core features. 264 tests passing.

### 3.2.0 (2026-05-15)
Output Tab 3-column redesign; OCR and Page Size checkbox-gated dropdowns; PRO badge always visible; license key validation hardening.

### 2.5.0 (2026-05-10)
Product renamed PDF Merger → DocRivet. Draw-to-redact; forensic burn-in; AES-encrypted PDF fix.

---

**Downloads:** https://github.com/agissimo/DocRivet-Releases/releases
**Bugs / feedback:** https://github.com/agissimo/DocRivet-Releases/issues
