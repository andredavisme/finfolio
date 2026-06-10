# FinFolio — Functional Testing Checklist

**Version tested:** post `6f97527` (ZIP download fix)  
**Tester:**  
**Date:**  
**Browser/OS:**  

> **How to use this document:**  
> Work through each section in order. Tests are sequenced so foundational pieces (rendering, navigation, data entry) are verified before features that depend on them (dashboard calculations, export/import round-trips). If a step fails and requires a code change, re-run all sections from that block forward before continuing.  
> Mark each item: ✅ Pass &nbsp;|&nbsp; ❌ Fail &nbsp;|&nbsp; ⚠️ Needs adjustment  
> Use the **Notes** field to capture what to fix.

---

## Block 1 — Static Load & UI Baseline
*No data entered yet. Validates that the app loads and renders correctly before any state is introduced.*

### 1.1 App loads at GitHub Pages URL
- [ ] Open `https://andredavisme.github.io/finfolio/`
- [ ] Startup overlay appears (logo, 4 buttons visible)
- [ ] No console errors on load
- [ ] Fonts load (Clash Display headline, Satoshi body)

**Notes:**

---

### 1.2 Theme toggle
- [ ] Click **Toggle theme** in sidebar footer (or topbar on mobile) — switches to dark mode
- [ ] Click again — returns to light mode
- [ ] Colors update across sidebar, cards, and topbar

**Notes:**

---

### 1.3 Startup overlay — blank slate entry
- [ ] Click **Start with a blank slate**
- [ ] Overlay dismisses; app shows Setup view
- [ ] Topbar shows `0 records` and `0 goals` pills
- [ ] Save button is gray (clean state)

**Notes:**

---

### 1.4 Navigation
- [ ] Click each nav button in order: Setup, Dashboard, Transactions, Planner, Documents, Notes
- [ ] Active button highlights correctly each time
- [ ] Topbar title and subtitle update to match each view
- [ ] No blank or broken sections

**Notes:**

---

## Block 2 — Intake Flow (`intake.html`)
*Tests the guided onboarding separately from the main app. Validates the localStorage handoff to `index.html`.*

### 2.1 Open intake
- [ ] Navigate to `https://andredavisme.github.io/finfolio/intake.html`
- [ ] Step 1 of 5 is shown; progress dots render
- [ ] Theme toggle works on this page independently

**Notes:**

---

### 2.2 Step 0 — Welcome
Enter the following:

| Field | Value |
|---|---|
| First name | `Jordan` |
| Pay frequency | `Every two weeks (26×/year)` |
| What do you want to feel better about? | `Know exactly how much I can spend after bills without guessing.` |

- [ ] All fields accept input
- [ ] Click **Let’s do it** — advances to Step 2
- [ ] Progress dot advances

**Notes:**

---

### 2.3 Step 1 — Income
Enter the following:

| Field | Value |
|---|---|
| Primary take-home pay | `1,450` |
| Per | `2 weeks` (should be pre-selected from Step 0) |
| Other regular income | `300` |
| Other frequency | `month` |
| Income consistency | `Varies a bit` |

- [ ] Bi-weekly frequency pre-selected from Step 0 choice
- [ ] Monthly equivalent shown next to primary income (expect ≈ `$3,150 / mo`)
- [ ] Monthly equivalent shown next to other income (expect `$300 / mo`)
- [ ] Click **Next: Fixed costs**

**Notes:**

---

### 2.4 Step 2 — Fixed Costs
Enter the following:

| Row | Amount | Frequency |
|---|---|---|
| Rent or mortgage | `1,100` | / mo |
| Car payment | `275` | / mo |
| Insurance | `148` | / mo |
| Phone | `65` | / mo |
| Internet | `55` | / mo |
| Student loans | `200` | / mo |
| Minimum credit card payments | `75` | / mo |

- [ ] Click **➕ Add another fixed bill**
- [ ] New row appears with a text name field and amount
- [ ] Enter name: `Gym membership`, amount: `35`, frequency: `/ mo`
- [ ] All inputs accept values
- [ ] Click **Next: Variable spending**

