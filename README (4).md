
# Loan Default Risk Analysis – SQL Server + Power BI

An end-to-end analytics project on **255,347 loans (2013 – 2018)** that answers one business question: **who defaults on loans, and what should the lender do about it?**

The data is loaded into **SQL Server**, modelled and measured in **Power BI** (Power Query + DAX), presented as a **3-page interactive dashboard**, and summarised for decision-makers in a **stakeholder slide deck**.

---

## 📦 What is in this repository

| File / folder | Purpose |
|---------------|---------|
| `Loan_Default.pbix` | Power BI report (3 pages, 13 DAX measures, 4 calculated columns) |
| `data/Loan_default.csv` | Source data – 255,347 rows × 19 columns (~27 MB) |
| `sql/load_dataset_into_sql.sql` | Creates the `Loan` database and checks the loaded table |
| `presentation/Loan_Default_Stakeholder_Deck.pptx` | 9-slide briefing for stakeholders |
| `docs/Dashboard_Questions.md` | Every business question the dashboard answers, with the answer |
| `screenshots/` | Dashboard images (add yours) |

---

## 🎯 Business Problem

A lender wants to understand its default risk so it can approve, price and monitor loans better. The project looks at default behaviour across **time, loan purpose, employment, age, income, credit score, education and family status**, and at how the loan book has grown year over year.

---

## 🗂️ Dataset

Each row is one loan.

| Group | Columns |
|-------|---------|
| Identifier | `LoanID` |
| Applicant | `Age`, `Income`, `Education`, `EmploymentType`, `MonthsEmployed`, `MaritalStatus`, `HasDependents`, `HasMortgage` |
| Credit | `CreditScore`, `NumCreditLines`, `DTIRatio` |
| Loan | `LoanAmount`, `InterestRate`, `LoanTerm` (12–60 months), `LoanPurpose`, `HasCoSigner`, `Loan Date` |
| **Target** | `Default` (1 = defaulted, 0 = repaid) |

Size and quality: 255,347 rows, **no missing values and no duplicate loans**. Loan dates run 1 Jan 2013 – 31 Dec 2018 with about 42,500 loans per year.

---

## 🏗️ Architecture

```
Loan_default.csv ──► SQL Server (database: Loan, table: dbo.Loan_default)
                          │
                          ▼
                 Power BI (Power Query → data model → DAX)
                          │
                          ▼
        3-page dashboard  +  stakeholder deck  +  question log
```

### 1. Load the data into SQL Server

`sql/load_dataset_into_sql.sql` creates the database and checks the table:

```sql
create database Loan
use Loan

select * from [dbo].[Loan_default]
```

The script does not create the table itself. Load the CSV with the SSMS wizard: right-click the `Loan` database → **Tasks → Import Flat File…** → choose `Loan_default.csv` → name the table `Loan_default`. Then run the `select` to confirm 255,347 rows.

### 2. Power BI model

One fact table (`Loan_default`) plus three empty "measure tables" that hold the DAX measures. Power Query changes the types of `Age`, `CreditScore`, `MonthsEmployed`, `NumCreditLines` and `LoanTerm`; `Yes/No` fields become true/false and the date column becomes a date.

**Calculated columns**

| Column | Rule |
|--------|------|
| `Year` | Year of the loan date |
| `Age Group` | ≤ 19 *Teen* · 20–39 *Adult* · 40–59 *Middle Age Adults* · 60+ *Senior Citizen* |
| `Credit Score Bins` | ≤ 400 *Very Low* · 401–450 *Low* · 451–650 *Medium* · > 650 *High* |
| `Income Bracket` | < 30,000 *Low* · 30,000–59,999 *Medium* · ≥ 60,000 *High* |

**DAX measures** (by page)

| Page | Measures |
|------|----------|
| Overview | Default Rate By Year · Default Rate By Employment Type · Loan Amount By Purpose · Average Income By Employment Type · Average Loan By Age Group |
| Applicant | Median by Credit Score · Total Loan Amount (Credit bins Adult) · Total Loan (Middle Age Adults) · Average Loan Amount (High Credit) · LoanId Not Blank |
| Financial Risk | YOY Loan Amount · YOY Default Loans Changes · YTD Loan Amount |

---

