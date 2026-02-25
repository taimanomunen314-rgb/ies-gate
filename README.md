# IES-GATE v1.0 Specification (Frozen)

IES-GATE is **NOT physics**. It is a **meta-protocol** that constrains when aggregation and differential evaluation are legitimate.

## What it is
IES-GATE permits aggregation only on intervals judged to share a common causal structure:
- replace `T(x)` with `T(x | continuity)`

ΔGATE restricts *differential evaluation* to the common accepted domain between conditions/datasets.

## What it is NOT (MUST NOT)
- MUST NOT be interpreted as physics, state-control, or a generative theory.
- MUST NOT be used to tune or justify any external physical theory by changing gate choices.
- MUST NOT claim to identify “true causality”; it enforces **auditable aggregation legitimacy** only.
- MUST NOT output numeric results when invalidity rules trigger.

## Canonical spec (v1.0 Frozen)
- `SPEC.md` is the canonical frozen protocol for v1.0: definitions, procedure, ΔGATE rule, MUST reporting, and failure modes.
- Freeze policy: **no extensions in v1.x** (backward-compatible fixes only). Any extension is v2.0+ with explicit deltas.

## Minimal usage
This repository is a specification-first release. If a minimal reproducible demo is included, run it via:
- `bash scripts/run_demo.sh`

## Citation
Use `CITATION.cff`. Prefer the Zenodo **concept DOI** for canonical reference, and **version DOI** for exact reproducibility.
