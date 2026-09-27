# Retail Sales — Data Cleaning, Analysis & Dashboard

## Overview

This project works with a 1,825-row daily retail sales dataset covering a full calendar year (2023), broken down by `Date`, `Category`, `Sales`, `Quantity`, `Profit`, and `Region`. Each day contains exactly one row for each of five product categories: Electronics, Clothing, Home Goods, Sports, and Books.

Unlike a typical "already clean" sample dataset, this one contained a mix of missing values and disguised nulls (text values like `"NaN?"`, `"Null"`, and `"Nan"` sitting alongside genuine blank cells) — requiring a deliberate approach to distinguish what could be safely recovered from what had to be left as an honest gap.

## Problem statement

Given a daily sales dataset with missing and disguised-null values across `Category`, `Sales`, `Quantity`, and `Region`, the goals were to:

1. Identify and standardize disguised nulls (`"NaN?"`, `"Null"`, `"Nan"`) alongside genuine blanks, rather than treating them as valid category or region values.
2. Recover missing values only where the data structure allowed it with certainty — and avoid fabricating values where it didn't.
3. Build a dashboard summarizing sales, quantity, and profit trends by month, category, and region.
4. Document every cleaning decision so the reasoning is traceable, including where data was deliberately left incomplete.

## Cleaning process

### Category (7 missing/disguised-null rows)

Every date in the dataset has exactly one row per category, all five categories represented every single day. This structure made it possible to recover the 7 missing `Category` values **with certainty, by elimination**: for any date missing one category, the missing value must be whichever of the five categories isn't already present among that date's other four rows. This is a deduction, not a guess — verified against the full dataset before being applied. Given the low row count, missing categories were confirmed and corrected manually rather than automated further.

### Sales and Quantity (2 and 5 missing rows, respectively)

Unlike a dataset with a fixed formula linking these columns (e.g. `Total = Price × Quantity`), this dataset's `Profit` margin varies row to row even within the same category, so there was no reliable calculation to back-derive an exact missing `Sales` or `Quantity` value from `Profit`. Recalculating from an unfixed relationship would have produced a plausible-looking but fabricated number, not a recovered one.

**Approach:** missing values were imputed using the **category median** (chosen over the mean for resistance to outliers), with a flag column marking imputed rows so they remain traceable and excludable from analysis that requires only observed data.

One pattern worth noting: **all 5 rows missing `Quantity` belonged to the `Sports` category** — a possible data-entry or logging issue specific to that category, kept as a documented observation rather than something the imputation should paper over.

### Profit (0 missing rows)

`Profit` had no missing values and was left untouched — it is independently observed data, and since it doesn't derive from a fixed formula involving `Sales`/`Quantity`, it was never a candidate for recalculation, even after those columns were imputed.

### Region (6 missing/disguised-null rows)

Region does not share Category's "one guaranteed value per day" structure, so there was no reliable way to recover a missing value with certainty. Rather than guessing or defaulting to the most common region (which would silently bias regional totals), these 6 rows were labeled **"Unknown"** and treated as their own category in region-based breakdowns — keeping their Sales/Quantity/Profit data usable everywhere else while flagging that they shouldn't be attributed to a specific region.

## Dashboard

Built in Excel using PivotTables and PivotCharts, with slicers for Region, Category, and Month. KPI cards and charts:

- **Total Transactions, Total Sales, Total Quantity, Total Profit** — headline KPI cards
- **Sales by Month** — ranked bar chart, highest to lowest
- **Monthly Profit Trend** — line chart across the full year
- **Profit Share by Region** — donut chart, including the "Unknown" region as its own slice
- **Profit by Category, Monthly** — clustered column chart comparing all five categories across every month

## Key insights

1. Total sales across the year reached **R1,374,503.17** from **1,825 transactions**, moving **18,348 units**.
2. **March was the strongest month for profit** (R121,248.87), followed closely by July and January, while **February came in lowest** (~R95,000) — suggesting a seasonal dip early in the year that recovers into spring.
3. **Region performance is fairly balanced but not even**: West leads at 27% of total profit, followed by North (26%) and South (25%), with East trailing at 22%.
4. **Category performance is relatively close across the board** (Books, Clothing, Electronics, Home Goods, Sports), with no single category dominating overall profit — indicating a well-diversified product mix rather than reliance on one category.
5. A small "Unknown" region slice reflects the 6 rows where region could not be reliably determined — kept visible rather than folded into an existing region, so it doesn't distort regional totals.

## Caveats and limitations

- **Imputed Sales/Quantity values** (7 rows total) are category-median estimates, not observed figures — flagged in the data and excludable from analysis requiring only real values.
- **6 rows labeled "Unknown" region** should not be included in any analysis that assumes every row has a known, valid region.
- The dataset spans a single year (2023); seasonal patterns described here reflect this one year only and shouldn't be generalized without further years of data.
- This dataset is used for practicing data cleaning and dashboarding; findings describe patterns within this dataset only.

## Tools used

- Microsoft Excel (PivotTables, PivotCharts, slicers, array formulas)
- Manual verification of inferred and imputed values before applying them at scale
