# Release Notes - DocRivet

## Version 3.5.0
**Release Date:** 2026-05-20
**Status:** Latest Stable
**Editions:** Standard (free) + Premium (license key)

### ✨ Premium Feature Gating & UI Polish

#### Premium Gating — Redact & Search
- Redact tab now shows a **PRO chip** in the tab header for non-premium users; all palette tools (Select, Rectangle, Mark Text, Eraser) are disabled
- Search tab entry, navigation buttons, and Mark All are disabled for non-premium users
- Canvas drawing is blocked for non-premium users — no accidental redaction rectangles can be drawn

#### Theme Consistency Fixes
- Redact tab preview canvas background now correctly follows the active theme (cool grey `surface_2`), matching the Files tab
- "FILES WITH MARKS" scroll panel in the Redact tab no longer retains the previous theme's background color after a theme switch
- Search tab match list panel has the same fix — no stale background after Parchment ↔ Slate switch

#### UI Cleanup
- PRO chip removed from the status bar (bottom-right) — the title bar PRO button already signals premium status
- Search tab PRO chip suppressed for premium users; sub-text updated from "Premium" to "0 results" when premium is active
- Redact tab PRO chip suppressed for premium users

---

## Version 3.4.1
**Release Date:** 2026-05-20
**Status:** Latest Stable
**Editions:** Standard (free) + Premium (license key)

### 🔧 Bug Fixes

#### OCR-on-Add — Duplicate File Deduplication
Folders containing a mix of original PDFs and their previously OCR'd copies (`_ocr_YYYY-MM-DD_HH-MM.pdf`) could result in multiple variants of the same source appearing in the file list simultaneously. This was visible when re-adding a folder after OCR-on-add had already run, or when opening a project that had accumulated several OCR passes.

- **Batch dedup** — when a folder add (or project load) delivers both `foo.pdf` and one or more `foo_ocr_….pdf` files in the same batch, only the preferred variant is kept: the most-recent `_ocr_` copy when OCR mode is on; the original when OCR mode is off
- **OCR fast-path** — if a fresh `_ocr_` sibling (< 24 h) already exists on disk for a source file, it is added directly instead of re-running Tesseract; applies to both interactive adds and folder adds
- **No redundant writes** — `_ocr_and_add` re-checks for an existing sibling at worker-thread start before writing a new timestamped copy; concurrent jobs that finish while another is queued no longer produce a second file
- **Project load dedup** — `_load_project` filters the `source_files` list before adding to the UI, keeping only the most-recent `_ocr_` copy per source stem+directory pair
- **Ingest guard** — a secondary per-stem filter in `add_files` and `_accept_bg_batch` catches any `_ocr_` copies whose source is already present in the current project list

No source files are modified. No on-disk OCR copies are deleted. Dedup is purely a display/ingest concern.

---

## Version 3.4.0
**Release Date:** 2026-05-17
**Status:** Latest Stable
**Editions:** Standard (free) + Premium (license key)

### ✨ New Features

#### PDF Split & Extract (Files Tab)
- **Extract Pages** — select any PDF in the file list and click the new crop icon to extract chosen pages into a new PDF file
- **Split into Pages** — click the layers icon to write one PDF per page (or custom ranges) into a chosen output folder
- New `pdf_split_service` backs both operations with the same atomic-write + cancel/progress protocol used by the merge engine
- Split/Extract toolbar buttons enable only when a PDF is selected; disabled for images
- Both operations offer a progress bar, cancellable mid-run, and a status message in the main window
- Extracted files can optionally be added back to the file list immediately after creation

#### Compress Output PDF ★ Premium
- New **"Compress output PDF"** checkbox in the Output tab → PDF Tools section
- Applies maximum fitz deflate compression (`garbage=4, deflate, deflate_images, deflate_fonts`) to the merged output as a post-merge step
- Falls back to a `-compressed` sibling file if the original is locked by a viewer
- Setting persisted to `.docrivet` preferences across sessions

