# remittance-splitter-releases
Releases for Remittance PDF Splitter

# How It Works — Remittance PDF Splitter

Takes a single combined PDF containing multiple Internal Remittance Advice documents and splits it into individual PDFs — one per remittance — named using metadata extracted from each cover page.

---

## The Pipeline

```
Input PDF / ZIP
      │
      ▼
1. Extract pages as images
      │
      ▼
2. Detect yellow cover pages
      │
      ▼
3. Group pages into sections
      │
      ▼
4. OCR each cover page → extract metadata
      │
      ▼
5. Format output filename
      │
      ▼
6. Assemble output PDFs
```

---

## Step 1 — Input Normalisation

The input may be a standard PDF or a ZIP archive of JPEG images (some remittance systems export in this format). Both are normalised to a flat list of page images before any further processing.

---

## Step 2 — Yellow Cover Page Detection

Each remittance begins with a **yellow-background cover page**. A page is classified as yellow by sampling the border pixels (15 px inset on all four edges) and checking the mean RGB values:

- Red > 200
- Green > 200
- Blue < 180
- Red − Blue > 50
- Green − Blue > 30

This tolerates scanner variation in the yellow shade while reliably excluding white or off-white pages.

---

## Step 3 — Section Grouping

Pages are walked in order. Each yellow page starts a new section; all following non-yellow pages belong to that section until the next yellow page. The result is a list of sections, each with a cover page and zero or more body pages.

---

## Step 4 — Metadata Extraction (OCR)

The yellow cover page is preprocessed before OCR:

- Converted to greyscale using a channel-weighted formula that suppresses the yellow background and amplifies text contrast
- Sharpened to improve edge definition

Tesseract then extracts four fields:

| Field | Method |
|---|---|
| **Payment date** | Regex — `DD/MM/YY` or `DD/MM/YYYY` |
| **Payment amount** | Regex — `$` followed by digits and decimal |
| **Company name** | First non-empty line before the "REMITTANCE ADVICE" heading |
| **Vendor name** | First substantive line after the heading |

---

## Step 5 — Output Filename

```
{Company} {YYYYMMDD} {Vendor} - {Amount}.pdf
```

**Example:** `Aurexis 20260308 Acme Supplies - $4250.00.pdf`

- Date is reformatted to `YYYYMMDD` for chronological sort order
- Characters illegal on Windows are stripped (`< > : " / \ | ? *`)
- Duplicate filenames get a numeric suffix (`_2`, `_3`, …)

---

## Step 6 — PDF Assembly

Each section's page images are reassembled into a single PDF. The yellow cover page is always the first page of the output file.

---

## Dependencies

| Library | Purpose |
|---|---|
| [PyMuPDF](https://pymupdf.readthedocs.io) | Render PDF pages to images at 200 DPI |
| [Pillow](https://python-pillow.org) | Image processing and PDF assembly |
| [NumPy](https://numpy.org) | Border pixel analysis for yellow page detection |
| [pytesseract](https://github.com/madmaze/pytesseract) | Python wrapper for Tesseract OCR |
| [Tesseract OCR](https://github.com/UB-Mannheim/tesseract/wiki) | OCR engine (auto-detected or installed via winget) |