**Notes:**

---

### 2.5 Step 3 — Variable Spending
Enter the following:

| Row | Amount | Frequency |
|---|---|---|
| Groceries | `90` | / wk |
| Gas / transit | `50` | / mo |
| Utilities (electric, water, gas) | `140` | / mo |
| Dining out / takeout | `200` | / mo |
| Entertainment & fun | `80` | / mo |
| Personal care & health | `40` | / mo |
| Clothing & misc shopping | `60` | / mo |

- [ ] Groceries weekly → monthly equivalent should calculate automatically if shown
- [ ] Click **Next: Your picture**

**Notes:**

---

### 2.6 Step 4 — Priorities
- [ ] Select priorities: `Emergency fund`, `Know where money goes`, `Less money stress` (max 3)
- [ ] Attempting to select a 4th tile should be blocked
- [ ] Select money mindset: `Curious but confused`
- [ ] Select check-in frequency: `Weekly`
- [ ] Click **See my snapshot →**

**Notes:**

---

### 2.7 Done panel — Snapshot
- [ ] Done screen appears with `Jordan, here's your starting picture.`
- [ ] Four insight cards show calculated values (Monthly take-home, Fixed costs, Variable, Net)
- [ ] Net value is colored (green if positive, red if negative)
- [ ] Progress dots are hidden
- [ ] “Open my finfolio” button is visible
- [ ] Click **Open my finfolio**

**Notes:**

---

### 2.8 Intake → Dashboard handoff
- [ ] `index.html` opens directly to Dashboard view (not the startup overlay)
- [ ] Topbar shows record count > 0
- [ ] Dashboard KPI cards are populated with intake-derived values
- [ ] Save button is **active/teal** (data is dirty, unsaved)
- [ ] A note titled `Setup goal — Jordan` appears in Notes view

**Notes:**

---

## Block 3 — Manual Transaction Entry
*Validates the core data entry loop. Do not save to file yet — keep state in-memory.*

### 3.1 Add income transaction
Go to **Transactions** view. Enter:

| Field | Value |
|---|---|
| Date | `2026-06-01` |
| Amount | `1450.00` |
| Category | `Paycheck` |
| Type | `Income` |
| Status | `Actual` |
| Tag | `Primary salary` |
| Description | `Bi-weekly payroll — June 1` |

- [ ] Click **Save transaction**
- [ ] Row appears in ledger with correct values
- [ ] Type badge shows green `income`
- [ ] Record count pill increments
- [ ] Save button turns teal (dirty state)

**Notes:**

---

### 3.2 Add expense transaction
Enter:

| Field | Value |
|---|---|
| Date | `2026-06-02` |
| Amount | `1100.00` |
| Category | `Rent` |
| Type | `Expense` |
| Status | `Actual` |
| Tag | `Housing` |
| Description | `June rent payment` |

- [ ] Row appears in ledger
- [ ] Type badge shows red `expense`

**Notes:**

---

### 3.3 Add savings goal transaction
Enter:

| Field | Value |
|---|---|
| Date | `2026-06-03` |
| Amount | `250.00` |
| Category | `Emergency Fund` |
| Type | `Savings goal` |
| Status | `Expected` |
| Tag | `Emergency fund` |
| Description | `Monthly emergency fund target` |

- [ ] Row appears with orange `goal` badge
- [ ] Goals pill in topbar increments to at least 1

**Notes:**

---

### 3.4 Add expected (planned) expense
Enter:

| Field | Value |
|---|---|
| Date | `2026-06-15` |
| Amount | `275.00` |
| Category | `Car payment` |
| Type | `Expense` |
| Status | `Expected` |
| Tag | `Transport` |
| Description | `Monthly car payment — expected` |

- [ ] Status column shows `expected`

**Notes:**

---

