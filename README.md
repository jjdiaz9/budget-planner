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

Open `budget.html` in any modern browser, or visit the hosted copy. That's it.

### Trying it without your own data

`sample-data.json` holds two months of **made-up** figures. Click **Load**, pick that file, and the app fills in — useful for seeing how shared splits, rollover and the transfer plan behave before entering anything real.

### Bringing in your own data

This repo ships with **no personal financial data** — all months start blank. There are three ways to get numbers in:

1. **Type them in** directly.
2. **Load shared expenses from your private repo** — see below.
3. **Export / Load JSON** — the Export button saves a snapshot of every month; Load restores it. Useful for backups or moving between browsers.
4. **Sync the whole budget with a private repo** — see below.

### Shared expenses

**Load shared expenses** on the Shared card reads a household-expense file from your private repo — `expenses.json` by default — and fills in whichever month you're viewing.

**This file is read-only.** It is owned by a separate application; the budget planner never writes to it. That is enforced in `ghWrite`, which refuses any request aimed at the configured shared-expenses path, so no future code path can write to it by accident.

Expected shape:

```json
{
  "app": "household-expense-manager",
  "people": [{ "id": "alex", "name": "Alex" }, { "id": "sam", "name": "Sam" }],
  "years": {
    "2026": {
      "categories": [
        { "name": "Rent",  "kind": "normal",    "onCard": null,
          "split": { "mode": "even" },
          "amounts": { "1": 2200, "2": 2200 } },
        { "name": "Card",  "kind": "statement", "onCard": null,
          "split": { "mode": "solo", "personId": "alex" },
          "amounts": { "1": 812.4 } }
      ]
    }
  }
}
```

How each field is read:

| Field | Effect |
| --- | --- |
| `onCard` set | Skipped — the charge is already inside a statement line listed separately, so counting it again would double it |
| `split.mode: "even"` | 50% |
| `split.mode: "solo"` | 100% if `personId` is you, 0% otherwise |
| `amounts` | Keyed by month number, `"1"`–`"12"` |

**You are** in the Sync panel picks which person you are; it's populated from the file's `people` list on first load.

The file has no concept of a fixed amount one person always pays on top of a split. If you need that, set the **+** field on a line and the app remembers it for that expense name — but nothing of the sort is ever assumed for you.

### Syncing across machines

**Sync…** stores your budget as a single JSON file in a private GitHub repository. **Push** sends this browser's copy up, **Pull** brings the other machine's copy down.

Settings: repository owner and name, the budget file path, the shared-expenses file path, the branch, and a fine-grained access token scoped to that one repository with **Contents: Read and write**.

Three things are kept in separate browser storage keys and never mixed: the budget data (the only thing that travels), the repo configuration, and the token. An exported or synced file therefore cannot carry a credential, and a leaked data file reveals nothing about where it came from.

**Conflicts are never resolved silently.** If both machines changed since they last matched, the write is blocked and you're shown both copies — device, timestamp and a summary of contents. Whichever you discard is downloaded as a backup first. There is no last-write-wins.

Auto-push is available but **off by default**: each push is a commit, and enabling it turns the repo history into noise.

> **A note on tokens and GitHub Pages.** All `username.github.io` project sites share a single browser origin, because same-origin policy keys on host rather than path. A token stored while using the hosted copy is readable by any other Pages site published under the same account. The app warns about this on-screen. If that matters to you, open a local copy of `budget.html` when syncing.

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
- `.gitignore` denies `*.json`, `*.xlsx` and `*.csv` by default, allowing only `sample-data.json`. Your own spreadsheet or exports can sit in this folder without risk of being committed.
