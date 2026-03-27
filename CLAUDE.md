# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

A Korean personal finance tracker (가계부) built as a static single-page application — no build step, no server, no dependencies beyond a CDN-loaded Chart.js. All files are plain HTML with embedded CSS and JavaScript.

## Development

Since there is no build system, development is just editing HTML files and opening them in a browser. No install, compile, or test commands exist.

To preview locally, open any HTML file directly in a browser (file:// protocol works fine, as all data is stored in `localStorage`).

## File Structure

- `index.html` — redirect shim to `household-budget.html`
- `household-budget.html` — main app (primary working file)
- `pension_reservation.html` — in-progress variant/copy of the main app (untracked)
- `cleanup-storage.html` — utility page to trim `localStorage` data (keeps only 1 row per book)

## Architecture

The entire app lives in `household-budget.html`. All CSS, HTML markup, and JavaScript are in one file. There is no module system.

**Key global state variables (JS, ~line 604):**
- `DATA` — array of entry rows for the current book
- `COLUMNS` — array of `{key, label}` column definitions (user-configurable)
- `DEFAULT_COLUMNS` — fallback column set (includes kb, kbm, deposit, saving, pension, mirae)
- `BOOKS` — array of `{id, title}` workspace entries
- `CURRENT_BOOK_ID` — active book identifier
- `APP_TITLE` — display name for the current book
- `UNIT_MODE` — `'man'` (만원) or `'won'` (원); display only, inputs always stored in 만원

**localStorage keys:**
- `hb_workspace` — `{current, books[]}` workspace index
- `hb_data_<bookId>` — entry rows for a specific book
- `hb_cols_<bookId>` — column definitions for a specific book
- `hb_unit` — persisted unit toggle preference
- Legacy keys `hb_title`, `hb_columns`, `hb_version`, `hb_data` are migrated on first load

**Render flow:** `render()` calls `updateSummary()` + `renderFilters()` + `renderTable()`. All DOM updates are full re-renders (innerHTML replacement), not incremental.

**Modals:** add/edit entry, delete confirm, column editor, and chart view are all overlay modals defined in the HTML and toggled via `.open` class.

**Multi-book workspace:** users can create multiple named books; switching books saves current state and loads the selected book's data and columns from `localStorage`.
