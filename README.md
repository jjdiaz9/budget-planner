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

This repo ships with **no personal financial data** — all months start blank. To load your own numbers:

1. Open the app and fill in a month, or
2. Use the **Export** button to save a JSON snapshot of your data, and the **Load** button to import a previously exported JSON file back in (useful for moving data between browsers/devices, or restoring a backup).

There are also two spots in the source (`SHARED_EXPENSE_DATA` and `SHARED_SHEET` in `budget.html`) where you can hardcode your own recurring shared-expense figures if you want a one-click "Load shared expenses" shortcut — they're empty objects by default.

## Notes

- All data stays in `localStorage` in your browser; clearing site data will clear the budget.
