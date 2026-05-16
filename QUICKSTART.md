# DocRivet — Quick Start Guide

**Version 3.2.0**

Get up and running in 5 minutes.

---

## Quick Navigation

1. [Interface at a Glance](#interface-at-a-glance)
2. [Merge Multiple PDFs](#workflow-1-merge-multiple-pdfs) ⭐ Most Common
3. [Extract & Combine Pages](#workflow-2-extract--combine-pages)
4. [Add Scanned Images](#workflow-3-add-scanned-images-to-pdf)
5. [Redact Sensitive Content](#workflow-4-redact-sensitive-content--visual-walk-through)
6. [Search & Redact](#workflow-5-search--redact-premium) ★ Premium
7. [OCR Searchable Output](#workflow-6-ocr-searchable-output-premium) ★ Premium

---

## Editions at a Glance

| Feature | Standard | Premium |
|---------|----------|---------|
| Merge PDFs & images | ✓ | ✓ |
| Page selection | ✓ | ✓ |
| Manual redaction (draw) | ✓ | ✓ |
| Project save / load | ✓ | ✓ |
| OCR searchable layer | — | ✓ |
| Search & auto-redact | — | ✓ |
| Redaction audit log | — | ✓ |

Check your edition: **Help → About**.  
Upgrade or enter a key: **Help → Enter License Key…**

---

## Installation

### Windows (standalone)
Double-click `DocRivet.exe` — no setup needed.

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
| **Redact** | Draw redaction rectangles on any page |
| **Search** ★ | Find text across all files; mark matches for redaction |
| **Output** | Set destination, options (OCR, clean metadata), and merge |

**Title bar controls**
- Theme picker (Parchment / Lights Out / Slate)
- **Project** — save / load a `.docrivet` session file
- **PRO** pill — shows your edition; click to manage license
- **Merge ▶** — always visible; kicks off the merge from any tab

**Font zoom**: Ctrl+  /  Ctrl−  /  Ctrl+0 (75 %–200 %, persisted across sessions)

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
│   budget.pdf  │   Revenue: $5,240,000   │              │
│               │   Margin:  18.5%        │              │
│               │   Client:  Acme Corp    │              │
│               │                         │              │
│               │  Hint: click-drag to    │              │
│               │  redact. Right-click    │              │
│               │  to clear page.         │              │
└───────────────┴─────────────────────────┴──────────────┘
Status: Ready to redact — report.pdf, Page 1/3
```

Click a file in the left panel. The preview loads on the right. Navigate pages with ◄ ►.

### Step 2 — Draw a rectangle (mouse down + drag)

```
│               │   Revenue: $5,240,000   │
│               │   ┌ ╌ ╌ ╌ ╌ ╌ ╌ ╌ ┐    │  ← dashed red
│               │   ╌  Margin: 18.5% ╌    │    rubber-band
│               │   └ ╌ ╌ ╌ ╌ ╌ ╌ ╌ ┘    │
│               │   Client:  Acme Corp    │
```

A live dashed outline follows your mouse. Release to commit.

### Step 3 — Rectangle commits (solid black bar)

```
│               │   Revenue: $5,240,000   │
│               │   ┌─────────────────┐   │
│               │   │█████████████████│   │  ← permanent in output
│               │   │█████████████████│   │
│               │   └─────────────────┘   │
│               │   Client:  Acme Corp    │
│               │                         │
│               │  1 redaction this page  │
```

Draw again to add more. Right-click the preview → **Clear redactions on this page** to undo all marks on the current page.

### Step 4 — Redactions persist as you navigate

Switch pages or files freely. Each page remembers its own set of redaction marks. The audit log panel (Premium) records every action with timestamp and reason code.

### Step 5 — Merge burns them in permanently

Go to the **Output** tab. The review card shows total redaction count across all files. Click **Merge ▶**. The output dialog confirms:

```
Redactions applied (securely burned):
• report.pdf, page 1: 2 regions  (Margin, Client)
• invoice.pdf, page 2: 1 region  (Amount)

These regions are permanently destroyed and unrecoverable.
Content-stream redaction with garbage collection (level 4).
```

**Key rules**
- Right-click the preview → **Clear page** removes all marks on that page only
- A page can be deselected from merge and still have marks; they are simply skipped
- Marks are saved in `.docrivet` project files and survive close/reopen

---

## Workflow 5: Search & Redact ★ Premium

Find every occurrence of a term across all loaded files and redact them all in one step.

1. Go to the **Search** tab → type a term (e.g. `SSN`, `CONFIDENTIAL`)
2. Click **Search** — all matches highlight in the preview; use ↑ ↓ to step through them
3. Click **Mark All for Redact** to queue every match as a redaction region
4. **Merge ▶** — every occurrence is burned out of the output

Tip: works on text-layer PDFs. For scanned sources, enable OCR first (see Workflow 6).

---

## Workflow 6: OCR Searchable Output ★ Premium

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
| **Show full path** toggle | Filename-only vs. full path in the list |
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
- [ ] Sensitive regions: **redacted** (manual or Search & Redact)

---

## Need More Help?

- **Full User Guide**: [docrivet.com](https://docrivet.com)
- **What's New**: [RELEASE_NOTES.md](RELEASE_NOTES.md)
- **Issues / Feedback**: https://github.com/agissimo/DocRivet/issues
