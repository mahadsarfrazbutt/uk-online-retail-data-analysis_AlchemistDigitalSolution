# uk-online-retail-data-analysis

An exploratory and business-focused analysis of online retail transactions, examining revenue trends, purchasing patterns, geographic performance, and customer behaviour through RFM segmentation.

[![Python](https://img.shields.io/badge/Python-3.x-3776AB?logo=python&logoColor=white)](https://www.python.org/)
[![Pandas](https://img.shields.io/badge/Pandas-data%20analysis-150458?logo=pandas&logoColor=white)](https://pandas.pydata.org/)
[![NumPy](https://img.shields.io/badge/NumPy-numerical%20computing-013243?logo=numpy&logoColor=white)](https://numpy.org/)
[![Matplotlib](https://img.shields.io/badge/Matplotlib-visualization-11557C)](https://matplotlib.org/)
[![Seaborn](https://img.shields.io/badge/Seaborn-statistical%20visualization-4C72B0)](https://seaborn.pydata.org/)
[![Plotly](https://img.shields.io/badge/Plotly-interactive%20charts-3F4F75?logo=plotly&logoColor=white)](https://plotly.com/python/)
[![Jupyter](https://img.shields.io/badge/Jupyter-Notebook-F37626?logo=jupyter&logoColor=white)](https://jupyter.org/)

## Table of Contents

- [2. Project Overview](#2-project-overview)
- [3. Business Questions](#3-business-questions)
- [4. Dataset Description](#4-dataset-description)
- [5. Tools and Technologies](#5-tools-and-technologies)
- [6. Project Workflow](#6-project-workflow)
- [7. Data Cleaning and Preprocessing](#7-data-cleaning-and-preprocessing)
- [8. Revenue Analysis and Exploratory Data Analysis](#8-revenue-analysis-and-exploratory-data-analysis)
- [9. RFM Customer Segmentation](#9-rfm-customer-segmentation)
- [10. Key Findings and Business Insights](#10-key-findings-and-business-insights)
- [11. Business Recommendations](#11-business-recommendations)
- [12. Project Structure](#12-project-structure)
- [13. How to Run the Project](#13-how-to-run-the-project)
- [14. Limitations](#14-limitations)
- [15. Future Improvements](#15-future-improvements)
- [16. Author and Acknowledgements](#16-author-and-acknowledgements)

## 2. Project Overview

This project uses transaction-level data from the UCI Online Retail dataset to explore when revenue is generated, how it is distributed across countries, and how purchasing activity differs among identifiable customers. The analysis includes data inspection, type standardization, descriptive aggregations, visual analysis, and rule-based RFM segments.

Retail transaction data helps a business connect day-to-day orders with broader operational questions. Revenue by date and market can inform campaign timing and market prioritization. Invoice-level customer measures add a view of recency, repeat purchasing, and customer revenue that product-line totals alone cannot provide.

The current analysis is descriptive. It identifies patterns in the historical extract; it does not establish why a pattern occurred or predict future sales.

## 3. Business Questions

**Business Question 1: Revenue and Temporal Analysis**

> Which months, days, and hours generate the most revenue, and how does average order value change over time?

Revenue and order volume by time can help a retailer plan promotions, staffing, and campaign schedules. Average order value can indicate whether revenue changes alongside the number of orders or the value of each order. The current notebook charts revenue and order volume over time, but it does not yet calculate or visualize average order value over time.

**Business Question 2: Geographic Revenue Analysis**

> Which countries contribute the most to total revenue, and how does revenue distribution vary across geographic markets?

Country-level comparison shows the dominant markets and the relative scale of other countries. These results can guide further market research, while the concentration in the largest market cautions against assuming that all countries behave similarly.

**RFM extension:** Recency, Frequency, and Monetary analysis adds a customer-level view. It groups customers by past purchasing activity and net revenue contribution, helping distinguish recently active and high-value groups from customers who may need retention outreach.

## 4. Dataset Description

The project uses the **Online Retail** workbook from the [UCI Machine Learning Repository](https://archive.ics.uci.edu/dataset/352/online+retail). UCI describes it as transactional data from a UK-based, non-store online retailer, covering **1 December 2010 through 9 December 2011**. The business sold gifts, and many customers were wholesalers. The local workbook contains **541,909 rows and 8 columns** before the project’s cleaning filter.

The original file is named `Online Retail.xlsx`. Each row describes a product line within a transaction. Several product rows can share an `InvoiceNo`; those rows together form an order and must be consolidated before calculating invoice-level order values or order counts.

| Column | Meaning in this project |
|---|---|
| `InvoiceNo` | Invoice identifier. A code beginning with `C` identifies a cancellation in the source data. |
| `StockCode` | Product or stock identifier. |
| `Description` | Product name or description. |
| `Quantity` | Number of units on the invoice line. Negative values are treated as return lines in the analysis. |
| `InvoiceDate` | Date and time the transaction was generated. |
| `UnitPrice` | Price per unit, in sterling (GBP) according to UCI. |
| `CustomerID` | Customer identifier. Missing IDs prevent attributing a line to a known customer. |
| `Country` | Country associated with the customer. |

The initial workbook survey found **38 countries**, **25,900 distinct invoice identifiers**, **4,070 distinct stock codes**, **1,454 missing descriptions**, and **135,080 missing customer IDs** in the raw file. The invoice date range in the workbook is 2010-12-01 08:26 through 2011-12-09 12:50. These are source-workbook figures, before the two negative-unit-price rows are filtered in the analysis notebook.

## 5. Tools and Technologies

| Technology | Role in the project |
|---|---|
| Python | Core programming language for the notebook analysis. |
| Pandas | Loads the workbook, standardizes fields, groups transaction rows, and builds customer-level metrics. |
| NumPy | Supports numerical operations, scoring, and rule-based segment assignment. |
| Matplotlib | Renders static charts. |
| Seaborn | Produces the static bar charts and heatmaps. |
| Plotly | Produces interactive time-series and geographic charts. |
| Jupyter Notebook | Provides the interactive analysis environment and preserves code, explanations, tables, and chart outputs. |
| Python `sqlite3` (standard library) | Stores an unchanged copy of the original table in the SQLite database created in the exploratory notebook. SQL is not used for the dataframe analysis. |

The notebooks use the `python-calamine` Excel engine for workbook loading. `openpyxl` is imported by the expanded inventory notebook but was not used due to problems raised by windows defender. **SciPy is not used** in the current analysis.

## 6. Project Workflow

1. **Dataset inspection:** the eda notebook load the workbook, preview rows, and report dimensions, columns, inferred types, distinct values, and missingness. This establishes what the source actually contains.
2. **Data quality assessment:** the survey reports exact duplicate rows, missing identifiers and descriptions, negative quantities, negative prices, zero prices, and date ranges. These checks make data limitations visible before analysis.
3. **Data cleaning:** the data_cleaning_and_analysis notebook converts text and identifier fields to pandas string type, parses `InvoiceDate`, keeps `Quantity` as an integer, and makes `UnitPrice` a float. The code filters negative `UnitPrice` rows.
4. **Missing-value handling:** missing descriptions become pandas `NA`; missing customer identifiers remain missing rather than being imputed. Missing IDs are excluded from customer-level RFM attribution but remain in the cleaned transaction table.
5. **Cancellations and returns:** invoice identifiers beginning with `C` mark cancellations. Negative-quantity lines are recognized separately as returns. Completed-sales visualizations exclude both. RFM monetary value subtracts return amounts when a valid customer can be identified; in the current data, the non-cancelled return lines have no valid customer ID.
6. **Feature engineering:** the notebook calculates line revenue and derives date, month, month-year, weekday, and hour fields for charts. It also derives calendar quarter.
7. **Invoice-level aggregation:** product lines are grouped by `InvoiceNo` for order totals and unique order counts. This prevents a basket with several products from being counted as several orders.
8. **Exploratory data analysis:** daily, monthly, weekday, hour, and country-level completed-sales summaries are prepared.
9. **Business-oriented visualizations:** static Seaborn charts and interactive Plotly charts present temporal and geographic patterns.
10. **RFM segmentation:** valid customer purchase invoices are consolidated before computing recency, frequency, and monetary metrics. Customers then receive scores and rule-based segments.
11. **Interpretation:** notebook outputs summarize the observed market and segment patterns. Findings remain descriptive and depend on the transaction and customer-ID coverage described below.

## 7. Data Cleaning and Preprocessing

### Missing values

The inventory notebook calculates both missing counts and percentages. In raw format, `Description` has 1,454 missing values and `CustomerID` has 135,080. The data_cleaning_and_analysis notebook converts `Description` to pandas string type and represents missing descriptions as `pd.NA`. It does not delete those product lines or fill descriptions with an invented value.

Missing `CustomerID` values are retained in `retail_df`. They cannot be reliably assigned to a customer, so RFM excludes those lines from customer-level metrics and reports the excluded sales and returns. This preserves transaction-level analysis while avoiding fabricated customer identities.

### Duplicate records

The survey reports **5,268 exact duplicate rows** in the raw format. The cleaning code does not call `drop_duplicates`; those records remain. This follows the project’s row-preservation rule, but repeated lines can inflate revenue and quantity sums. The duplicate count should be considered when interpreting totals. Duplicate values and rows can also occur in the case of same InvoiceNo over different products.

### Cancelled invoices

The notebook identifies cancellation invoices with `InvoiceNo` values beginning with `C`. They remain in the cleaned transaction DataFrame and in the general `countryrevenue_df`, `invoice_df`, and `stock_df` aggregates. For completed-sales visualizations and RFM purchase metrics, those invoice lines are excluded because cancellation activity should not be treated as completed sales. The current execution identifies **9,288 cancellation rows across 3,836 invoice identifiers**.

### Returns and negative quantities

Negative `Quantity` values are treated as return lines. They remain in the cleaned transaction DataFrame, and the line-level `Revenue = Quantity × UnitPrice` calculation therefore preserves negative revenue where it occurs.

The temporal and geographic visualization dataset is `sales_rows`: it excludes cancellation invoices and all negative-quantity lines. Its revenue is therefore **completed-sales revenue**, not net sales after returns. In RFM, returns are counted separately and their positive refund value is subtracted from purchase revenue for a customer with a valid ID. In the current run, all **1,336 non-cancelled negative-quantity lines** lack a valid `CustomerID`; the recorded return value on those lines is £0.00. They cannot be attributed to a known customer, so no return amount is subtracted from an RFM customer.

### Invalid and unusual transactions

- The cleaning code filters rows where `UnitPrice < 0`, with the comment that a unit selling price cannot be negative. The source workbook contains **2 such rows**; re-running the current code removes them. The saved output attached to the cleaning cell reports zero dropped rows, so that output is stale and should be refreshed before publication.
- Zero-price lines are retained; the raw workbook contains **2,515** of them.
- Zero-quantity lines are not present in the raw survey. Negative quantities are retained in the cleaned table and handled as returns in downstream sales/RFM logic.
- Exact duplicate rows are retained, as described above. No other rows are removed by the cleaning step.

### Data types and derived features

The cleaning code uses pandas strings for `InvoiceNo`, `StockCode`, `Description`, `CustomerID`, and `Country`; integer `Quantity`; datetime `InvoiceDate`; and floating-point `UnitPrice`. It does not impute missing customer IDs.

| Derived field | Calculation and use |
|---|---|
| `Revenue` | `Quantity × UnitPrice` for each product line. Negative quantity can produce negative line revenue. |
| `Date` | Normalized date component of `InvoiceDate`, used for daily revenue aggregation. |
| `Month` | Calendar month name from `InvoiceDate`. |
| `MonthYear` | Calendar year-month period, used to order monthly summaries chronologically. |
| `DayofWeek` | Weekday name from `InvoiceDate`. The code uses this capitalization. |
| `Hour` | Hour component from `InvoiceDate`, used in the weekday-hour heatmap. |
| `Quarter` | Calendar quarter (`Q1`–`Q4`) derived from `InvoiceDate`. |

The general `invoice_df` groups line items by `InvoiceNo`, summing `Revenue` and retaining the date, description list, and stock-code list. It includes cancellation and return activity. The separate `sales_invoice_df` is built from completed-sale lines and has one row per invoice; it is used where completed order values or unique order counts are needed.

## 8. Revenue Analysis and Exploratory Data Analysis

The current saved data_cleaning_and_analysis notebook contains numerous temporal/geographic charts.
The EDA revenue charts use `sales_rows`, which keeps non-cancelled lines with positive quantity and non-negative unit price. Cancellations and return lines are excluded rather than netted against completed sales. The completed-sales total under this rule is **£10,666,684.54**.

### 1. Daily Revenue Trends

**Implemented — interactive Plotly line chart.** The notebook sums completed-sales line revenue by `Date` before plotting. A line chart is suitable for a sequential daily measure and helps reveal fluctuations and peaks across the dataset’s date range. It addresses the temporal part of Business Question 1. The interactive chart supports hover, zooming, panning, and a range slider.

<img width="1423" height="429" alt="image" src="https://github.com/user-attachments/assets/11515ab0-2c45-437c-87c8-6cafa2b464f1" />


### 2. Monthly Revenue Comparison

**Implemented — Seaborn bar chart.** Revenue is summed by `MonthYear`, which preserves chronology across calendar years. A bar chart makes month-to-month totals easy to compare and highlights stronger and weaker periods. It addresses Business Question 1. The dataset ends on 9 December 2011, so December 2011 is an incomplete month and should not be compared with full months without qualification.

<img width="1181" height="474" alt="image" src="https://github.com/user-attachments/assets/3d5ba9b6-733e-4109-afba-ace4e70da11b" />


### 3. Order Volume by Day of the Week

**Implemented — Seaborn bar chart.** The notebook first consolidates completed sales to one row per invoice, then counts unique `InvoiceNo` values by weekday. This measures orders, not product lines. The weekday order follows Monday through Sunday, with Saturday shown as zero because there are no completed-sale observations for that day in this extract. It addresses purchasing activity by day and complements the revenue comparisons in Business Question 1.

<img width="971" height="470" alt="image" src="https://github.com/user-attachments/assets/55f90eff-3e8f-474b-a831-994480722129" />

### 4. Revenue Distribution by Day and Hour

**Implemented — Seaborn heatmap.** The heatmap sums completed-sales revenue by weekday and hour, then reindexes to include all seven weekdays and 24 hours. Its color intensity makes combinations of day and hour with higher sales visible at a glance. It addresses the hour and weekday portions of Business Question 1. Cells are not annotated because 168 labels would reduce readability.

<img width="1313" height="534" alt="image" src="https://github.com/user-attachments/assets/b7bd5503-5fdd-4789-a46c-f293502982d5" />

### 5. Geographic Revenue Concentration

**Implemented — interactive Plotly treemap.** Each rectangle represents one country, and its area and color encode the country’s completed-sales revenue. Hover details show the total and share. A treemap is useful for seeing how concentrated revenue is across markets, addressing Business Question 2. The chart’s hover and color-bar formats currently use a dollar sign even though the UCI source prices are in sterling; the displayed currency formatting should be corrected before publication.

<img width="1425" height="648" alt="image" src="https://github.com/user-attachments/assets/3e2ec30c-282a-4c54-b133-775672006242" />

### 7. Revenue Contribution by Country

**Implemented — interactive Plotly horizontal bar chart.** The notebook sorts all 38 countries by completed-sales revenue and highlights the United Kingdom separately. A horizontal bar chart supports direct comparisons and keeps country labels readable. It addresses which markets contribute the most in Business Question 2. Its current axis and hover formatting also use dollars rather than GBP, so the chart labels need correction before publication.

<img width="1418" height="830" alt="image" src="https://github.com/user-attachments/assets/f11b1983-e81a-4a67-ae6a-315a0c349b08" />

**Average order value status:** the notebook has invoice-level totals available in `sales_invoice_df`, but it does not aggregate or plot average order value by month. That part of the first business question remains unimplemented.

## 9. RFM Customer Segmentation

RFM summarizes customer behavior using three measures:

- **Recency:** days since the customer’s most recent valid purchase. Lower values mean more recently active customers.
- **Frequency:** number of unique completed purchase invoices for the customer.
- **Monetary:** completed purchase revenue less attributable return value.

### Calculation and data scope

The RFM analysis requires a nonblank `CustomerID`, a valid invoice identifier, a non-cancelled invoice, positive quantity, and non-negative unit price for a purchase line. Product rows are grouped by `CustomerID` and `InvoiceNo` before customer aggregation. This makes frequency a count of invoices rather than product lines.

The reference date is **10 December 2011**, one day after the latest valid completed transaction date used in the current run. Recency is the calendar-day difference between that reference date and each customer’s latest purchase date. Frequency counts distinct valid purchase invoices. Purchase revenue is summed at invoice level. Non-cancelled negative-quantity lines are treated as returns; their refund value is subtracted from Monetary when it can be assigned to a customer. Return-only customers cannot receive Recency or Frequency scores and are reported separately.

The resulting RFM population contains **4,339 customers**. The current data contains 133,359 positive-sale lines without a valid customer ID, representing £1,755,276.64 in completed sales. Those sales remain part of overall EDA but cannot be attributed to an RFM customer. The 1,336 non-cancelled return lines also lack valid customer IDs, so the current RFM monetary totals have no attributable return deduction.

### Scoring and combined RFM score

Each metric is assigned a score from 1 to 5 using percentile ranks and five equal score bands. Average ranks keep tied metric values together; if a metric has no variation, the notebook assigns a neutral score of 3 to everyone for that metric. Recency scoring is reversed so a smaller number of days produces a higher score. Higher Frequency and Monetary values produce higher scores. The three scores are concatenated into a string such as `555`.

### Segment rules

Rules are applied in the following order, ensuring every customer in the RFM population receives one segment:

| Segment | Implemented rule |
|---|---|
| Champions | `R_Score >= 4`, `F_Score >= 4`, and `M_Score >= 4` |
| Loyal Customers | `F_Score >= 4` and `R_Score >= 3` (after Champions) |
| Potential Loyalists | `R_Score >= 4` and `F_Score` is 2 or 3 |
| At Risk | `R_Score <= 2` and (`F_Score >= 3` or `M_Score >= 3`) |
| Hibernating | `R_Score <= 2` and `F_Score <= 2` (after At Risk) |
| Regular Customers | Does not meet an earlier rule |

These segments help distinguish high-value, active customers from customers with declining or limited purchasing activity. They support targeted retention planning, but they describe past behavior rather than future likelihood.

### RFM visualizations

1. **Customer Distribution Across RFM Segments — implemented.** A Seaborn bar chart counts customers per segment and labels the bars. It shows the relative size of each group.
2. **Revenue Contribution by Customer Segment — summary table implemented; chart not present.** The notebook reports segment net Monetary and its percentage of RFM customer revenue in a table. A separate revenue-contribution visualization has not been implemented.
3. **Average RFM Scores Across Customer Segments — implemented.** A Seaborn heatmap displays the mean R, F, and M scores within each segment. It shows how the rule-based groups differ across the three scoring dimensions.

<!-- IMAGE: RFM CUSTOMER DISTRIBUTION -->
<img width="1084" height="534" alt="image" src="https://github.com/user-attachments/assets/52dffd8d-eec6-49ea-8e3d-57feb110a531" />


<!-- IMAGE: RFM SEGMENT HEATMAP -->
<img width="1069" height="694" alt="image" src="https://github.com/user-attachments/assets/9550a375-b69d-4c1f-a250-888d048fd9a0" />


## 10. Key Findings and Business Insights

The values below were calculated from the supplied workbook using the completed-sales filter implemented in the notebook: exclude `C` invoices and negative-quantity lines, then sum positive-quantity line revenue. Revenue is in GBP based on the source data dictionary.

### Revenue and timing

- The completed-sales view covers **£10,666,684.54** across the extract.
- **November 2011** is the highest-revenue month at **£1,509,496.33**, followed by October (**£1,154,979.30**) and September (**£1,058,590.17**). December 2011 is partial, ending on 9 December.
- **Thursday** has the highest weekday revenue (**£2,203,161.24**), followed by Tuesday (**£2,178,632.61**). No completed-sale rows fall on Saturday in this extract.
- **10:00** has the highest hourly revenue (**£1,446,742.70**), closely followed by 12:00 (**£1,444,245.88**). These are descriptive totals, not evidence that the hour caused higher sales.
- **Average order value by month is not implemented** in the current notebook, so no AOV trend is reported here.

### Geographic revenue

| Country | Completed-sales revenue | Share of completed-sales revenue |
|---|---:|---:|
| United Kingdom | £9,025,222.08 | 84.6% |
| Netherlands | £285,446.34 | 2.7% |
| EIRE | £283,453.96 | 2.7% |
| Germany | £228,867.14 | 2.1% |
| France | £209,715.11 | 2.0% |

The United Kingdom is the dominant market in the completed-sales view. The other leading countries are much smaller contributors; their performance should be compared separately rather than hidden in the UK total.

### Customer segments

| Segment | Customers | Customer share | Net Monetary | Share of RFM net revenue |
|---|---:|---:|---:|---:|
| Champions | 911 | 21.0% | £5,688,406.20 | 63.8% |
| Loyal Customers | 381 | 8.8% | £826,118.57 | 9.3% |
| Potential Loyalists | 484 | 11.2% | £586,236.63 | 6.6% |
| At Risk | 936 | 21.6% | £1,163,799.47 | 13.1% |
| Hibernating | 791 | 18.2% | £178,824.76 | 2.0% |
| Regular Customers | 836 | 19.3% | £468,022.27 | 5.3% |

At Risk is the largest group by customer count. Champions generate the largest share of identified customers’ net Monetary. Loyal Customers are a smaller group whose revenue share (9.3%) is slightly above its customer share (8.8%). At Risk and Hibernating together include **1,727 customers (39.8%)**.

### Data patterns requiring care

- The raw file contains **5,268 exact duplicate rows**, which are retained and may raise line-level revenue or quantity totals.
- There are **10,624 negative-quantity rows** in the raw workbook. The completed-sales charts exclude non-cancelled returns rather than netting refunds against sales.
- There are **1,454 missing descriptions** and **135,080 missing customer IDs** in the raw data. Missing IDs limit customer-level coverage.
- The current saved output for the cleaning cell says zero negative-price rows were removed, but rerunning the code on the workbook finds **2** and filters them. Refresh notebook outputs before treating saved cell outputs as current.
- Country treemap and bar chart labels currently format values with `$`, despite the source unit price being in GBP. Correct this display issue before using screenshots externally.

## 11. Business Recommendations

The following are proposed actions based on the descriptive findings; they are not tested interventions.

- **Plan around observed periods:** use the strong November and Thursday/10:00–12:00 totals as hypotheses for campaign and staffing tests. Compare results over additional periods before making permanent schedule changes.
- **Protect Champions:** the segment contributes 63.8% of identified-customer net revenue. Prioritize service reliability, relevant early access, and loyalty benefits, and monitor whether these actions sustain repeat purchases.
- **Support Loyal and Potential Loyalist customers:** test relevant bundles, replenishment reminders, or next-purchase offers, measuring incremental margin rather than discount redemptions alone.
- **Run measured win-back campaigns for At Risk customers:** this is the largest segment and contributes 13.1% of RFM net revenue. Test modest, targeted outreach and track reactivation.
- **Keep Hibernating outreach economical:** their revenue share is 2.0%; test low-cost messaging and limit repeated incentives to customers who do not respond.
- **Investigate markets outside the UK:** the Netherlands and EIRE are the leading non-UK contributors in the current completed-sales calculation. Examine order economics, shipping costs, and repeat purchase behavior before allocating more budget.
- **Review duplicate and missing-ID data collection:** resolving the source of duplicate lines and improving customer-ID capture would strengthen transaction totals and customer segmentation coverage.

## 12. Project Structure

The current project files are organized at the repository root. The main notebook is named `02_data_cleaning_and_analysis.ipynb`.

```text
.
├── 01_eda.ipynb               # Initial data load, inventory, and SQLite copy
├── 02_data_cleaning_and_analysis.ipynb  # Cleaning, EDA visualizations, and RFM analysis
├── Online Retail.xlsx         # Local source workbook; obtain from UCI when reproducing
├── online_retail_db.sqlite    # SQLite storage copy created by the exploratory notebook
└── README.md
```

The workbook and SQLite database are local project artifacts and are not embedded in this README. Check the UCI attribution and dataset license before redistributing the workbook or database.

## 13. How to Run the Project

The notebooks expect `Online Retail.xlsx` in the project root. The current notebook code contains a Windows-specific `project_dir` path; change that path in the first setup cell to the local checkout path when running elsewhere.

### Windows PowerShell

```powershell
git clone <REPOSITORY_URL>
Set-Location <REPOSITORY_DIRECTORY>
py -m venv .venv
.\.venv\Scripts\Activate.ps1
python -m pip install --upgrade pip
python -m pip install pandas numpy matplotlib seaborn plotly jupyter python-calamine openpyxl
```

### macOS or Linux

```bash
git clone <REPOSITORY_URL>
cd <REPOSITORY_DIRECTORY>
python3 -m venv .venv
source .venv/bin/activate
python -m pip install --upgrade pip
python -m pip install pandas numpy matplotlib seaborn plotly jupyter python-calamine openpyxl
```

If the workbook is not included in a clone, download `Online Retail.xlsx` from the [UCI dataset page](https://archive.ics.uci.edu/dataset/352/online+retail) and place it beside the notebooks. Then launch Jupyter:

```bash
jupyter notebook
```

Open `02_data_cleaning_and_analysis.ipynb` for the cleaning, charts, and RFM workflow. `sqlite3` is part of Python’s standard library; it is used to create the storage copy in `01_eda.ipynb`, not as the analysis engine.

## 14. Limitations

- The source covers roughly one year and ends on 9 December 2011. It is historical and December 2011 is incomplete.
- Missing customer IDs affect 135,080 raw rows, so customer-level RFM describes only the identifiable purchaser subset.
- Cancellations and returns are treated differently by analysis stage. Sales charts exclude both, while RFM subtracts returns only when attributable to an identified customer. These figures should not be described interchangeably as gross, net, or total revenue.
- Exact duplicate rows are reported but retained; they may inflate line-level measures.
- Extreme order and line values can influence totals and averages. The current notebook does not implement the requested order-value box plot.
- The notebook is descriptive and does not use hypothesis tests, causal identification, forecasting, or predictive churn modeling.
- RFM summarizes historical behavior. It does not estimate future purchases or customer lifetime value.
- Saved notebook outputs can be stale, as shown by the negative-unit-price drop-count discrepancy. Re-run from the top before publishing screenshots or relying on displayed output.
- Some geographic chart labels use dollar formatting even though the source prices are denominated in sterling.

## 15. Future Improvements

Potential extensions, not completed features:

- Implement monthly average order value from invoice-level totals and add a weekday order-value box plot.
- Add a dedicated customer-segment revenue contribution chart and export all chart screenshots after correcting GBP formatting.
- Reconcile and document duplicate-line handling with a clear source-data policy.
- Separate gross completed sales, returns, cancellations, and net revenue in the reporting layer.
- Perform statistical hypothesis tests for observed temporal or geographic differences.
- Estimate customer lifetime value and develop predictive churn models.
- Forecast sales using the time series, accounting for the short historical period.
- Apply market basket analysis to product combinations.
- Build an interactive business intelligence dashboard.
- Replace hard-coded local paths with paths relative to the notebook or project root and add a dependency file for reproducible setup.

## 16. Author and Acknowledgements

**Author:** Mahad Sarfraz

The dataset is provided by the [UCI Machine Learning Repository](https://archive.ics.uci.edu/dataset/352/online+retail). UCI lists the Online Retail dataset under a **CC BY 4.0** license; provide attribution when sharing or adapting the dataset. A suggested citation is: Chen, D. (2015). *Online Retail* [Dataset]. UCI Machine Learning Repository. <https://doi.org/10.24432/C5BW33>.
