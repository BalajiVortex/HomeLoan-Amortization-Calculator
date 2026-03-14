# 🏦 HomeLoan Amortization Calculator

> A bank-accurate, single-file home loan amortization calculator — built for the way Indian banks actually work.

---

## ✨ What makes this different

Most online EMI calculators assume a clean, simple loan — one disbursement, one fixed rate, and a textbook amortization formula. Real home loans are messier. Construction-linked disbursements happen in tranches. Interest rates change with RBI policy. You make advance payments. Banks sometimes change your EMI instead of your tenor.

This calculator handles all of that, correctly.

---

## 📸 Preview

> Dark-themed, responsive, single HTML file — open it in any browser, no server required.

```
┌─────────────────────────────────────────────────────┐
│  🏦 Loan Amortization Calculator          ● Saved   │
├──────────────┬──────────────────────────────────────┤
│  Base Loan   │  Summary Cards                       │
│  Events      │  ┌──────────┬──────────┬──────────┐  │
│              │  │ Disbursed│ Interest │ Base EMI │  │
│  + Add Event │  └──────────┴──────────┴──────────┘  │
│              │                                      │
│  ⚙ Calculate │  Amortization Schedule | Charts      │
└──────────────┴──────────────────────────────────────┘
```

---

## 🧠 The Interest Logic (Bank Method)

This calculator mirrors exactly how Indian banks compute home loan interest:

1. **Interest accrues on the last day of every calendar month** using the Actual/365 method
2. **Accrued interest is added to the outstanding balance** — not held separately
3. **EMI hits on the payment date** — pays off all accrued interest first, remainder reduces principal
4. **First EMI date is fully flexible** — if your first disbursement was on 31-MAR and your bank skips April, you can set first EMI to 05-MAY and the calculator will accumulate March + April interest into that first payment

### Example

| Date | Event | Days | Calculation |
|------|-------|------|-------------|
| 31-MAR-2023 | Disbursement | — | Balance = ₹10,25,000 |
| 31-MAR-2023 | Interest Accrual | 1 | ₹10,25,000 × 8.7% × 1/365 = **₹244.32** |
| 30-APR-2023 | Interest Accrual | 30 | Balance × 8.7% × 30/365 |
| 05-MAY-2023 | EMI #1 | — | Interest = Mar + Apr accrual combined |
| 31-MAY-2023 | Interest Accrual | 31 | Balance × 8.7% × 31/365 |
| 05-JUN-2023 | EMI #2 | — | Interest = May accrual only |

---

## 🚀 Features

### Core
- **Single HTML file** — no installation, no server, no dependencies (except Chart.js via CDN)
- **Auto-calculates EMI** from principal, rate, and tenor — or accept a manually entered EMI
- **Actual/365 interest** — every month uses real calendar days (28/29/30/31)
- **Flexible first EMI date** — enter the exact date your bank tells you
- **Separate EMI day setting** — for all subsequent EMIs after the first

### Events System
Add any combination of these events on any date:

| Event | What it does |
|-------|-------------|
| 💰 **Additional Disbursement** | Adds to outstanding principal on that date. Common in construction-linked loans. |
| 📈 **Rate Revision** | Changes the interest rate from that date. Choose whether tenor adjusts (EMI fixed) or EMI changes (tenor fixed). |
| ⚡ **Advance Payment** | Reduces principal on that date. EMI stays fixed, tenor reduces. |

### Rate Revision — Two Modes
When your bank revises the rate, you get to choose:
- **Tenor Changes** — your EMI stays exactly the same, but the loan ends earlier or later
- **EMI Changes** — enter the new EMI your bank communicated; tenor stays fixed

### Schedule Table
Every row is clearly typed and colour-coded:

| Row Type | Colour | Shows |
|----------|--------|-------|
| 💰 Disbursement | Blue tint | Amount added, new balance |
| 📆 Interest Accrual | Amber tint | Days, interest computed, balance after |
| 💳 EMI Payment | Default | EMI, interest component, principal component |
| 📈 Rate Revision | Orange tint | Old rate → new rate, impact note |
| ⚡ Advance Payment | Green tint | Amount, new balance, revised tenor |

**Columns include:**
- Opening Balance
- Days (for accrual rows)
- Interest Accrued
- Balance After Interest
- EMI / Payment
- Interest Component
- Principal Component
- Cumulative Interest Paid
- Cumulative Principal Paid
- Closing Balance
- Rate %

