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

## 📊 Diagnostic Findings & Operational Case Studies

### 1. Macro Seasonality & Post-Holiday Return Surges
* **Q4 Holiday Peak:** December achieved peak performance with **$540.89K Net Sales** and **$522.05K Net Profit** across 291K confirmed orders, maintaining a healthy **2.80% return rate**.
* **Post-Holiday Return Lag (EIRE Case Study):** 
  * **January:** Strong initial net sales (**$35.68K**) with negligible returns (**0.03% return rate**, 4 orders).
  * **February:** Net sales normalized to **$8.63K**, while return velocity surged dramatically to **14.42%** (676 returned orders).
  * *Takeaway:* Return processing lags by ~30 days, causing post-holiday cash flow distortion if unmonitored.

### 2. Regional Inventory Misallocation (SKU `M`)
* **Finland:** SKU `M` operated as the **#1 performing product**, bringing in **$551.20** with zero returns.
* **Spain:** SKU `M` inverted into a net loss driver (**-100 returned units**), pushing Spain into a monthly net loss of **-$606.85**.
* *Takeaway:* Stock allocation in southern European nodes created unnecessary return shipping overhead, whereas shifting allocation to Nordic markets captures immediate, unfulfilled demand.

### 3. Hero Performance vs. Defect Anomalies
* **Hero Asset (SKU `22423`):** Consistently ranked as a top revenue driver across global markets (December Global #1 at $13.8K; EIRE top performer in Jan/Feb; Netherlands #2 in Feb; Australia #1 in Feb).
* **Major Loss Drivers:**
  * **Global Peak Leak (Dec):** SKU `20985` registered **1,500 returned units**, standing as the single largest peak-season margin leak.
  * **Batch Defect Spikes (Feb):** EIRE and Australia exhibited standardized bulk return clusters (e.g., **-222 units** on SKU `22699` and repeated **-100 unit** return spikes across SKUs `22738` and `23079`), indicating supplier batch defects.

### 4. Account Concentration Risk
* **EIRE Revenue Concentration:** Account `14911` generated **$35,326.32** out of EIRE’s total **$35,680** net sales in January (**>98% revenue reliance**).
* **Spain Deficit Source:** Two accounts (`12539` and `12540`) accounted for 100% of Spain’s **-$606.85** deficit.
* **Netherlands Dependence:** Account `14646` generated **$25,978.09** of the region's total **$25.98K** sales in February.

---

## 💡 Strategic Business Recommendations

| Priority Area | Tactical Action | Financial Impact |
| :--- | :--- | :--- |
| **Inventory Balancing** | Shift SKU `M` inventory allocations directly from Spain to Finland distribution nodes. | Eliminates localized return handling overhead while fulfilling high-intent Nordic demand. |
| **Quality Control Audit** | Flag SKUs `20985` and `22699` for vendor specification and sizing audits. | Plugs Q4/Q1 return leaks caused by product defects or misleading listing details. |
| **B2B Account Management** | Establish custom re-order limits and bulk return terms for key accounts (`14911`, `14646`). | Protects operational cash flows against single-buyer return shocks. |

---

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
