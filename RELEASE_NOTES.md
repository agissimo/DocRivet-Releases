# Release Notes - DocRivet

## Version 3.3.0
**Release Date:** 2026-05-16
**Status:** Latest Stable
**Editions:** Standard (free) + Premium (license key)

### What's in DocRivet 3.3.0

DocRivet assembles multi-source PDF packages — redacted, normalized, and OCR-searchable — in a single offline session. No files leave the machine at any point.

---

#### Document Assembly

- **PDF merge** — combine any number of PDFs into a single output file; sources are never modified
- **Image ingest** — JPG, PNG, TIFF (including multi-page), GIF, WebP, BMP, PNM, PGM, PBM, PPM all convert to PDF pages automatically
- **Folder import** — recursive folder scan adds every supported file in one step
- **Drag-and-drop** — drag files or folders from Explorer directly onto the file list
- **Per-source page selection** — click any file to reveal a thumbnail strip; pick individual pages or ranges; an empty selection means "all pages"
- **Reorder by drag or keyboard** — Alt+↑ / Alt+↓ moves rows; merge order follows list order
- **Check / uncheck sources** — unchecked files stay in the list but are excluded from the merge; toolbar buttons for All / None / Invert
- **Password-protected PDFs** — click the lock icon on any encrypted file to enter its password; the password lives in memory only, never on disk

#### Page Normalization ★

- Mixed page sizes (Letter, A4, Legal, native image dimensions) are unified to a single output size
- **Standard (free):** output at native size — images embedded at their natural dimensions
- **Premium:** choose A4, Letter, or Legal from the Output tab; all image sources are scaled to fit the selected size
- PDF page dimensions are preserved from source; image sources are fitted to the chosen target

#### Manual Redaction

- **Draw-to-redact** — switch to the Redact tab, select a file, drag a rectangle over any sensitive region on any page; the region turns solid black immediately in the preview
- **Forensic burn-in** — redaction destroys content at the PDF content-stream level via PyMuPDF; no overlay, no hidden data; old bytes purged with `garbage=4`
- **Image source redaction** — works on JPG, PNG, TIFF, and other image inputs; the image is converted to PDF in memory, redacted, then merged
- **Per-source, per-page scope** — redaction zones are tracked independently per source file and per page; nothing bleeds between sources
- **Right-click to clear** — removes all marks on the current page without affecting other pages
- **Applied at merge time only** — source files are never touched

#### Search & Auto-Redact ★ Premium

- **Full-text search** — search a term across all loaded files simultaneously; results highlight in the preview
- **Step through matches** — ↑ / ↓ navigate match by match; the preview jumps to each occurrence
- **Mark All for Redact** — converts every match in every file into a redaction zone in one click
- **Works on OCR'd sources** — if a scanned PDF has been OCR-processed, search finds text in the invisible layer

#### OCR — Searchable Text Layer ★ Premium

- **OCR on Add** — as each file is added to the list, OCR runs in the background; the text layer is ready before merge
- **OCR at merge** — the merged output gets an invisible text layer on image-only pages, making the final PDF fully searchable
- **Pre-merge OCR** — text layer is available in the preview and usable for Search & Redact before the output is assembled
- **18 languages** — English default; custom language selectable per session via the Output tab dropdown
- **Fully offline** — Tesseract is bundled in the EXE; no internet connection, no cloud upload
- **Leptonica thread-safe** — concurrent multi-file OCR is serialized internally; no cross-thread crashes
- **Degenerate block guard** — OCR blocks with zero-size or infinite bounding coordinates are skipped silently; OCR never crashes on malformed Tesseract output

#### Redaction Audit Log ★ Premium

- Every redaction action is logged automatically: file path, page number, bounding coordinates, reason code, and timestamp
- **CSV export** — audit log saved alongside the merged PDF on every merge; filename matches the output
- **In-session view** — audit panel in the Redact tab shows all entries for the current project
- **Project persistence** — audit log is saved into the `.docrivet` project file and restored on open

#### Reason Codes ★ Premium

- Reason dropdown in the Redact toolbar: FOIA Exemption, HIPAA PHI, Attorney-Client Privilege, Confidential Business Information, PII, Court Order, and Custom
- Each redaction mark carries its reason code into the audit log
- Codes follow FOIA and HIPAA compliance conventions

#### Stamp / Watermark ★ Premium

- Add a text overlay to every page of the merged output
- **Tokens:** CONFIDENTIAL, project name, date, page numbers — combinable freely
- **Position:** Footer / Header / Diagonal watermark
- **Colour:** Black, Red, or custom
- Stamp settings saved to the `.docrivet` project file

#### Output Tab

- **3-column layout** — Destination (left), Options (centre), Metadata (right)
- **Destination** — choose output folder via Browse; set filename; toggle "Open after merge"
- **Review card** — live summary: file count, total pages, combined source size, estimated output size, redaction mark count, OCR-needed page count, free disk space
- **OCR options** — "OCR searchable layer" checkbox; "Set custom OCR language" checkbox gates the language dropdown; unchecked = English, not persisted
- **Page size** — "Set custom page size" checkbox gates the A4/Letter/Legal dropdown; unchecked = Native
- **Metadata** — "Clean metadata" strips Title, Author, Creator from output (on by default, recommended for external sharing); when off, edit all five fields inline
- **Atomic write** — output is written to a `.tmp` staging file then renamed; no partial file on failure or cancellation

#### Project Save / Load

- `.docrivet` files (plain JSON) capture the complete session: file list, check states, page selections, redaction zones, reason codes, audit log, output path, OCR language, page size choice, and stamp settings
- Reopen a project days later and pick up exactly where you left off
- **New Project** resets every subsystem: file list, redactions, audit log, output filename, options, and metadata

#### License Management

- **PRO badge** always visible in the title bar — gold when a Premium license is active, grey when Standard or expired
- Click the badge to open the license dialog and enter or renew a key
- **Key format:** `DRPREM-…` prefix, HMAC-SHA256 signed; mistyped characters normalized (`0→O`, `1→I`, `8→B`) before validation
- License stored at `%APPDATA%\DocRivet\license.key`; never transmitted

#### Themes

- **Parchment** (light), **Lights Out** (dark), **Slate** — switchable at runtime via the title bar picker
- Colour swatch previews in the theme picker
- Theme preference persisted across sessions

#### Keyboard Shortcuts

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

#### Help & Website

- **ⓘ button** in the title bar (between Project and PRO) opens `docrivet.com` in the default browser
- **F1 / Ctrl+?** opens the About dialog with version and license status

### 🔧 Bug Fix

- **OCR crash on degenerate text blocks** — Tesseract occasionally returns blocks with zero-size or infinite bounding coordinates; `insert_textbox` raised `ValueError: text box must be finite and not empty`. The `fitz.Rect()` construction is now inside the per-block try, and empty/infinite rects are skipped before reaching `insert_textbox`. OCR completes on all pages; affected blocks are silently skipped.

### 🧪 Tests

- 264 passing, 1 skipped (UI headless skip on CI)
- Full coverage: merge engine, redaction service, OCR service, license service (35 tests), search service, audit log, output tab state (24 UI tests), checked listbox, smoke tests

---

*Earlier version history is available on request.*

**Bugs / feedback:** https://github.com/agissimo/DocRivet/issues
