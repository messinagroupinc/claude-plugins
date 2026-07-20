---
name: airdd-preloi
description: Turn any VDR (a local folder, a .zip, or a URL) into a format-faithful Pre-LOI investment-summary deck — an editable .pptx plus an HTML version for Claude Design — evidence-gated, with deep-linked source citations and complete metadata. Runs on this machine via the local AIRDD Pre-LOI connector; the data room never leaves the PC.
argument-hint: "[VDR path | .zip | URL]"
---

# AIRDD Pre-LOI

Build a Pre-LOI investment-summary deck from a data room, evidence-first. This works in **Chat, Cowork, and
Code** by driving the local **AIRDD Pre-LOI connector** (`mcp__airdd-preloi__*` tools). All work happens on
this machine — the deal data never leaves the PC. Nothing is invented: every figure traces to its source.

If the `airdd_preloi_*` tools are not available, the connector isn't installed/enabled — tell the user to run
`AIRDD-PreLOI-Setup.exe` and fully restart the Claude app, then try again.

## Steps

1. **Extract.** Call `airdd_preloi_extract` with `source` = the VDR the user gave (`$ARGUMENTS`). You get a
   `run_id`, the `classification_prompt`, a `spans_file` (local path), a `spans_sample`, and a `span_count`.

2. **Classify (deep and wide).** Follow the returned `classification_prompt` exactly to turn the spans into
   evidence. Read the full `spans_file` if you can; otherwise use `spans_sample`. For a large room, dispatch
   parallel subagents over span groups and merge their evidence before submitting — miss nothing material.
   Preserve each span's `source_path` / `page` / `sheet` / `cell_range` / `quote` on every evidence record so
   citations deep-link. Then call `airdd_preloi_submit_evidence` with `run_id`, your `evidence` array, and
   `report_meta` (company_name, project_code, grade, statuses). It returns one structuring prompt per section.

3. **Structure.** For each returned section prompt, produce the dense slide block it asks for (numbers only
   from the cited claims). Call `airdd_preloi_submit_sections` with `run_id` and a `sections` map
   (semantic_key → block). Sections you omit fall back to a safe text layout. The engine's anti-fabrication
   guard drops any block that emits a number absent from the cited evidence.

4. **Compose.** Call `airdd_preloi_compose` with `run_id` (and an `out_dir` if the user wants a specific
   folder). It builds `investment_summary.pptx` + `investment_summary.html`, deep-linked notes, a `sources/`
   pack, and the metadata bundle. Report the output paths, slide count, `qa_passed`, and coverage. Default the
   output under a short root (e.g. `C:\decks\<deal>`) to avoid Windows MAX_PATH issues.

5. **Hand off.** Offer to open `investment_summary.html` in Claude Design for visual tweaks, or edit the
   `.pptx` conversationally. Summarize `MANUAL_REVIEW` (the judgment slides the deal team must complete).
   Every deck is an AI-GENERATED DRAFT — say so; figures must be verified against source before external use.
