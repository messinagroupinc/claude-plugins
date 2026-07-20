# Messina Group — Claude Plugins

Internal Claude plugin marketplace for Messina Group. Connect this repo in
**claude.ai → Organization settings → Plugins** (GitHub sync) to make these plugins
available to the team in **Chat and Cowork**; installing one drops its `/` commands and
skills straight into the `/` menu.

## Plugins

### `airdd-preloi` — AIRDD Pre-LOI Deck
Turn any VDR (a local folder, a `.zip`, or a URL) into a format-faithful **Pre-LOI
investment-summary deck** — an editable `.pptx` plus an HTML version for Claude Design —
evidence-gated, with deep-linked source citations and complete metadata.

- **Command:** `/airdd-preloi <VDR path | .zip | URL>`
- **Surfaces:** Chat, Cowork, and Claude Code.
- **How it runs:** this plugin ships only the thin orchestration skill (no proprietary
  logic). The sealed engine and the local `airdd_preloi_*` connector are installed
  separately by **`AIRDD-PreLOI-Setup.exe`** (see
  [airdd-preloi-dist releases](https://github.com/messinagroupinc/airdd-preloi-dist/releases/latest)).
  All extraction and generation happen on the user's machine — **deal data never leaves the PC.**

> First run in Chat/Cowork: if Claude says the `airdd_preloi_*` tools aren't available,
> run `AIRDD-PreLOI-Setup.exe` and fully restart the Claude app, then try again.

## Layout
```
.claude-plugin/marketplace.json      # marketplace manifest (lists the plugins)
plugins/airdd-preloi/
  .claude-plugin/plugin.json         # plugin manifest
  skills/airdd-preloi/SKILL.md       # the /-menu skill (orchestrates the local connector)
  commands/airdd-preloi.md           # the /airdd-preloi slash command
```

## Updating
Edit the plugin, bump its `version` in both `plugin.json` and `marketplace.json`, and push.
The org marketplace re-syncs automatically; installed clients pick up the new version.