### Persistence
- **Auto-saves to localStorage** — all inputs and events survive browser refresh
- **Debounced saving** — saves 800ms after your last change, with a status indicator
- **Reset All** — clears everything and starts fresh

### Export & Print
- **Export CSV** — full schedule with all columns, opens in Excel
- **Print** — browser print dialog

### Charts
- **Principal vs Interest bar chart** — stacked per EMI, shows how the ratio shifts over time
- **Outstanding Balance line chart** — visualise how your principal reduces

---

## 📂 Getting Started

**No installation needed.**

1. Download `loan_amortization_calculator.html`
2. Open it in any modern browser (Chrome, Firefox, Safari, Edge)
3. Fill in your loan details and click **Calculate Amortization**

That's it.

---

## 🔢 How to Use

### Step 1 — Base Loan
| Field | Description |
|-------|-------------|
| Principal Amount | The first / base disbursement amount |
| Interest Rate | Annual rate in % (e.g. 8.7) |
| Disbursement Date | Date the first amount was released |
| First EMI Payment Date | Exact date of your first EMI — enter what your bank says |
| EMI Day (2nd EMI onwards) | Day of month for all subsequent EMIs (e.g. 5) |
| Tenor | Total loan tenure in months |
| Fixed EMI Amount | Optional — leave blank to auto-calculate |

### Step 2 — Add Events (optional)
Click **+ Add** and select the event type. Enter the date and relevant details.

Events are processed in chronological order. Multiple events in the same month are handled correctly — interest is split at each event date.

### Step 3 — Calculate
Click **⚙ Calculate Amortization**. The schedule, summary cards, and charts render instantly.

---

## 🏗️ Technical Details

**Stack:**
- Pure HTML + CSS + Vanilla JavaScript
- [Chart.js 4.4.0](https://www.chartjs.org/) via CDN (for charts)
- Google Fonts: DM Serif Display, DM Mono, DM Sans

**Interest formula:**
```
Interest (period) = Outstanding Balance × (Rate / 100 / 365) × Actual Days
```

**EMI formula (standard reducing balance):**
```
EMI = P × r × (1+r)^n / ((1+r)^n - 1)
where r = rate/100/12, n = tenor in months
```

**Architecture:**
- Two independent timelines merged chronologically: accrual dates (month-ends) and EMI dates
- An `accrualPot` accumulates unpaid interest across months and is fully cleared on each EMI
- Mid-month events (disbursements, rate changes, advance payments) split the interest period into sub-periods, each computed independently

**localStorage key:** `loan_amort_v4`

---

## 📋 Amortization Schedule — Column Reference

| Column | Populated on |
|--------|-------------|
| # | EMI rows only |
| Date | All rows |
| Row Type | All rows |
| Opening Balance | All rows |
| Days | Accrual rows only |
| Interest Accrued | Accrual rows only |
| Bal After Interest | Accrual rows only |
| EMI / Payment | EMI rows only |
| Interest Component | EMI rows only |
| Principal Component | EMI rows only |
| Cumul. Interest | EMI rows only |
| Cumul. Principal | EMI rows only |
| Closing Balance | All rows |
| Rate % | All rows |

---

## 🙋 Common Questions

**Q: Why does my first EMI show higher interest than subsequent ones?**
Because the first EMI covers multiple months of accrual (e.g. March + April), while all subsequent EMIs cover exactly one month.

**Q: Why is interest calculated at month-end and not on the EMI date?**
This is how Indian banks actually work. Interest accrues daily on your outstanding principal and is crystallised at the end of each calendar month. The EMI on the 5th (or whichever day) then pays off that crystallised interest and reduces principal.

**Q: My data disappears when I clear browser cache. How do I save permanently?**
Export to CSV before clearing cache. The calculator uses `localStorage` which is tied to your browser — it is not backed up to any server.

**Q: Can I use this for a loan with only one disbursement (simple home loan)?**
Yes — just don't add any events. The base loan fields are all you need.

**Q: What happens if the EMI day is the same as the last day of the month (e.g. EMI day 30 in April)?**
The accrual row still appears first, then the EMI row — both on 30-APR. Two separate rows, correct sequence.

---

## 📄 License

MIT License — free to use, modify, and distribute.

---

## 🤝 Contributing

Issues and pull requests are welcome. If you find a calculation that doesn't match your bank statement, please open an issue with:
- Disbursement date and amount
- Rate and tenor
- First EMI date
- The specific row where the number differs
- What your bank statement shows

---

*Built with ❤️ for anyone who has stared at a bank amortization statement and wondered how they got those numbers.*
