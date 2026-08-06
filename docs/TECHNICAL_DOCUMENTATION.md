# 🛠️ Technical & Data Engineering Specifications

This document outlines the system architecture, ETL pipeline, data dictionary, and DAX measure dictionary for the Online Retail performance dashboard.

---

## 1. System & Data Pipeline Architecture

The pipeline standardizes unstructured transactional logs from `raw_online_retail.xlsx` into a structured analytics model in `processed_online_retail.xlsx`, which is then ingested into Power BI.

```text
[Raw Excel Logs] ➔ [Power Query ETL & Cleansing] ➔ [DAX Data Modeling] ➔ [Power BI Dashboard Visuals]
```

## 2. ETL & Data Transformation Logic

### A. Timestamp Separation
* **Action**: Split the composite `InvoiceDate` timestamp into two distinct scalar attributes:
  * `InvoiceDate` (Date type, format: `YYYY-MM-DD`)
  * `InvoiceTime` (Time type, format: `HH:MM:SS`)
* **Rationale**: Eliminates time-zone dependency issues, enables daily calendar intelligence, and allows time-of-day basket analysis.

### B. Transaction Segregation (Orders vs. Returns)
* **Action**: Segregated standard purchases (Quantity > 0) from cancellations and returns (Quantity < 0).
* **Prefix Handling**: Invoices starting with the letter **'C'** indicate cancellations (`InvoiceNo` prefix `C`).
* **Rationale**: Prevents skewed average basket size metrics and enables accurate isolated calculation of return rates.

### C. Customer Identification
* **Action**: Converted `CustomerID` into integer data types. Missing values were preserved and flagged as non-registered guest checkouts rather than dropped.

### D. Calculated Line Item Column
* **Formula**:
  TotalSales = Quantity * UnitPrice

---

## 3. Data Dictionary

| Column Name | Raw Data Type | Target Data Type | Field Type | Description |
| :--- | :--- | :--- | :--- | :--- |
| `InvoiceNo` | Text / String | String | Identifier | 6-digit transaction ID. Prefix 'C' indicates return. |
| `StockCode` | Text / String | String | Identifier | 5-digit unique product SKU code. |
| `Description` | Text / String | String | Attribute | Product item name and detail description. |
| `Quantity` | Integer | Integer | Metric | Units ordered per line item. Negative for returns. |
| `InvoiceDate` | DateTime | Date | Dimension | Calendar date when order was executed. |
| `InvoiceTime` | DateTime | Time | Dimension | Time of day when order was executed. |
| `UnitPrice` | Float | Decimal | Metric | Product unit price in GBP / USD. |
| `CustomerID` | Float | Integer (Nullable) | Identifier | 5-digit unique registered customer ID. |
| `Country` | Text / String | String | Dimension | Geographic location of buyer. |
| `TotalSales` | Computed | Decimal | Metric | Line-item extended total (Quantity * UnitPrice). |


## DAX Measure Dictionary:
// 1. Gross Revenue
Gross Revenue = 
SUMX(
    FILTER('Online Retail', 'Online Retail'[Quantity] > 0), 
    'Online Retail'[Quantity] * 'Online Retail'[UnitPrice]
)

// 2. Return Revenue Amount
Return Revenue = 
ABS(
    SUMX(
        FILTER('Online Retail', 'Online Retail'[Quantity] < 0), 
        'Online Retail'[Quantity] * 'Online Retail'[UnitPrice]
    )
)

// 3. Net Sales
Net Sales = [Gross Revenue] - [Return Revenue]

// 4. Net Profit (Assuming 90.8% Gross Margin baseline)
Net Profit = [Net Sales] * 0.908

// 5. Confirmed Order Count
Confirmed Orders = 
CALCULATE(
    COUNT('Online Retail'[InvoiceNo]), 
    'Online Retail'[Quantity] > 0
)

// 6. Return Order Count
Return Orders = 
CALCULATE(
    COUNT('Online Retail'[InvoiceNo]), 
    'Online Retail'[Quantity] < 0
)

// 7. Net Orders
Net Orders = [Confirmed Orders] - [Return Orders]

// 8. Confirmation Rate (%)
Confirmation Rate = 
DIVIDE([Confirmed Orders], [Confirmed Orders] + [Return Orders], 0) * 100

// 9. Return Rate (%)
Return Rate = 
DIVIDE([Return Orders], [Confirmed Orders] + [Return Orders], 0) * 100