### 3.5 Quick-add buttons
- [ ] Click **Groceries** quick-add — switches to Transactions view, pre-fills `$85`, category `Groceries`, type `expense`
- [ ] Verify date is today’s date
- [ ] Click **Save transaction**
- [ ] Click **Paycheck** quick-add — pre-fills `$2500`, type `income`
- [ ] **Do not save** this one; navigate away to verify form resets

**Notes:**

---

### 3.6 Delete a transaction
- [ ] Click **Delete** on the Groceries quick-add row just created
- [ ] Row is removed from ledger
- [ ] Record count decrements

**Notes:**

---

## Block 4 — Dashboard Verification
*Validates that KPI calculations, charts, and insights reflect the current state correctly.*

### 4.1 KPI cards
With the transactions from Block 3 in state, go to **Dashboard**:

| Card | Expected behavior |
|---|---|
| Actual income | Should reflect the `$1,450.00` actual income |
| Actual expenses | Should reflect `$1,100.00` actual expense |
| Net cash flow | Should be `$350.00` (positive, green) |
| Savings goals | Should show `$250.00` across 1 active goal |

- [ ] All four KPI cards populated
- [ ] Net cash flow is green/positive
- [ ] Goals count matches

**Notes:**

---

### 4.2 Charts
- [ ] **Cash flow trend** chart renders (line chart)
- [ ] **Budget mix** chart renders (bar chart)
- [ ] Toggle theme — chart colors adapt
- [ ] No JavaScript errors in console

**Notes:**

---

### 4.3 Insights list
- [ ] At least 3 auto-generated insight sentences appear
- [ ] Savings rate figure is present
- [ ] Goal progress section shows Emergency Fund

**Notes:**

---

## Block 5 — Planner View
*Validates budget vs actual comparison table.*

### 5.1 Budget vs actual table
- [ ] Go to **Planner** view
- [ ] Table rows show categories from entered transactions
- [ ] Expected, Actual, and Variance columns populate correctly
- [ ] Positive variance (under budget) shown in green; negative in red
- [ ] Portfolio summary list shows calculated figures

**Notes:**

---

## Block 6 — Documents View (CSV Import & Document Parser)
*Two independent import paths. Test CSV first since it’s more structured.*

### 6.1 Download CSV template
- [ ] Go to **Documents** view
- [ ] Click **⬇ Download CSV template**
- [ ] File `finfolio-import-template.csv` downloads
- [ ] Open it — verify 7 columns: `date, amount, category, type, status, tag, description`
- [ ] Three sample rows present

**Notes:**

---

### 6.2 Import CSV file
Create a CSV file (or edit the template) with the following rows and save as `test-import.csv`:

```
date,amount,category,type,status,tag,description
2026-06-05,86.40,Groceries,expense,actual,Food,Weekly grocery run
2026-06-06,150.00,Vacation fund,goal,expected,Travel,Save toward trip
2026-06-07,65.00,Dining out,expense,actual,Food,Dinner with friends
2026-06-08,120.00,Electric,expense,actual,Utilities,Monthly electric bill
2026-06-10,400.00,Freelance,income,actual,Side income,Project payment received
```

- [ ] Drag-and-drop `test-import.csv` onto the drop zone OR click to browse
- [ ] Preview table shows up to 5 rows (all 5 in this case)
- [ ] Row count shows `5`
- [ ] **Import 5 rows** button appears
- [ ] Click import — switches to Transactions view
- [ ] All 5 rows appear in ledger
- [ ] Record count increments by 5

**Notes:**

---

### 6.3 CSV edge cases
Test each individually; note results:

- [ ] Upload a CSV with a missing `date` column — should show “No valid rows found”
- [ ] Upload a CSV with an unknown `type` value (e.g. `transfer`) — should default to `expense`
- [ ] Upload a CSV with an unknown `status` value — should default to `actual`

**Notes:**

---

### 6.4 Document parser
Go to the **Document parser** card. Paste the following text:

```
2026-06-09 Grocery Mart $92.15
2026-06-10 Payroll Deposit $1450.00
2026-06-11 Netflix subscription $17.99
06/12/2026 Student loan payment $200.00
2026-06-13 Save for emergency fund $300
```

