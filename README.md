# DocRivet

**Assemble, redact, and publish multi-source PDF packages — entirely offline.**

No cloud upload. No subscription required. No installer — just run the EXE.

---

## Download

| Version | File | Size |
|---------|------|------|
| **3.9.5** (latest) | [DocRivet-v3.9.5.exe](DocRivet-v3.9.5.exe) | 60.6 MB |
| 3.9.0 | [DocRivet-v3.9.0.exe](DocRivet-v3.9.0.exe) | 60.5 MB |
| 3.7.0 | [DocRivet-v3.7.0.exe](DocRivet-v3.7.0.exe) | 60.6 MB |
| 3.6.0 | [DocRivet-v3.6.0.exe](DocRivet-v3.6.0.exe) | 60.6 MB |

Windows 10/11 · No installer · No admin rights required

---

## What It Does

DocRivet takes any mix of PDFs, scanned images, and office documents and merges them into a single output file — redacted, normalized, OCR-searchable, and PII-scanned — without any file leaving your machine.

### Document Assembly (Standard — free)
- Merge any number of PDFs and images into one output file
- Import JPG, PNG, TIFF (multi-page), GIF, WebP, BMP — converted to PDF pages automatically
- Recursive folder import; drag-and-drop from Explorer
- Per-source page selection — pick individual pages or ranges from each file
- Reorder sources by drag or Alt+↑ / Alt+↓
- Password-protected PDF support
- PDF Split & Extract — extract pages or split into one-per-page files
- Project save / load (`.docrivet` files) — resume any session exactly where you left off

### Premium Features (license key)
- **PII Auto-Redact** — scan all loaded files for SSNs, credit cards, IBANs, SINs, phone numbers, and 20+ other patterns; review each match; commit approved ones to the redaction layer in one click. All local, pure regex, no ML.
- **Manual Redaction** — draw rectangles over any sensitive region; forensically burned into the merged output at the PDF content-stream level; source files never modified
- **Search & Redact** — full-text search across all files; mark all matches for redaction in one step
- **OCR Searchable Layer** — Tesseract bundled; 18 languages; invisible text layer added to scanned pages
- **Redaction Audit Log** — every redaction logged (file, page, coordinates, reason code, timestamp); CSV exported with each merge
- **Reason Codes** — FOIA Exemption, HIPAA PHI, Attorney-Client Privilege, and more
- **Stamp / Watermark** — CONFIDENTIAL, project name, date, page numbers; footer / header / diagonal
- **Compress Output** — maximum PDF deflate compression as a post-merge step
- **Page Normalization** — unify mixed page sizes to A4, Letter, or Legal

---

## Screenshots

| Files | Redact | Search | Output |
|-------|--------|--------|--------|
| ![Files tab](screenshots/01%20DocRivet-FilesPage.png) | ![Redact tab](screenshots/02%20DocRivet-Redact.png) | ![Search tab](screenshots/03%20DocRivet-Search.png) | ![Output tab](screenshots/04%20DocRivet-Output.png) |

---

## Quick Start

See [QUICKSTART.md](QUICKSTART.md) for step-by-step workflows.

---

## Editions

| Feature | Standard | Premium |
|---------|----------|---------|
| Merge PDFs & images | ✓ | ✓ |
| Page selection & reorder | ✓ | ✓ |
| PDF Split & Extract | ✓ | ✓ |
| Project save / load | ✓ | ✓ |
| Manual redaction (draw) | ✓ | ✓ |
| PII auto-redact (regex scan) | — | ✓ |
| Search & auto-redact | — | ✓ |
| OCR searchable layer (18 languages) | — | ✓ |
| Redaction audit log + CSV | — | ✓ |
| Reason codes (FOIA / HIPAA / etc.) | — | ✓ |
| Stamp / watermark | — | ✓ |
| Compress output PDF | — | ✓ |
| Page normalization (A4 / Letter / Legal) | — | ✓ |

To upgrade: click the **PRO** badge in the title bar → enter your license key.

---

## Privacy

- All processing is local — no data ever leaves your machine
- No internet connection required (Tesseract OCR is bundled in the EXE)
- License key is stored at `%APPDATA%\DocRivet\license.key` and never transmitted
- PII scan runs entirely in-process using Python `re` — no cloud API, no ML model

---

## Release Notes

See [RELEASE_NOTES.md](RELEASE_NOTES.md) for the full feature list and version history.

---

## Support

- **Issues / feedback:** https://github.com/agissimo/DocRivet-Releases/issues
- **Website:** https://docrivet.com