#### OCR Concurrency — Semaphore-Throttled
- OCR-on-add now runs through a module-level semaphore (max 2 concurrent Tesseract jobs) to prevent CPU thrash on multi-file drops
- Live status updates: "OCR 2/5: filename.pdf…" and "OCR: 3 files remaining…" appear in the main window status bar
- PDFs are fast-checked on the main thread (first-page text scan) before deciding whether to queue OCR, eliminating unnecessary background jobs for PDFs that already have a text layer

### 🔧 Improvements

- **Toolbar tooltips** — all Files-tab toolbar buttons now show hover tooltips via `_bind_tooltip`
- **theme.py lazy CTk import** — `customtkinter` is no longer imported at module level in `theme.py`; deferred to first use to speed up cold-start and allow headless test imports
- **Cleaner threading imports** — `threading` imported once at the top of `files_tab.py`; removed stale per-call `import threading as _threading`
- **atoms.py style cleanup** — multi-statement lines split for readability; no behaviour change
- **`_pdf_needs_ocr_fast` helper** — checks only page 1 for a text layer; fast enough for the main thread; false negatives (text on later pages only) are acceptable

### 🧪 Tests

- New `tests/test_pdf_split_service.py` covering `extract_pages`, `split_to_pages`, and `compress_pdf`

---

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

## Version 3.2.0
**Release Date:** 2026-05-15
**Status:** Latest Stable
**Editions:** Standard (free) + Premium (paid, license key)

### ✨ New Features

#### Output Tab — 3-Column Redesign
- Equal-width 3-column layout (Destination | Options | Metadata) with dynamic resize
- Review panel moved to left column below Destination; "Open after merge" moved under filename
- Metadata section now inline in right column (no popup dialog)
- Custom Metadata fields hidden automatically when "Clean metadata" is checked
- "STAMP / WATERMARK" sub-panel heading added to Options column

#### OCR & Page Size — Checkbox-Gated Dropdowns
- "Set custom OCR language" checkbox gates the language dropdown; unchecked = English default, not persisted
- "Set custom page size" checkbox gates the size dropdown; unchecked shows "Native" in a disabled menu
- Both custom selections saved to `.docrivet` project file and restored on project load
- New/unnamed projects always reset to defaults (English, Native) regardless of last session prefs

#### License UX — PRO Badge Always Visible
- PRO badge in the title bar now renders even when the license is expired or absent
- Expired: badge is muted grey (still clickable to open the license dialog and enter a new key)
- Active: badge is gold as before
- Dedicated theme recolor hook `license_pill_inactive` for consistent look across all three themes

#### License Key Validation Hardening
- `_KEY_RE` regex pre-validates key shape (prefix `DRPREM`/`DRBASIC`, 35–60 base32 groups) before any decoding — eliminates raw `binascii.Error` crashes from malformed input
- Mistyped-character normalization: `0→O`, `1→I`, `8→B` so users who transcribe keys manually aren't blocked by lookalike digits

### 🔧 Bug Fixes

1. **Leptonica thread-safety crash** — concurrent OCR on multiple files triggered `Attempt to use Leptonica from 2 threads at once`; fixed with a module-level `threading.Lock()` serializing all `get_textpage_ocr()` calls
2. **New Project didn't clear all state** — redactions, audit log, tab metadata, output filename, and options were not reset; `_new_project()` now clears every subsystem
3. **Tab strip clipped at 130–150% font scale** — fixed height + `pack_propagate(False)` prevented the strip from expanding; removed both constraints and increased inner padding
4. **Checkbox "red filler" blocks** — `CTkCheckBox` with `text=""` defaulted to ~200px minimum width, rendering as a colored block; fixed with `width=0` on all three silent checkboxes
5. **"Native" truncated in page-size dropdown** — width was 100px; increased to 120px
6. **OCR language defaulting to last session on project load** — `set_options` now always resets `_lang_var` to "English (eng)" when the custom flag is absent in project JSON

### 🧪 Testing