- [ ] Click **Parse document**
- [ ] Parsed rows table populates with 5 rows
- [ ] `Payroll Deposit` inferred as `income`
- [ ] `emergency fund` inferred as `goal`
- [ ] Remaining rows inferred as `expense`
- [ ] Both date formats (YYYY-MM-DD and MM/DD/YYYY) parsed correctly
- [ ] Click **Import parsed rows** — switches to Transactions view
- [ ] 5 new rows appear in ledger

**Notes:**

---

## Block 7 — Sample Data System
*Validates the built-in sample data feature — load, banner, and clear.*

### 7.1 Load sample data via Transactions view
- [ ] Go to **Transactions**, click **Load sample data**
- [ ] Orange sample banner appears at top: “Sample data is loaded.”
- [ ] Sample rows in ledger show `sample` badge next to category
- [ ] Record count increments (adds 8 sample transactions + 2 notes)

**Notes:**

---

### 7.2 Clear sample data
- [ ] Click **Clear sample data** in the banner
- [ ] Toast confirms removal with correct counts
- [ ] Sample banner disappears
- [ ] Sample-badged rows removed; manually entered rows remain
- [ ] Record count reflects only manual entries

**Notes:**

---

## Block 8 — Notes View
*Validates note creation and display.*

### 8.1 Add a note
Go to **Notes** view. Enter:

| Field | Value |
|---|---|
| Title | `Emergency fund priority` |
| Tag | `Savings` |
| Note | `Target: 3 months of fixed costs ($5,748). Current pace: $250/mo. Estimated months to goal: 23. Revisit if freelance income stabilizes.` |

- [ ] Click **Save note**
- [ ] Note card appears in the grid with title, tag, date, and body
- [ ] Multiple notes stack in 3-column grid (desktop) or 1-column (mobile)

**Notes:**

---

### 8.2 Share box — Copy link
- [ ] Click **Copy link** next to the live URL
- [ ] Toast shows “✓ Link copied!”
- [ ] Paste elsewhere — confirm URL is `https://andredavisme.github.io/finfolio/`

**Notes:**

---

### 8.3 Download app button
- [ ] Click **⬇ Download FinFolio (ZIP)**
- [ ] Browser initiates download (or 404 if `finfolio.zip` not yet committed — expected until deployment task is complete)
- [ ] Toast shows “✓ Downloading finfolio.zip…” regardless of outcome
- [ ] If file downloads: unzip and confirm `index.html` + `intake.html` present

> ⚠️ This step will 404 until `finfolio.zip` is committed to the repo root. See `architecture-and-deployment.md`.

**Notes:**

---

## Block 9 — Save / Export / Import Round-Trip
*The most critical data integrity test. Validates that nothing is lost or corrupted through a full save-and-reload cycle.*

### 9.1 Note current state
Before saving, record:
- [ ] Record count shown in topbar: `______`
- [ ] Goals count: `______`
- [ ] Note count visible in Notes grid: `______`

**Notes:**

---

### 9.2 Save to JSON
- [ ] Click the **Save** button in topbar
- [ ] File `finfolio-data.json` downloads
- [ ] Save button returns to gray (clean state)
- [ ] Toast confirms `✓ Data saved to finfolio-data.json`

**Notes:**

---

### 9.3 Inspect the JSON file
- [ ] Open `finfolio-data.json` in a text editor
- [ ] Top-level keys are `transactions` and `notes`
- [ ] Transaction count matches what was recorded in 9.1
- [ ] Each transaction has: `id`, `date`, `amount`, `category`, `type`, `status`, `tag`, `description`
- [ ] No data appears truncated or malformed

**Notes:**

---

### 9.4 Reload and re-import
- [ ] Refresh the browser (or close and reopen)
- [ ] Startup overlay appears (data was not persisted — expected behavior)
- [ ] Click **Load my data (JSON)** and select `finfolio-data.json`
- [ ] App loads; startup overlay dismisses
- [ ] Record count matches the value recorded in 9.1
- [ ] Goals count matches
- [ ] Notes grid shows same notes
- [ ] Ledger rows match prior entries

