# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

A single-file static web app (`index.html`) that generates RR14 (Race Result 14) event configuration prompts for an LLM and a human-readable HTML configuration report. No build tools, no dependencies, no package manager.

## Running / Developing

Open `index.html` directly in a browser — no server needed. The cPanel deployment copies all files to `~/public_html/projects/rr14form` via `.cpanel.yml`.

## Architecture

Everything lives in `index.html`: CSS (custom properties via `:root`), HTML form, and vanilla JS — all in one file with no external dependencies.

### JS State

Module-level variables track dynamic form state:
- `timingPointCount` / `timingPointsData` — counter and `{id: name}` map for timing point rows
- `contestCount` / `splitCounts` — counters for contests and per-contest splits
- `customRankings` — array backing the tag-input widget

### ID Naming Conventions

Dynamic elements use predictable compound IDs:
- Timing points: `tp_{id}`, input `tpName_{id}`
- Contests: `contest_{id}`, `cName_{id}`, `cTimingMethod_{id}` (radio group), `cNotes_{id}`
- Splits: `split_{contestId}_{sid}`, `sName_{cid}_{sid}`, `sDisc_{cid}_{sid}`, `sDist_{cid}_{sid}`, `sUnit_{cid}_{sid}`, `sTimingPt_{cid}_{sid}`

The helper `v(id)` reads any input's trimmed value by ID.

### Key Behaviors

- **Timing point → split linkage**: `updateAllSplitTimingSelects()` re-populates every `.split-timing-select` dropdown whenever timing points are added/renamed/removed, preserving prior selection via `data-tp-id` attributes.
- **Option toggles**: `OPTION_TOGGLES` array wires checkbox↔settings-panel show/hide for both Results (section 4) and Scoring (section 6).
- **Split discipline**: Selecting `race_start` disables and zeroes the distance/unit fields via `onSplitDiscChange()`.
- **Last split = finish**: The final split in each contest defines total race distance; the report and prompt label it `[FINISH]`.

### Output Generation

Two output modes toggled by tabs:
- `buildPrompt()` → plain text with `[INSTRUCTION PLACEHOLDER — …]` markers where RR14 MCP tool call instructions will be inserted in the future.
- `buildReport()` → styled HTML with `.ph-box` `<div>` placeholders for the same pending MCP instructions.

Both are generated together on form submit and displayed in the `#outputSection` element.

### Placeholder TODOs

Throughout the codebase, `[INSTRUCTION PLACEHOLDER]` comments and `ph-box` divs mark locations where RR14 MCP tool invocation instructions must be added. These cover: timing point setup, contest/split configuration, results list configuration, special rankings, and scoring.
