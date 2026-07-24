# Budget & Transfers

A single-file, offline, dependency-free monthly budgeting app. Everything runs client-side in `budget.html` — no build step, no server, no external JS libraries.

## Features

- Track income, fixed expenses, variable expenses, shared expenses, and savings by month
- Split shared bills by percentage plus an optional fixed "extra" amount
- Auto-generates a step-by-step transfer plan between accounts
- Rollover balances carry forward month to month
- Pie and bar charts of where money goes
- Multi-currency display formatting
- Data is saved to your browser's `localStorage` — nothing leaves your machine

## Using it

Open `budget.html` in any modern browser. That's it.

### Bringing in your own data

This repo ships with **no personal financial data** — all months start blank. There are three ways to get numbers in:

1. **Type them in** directly.
2. **Load an Excel sheet** — the "Load shared expenses" button on the Shared card opens a file picker and reads an `.xlsx` right in the browser. Nothing is uploaded; the file is unzipped and parsed locally using native browser APIs (`DecompressionStream`, `TextDecoder`) with no libraries.
3. **Export / Load JSON** — the Export button saves a snapshot of every month; Load restores it. Useful for backups or moving between browsers.

#### Expected spreadsheet layout

- One worksheet per year, named for the year (`2026`, `2025`, …).
- A header row with month names (`January` … `December`) across the columns.
- Below it, one row per expense: the name in column A, the amount in that month's column.
- The block ends at a row labelled `Total`.
- Rows whose name contains `*` are treated as **sub-items of a credit-card balance** that is already listed separately, so they are skipped to avoid double-counting. Rows starting with `Owed to` are also skipped.

#### Split rules

Each shared line has a percentage and an optional fixed "extra" that you always pay on top: your share is `(amount − extra) × % + extra`. Whatever you set for a given expense name is remembered (in `localStorage`) and reapplied automatically on future imports, so you only configure each line once.

## Notes

- All data stays in `localStorage` in your browser; clearing site data will clear the budget.