**Notes:**

---

### 9.5 Import via sidebar
- [ ] With data loaded, click **Import JSON** in sidebar
- [ ] Select the same `finfolio-data.json`
- [ ] Existing data is fully replaced with file contents
- [ ] Record count unchanged (same file)
- [ ] Toast confirms `✓ Data loaded from finfolio-data.json`

**Notes:**

---

## Block 10 — Setup Form (in-app quick intake)
*Separate from `intake.html` — this is the simple form on the Setup view.*

### 10.1 Fill out setup form
Go to **Setup** view. Fill in:

| Field | Value |
|---|---|
| Monthly take-home income | `3150` |
| Housing payment | `1100` |
| Utilities + essentials | `285` |
| Minimum debt payments | `275` |
| Cash or checking balance today | `1840` |
| Monthly savings goal | `250` |
| Savings goal name | `Emergency fund` |
| Where money feels hardest | `Not knowing where it goes` |
| What do you want this budget to help you notice? | `Whether my variable spending is actually in line with what I estimate each month.` |

- [ ] Click **Build my starter budget**
- [ ] App switches to Dashboard view automatically
- [ ] Toast: `✓ Starting budget created — X entries added`
- [ ] New transactions visible in ledger (income, housing, utilities, debt, goal, cash balance entries)
- [ ] Note created with setup context

**Notes:**

---

### 10.2 Setup form — Clear
- [ ] Return to **Setup** view
- [ ] Fill any field with a value
- [ ] Click **Clear form**
- [ ] All fields reset to empty

**Notes:**

---

### 10.3 Load existing file from Setup
- [ ] Click **Load existing file** button in Setup hero
- [ ] File picker opens
- [ ] Select `finfolio-data.json`
- [ ] Data loads correctly

**Notes:**

---

## Block 11 — Unsaved Changes Guard
*Validates the browser’s beforeunload warning.*

### 11.1 Dirty state warning
- [ ] Make any change (add a transaction) without saving
- [ ] Save button is teal (dirty)
- [ ] Attempt to close the tab or navigate away
- [ ] Browser shows “You have unsaved changes” confirmation dialog
- [ ] Cancel — stay on page
- [ ] Save, then attempt to close again — no warning shown

**Notes:**

---

## Block 12 — Responsive / Mobile Layout
*Quick visual check — no data entry required.*

### 12.1 Desktop (≥1024px)
- [ ] Sidebar fully visible with labels
- [ ] KPI grid shows 4 columns
- [ ] Two-column layouts (dashboard, transactions) render side-by-side

**Notes:**

---

### 12.2 Tablet (768px–1024px)
- [ ] Sidebar collapses to icon-only (88px)
- [ ] Labels hidden, icons centered

**Notes:**

---

### 12.3 Mobile (<768px)
- [ ] Sidebar hidden
- [ ] Bottom mobile nav bar visible with 6 buttons
- [ ] All grid layouts collapse to single column
- [ ] Topbar stacks vertically
- [ ] Content has bottom padding (avoids overlap with mobile nav)

**Notes:**

---

## Final Sign-Off

| Block | Status | Notes |
|---|---|---|
| 1 — Static load & UI | | |
| 2 — Intake flow | | |
| 3 — Manual transactions | | |
| 4 — Dashboard | | |
| 5 — Planner | | |
| 6 — Documents (CSV + parser) | | |
| 7 — Sample data | | |
| 8 — Notes | | |
| 9 — Save/export/import | | |
| 10 — Setup form | | |
| 11 — Unsaved changes guard | | |
| 12 — Responsive layout | | |

**Outstanding items before `finfolio.zip` commit:**
- [ ] Block 8.3 ZIP download returns 200 (not 404)
- [ ] Zip verified to contain current `index.html` and `intake.html`

**Overall status:** ⚠️ Pending / ✅ Cleared for ZIP commit / ❌ Blocked by issues

**Tester sign-off:**
