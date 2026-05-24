# DocRivet — Quick Start Guide

**Version 3.6.0**

Get up and running in 5 minutes.

---

## Quick Navigation

1. [Interface at a Glance](#interface-at-a-glance)
2. [Merge Multiple PDFs](#workflow-1-merge-multiple-pdfs) ⭐ Most Common
3. [Extract & Combine Pages](#workflow-2-extract--combine-pages)
4. [Add Scanned Images](#workflow-3-add-scanned-images-to-pdf)
5. [Redact Sensitive Content](#workflow-4-redact-sensitive-content--visual-walk-through)
6. [PII Auto-Redact](#workflow-5-pii-auto-redact-premium) ★ Premium
7. [Search & Redact](#workflow-6-search--redact-premium) ★ Premium
8. [OCR Searchable Output](#workflow-7-ocr-searchable-output-premium) ★ Premium

---

## Editions at a Glance

| Feature | Standard | Premium |
|---------|----------|---------|
| Merge PDFs & images | ✓ | ✓ |
| Page selection | ✓ | ✓ |
| PDF Split & Extract | ✓ | ✓ |
| Manual redaction (draw) | ✓ | ✓ |
| Project save / load | ✓ | ✓ |
| PII auto-redact (regex scan) | — | ✓ |
| OCR searchable layer | — | ✓ |
| Search & auto-redact | — | ✓ |
| Redaction audit log | — | ✓ |

Check your edition: **Help → About**.
Upgrade or enter a key: click the **PRO** badge in the title bar.

---

## Installation

### Windows (standalone)
Double-click `DocRivet-v3.6.0.exe` — no setup needed.

### From Python source
```
python -m docrivet.main
```
Requires Python 3.10+. Optional extras: `pip install pymupdf tkinterdnd2`

---

## Interface at a Glance

DocRivet is a 4-tab workspace:

```
┌─────────────────────────────────────────────────────────┐
│  DocRivet          [Parchment ▾]   [Project]  [PRO] [Merge ▶]
├──────────────────────────────────────────────────────────┤
│  [Files]   [Redact]   [Search]   [Output]               │
├──────────────────────────────────────────────────────────┤
│                                                          │
│   (tab content here)                                     │
│                                                          │
├──────────────────────────────────────────────────────────┤
│  Status bar                                              │
└──────────────────────────────────────────────────────────┘
```

| Tab | What you do there |
|-----|-------------------|
| **Files** | Add, reorder, and preview source files |
| **Redact** | Draw redaction rectangles or run a PII auto-scan |
| **Search** ★ | Find text across all files; mark matches for redaction |
| **Output** | Set destination, options (OCR, clean metadata), and merge |

**Title bar controls**
- Theme picker (Parchment / Lights Out / Slate)
- **Project** — save / load a `.docrivet` session file
- **PRO** pill — shows your edition; click to manage license
- **Merge ▶** — always visible; kicks off the merge from any tab

**Font zoom**: Ctrl+  /  Ctrl−  /  Ctrl+0 (75%–200%, persisted across sessions)

---

## Workflow 1: Merge Multiple PDFs

1. Go to the **Files** tab → click **Add Files** (or drag files onto the list)
2. Your PDFs appear checked ✓ — drag rows to reorder if needed
3. Go to **Output** tab → confirm destination path → click **Merge ▶**

Done. The merged PDF opens automatically if "Open after merge" is checked.

---

## Workflow 2: Extract & Combine Pages

1. **Files** tab → add a PDF
2. Click the file → the page strip appears; click **None** to deselect all, then click individual thumbnails to select only the pages you want
3. **Output** tab → **Merge ▶**

---

## Workflow 3: Add Scanned Images to PDF

1. **Files** tab → add both PDFs and image files (PNG / JPG / TIFF / etc.) in order
2. In the Output options, choose an **Image pages** size (A4, Letter, Legal, or Native)
3. **Merge ▶** — images are converted to PDF pages automatically

---

## Workflow 4: Redact Sensitive Content — Visual Walk-Through

Redaction permanently destroys content at the PDF content-stream level. Source files are never modified. The burn happens only in the merged output.

### Step 1 — Open the Redact tab, select a file

```
┌───────────────┬─────────────────────────┬──────────────┐
│ Files list    │ Preview                 │ Audit log    │
├───────────────┼─────────────────────────┼──────────────┤
│ ▶ report.pdf  │   REPORT Q4 2024        │ (empty)      │
│   invoice.pdf │                         │              │
│               │   Revenue: $5,240,000   │              │
│               │   Margin:  18.5%        │              │
│               │   Client:  Acme Corp    │              │
└───────────────┴─────────────────────────┴──────────────┘
```

The palette is in **MANUAL** mode by default. Click a file in the left panel. Navigate pages with ◄ ►.

### Step 2 — Draw a rectangle (mouse down + drag)

A live dashed outline follows your mouse. Release to commit. The rectangle turns solid black — that region will be permanently destroyed in the merged output.

### Step 3 — Merge burns them in permanently

Go to the **Output** tab → **Merge ▶**. The output dialog confirms which regions were applied.

**Key rules**
- Right-click the preview → **Clear page** removes all marks on that page only
- Marks are saved in `.docrivet` project files and survive close/reopen

---

## Workflow 5: PII Auto-Redact ★ Premium

Automatically find SSNs, credit card numbers, IBANs, SINs, phone numbers, and 20+ other patterns across all loaded files, review each hit, and commit approved ones in one click.

### Step 1 — Switch to AUTO mode

In the Redact tab, click the **AUTO** toggle at the top of the palette (next to MANUAL).

### Step 2 — Select patterns

Check the jurisdictions and pattern categories you want to scan for. US, CA, and Universal are available in V1. Selections persist across MANUAL/AUTO toggles within the session.

### Step 3 — Scan

Click **Scan**. A background thread processes all loaded files; per-file progress shows in the status bar. Click **Cancel** at any time to stop.

### Step 4 — Review matches

Each match appears as a row: category chip · matched text · filename + page. Click any row to jump to that location in the preview. Staged matches show as orange dashed outlines on the canvas.

### Step 5 — Select and commit

Check or uncheck individual matches. Use **Mark All** / **Clear All** for bulk selection. Click **Commit** — approved rects are written to the redaction layer, the audit log is updated, and the palette returns to MANUAL mode.

### Step 6 — Merge

**Merge ▶** burns all committed PII redactions into the output, alongside any manual marks.

**Tips**
- Re-scanning the same files is safe — duplicate marks and audit entries are never created
- Date of Birth is opt-in (unchecked by default) — high false-positive risk on non-PHI dates
- Committed marks are solid red; pending (staged) marks are orange dashed

---

## Workflow 6: Search & Redact ★ Premium

Find every occurrence of a term across all loaded files and redact them all in one step.

1. Go to the **Search** tab (tab 3) → type a term (e.g. `CONFIDENTIAL`, `Acme Corp`)
2. Click **Search** — all matches highlight in the preview; use ↑ ↓ to step through them
3. Click **Mark All for Redact** to queue every match as a redaction region
4. **Merge ▶** — every occurrence is burned out of the output

Tip: works on text-layer PDFs. For scanned sources, enable OCR first (see Workflow 7).

---

## Workflow 7: OCR Searchable Output ★ Premium

Turn a scan-only PDF into one you can search and copy text from.

1. **Output** tab → check **OCR searchable** before merging
2. **Merge ▶** — a hidden text layer is added to every scanned page in the output

Optional: check **OCR on add** to process each file as it's added to the list, so you can verify the text layer in the preview before merging.

---

## Common Controls

| Control | What it does |
|---------|-------------|
| **Add Files / Add Folder** | Add individual files or all files in a folder |
| Drag rows in Files tab | Reorder merge order |
| **Select All / None / Invert** | Bulk check state |
| **Remove** / Delete key | Remove file from list |
| **Clean metadata** (Output tab) | Strip author/title/creator from output (default ON) |
| **Open after merge** | Auto-open output PDF when done |

---

## Keyboard Shortcuts

| Shortcut | Action |
|----------|--------|
| Ctrl+O | Add files |
| Ctrl+Shift+O | Add folder |
| Ctrl+S | Save project |
| Ctrl+N | New session |
| Ctrl+M | Merge |
| Ctrl+F | Switch to Search tab |
| Ctrl+1–4 | Switch tabs (Files/Redact/Search/Output) |
| Alt+↑ / Alt+↓ | Move file up / down |
| Ctrl++ / Ctrl+− / Ctrl+0 | Zoom font in / out / reset |
| Delete | Remove selected file |

---

## Before You Merge — Checklist

- [ ] At least one file **checked** ✓
- [ ] Files in the **correct order**
- [ ] **Output path** set (Output tab → Browse)
- [ ] Encrypted PDFs: **passwords entered**
- [ ] Page selections: **at least one page per file**
- [ ] Sensitive regions: **redacted** (manual draw or PII auto-redact or Search & Redact)

---

## Need More Help?

- **Full release notes**: [RELEASE_NOTES.md](RELEASE_NOTES.md)
- **Website**: https://docrivet.com
- **Issues / Feedback**: https://github.com/agissimo/DocRivet-Releases/issues
