# felixorg-handbook

Source-of-truth handbook content lives in `handbook/` and `assets/`.
Generated outputs (PDF, single-file markdown, etc.) go in `dist/` and are not hand-edited.

## Quick start

- Edit:
  - Markdown sections: `handbook/**.md`
  - Spreadsheet sources: `assets/spreadsheets/**.xlsx`
  - Diagram sources: `assets/diagrams/**.vsdx`

- Build order is defined in: `build/chapters.txt`
- Build metadata is in: `build/metadata.yaml`

## Conventions

- `dist/` is output only.
- Keep exported/derived assets alongside sources when helpful:
  - XLSX → CSV for diffs
  - VSDX → PDF/SVG for viewing and future-proofing
