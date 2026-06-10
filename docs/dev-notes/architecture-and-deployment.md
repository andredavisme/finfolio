# FinFolio — Technical Architecture & Development Notes

**Last updated:** 2026-06-10  
**Status:** Active development

---

## Functional Goals

FinFolio is a fully client-side personal financial portfolio app designed around three core principles:

1. **No server, no account, no sync.** All data lives in a single `finfolio-data.json` file on the user's device.
2. **Honest, low-friction setup.** Users start with a few known facts (income, fixed bills, one goal) rather than a complete financial picture.
3. **Portable and shareable.** The entire app is two HTML files that run in any modern browser, online or offline.

---

## Technical Architecture

### Stack

| Layer | Technology | Notes |
|---|---|---|
| Frontend | Vanilla HTML/CSS/JS (single-file) | No build step, no framework |
| Charts | Chart.js (CDN) | `cashflowChart`, `budgetChart` |
| Fonts | Fontshare CDN | Satoshi + Clash Display |
| Hosting | GitHub Pages (`andredavisme.github.io/finfolio/`) | Served from `main` branch root |
| Persistence | Browser `localStorage` (intake only) + JSON file download | No database |
| Auth | None — fully anonymous, local-only | By design |

### File Structure

```
finfolio/
├── index.html          # Main app (dashboard, transactions, planner, notes, docs)
├── intake.html         # Guided onboarding flow (writes to localStorage)
├── sample-data.json    # Static sample dataset reference
├── finfolio.zip        # ⚠️  PENDING — see deployment task below
├── README.md
└── docs/
    └── dev-notes/
        └── architecture-and-deployment.md   # This file
```

### Data Model

All state lives in a single JS object:

```js
const state = {
  transactions: [ /* { id, date, amount, category, type, status, tag, description, sample? } */ ],
  notes:        [ /* { id, title, tag, body, createdAt, sample? } */ ],
  parsedRows:   [],   // transient — document parser staging
  csvRows:      []    // transient — CSV import staging
};
```

**Transaction types:** `income` | `expense` | `goal`  
**Transaction statuses:** `actual` | `expected`  

Exported as `finfolio-data.json` via `Blob` + anchor download. Re-imported via `FileReader`. Nothing ever leaves the device.

### Intake → Dashboard Flow

1. `intake.html` collects income sources, fixed costs, variable estimates, and a budget goal.
2. On completion it serializes intake data to `localStorage` under key `finfolio_intake`.
3. It then navigates to `index.html#dashboard`.
4. `index.html` detects the `#dashboard` hash on load, reads `localStorage`, converts intake records into `state.transactions` entries, clears the localStorage key, and calls `switchView('dashboard')`.

### ZIP App Download

**Current implementation (as of 2026-06-10):**

```js
function downloadApp() {
  const a = document.createElement('a');
  a.href = 'https://andredavisme.github.io/finfolio/finfolio.zip';
  a.download = 'finfolio.zip';
  a.click();
  showToast('\u2713 Downloading finfolio.zip\u2026');
}
```

This replaces the previous JSZip + `fetch()` approach, which failed under CORS on GitHub Pages (same-origin HTML fetches are blocked for hosted static files).

---

## ⚠️ Pending Deployment Task

### Commit `finfolio.zip` to repo root

The "Download FinFolio (ZIP)" button in the Notes view points to:

```
https://andredavisme.github.io/finfolio/finfolio.zip
```

This file **does not yet exist** in the repository. Until it is committed, the download button will 404.

**To complete this task locally:**

```bash
# 1. From the repo root, create the ZIP
zip finfolio.zip index.html intake.html

# 2. Optionally add a README inside the ZIP first:
#    Create a README.txt, then:
zip finfolio.zip index.html intake.html README.txt

# 3. Commit and push
git add finfolio.zip
git commit -m "Add finfolio.zip for direct app download"
git push
```

**Important:** Whenever `index.html` or `intake.html` are updated, `finfolio.zip` should be rebuilt and re-committed to keep the download current. Consider adding this as a reminder step in any future release checklist.

---

## Deployment

- **Live URL:** https://andredavisme.github.io/finfolio/
- **Branch:** `main` (GitHub Pages serves from root)
- **No CI/CD required** — changes pushed to `main` are live within ~1 minute via GitHub Pages CDN propagation.

---

## Future Considerations

- A GitHub Actions workflow could auto-rebuild `finfolio.zip` on every push to `main` that touches `index.html` or `intake.html`.
- If the app grows beyond two files (e.g., a JS module or CSS asset is split out), the ZIP generation step becomes more important to automate.
- Supabase migration path: the `state.transactions` and `state.notes` arrays map cleanly to two PostgreSQL tables; the JSON export format is already structured for that transition.