## 📊 Dashboard Pages

| Page | What it shows |
|------|---------------|
| **1. Loan Default & Overview** | Default rate by year · default rate by employment type · loan amount by purpose · average income by employment type · average loan by age group |
| **2. Applicant Demographic and Financial** | Median loan by credit-score band · total loan by credit band for adults · loans to middle-aged adults by mortgage × dependents · average loan for high-credit borrowers by age group × marital status · number of loans by education |
| **3. Financial Risk Metrics** | Year-over-year change in loan amount and in defaulted loans · YTD loan amount by credit band × marital status · decomposition tree of loan amount by income bracket → employment type |

The full list of questions and answers is in [`docs/Dashboard_Questions.md`](docs/Dashboard_Questions.md).

---

## 🔍 Key Findings

| Metric | Value |
|--------|-------|
| Loans | **255,347** |
| Total loan amount | **32.6 billion** (currency units not stated in the data) |
| Default rate | **11.6 %** (29,653 loans) |
| Defaulted principal | **4.29 billion = 13.2 %** of the book – defaulted loans are bigger (avg 144.5K vs 125.4K) |

1. **Default rate is flat over time** – 11.5 % to 11.75 % every year from 2013 to 2018, with steady volume. Risk comes from *who* borrows, not *when*.
2. **Age is a strong driver** – 21.0 % for ages 18–24 down to 4.9 % for ages 65–69. The dashboard's wide *Adult (20–39)* group (16.7 %) hides this slope.
3. **Income is the clearest segment split** – low income (< 30K) defaults at **22.0 %**, medium 12.8 %, high 9.5 %.
4. **Employment** – Unemployed 13.6 %, Part-time 12.0 %, Self-employed 11.5 %, Full-time 9.5 %.
5. **Pricing tracks risk** – default rises from 6.3 % in the lowest interest-rate quintile to 18.0 % in the highest (likely risk-based pricing).
6. **Credit score is a weak separator here** – 13.3 % (≤ 400) to 10.1 % (> 750).
7. **Other protective factors** – co-signer (10.4 % vs 12.9 %), dependents (10.5 % vs 12.7 %), mortgage (10.9 % vs 12.4 %), longer job tenure (8.9 % above 5 years vs 16.9 % for 12 months or less), higher education.
8. **Highest-risk segment** – age ≤ 34 *and* income < 30K: 3.6 % of loans, **32.5 % default**. Safest large segment – age 45+ with income 60K+: 5.4 %.
9. **Loan size and purpose do not differentiate** – all purposes total about 6.5 B, and average loan is about 127K in every age, employment and credit group. Loans in the top size quartile default at 15.6 % vs 8.3 % in the bottom.
10. **Loan book is stable** – yearly loan amount moves between −1.5 % and +1.7 %.

### Recommended actions
1. Tighten approval/limits for young, low-income borrowers.
2. Require co-signers for the highest-risk segments.
3. Review pricing above 20 % interest to confirm it covers expected loss.
4. Add job tenure to approval rules and early-warning monitoring.

See the stakeholder deck for the visuals.

---

## 🧹 Data Notes & Issues Found

| # | Issue | Detail | Handling / advice |
|---|-------|--------|-------------------|
| 1 | **Date column header is misleading** | Header says `Loan Date (DD/MM/YYYY)` but values are **M/D/YYYY** (e.g. `10/15/2018`; the first part never exceeds 12, the second goes up to 31) | The Power BI model parsed them correctly (100 % match with M/D/YYYY). Rename the column to `Loan Date` to avoid confusion |
| 2 | **Dataset looks synthetic** | Categories are almost perfectly balanced (4 employment types ≈ 25 % each); income, loan amount, age and credit score are close to uniform; average loan and income are identical across groups | Treat results as a demonstration of method. Confirm on real portfolio data before changing policy |
| 3 | Imbalanced bins | Credit bins hold 47K, 23K, 93K and 92K loans; *Teen* has only 9.8K (ages 18–19) | Use equal-width or quantile bins where possible |
| 4 | Label typo | `Senoir Citizen` in the `Age Group` column | Fix the spelling in the DAX column |
| 5 | Age range | Ages are 18–69 only, so "Senior" means 60–69 | State the range on the dashboard |
| 6 | No currency | Amounts have no currency | Shown as "currency units" |

