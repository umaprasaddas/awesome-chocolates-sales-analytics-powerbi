# 🍫 Awesome Chocolates: Sales & Profitability Dashboard

An interactive executive dashboard built in **Power BI Desktop** for Awesome Chocolates, a multi-region confectionery manufacturer. It tracks revenue, profit, shipment efficiency and salesperson performance across six markets.

> 📄 **No Power BI Desktop?** [View or download the dashboard PDF](Awesome_Chocolates_Dashboard.pdf) to see the full layout in your browser.

**Tools & skills:** Power BI · DAX · Star schema modeling · Time intelligence · Field parameters · Bookmarks · Conditional formatting

---

## Table of Contents

1. [Project Overview](#project-overview)
2. [Dashboard Preview](#dashboard-preview)
3. [Key Insights](#key-insights)
4. [Data Model](#data-model)
5. [DAX Measures](#dax-measures)
6. [Dashboard Features](#dashboard-features)
7. [How to Open This Project](#how-to-open-this-project)

---

## Project Overview

Publishing to Power BI Service requires an organizational or school Microsoft tenant, so this repository works as a self-contained portfolio piece. It documents the data model, DAX measures, KPIs and design choices used to evaluate:

- Product performance
- Salesperson benchmarks
- Shipment efficiency

---

## Dashboard Preview

### Main Executive View

![Awesome Chocolates Dashboard](0.%20Main.png)

The page includes:

- Single-row KPI cards with month-on-month (MoM) indicators
- A trend chart driven by field parameters
- A histogram of shipment box quantities
- Category and geography slicers
- A bookmark-toggled performance matrix

---

## Key Insights

### 1. Financial Health

| Metric | Value |
|---|---|
| Total Sales | **$34.0M** ($34,042,511) |
| Total Costs | **$13.5M** ($13,518,441) |
| Total Profit | **$20.5M** ($20,524,070) |
| Profit Margin | **60.3%** (target: 60.0%) |

The company is slightly above its 60% margin benchmark.

### 2. Trends and Seasonality

- A measure selector lets users view monthly patterns in sales, boxes, shipments, costs and profit.
- In the latest period (February 2024), sales were **$2.53M** ($2,528,118), down **10.8%** from January 2024 ($2.83M).

### 3. Low-Box Shipments (LBS)

Shipments under 50 boxes are treated as samples or sub-optimal orders.

| Metric | Value |
|---|---|
| LBS orders | **624** |
| Total shipments | **6,113** |
| LBS share | **10.2%** (target: 10.0%) |
| Total boxes delivered | **2.08M** (2,077,844) |

The LBS share is slightly **above** the 10% target. Since a lower share is better, this is an area to watch.

### 4. Sales Team and Product Accountability

- Each salesperson's margin is compared to the 60% target using Red-Amber-Green (RAG) icons.
- **Husein Augar** ($1,473K sales, 62.9% margin) and **Dotty Strutley** ($1,424K sales, 62.7% margin) are above target.
- Users can switch between the salesperson ranking and the product performance view without leaving the page.

---

## Data Model

The model uses a **star schema** for fast queries and clean relationships.

### Fact Table

| Table | Description |
|---|---|
| `Shipments` | One row per shipment: `Date`, `Salesperson`, `Product`, `Geography`, `Boxes`, `Sales`, `Cost` |

### Dimension Tables

| Table | Description |
|---|---|
| `Salesperson` | Sales rep details, regional alignment and profile images |
| `Product` | Product hierarchy, categories (`Bars`, `Bites`, `Other`) and unit cost per box |
| `Geography` | Markets: Australia, Canada, India, New Zealand, UK, USA |
| `Calendar` | Date table marked as the official Date Table for time-intelligence functions |

---

## DAX Measures

All measures are stored in a dedicated `_measures` table.

### 1. Revenue and Profitability

```dax
Total Sales = SUM(Shipments[Sales])

Total Costs = SUM(Shipments[Cost])

Total Profit = [Total Sales] - [Total Costs]

Profit % = DIVIDE([Total Profit], [Total Sales], 0)
```

### 2. Low-Box Shipments

```dax
Total Shipments = COUNTROWS(Shipments)

LBS Count =
CALCULATE(
    COUNTROWS(Shipments),
    Shipments[Boxes] < 50
)

LBS % = DIVIDE([LBS Count], [Total Shipments], 0)
```

### 3. Time Intelligence (Month-on-Month)

```dax
Total Sales Previous Month =
CALCULATE(
    [Total Sales],
    PREVIOUSMONTH('Calendar'[Date])
)

MoM Sales Change % =
VAR CurrentMonth = [Total Sales]
VAR PriorMonth = [Total Sales Previous Month]
RETURN
    DIVIDE(CurrentMonth - PriorMonth, PriorMonth, 0)
```

### 4. Profit Target and Status Indicator

```dax
Profit Target = 0.60

Profit Target Indicator =
IF(
    [Profit %] >= [Profit Target], 2,                // Green: target met
    IF([Profit %] >= 0.9 * [Profit Target], 1,       // Amber: within 10% of target
    0)                                               // Red: below target
)
```

---

## Dashboard Features

| Feature | What it does |
|---|---|
| **Field parameters** | Button slicers switch the trend line between Sales, Boxes, Shipments, Costs and Profit, so no duplicate charts are needed. |
| **Bookmarks and navigation** | Buttons switch between the Salesperson and Product matrices while keeping the active date and market filters. |
| **Shipment histogram** | Box quantities are grouped into bins, with a zoom slider (0-1,000 range) and an LBS KPI gauge alongside. |
| **Custom tooltips** | Hovering over the trend line shows each country's percentage contribution for that month. |
| **Conditional formatting** | Color thresholds and traffic-light icons flag underperforming products and salespeople. |

---

## How to Open This Project

1. Install **Power BI Desktop** on your computer.
2. Clone or download this repository:

   ```bash
   git clone https://github.com/<your-username>/<your-repo-name>.git
   ```

3. Open `Awesome_Chocolates_Dashboard.pbix` (or your saved `.pbix` file name).

All tables, models and visuals are bundled in the file, so you can use the slicers, switch bookmarks and inspect the DAX offline.
