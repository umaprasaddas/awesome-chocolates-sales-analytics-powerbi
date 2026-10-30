# Awesome Chocolates — Executive Sales Analytics Dashboard

An end-to-end Power BI analytics solution tracking sales performance, cost structures, and shipment dynamics for a multi-regional confectionery business.

---

## 📊 Dashboard Preview
![Dashboard Overview](assets/dashboard_view.png)

> **Note:** The complete interactive report file (`.pbix`) and a viewable [PDF Export](Awesome_Chocolates_Dashboard.pdf) are available directly in this repository.

---

## 🎯 Business Objectives & Problem Statement
The leadership team required clear visibility into:
- Revenue, gross profit margins, and shipment volume trends across 13 months.
- Sales representative performance against a 60% gross profit benchmark.
- Product-level profitability and operational risk in order fulfillment (tracking Low Box Shipments).

---

## 🧱 Data Architecture & Modeling
- **Schema:** Star schema consisting of 1 central Fact table (`Shipments`) connected in 1-to-many relationships with 4 Dimension tables:
  - `Calendar` (marked as official Date table)
  - `People` (Salespersons & regional assignments)
  - `Product` (Categories, unit costs, and pricing)
  - `Geo` (Country and regional sales territories)
- **ETL:** Cleaned data types and derived calendar attributes (Start of Month, Day of Week) via Power Query.

---

## 🧮 Key DAX Measures Implemented
- **Total Sales:** `SUM(Shipments[Sales])`
- **Total Cost:** `SUM(Shipments[Cost])` *(Row-level calculation via `RELATED(Product[Cost_per_Box]) * Shipments[Boxes]`)*
- **Total Profit & Margin:** 
  - `[Total Sales] - [Total Costs]`
  - `DIVIDE([Total Profit], [Total Sales], 0)`
- **Low Box Shipments (LBS %):**
  - `CALCULATE([Total Shipments], Shipments[Boxes] < 50)`
  - Evaluates operational sample shipments vs bulk orders.
- **Time Intelligence:** Month-over-Month change calculations comparing current month to previous month performance using dynamic variables (`VAR`) and `EDATE`.

---

## 💡 Key Features & Visual Design
- **KPI Summary Cards:** Custom multi-row KPI headers featuring conditional trend indicators and icon accents.
- **Dynamic Metric Selector:** Field parameters allowing end users to seamlessly toggle between Sales, Cost, Profit, Boxes, and Shipments on a single line chart.
- **Interactive Bookmarks:** Seamless toggle buttons to alternate between **Salesperson Performance** and **Product Performance** detail tables without cluttering canvas space.
- **Custom Tooltips:** Report-page hover tooltips displaying geographic breakdowns dynamically based on the selected metric.
- **Shipment Distribution:** Histogram binning with an interactive zoom slider to analyze order density.

---

## 📁 Repository Structure
```text
├── assets/
│   └── dashboard_view.png
├── data/
│   └── Awesome_Chocolates_Data.xlsx
├── Awesome_Chocolates_Dashboard.pbix
├── Awesome_Chocolates_Dashboard.pdf
└── README.md