---

## ⚠️ Known Limitations & Planned Improvements

### Bugs to fix first
- [ ] **`Default Rate By Employment Type` is wrong.** It divides defaults in the group by **all** loans, because the variables are evaluated before `CALCULATE` removes the filter. The chart shows 2.4 – 3.4 % when the true rates are **9.5 – 13.6 %**. (The ranking is right; the magnitude is about 4× too low.) Replace it with one reusable measure that works in any chart:

  ```DAX
  Default Rate % =
  DIVIDE(
      CALCULATE( COUNTROWS( 'Loan_default' ), 'Loan_default'[Default] = TRUE() ),
      COUNTROWS( 'Loan_default' )
  ) * 100
  ```
  Use it for employment, age, credit, income, purpose, education and year; the separate "by Year" version can then be retired.
- [ ] **`YTD Loan Amount`** uses `DATESYTD` on a chart with no date axis, so it appears to return only the last year (2018). Add a year slicer or rename the measure – please confirm in Desktop.
- [ ] Fix typos in measure and column names: `Averge`, `Avergae`, `Eduction`, `Senoir`.

### Dashboard design
- [ ] **No slicers or KPI cards.** Add cards for *Total Loans, Total Loan Amount, Default Rate, Defaulted Amount* and slicers for Year, Loan Purpose, Employment Type, Age Group, Credit Bin and Income Bracket.
- [ ] **Line charts are used for categories** (employment, purpose, credit bins, age group, education). A line implies order or time – use **bar/column charts** (sorted for categories, ordered for bins).
- [ ] Several visuals plot **averages that are nearly identical** (average income by employment type, average loan by age). They do not help decisions – replace with *default rate* by the same dimension.
- [ ] Put **default rate** (the outcome) in more visuals; most visuals currently show loan amount.
- [ ] Add default-rate views by **income bracket, age band, interest-rate band, job tenure and co-signer**, which are the strongest drivers.
- [ ] Add a **default heat-map** (age band × income bracket) and a **top-risk segments** table.
- [ ] Show the **overall default rate as a reference line** on every rate chart.
- [ ] Start Y-axes at zero, add data labels, and replace the three empty "Measures Table" tables with one.
- [ ] Add a drill-through page to see the loans behind a segment, page navigation buttons and a short "how to read" note.

### Analytics
- [ ] Build a simple **logistic-regression / decision-tree** model to rank default drivers and score risk.
- [ ] Add **expected loss** (probability of default × loan amount) per segment.
- [ ] Re-test findings on a real, non-synthetic dataset.

---

## 🛠️ Tools & Skills

SQL Server (SSMS, database setup, flat-file import) · Power BI Desktop · Power Query · DAX (`CALCULATE`, `ALLEXCEPT`, `AVERAGEX`, `MEDIANX`, time intelligence) · calculated columns & binning · line, donut, clustered column, ribbon and decomposition-tree visuals · data validation · stakeholder storytelling

---

## ▶️ How to Run

1. Install **SQL Server** and **Power BI Desktop**.
2. Create the database and import the CSV as described in *Architecture → 1. Load the data into SQL Server* (database `Loan`, table `dbo.Loan_default`).
3. Open `Loan_Default.pbix`. The report points to server `DESKTOP-AQU1H18`, so go to **Home → Transform data → Data source settings → Change Source** and enter your own server name and the database `Loan`.
4. Click **Refresh**.

*No SQL Server?* In Power Query replace the `Source` step with `Csv.Document(File.Contents("<path>\data\Loan_default.csv"))`, promote headers and parse the date column as **M/D/YYYY (English – United States)**.

---

## 📁 Repository Structure

```
├── Loan_Default.pbix
├── README.md
├── data/
│   └── Loan_default.csv
├── sql/
│   └── load_dataset_into_sql.sql
├── presentation/
│   └── Loan_Default_Stakeholder_Deck.pptx
├── docs/
│   └── Dashboard_Questions.md
└── screenshots/
```

---

## 👤 Author

**<Your Name>** – [LinkedIn](https://linkedin.com/in/your-profile) · [GitHub](https://github.com/your-username)

*Practice project on a public loan-default dataset.*
