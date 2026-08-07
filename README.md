# Executive E-Commerce Analytics & Margin Leak Diagnostic

[![Power BI](https://img.shields.io/badge/Power_BI-F2C811?style=for-the-badge&logo=power-bi&logoColor=black)](#)
[![DAX](https://img.shields.io/badge/DAX-Data_Analysis_Expressions-blue?style=for-the-badge)](#)
[![Power Query](https://img.shields.io/badge/Power_Query-ETL-green?style=for-the-badge)](#)

---

## 📌 Business Overview

Standard e-commerce reporting often focuses strictly on top-line gross sales, masking underlying net profit drains. This diagnostic engine was built in Microsoft Power BI to analyze over 500,000 transaction records, systematically tracking net retained revenue, return velocity, and operational profit leaks across international retail markets.

---

## 🎯 Executive Strategy: The 80/20 Principle (Head & Tail Analysis)

Rather than analyzing routine operational noise, this project isolates the vital 20% of data metrics driving 80% of total financial impact:

* **The Head (Top Profit Drivers):** High-LTV customer accounts and hero SKUs generating consistent net retained margin.
* **The Tail (Defect & Profit Outliers):** Region-specific return surges, inventory misallocations, and single-SKU batch defect spikes.

---

## 📊 Executive Case Study: Diagnostic E-Commerce Analytics

### 1. Macro Volatility & Post-Holiday Return Lag
* **December Peak:** Analyzed 291K confirmed orders totaling **$540.89K Net Sales** and **$522.05K Net Profit**, maintaining a healthy **2.80% return rate**.
* **Lagged Return Surge (EIRE Market):** 
  * January recorded **$35.68K Net Sales** with a near-zero **0.03% return rate** (4 return orders).
  * February experienced a severe return velocity spike to **14.42%** (676 return orders), demonstrating a 30-day delayed return wave from peak holiday delivery cycles.

### 2. Regional Demand & Stock Arbitrage
* **SKU `M` Misallocation (Finland vs. Spain - Jan):**
  * **Finland:** Ranked as the **#1 top product** generating **$551.20** in net value with zero returns.
  * **Spain:** Inverted into the top returned product (**-100 units**), driving Spain into a **-$606.85 Net Profit deficit**.
* **Global Revenue Anchor (SKU `22423`):** Consistently ranked as a top revenue driver across multiple international markets (December Global #1 at $13.8K, EIRE #1/#2, Netherlands #2, Australia #1).

### 3. High-Impact Margin Leaks & Defect Diagnostics
* **Top Dollar-Value Leak (December):** SKU `20985` generated **$1,500 in returned product value** during peak season, serving as the single largest margin drain.
* **Batch Defect Anomalies (February):** SKU `22699` spiked in EIRE with **-222 returned units**, alongside standardized **-100 unit return clusters** across 8 additional SKUs, signaling supplier production/packaging issues.
* **Customer Account Concentration:** 100% of Spain's January loss (-$606.85) originated from just two B2B customer accounts (`12539` and `12540`).

### 🛠️ Strategic Recommendations
1. **Inventory Reallocation:** Shift inventory of SKU `M` away from low-converting southern European nodes directly to high-intent Nordic markets (Finland).
2. **Vendor Quality Audits:** Initiate supplier reviews for SKUs `20985` and `22699` to audit sizing accuracy and material quality.
3. **Credit Risk Controls:** Implement tiered return thresholds on high-concentration B2B accounts to mitigate single-client cash flow shocks.

## 🛠️ Technical Architecture & DAX Implementation

* **Data Transformation (Power Query):** Processed datetime stamps into segregated Date/Time dimensions, isolated return transactions (`C` prefix invoice codes), and cleaned null customer identifiers.
* **Data Modeling:** Implemented a star-schema architecture linking sales fact tables with Date, Customer, and Product dimensions.
* **DAX Formulas (Sample Core Measures):**

```dax
// 1. Net Sales Engine
Net Sales = 
SUMX(
    'Online_Retail',
    'Online_Retail'[Quantity] * 'Online_Retail'[UnitPrice]
)

// 2. Dynamic Return Rate Percentage
Return Rate % = 
VAR TotalReturnedUnits = ABS(CALCULATE(SUM('Online_Retail'[Quantity]), 'Online_Retail'[Quantity] < 0))
VAR TotalSoldUnits = CALCULATE(SUM('Online_Retail'[Quantity]), 'Online_Retail'[Quantity] > 0)
RETURN
DIVIDE(TotalReturnedUnits, TotalSoldUnits, 0)