- `TestExpiredLicenseReactivation` — 6 new tests: expired key → `is_premium()` False; tier preserved on expired validation; new valid key installed over expired restores premium; `reload_license()` after key file replaced; `install_key()` rejects expired keys without touching disk
- All 35 license service tests pass

### 🖥️ UI / UX

- Windows shortcut hints throughout (`Ctrl+O`, `Ctrl+Shift+O`, `Ctrl+M`) replacing Mac symbols
- "OCR on add" defaults to checked for Premium users
- Explainer text left-aligned throughout Output tab (no fixed `wraplength`)

---

## Version 2.7.0 (Unreleased — feature/F3-F4-ocr-search-redact)
**Status:** In development — uncommitted  
**Editions:** Standard (free) + Premium (paid, license key)

### Standard vs. Premium split
- Introduced two-edition build pipeline: `basic.spec` (Standard) and `premium.spec` (Premium)
- `build_windows.ps1` gains `-Edition basic|premium` parameter
- `license_service.py` — HMAC-SHA256 signed license keys, stored at `%APPDATA%\DocRivet\license.key`
- **Help → Enter License Key…** dialog to validate and install keys
- Startup warning dialog when a Premium license has expired

### Premium features (gated)
- **OCR Searchable Layer** (F3) — Tesseract via PyMuPDF, bundled in Premium EXE, checkbox in toolbar/menu
- **OCR on Add** — auto-OCR files in background as they are added to the list
- **Search & Redact panel** (F4) — text-search across all loaded files, highlight matches, Mark All to bulk-redact
- **Redaction reason codes** (F5) — FOIA, HIPAA, Attorney-Client Privilege, etc. in toolbar combobox
- **Redaction audit log** (F1) — auto-saved `.csv` alongside merged PDF on every merge

### Standard behaviour when Premium modules absent
All three Premium service modules (`ocr_service`, `search_service`, `audit_log_service`) are excluded from the Standard bundle. `main_form.py` handles `ImportError` with no-op stubs so the app runs cleanly without them.

### Other UI improvements
- Menu bar reorganised: Project / Files / File / Page / Merge / Help
- `_select_files` now adds files to existing list (no longer clears on each call)
- `_add_file` helper removed (superseded by `_select_files`)
- Page menu: Select All Pages / Deselect All Pages

---

## Version 2.5.0 (Current)
**Release Date**: 2026-05-10
**Status**: Latest Stable
**Note**: Product renamed from *PDF Merger* to *DocRivet* in this release.

### ✨ New Features

#### PDF Redaction (Content Sanitization)
- **Draw redaction rectangles** directly on the page preview — click and drag to mark sensitive regions (amounts, rates, names, signatures)
- **Forensically irreversible** — content is destroyed at the PDF content-stream level via PyMuPDF, not hidden by a visual overlay; old bytes are purged with `garbage=4`
- **Right-click to clear** all redaction marks on the current page
- **Persisted in projects** — redaction regions are saved to and restored from `.docrivet` project files
- **Applied at merge time** — original source files are never modified; redactions burn in only into the merged output
- **Image file redaction** — redaction now works on image inputs (JPG, PNG, TIFF, etc.); images are converted to PDF in memory via fitz, redactions burned in, then fed to pypdf. Multi-page TIFFs handled naturally.

#### Encrypted PDF Robustness
- **AES-encrypted PDF fix** — pypdf's lazy AES decryption silently corrupts deep object references on some PDFs, causing "not a multiple of the block length" errors; all encrypted PDFs are now linearised through fitz (which decrypts correctly) before pypdf processes them
- Validated against 24 real-world PDFs including DocuSign, FD Technologies, and Oracle-generated encrypted files

### 🔧 Bug Fixes

1. **AES block-length merge error** (Critical) — encrypted PDFs that passed `decrypt()` but failed during page cloning now route through fitz automatically
2. **Page selection false positive** (High) — empty page list (= all pages selected) was incorrectly triggering "No pages selected" warning
3. **Redaction coordinate guards** — zero-size or out-of-bounds rectangles are stripped before merge to prevent fitz errors
4. **Output file locked by viewer** (Windows) — merging over a PDF already open in a viewer now shows a friendly "close the file first" message instead of a raw OS error
5. **Redaction silently skipped on Windows** — tkinter's file dialog returns forward-slash paths (`Z:/file.pdf`) while `Path()` normalises to backslashes; path keys are now normalised at storage time so redaction lookups always match

