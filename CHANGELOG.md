# Changelog

All notable changes to Remittance PDF Splitter are documented here.

---

## [1.3.0] – 2026-03-12

### Added

- **Company name lookup table** — output filenames now use short codes (e.g. `MI`, `N1`, `HAW`) instead of full company names when a match is found in `company_names.json`, which ships alongside the exe. The file can be edited in any text editor without recompiling. Matching is case-insensitive. Falls back to the full OCR'd name if no mapping exists.

---

## [1.2.0] – 2026-03-08

### Fixed

- **Vendor name now always correct** — cover pages without an "INTERNAL REMITTANCE ADVICE" heading (vendor appears directly after the address block) were producing the ABN number as the vendor name; extraction now navigates the consistent cover page structure reliably
- **Output filenames no longer contain ABN numbers** — ABN lines are skipped when searching for the vendor name
- **Date OCR correction** — OCR misreads of `1` as `4` in the day/month field (e.g. `42/02/26` → `12/02/26`) are automatically corrected, preventing invalid date strings like `20260242` in filenames
- **First-run pip bootstrap** — new virtual environments now always include pip, preventing `No module named pip` errors on first launch

---

## [1.1.1] – 2026-03-08

### Fixed

- Suppressed winget spinner frames (`-\|/`) that cluttered the install log — only meaningful progress lines are now shown

---

## [1.1.0] – 2026-03-08

### Improved

- Replaced clickable status bar text with a dedicated **Install Prerequisites** button — clearer first-run experience
- Winget output now streams live into the log during Tesseract installation so users can see progress
- Suppressed the stray console window that appeared during installation
- Exe is now included in GitHub release zip/gz archives

---

## [1.0.0] – 2026-03-08

### Initial Release

- Split a combined Internal Remittance Advice PDF into individual PDFs, one per remittance
- Automatic yellow cover page detection
- Local OCR via Tesseract — no data sent externally
- Output filenames derived from cover page data: `{Company} {YYYYMMDD} {Vendor} - {Amount}.pdf`
- GUI with live progress log
- Clickable output folder path to open in Explorer
- Conflict handling when output folder already exists (replace or create new)
- Multi-run support within a single session
- Automatic Tesseract installation prompt on first run (via winget)
- Support contact: michael@aurexis.com.au
