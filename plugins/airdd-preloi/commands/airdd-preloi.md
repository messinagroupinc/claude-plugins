---
description: Turn a VDR (folder/zip/URL) into a Pre-LOI deck — editable .pptx + HTML for Claude Design — evidence-gated, deep-linked citations, full metadata.
argument-hint: "<VDR path | .zip | URL> [--out <dir>]"
---

Invoke the `airdd-preloi` skill with these arguments: $ARGUMENTS

Self-contained VDR-native flow (no /airdd dependency): extract → classify → prepare → structure → compose.
Every figure is evidence-gated and deep-links to its exact source (document/page/cell + verbatim quote);
judgment slides are emitted as MANUAL REVIEW placeholders. Outputs both an editable `.pptx` and an HTML deck
for Claude Design, plus a complete metadata bundle. Every deck is an AI-GENERATED DRAFT.