### 📊 Testing

- **59/59 tests passing** (up from 58 — pre-existing regex mismatch in encryption test also fixed)
- New test class `TestMergeFilesRedactions` — 3 tests covering redaction application, isolation, and no-op behaviour
- New `TestRedactionService` — 6 tests covering text removal, garbage collection, invalid inputs

### 🔄 Migration & Compatibility

**Backward Compatible**: All v2.4.0 projects load without changes.
- New `redactions` field in `.docrivet` format — old projects without it load cleanly (defaults to no redactions)
- Source files are **never modified** — safe to use on read-only or shared drives

---

## Version 2.4.0 (Previous)
**Release Date**: 2025-05-09
**Status**: Superseded by 2.5.0

### ✨ New Features

#### Robustness & Safety
- **File-Size Constraints**: Per-file and total merge size gates prevent memory exhaustion
  - Warning at 50 MB per individual file
  - Hard error at 500 MB per file
  - Confirmation dialog at 500 MB total input
- **Disk Space Pre-Check**: Verifies sufficient free disk space before merge (1.5× safety margin)
- **Atomic Write Safety**: Output written to `.tmp` staging file, then atomically renamed to prevent partial files on crash/failure

#### Error Handling & UX
- **Silent Failure Prevention**: Project save now shows error dialog on write failures (disk full, permission denied, etc.)
- **File Read Error Messages**: Corrupt or unreadable files now show friendly error dialogs instead of failing silently
- **Page Selection Validation**: Warns user if a file has all pages deselected before merge (prevents silent data loss)
- **Better Exception Messages**: Improved error context for file I/O and password-related failures

#### Performance Testing Infrastructure
- **Stress Test Suite**: New `test_performance.py` with 30+ tests covering capacity, memory, cancellation, and encryption
- **Performance Fixtures**: Extended test utilities (encrypted PDFs, image files, memory profiling)
- **Pytest Slow Marker**: Long-running tests marked with `@pytest.mark.slow` (skip with `-m 'not slow'`)
- **Memory Profiling**: Tests verify memory stays under 500 MB for 50×10-page merges

### 🔧 Technical Changes

#### Bug Fixes
1. **Page-Deselect Inversion** (Critical): Deselecting all pages on a file now warns + blocks merge (previously silently included all pages)
2. **Silent Save Failures** (High): Project save exceptions now caught and displayed to user
3. **Hard fitz Import Crash** (High): Made PyMuPDF import optional; app no longer crashes on startup if fitz unavailable
4. **Thread-Unsafe Tkinter Access** (Medium): Snapshotted `_fit_images_var` on main thread before passing to background worker
5. **Swallowed File Read Errors** (Medium): Password-check errors now reported; corrupted files no longer silently added

#### Code Quality
- **Thread Safety**: Eliminated Tkinter widget access from background threads (unsafe on non-CPython)
- **Exception Handling**: Improved granularity (specific error messages vs. silent passes)
- **Resource Cleanup**: Atomic write prevents orphaned `.tmp` files on merge failure
- **Constraint Validation**: File-size gates prevent exhaustion of system memory/disk

### 📊 Testing Improvements

**New Test Classes**:
- `TestMergeCapacity` — 10, 50, 100 file merges; 200-page single document; 50×10 realistic scenario
- `TestMergeCancellation` — Verify cancellation prevents partial output
- `TestMergeProgress` — Percent reporter reaches 1.0 monotonically; per-file status messages
- `TestMergePageSelections` — Page selection reduces output; empty list includes all; out-of-range discarded
- `TestMergeMemory` — tracemalloc peak <500 MB; no memory leak across sequential merges
- `TestMergeEncryption` — Encrypted PDFs with correct/wrong/missing passwords

