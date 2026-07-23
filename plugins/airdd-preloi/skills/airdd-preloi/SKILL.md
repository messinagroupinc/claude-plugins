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

1. **Extract (starts in the background).** Call `airdd_preloi_extract` with `source` = the VDR the user gave
   (`$ARGUMENTS`). It returns **immediately** with a `run_id` and `status:"running"` — extraction (including
   expanding nested zips) runs in the background and can take 1–2 min on a large room.

2. **Wait for it.** Poll `airdd_preloi_get_status` with the `run_id` until `status` is `"done"` (a few seconds
   between polls). When done you get `span_count`, a `spans_file` (local path), a `spans_sample`, the
   `classification_prompt`, a `gaps` list (archives/files that couldn't be expanded), and a **`needs_ocr`**
   list (image-only/scanned files that yielded no text). If `status` is `"error"`, report the message.

3. **Classify (deep and wide).** Follow the returned `classification_prompt` exactly to turn the spans into
   evidence. Read the full `spans_file` if you can; otherwise use `spans_sample`. **For every file in
   `needs_ocr`, open and read it visually yourself** (it is scanned/image-only — the engine ships no OCR;
   you are the OCR) and add its evidence. For a large room, dispatch parallel subagents over span groups and
   merge their evidence before submitting — miss nothing material. Preserve each span's `source_path` /
   `page` / `sheet` / `cell_range` / `quote` on every evidence record so citations deep-link. Then call
   `airdd_preloi_submit_evidence` with `run_id` (or `run_dir`), your `evidence` array, and `report_meta`
   (company_name, project_code, grade, statuses). It returns one structuring prompt per section.

4. **Structure.** For each returned section prompt, produce the dense slide block it asks for (numbers only
   from the cited claims). Call `airdd_preloi_submit_sections` with `run_id` and a `sections` map
   (semantic_key → block). Sections you omit fall back to a safe text layout. The engine's anti-fabrication
   guard drops any block that emits a number absent from the cited evidence — the returned QA lists exactly
   which number/id tripped it, so fix that block and re-submit if needed.

5. **Compose.** Call `airdd_preloi_compose` with `run_id` (and an `out_dir` if the user wants a specific
   folder). It builds `investment_summary.pptx` + `investment_summary.html`, native editable charts,
   deep-linked notes, a `sources/` pack, and the metadata bundle. Report the output paths, slide count,
   `qa_passed`, chart/table counts, and how many citations deep-linked.

6. **Hand off.** Offer to open `investment_summary.html` in Claude Design for visual tweaks, or edit the
   `.pptx` conversationally. Summarize `MANUAL_REVIEW` (the judgment slides the deal team must complete) and
   any `gaps`. Every deck is an AI-GENERATED DRAFT — say so; figures must be verified against source before
   external use.

> If a run's `run_id` is ever lost (e.g. the app restarted mid-run), you can resume by passing the `run_dir`
> (returned by `extract`/`get_status`) to `submit_evidence`/`submit_sections`/`compose` instead of `run_id`.
