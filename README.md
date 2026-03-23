# PDF Visual Compare Viewer

A **single-file HTML tool** for visually comparing differences between two PDF files.
No installation required — runs directly in the browser, and files are never sent to any server, making it safe to use with sensitive documents.

---

## Features

| Feature | Description |
|---------|-------------|
| **Pixel Comparison** | Renders both PDFs as images and detects differences at the pixel level |
| **Text Comparison** | Extracts text from each PDF and performs character-level diff using the Myers algorithm |
| **Auto Page Alignment** | Automatically aligns pages to account for insertions and deletions between documents |
| **Changed Pages List** | Sidebar lists only pages where changes were detected |
| **Diff Navigation** | Jump through detected differences in order using Prev/Next buttons |
| **Zoom Control** | Freely adjust page display size with Zoom +/- buttons |
| **Cancel** | Stop an in-progress comparison at any time with the Cancel button |

---

## How to Use

### 1. Open the File

Open `pdf-comparer.html` directly in your browser (Chrome recommended).

```
Double-click pdf-comparer.html → Opens in browser
```

### 2. Select PDF Files

Use the two file inputs in the top toolbar to select your PDFs.

- **Left input** → Base PDF (original / older version)
- **Right input** → Comparison PDF (new / updated version)

Comparison starts automatically once both files are selected.

### 3. Choose a Comparison Mode

| Mode | When to Use |
|------|-------------|
| **Pixel Only** | When you need to detect visual differences — images, layout, fonts, etc. |
| **Text Only** | When you only care about text content changes (faster) |

### 4. Review Results

```
┌───────────────────────────────────────────────────────────────────────┐
│  [File1] [File2] [Mode▼] [Zoom-] [Zoom+] [Prev] [Next] [Re-compare]   │
├────────────────┬──────────────────────────┬───────────────────────────┤
│                │   Left PDF               │   Right PDF               │
│ Changed Pages  │                          │                           │
│                │   Page 1                 │   Page 1                  │
│ • p.3 changed  │                          │                           │
│ • p.5 inserted │   Page 2                 │   Page 2                  │
│                │   [highlighted changes]  │   [highlighted changes]   │
│                │        ...               │        ...                │
└────────────────┴──────────────────────────┴───────────────────────────┘
```

- **Red areas**: Deleted or modified content
- **Green areas**: Added or modified content
- **Click a sidebar item**: Jump directly to that page
- **Prev / Next**: Navigate through diff locations one by one

### 5. Re-compare

Click the **[Re-compare]** button to re-run the comparison after changing the mode or to refresh results.

---

## Requirements

- **Browser**: Chrome, Edge, or Firefox (latest version recommended)
- **Internet**: Required on first load to fetch [PDF.js](https://mozilla.github.io/pdf.js/) from CDN
- **Installation**: None

> For offline use, download the PDF.js library locally and update the CDN paths in the HTML file.

---

## Comparison Pipeline

A loading overlay shows progress through four steps during comparison:

```
[1] Generate signatures / extract text from left PDF
[2] Generate signatures / extract text from right PDF
[3] Analyze page order alignment (match / insert / delete)
[4] Render pages & detect differences
```

---

## Privacy & Security

- Selected PDF files are **processed entirely in the browser** and never sent to any external server.
- All comparison logic runs client-side in JavaScript.

---

## Tech Stack

- **PDF.js** v3.11.174 — PDF rendering and text extraction
- **Canvas API** — Pixel-level image comparison

## Algorithms

| Algorithm | Mode | Usage |
|-----------|------|-------|
| **Needleman-Wunsch** | Pixel | Global sequence alignment for matching pages across two PDFs. Each page is treated as an element in a sequence; the algorithm finds the optimal pairing using a similarity score and a gap penalty (0.43), handling inserted or deleted pages gracefully. |
| **Myers Diff** | Text | Character-level diff across the full text stream of both documents. After extracting text from every page, all tokens are flattened into a single sequence and diffed in one pass — page boundaries are ignored to catch text that shifts between pages. |
| **Levenshtein Distance** | Pixel | Used internally during page similarity scoring to compare sampled text snippets from two pages, contributing to the similarity score that feeds into the Needleman-Wunsch alignment. |

- Pure HTML / CSS / JavaScript (no frameworks)
