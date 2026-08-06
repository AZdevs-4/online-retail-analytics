# 📊 E-Commerce Retail Performance & Return Analytics Dashboard

An end-to-end data engineering, business intelligence, and analytical dashboard solution analyzing over 500,000 international retail transactions. This repository covers the complete project lifecycle from raw data transformation to DAX modeling and Power BI executive reporting.

---

## 🖼️ Dashboard Preview

![Executive Dashboard Overview](dashboard/screenshots/executive_overview.png)

---

## 📈 High-Level Performance Summary

| Metric | Total Baseline | FY 2010 | FY 2011 | YoY Change |
| :--- | :--- | :--- | :--- | :--- |
| **Net Revenue** | **$9.76M** | $748.96K | $9.01M | **+1,103%** |
| **Net Profit** | **$8.86M** | $674.17K | $8.19M | **+1,115%** |
| **Confirmed Orders** | **6.00M** | 359.00K | 5.00M | **+1,292%** |
| **Order Confirmation Rate** | **94.77%** | 95.33% | 94.74% | **-0.59 bps** |
| **Product Return Rate** | **5.23%** | 4.67% | 5.26% | **+0.59 bps** |

---

## 🛠️ Project Workflow & Engineering

1. **Data Ingestion & Cleaning**: Split timestamps into discrete `Date` and `Time` fields, normalized customer identity attributes, and segregated sales orders from return transactions (`InvoiceNo` starting with 'C').
2. **DAX Modeling**: Engineered measures for Gross Sales, Return Amounts, Net Revenue, Confirmation Rates, and Return Rates.
3. **Power BI Visual Analytics**: Built an interactive multi-tab executive report analyzing monthly seasonal patterns, top international expansion markets, customer account revenue, and high-risk return SKUs.

---

## 📖 Deep-Dive Documentation

* 🛠️ [Technical & Data Engineering Specifications](docs/TECHNICAL_DOCUMENTATION.md) — Pipeline details, Data Dictionary, and complete DAX catalog.
* 📊 [Executive Business Report](docs/BUSINESS_EXECUTIVE_REPORT.md) — Business growth analysis, risk drivers, and operational recommendations.

---

## 📁 Repository Structure

```text
online-retail-analytics/
│
├── 📁 data/
│   ├── raw_online_retail.xlsx          # Source transactional logs
│   └── processed_online_retail.xlsx    # Transformed staging dataset
│
├── 📁 dashboard/
│   ├── Retail_Executive_Dashboard.pbix # Interactive Power BI report file
│   └── 📁 screenshots/                 # High-resolution dashboard previews
│       ├── executive_overview.png
│       ├── regional_performance.png
│       └── return_analysis.png
│
├── 📁 docs/
│   ├── TECHNICAL_DOCUMENTATION.md      # ETL, Data Dictionary & DAX catalog
│   └── BUSINESS_EXECUTIVE_REPORT.md    # Executive KPIs & strategic insights
│
├── .gitignore                          # Excludes temporary cache files
├── LICENSE                             # MIT Open-Source License
└── README.md                           # Main portfolio page
