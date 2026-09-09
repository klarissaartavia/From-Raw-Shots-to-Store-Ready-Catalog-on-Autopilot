# Evidence Notes

Prepared on September 7, 2026 from a read-only review of the original Elyon image pipeline and the project owner's description. These notes support future writing and fact checks.

Paths below are relative to the original `Pipeline/` directory. They identify source material in the private working project; they are not files included in this documentation package.

| Claim or topic | Source inspected |
|---|---|
| Client task and ownership | Project owner's account of building image creation automation for Elyon Industrial |
| Staged workflow, outputs, and conventions | `stages/PIPELINE_GUIDE.md` |
| Orchestration, saved brand history, snapshots, and browser completion polling | `run_pipeline.py` |
| Direct image editing, rate-limit retries, per-image quality, and prompt overrides | `stages/02_generate_api.py` |
| Product and view descriptions, preservation instructions | `products/abb.py` |
| Standard corner logo compositing and thin-product exception | `stages/03_logo.py` |
| Product masking, diagonal pattern, brightness rules, and thin-cable processing | `stages/04_watermark.py` |
| Mixed and WebP delivery conventions | `stages/05_formats.py` |
| Product review cards, response export, stage routing, and regeneration archival | `stages/06_qc.py` |
| Cross-brand duplicate expansion from explicit mappings | `stages/07_cross_brand_dedup.py` |
| Workbook-based source image retrieval and manufacturer-domain preferences | `tools/harvest_inventory_images.py` |
| Production failure examples and operating lessons | `QC/LESSONS.md` |
| Existing checks for retry parsing, quality settings, and review controls | `tests/test_pipeline_safety.py` |

## Interpretation details

- The guide describes the intended process. Where comments or guide language differ from inspected executable code, the case study uses the narrower claim supported by the code.
- The original workflow has both browser and API generation paths. Their existence does not establish what fraction of final images used each path.
- Retry and existing-file logic support resumability. QC notes document exceptions, including stale outputs and false-positive logo checks, so the documentation avoids promising unconditional idempotency.
- Generation rejection currently archives selected old outputs. Other correction paths can delete and recreate derivatives; the case study does not claim every modification is archived.
- A standard corner logo is baked into the non-diagonal-watermarked source. Some logo defects therefore require regeneration, rather than a simple overlay rerun.
- The six-folder export structure is a delivery convention. It does not establish that all six folders for every brand were audited as complete.
- Existing tests were inspected as source. They were not executed for this documentation task, and this package makes no claim that a test suite passed.
- No image-by-image fidelity review, full inventory reconciliation, timing study, cost analysis, or final approval audit was performed for these documents.

## Package scope

This package contains four Markdown documents. It includes no copied client photographs, inventory records, credentials, raw review exports, or production scripts. No GitHub repository was created or published as part of preparing the documents.