**Coverage**: 33 existing tests (validation, output, progress) + 30+ performance tests.

### 🔄 Migration & Compatibility

**Backward Compatible**: All v2.3.0 projects load without changes. Behavior changes:
- Deselecting all pages now warns instead of silently including all pages — **intentional safety improvement**
- Merges > 500 MB show confirmation dialog (can still proceed)
- Low-disk situations now show friendly warning instead of raw OS error

### 📝 Known Limitations (Unchanged from 2.3.0)

- Image-to-PDF conversion fully materialises in memory (no streaming)
- Each PDF opened twice (pre-flight validation + merge) — double I/O
- Folder recursion UI on main thread (no cancellation for large directory scans)
- DnD folder drop non-recursive (only root-level files, vs. Add Folder which recurses)

---

## Version 2.3.0 (Previous)
**Release Date**: 2025-02-xx  
**Status**: Latest Stable

### ✨ New Features

#### Desktop Application
- **Application Icon**: Added professional app icon for Windows taskbar and desktop
- **Desktop Integration**: Full Windows integration with icon bitmap support
- **Android Parity**: Consolidated feature set between desktop and mobile versions

### 🎨 UX Improvements

- Improved UI responsiveness and visual consistency
- Better error messaging and user feedback
- Enhanced icon visibility in taskbar and shortcuts
- Improved window appearance on Windows platforms

### 🔧 Technical Changes

#### Build & Deployment
- **Android Pipeline**: Hardened build pipeline for reliable APK generation
- **Build Stability**: Improved reliability of Android builds with better error handling
- **Separated Implementations**: 
  - Desktop (Python/Tkinter) confirmed as primary target
  - Android (Python/Kivy) maintained as separate, stable implementation
  - Retired C# legacy implementation

#### Codebase
- Promoted Python as the sole implementation language
- Removed C# codebase (legacy Windows Forms version)
- Consolidated mobile and desktop build pipelines
- Improved CI/CD for both platforms

### 🐛 Bug Fixes

- Fixed icon loading on Windows systems
- Improved window geometry handling
- Enhanced desktop/Android compatibility layer

### 📦 Dependencies

**Core Requirements**:
- Python 3.8+
- tkinter (stdlib)
- pypdf (PDF manipulation)

**Optional**:
- pymupdf (fitz) — Image support, thumbnails, preview
- tkinterdnd2 — Drag-drop file support

### ⚠️ Breaking Changes

**For C# Users**: The C# implementation has been retired. All users should transition to the Python desktop version (identical feature set, better maintenance).

### 🔄 Migration Guide

If upgrading from v2.2.x:
1. Download and extract v2.3.0
2. No data migration needed — .docrivet project files are compatible
3. Existing projects load without changes

---

## Version 2.2.x (Previous)
**Status**: Superseded by 2.3.0

### Features
- Core PDF merging with page selection
- Image embedding (PNG, JPG, GIF, TIFF, WebP, etc.)
- Password-protected PDF support
- Metadata editing (title, author, subject, keywords, creator)
- Project save/load (.docrivet format)
- Drag-drop file support
- Page preview and thumbnail selection
- Batch operations
- Multiple image fitting modes (Native, A4, Letter, Legal)

### Architecture
- Python 3 + Tkinter (desktop)
- Python + Kivy (mobile)
- Separate implementations (no shared source)

---

## Version 2.1.x (Legacy)
**Status**: Obsolete

### Changes from 2.0
- Improved metadata dialog
- Better page selection UI
- Enhanced error messages

---

## Version 2.0.x (Legacy)
**Status**: Obsolete

### Features
- Initial cross-platform PDF merger
- Basic file list management
- Simple merge operation

---

**Releases / downloads:** https://github.com/agissimo/DocRivet-Releases/releases  
**Bugs / feedback:** https://github.com/agissimo/DocRivet-Releases/issues  
**Roadmap:** [06 PRODUCT_ROADMAP.md](06%20PRODUCT_ROADMAP.md)
