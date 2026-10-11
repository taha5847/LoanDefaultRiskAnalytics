# Questions Answered by the Loan Default Dashboard

This document lists every business question the Power BI dashboard (`Loan_Default.pbix`) answers, which visual answers it, and what the data says. All numbers come from the 255,347-loan dataset (2013 – 2018).

**Overall context:** 255,347 loans · 32.6 billion total loan amount · 29,653 defaults = **11.6 % default rate** · defaulted principal 4.29 billion (13.2 % of the book).

> ⚠️ **Correction note.** The *Default Rate (%) by Employment Type* chart (Q2) currently shows 2.4 – 3.4 % because the measure divides by all loans instead of loans in each group. The values below are the correct ones; see the README for the fixed DAX.

---

## Page 1 – Loan Default & Overview

| # | Question | Visual | What the data says |
|---|----------|--------|--------------------|
| 1 | **How has the default rate changed from year to year?** | Line chart – *Default Rate (%) by Year* | It has not. 2013 11.6 % · 2014 11.5 % · 2015 11.7 % · 2016 11.75 % · 2017 11.5 % · 2018 11.6 %. About 42,500 loans per year. |
| 2 | **Which employment type has the highest default rate?** | Line chart – *Default Rate (%) by Employment Type* | **Unemployed 13.6 %**, Part-time 12.0 %, Self-employed 11.5 %, Full-time 9.5 %. (Dashboard currently shows 3.4 / 3.0 / 2.9 / 2.4 % – see correction note.) |
| 3 | **How much money is lent for each loan purpose?** | Line chart – *Loan Amount By Purpose* | Evenly spread: Home 6.55 B · Business 6.52 B · Education 6.51 B · Auto 6.50 B · Other 6.50 B. No purpose dominates. |
| 4 | **Does average income differ by employment type?** | Line chart – *Average Income By Employment Type* | Barely: Full-time 82.9K · Self-employed 82.4K · Part-time 82.4K · Unemployed 82.3K. |
| 5 | **Do older or younger borrowers take bigger loans?** | Line chart – *Average Loan Amount by Age Group* | No. Adult 127.9K · Middle Age 127.5K · Senior 127.4K · Teen 126.7K. Loan size is not age-driven. |

## Page 2 – Applicant Demographic and Financial

| # | Question | Visual | What the data says |
|---|----------|--------|--------------------|
| 6 | **What is the typical (median) loan amount in each credit-score category?** | Line chart – *Median Loan Amount By Credit Score Category* | About the same everywhere: Low 128.4K · Medium 127.8K · Very Low 127.5K · High 127.1K. Lenders are not lending less to weak credit. |
| 7 | **How much do adults (20–39) borrow in each credit-score category?** | Line chart – *Total Loan Amount By Credit Score Category (Adults)* | Medium 4.58 B · High 4.54 B · Very Low 2.31 B · Low 1.14 B – driven by how many borrowers fall in each band. |
| 8 | **How do mortgage and dependents affect borrowing for middle-aged adults (40–59)?** | Clustered column – *Loan (Middle Age Adults) by Mortgage / Dependents* | No effect on volume: all four combinations total 3.1 B. |
| 9 | **What is the average loan for high-credit borrowers by age group and marital status?** | Donut – *Average Loan Amount (High Credit)* | All 124K – 128K. Teen borrowers who are divorced or married are slightly lower (124.0K / 124.9K). |
| 10 | **How many loans are there for each education level?** | Line chart – *Number of Loans By Education Type* | Evenly split: Bachelor's 64,366 · High School 63,903 · Master's 63,541 · PhD 63,537. |

## Page 3 – Financial Risk Metrics

| # | Question | Visual | What the data says |
|---|----------|--------|--------------------|
| 11 | **How is the loan book growing year over year?** | Line chart – *YOY Loan Amount (%)* | Flat: 2014 −1.5 % · 2015 +1.3 % · 2016 0.0 % · 2017 −1.1 % · 2018 +1.7 %. |
| 12 | **Are defaults growing or shrinking year over year?** | Line chart – *YOY Default Loans Changes (%)* | Moves with volume: 2014 −2.6 % · 2015 +2.7 % · 2016 +0.8 % · 2017 −2.8 % · 2018 +1.9 %. No upward trend. |
| 13 | **How is the loan amount split by credit-score band and marital status?** | Ribbon chart – *YTD Loan Amount* | Roughly equal across Divorced / Married / Single within each band; Medium and High credit bands are the largest. (Measure appears to show only the last year, 2018 – see README.) |
| 14 | **Which income groups and employment types make up the loan book?** | Decomposition tree – Loan Amount by *Income Bracket* → *Employment Type* | **High income 66.7 %**, Medium 22.1 %, Low 11.2 % of loan amount. Within each bracket the four employment types are equal. |

---

## Questions the dashboard *could* answer with small changes

These are the questions a stakeholder is most likely to ask next. The data answers them; the dashboard does not yet show them.

| Question | Answer from the data |
|----------|----------------------|
| What is the overall default rate and defaulted amount? | 11.6 % and 4.29 B (13.2 % of the book) – add KPI cards. |
| **Which age group defaults most?** | 18–24: **21.0 %**; 25–34 16.6 %; 35–44 12.0 %; 45–54 8.8 %; 55–64 6.0 %; 65–69 4.9 %. Dashboard groups: Teen 22.1 %, Adult 16.7 %, Middle Age 8.7 %, Senior 5.1 %. |
| **Does income affect default?** | Yes: Low (< 30K) **22.0 %**, Medium 12.8 %, High 9.5 %. |
| Does credit score predict default? | Weakly: 13.3 % (≤ 400) to 10.1 % (> 750). |
| Do higher interest rates mean more defaults? | Yes: 6.3 % (lowest quintile) to 18.0 % (highest). |
| Do co-signers help? | Yes: 10.4 % with a co-signer vs 12.9 % without. |
| Does job tenure matter? | Yes: 16.9 % for ≤ 12 months vs 8.9 % for > 5 years. |
| Which borrower segment is riskiest? | Age ≤ 34 and income < 30K: 32.5 % default (3.6 % of loans). |
| Which segment is safest? | Age 45+ and income 60K+: 5.4 % default (32.1 % of loans). |
| Do larger loans default more? | Yes: 8.3 % (smallest quartile) to 15.6 % (largest quartile). |
| Does loan purpose or term matter? | Little: purposes 10.2 % (Home) to 12.3 % (Business); terms all ≈ 11.6 %. |

---

## How to read the answers

- The dataset is unusually balanced and likely synthetic, so many "no difference" results (Q3 – Q10) are expected. The useful risk signals are **age, income, job tenure, interest rate and co-signer**.
- Findings are associations, not proven causes.
- Currency is not stated in the data; amounts are in "currency units".
