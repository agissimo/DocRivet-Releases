# Release Notes - DocRivet

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
