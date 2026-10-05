# Asphalt Field Photo Analyzer

**Screen site photos for visible pavement distress** — potholes, cracking, rutting, edge deterioration, patching, standing water and surface anomalies — for asphalt field QA.

[![Python 3.12](https://img.shields.io/badge/Python-3.12-3776AB?logo=python&logoColor=white)](https://www.python.org/)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)
[![CLI](https://img.shields.io/badge/CLI-command--line-blue)](#usage)
[![Platform: Windows](https://img.shields.io/badge/Platform-Windows-0078D6)](https://www.microsoft.com/windows)

**English** | [简体中文](./README.zh-CN.md)

---

## What it solves

Site photos are the cheapest field record you have — but reviewing hundreds of them by eye is slow and inconsistent. This tool **analyzes JPG/PNG photos** for potholes, cracking, rutting, edge deterioration, patching, standing water and surface anomalies.

Photo analysis is **visual screening only** — it never infers CBR, structural capacity, remaining life or exact repair depth. Poor-quality images go to review, and any measured dimension is marked APPROXIMATE until verified.

## Features

- **Pavement distress screening** — potholes, cracking, rutting, edge deterioration, patching, standing water, surface anomalies
- **Batch photo analysis** — JPG/PNG site photos in one run
- **Honest scope** — visual screening only; never structural inference (CBR, capacity, remaining life, repair depth)
- **Quality gating** — poor-quality images → review queue
- **Approximate dimensions** — measured values marked APPROXIMATE until verified

## Install

```bash
pip install -r requirements.txt
```

## Usage

```bash
python -m scripts.s09_field_photo_analyzer --project ./project --input ./fixtures/photos
```

Run with `--help` for all options. Every script in this family shares the same CLI convention: `--project <path> --input <path> --output <path> --format json|csv|md|xlsx|pdf --config <path> --verbose --dry-run`.

## Outputs

- `photo_analysis.json` — per-photo screening results
- `photo_summary.md`
- `review_queue.json` — poor-quality / uncertain items

## How it works

- **Standard lifecycle** — `DISCOVER → INGEST → EXTRACT → NORMALIZE → VALIDATE → CLASSIFY → OUTPUT → AUDIT`
- **Evidence state machine** — every observation moves `EXTRACTED → NORMALIZED → VALIDATED → VERIFIED`; stopping earlier is valid
- **Screening, not engineering** — never infers structural capacity or remaining life

## Quality gates

- deterministic schema validation on every record;
- golden sample fixtures with exact expected values (`tests/`);
- review queue: low-confidence values, ambiguous scales and conflicts land in `review_queue.json` / summary — never a silent fix;
- audit log: every run writes `run_id`, timings, input hashes, engine version and outputs to `<project>/audit/`.

## Testing

```bash
python -m pytest -q
```

## Security & privacy

Local files stay local by default. API keys live in environment variables (`ASPHALTCOSTS_API_KEY`, `ADI_AI_API_KEY`) — never in source code. Derived files are written to `working/` or `output/`; source files are never modified.

## License

MIT — see [LICENSE](LICENSE). Part of the [Asphalt Desktop Intelligence](https://github.com/anleeylee/asphalt-desktop-intelligence) toolkit.

## The calculation engine

[**AsphaltCosts.com**](https://asphaltcosts.com/) is the deterministic calculation layer: area → compacted volume → net tons → order tons (allowance applied once) → truckloads → material cost, with sourced planning defaults (145 lb/ft³ FHWA density, editable allowance and truck capacity). This tool feeds measured and validated inputs into that engine (or its labeled local mirror, `asphaltcosts-web-engine/1.0-mirror`) and never re-implements the formulas.
