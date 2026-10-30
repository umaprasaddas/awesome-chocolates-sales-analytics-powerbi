# 🍫 Awesome Chocolates — Executive Sales & Profitability Analytics Dashboard

## 📌 Executive Summary
This end-to-end business intelligence project delivers an interactive executive sales dashboard built in Power BI Desktop for **Awesome Chocolates**, a multi-region confectionery manufacturer. 

Because publishing to Power BI Service requires an organizational or school Microsoft tenant, this repository serves as a self-contained portfolio artifact. It documents the underlying data architecture, dimensional modeling, custom DAX metrics, business KPIs, and UI/UX patterns used to evaluate product performance, salesperson benchmarks, and shipment efficiency.

---

## 📊 Dashboard Visual Showcase

### Primary Executive View (Sales & Salesperson Performance)
![Awesome Chocolates Dashboard](0.%20Main.png)

*The dashboard features single-row KPI cards with dynamic month-on-month (MoM) indicators, a multi-measure trend chart driven by field parameters, a low-box shipment distribution histogram, dynamic category/geography slicers, and a bookmark-toggled performance matrix.*

---

## 🔑 Key Business Questions & Strategic Insights

1. **Overall Financial Health**:
   - Total Gross Revenue generated: **$34M**
   - Total Operating & Product Cost: **$14M**
   - Total Net Profit: **$21M**
   - Company Gross Profit Margin: **60.3%** (exceeding the baseline enterprise target of 60.0%).

2. **Trend & Seasonality Analysis**:
   - Tracking monthly cyclicality across revenue, shipment volume, box volume, costs, and margins using a dynamic measure selector.
   - Month-on-Month (MoM) revenue performance shows a contraction of **-10.8%** in the latest reported period ($2.53M).

3. **Low-Box Shipment (LBS) Efficiency**:
   - Tracking shipments with fewer than 50 boxes (used primarily for client sampling or sub-optimal order sizes).
   - Total LBS orders count: **624 shipments** out of **6,113 total shipments** (**10.2%** of all deliveries), slightly exceeding the 10.0% internal operational target threshold.

4. **Sales Team & Product Accountability**:
   - Evaluating team performance against target profit margins using automated RAG (Red-Amber-Green) indicators.
   - Seamlessly toggling between individual sales representative rankings and product line performance without visual clutter.

---

## 🛠️ Data Architecture & Star Schema Modeling

The semantic data model is designed using a classic Star Schema pattern to guarantee query performance, scalability, and clean relationship cardinalities:

- **Fact Table**:
  - `Shipments`: Contains granular transactional records including `Sales`, `Boxes`, `Date`, `Salesperson`, `Product`, and `Geography`.
- **Dimension Tables**:
  - `Salesperson`: Sales representative master data, regional alignments, and profile images.
  - `Product`: Product hierarchy, categories (`Bars`, `Bites`, `Other`), and unit cost per box.
  - `Geography`: Operational markets (`Australia`, `Canada`, `India`, `New Zealand`, `UK`, `USA`).
  - `Calendar`: Dedicated date table marked as the official Date Table for DAX time intelligence functions.

---

## 📐 Core DAX Formulations

All key business metrics are centralized inside a dedicated `_measures` table for clean governance:

### 1. Revenue & Profitability
```dax
Total Sales = 
SUM(Shipments[Sales])

Total Costs = 
SUM(Shipments[Cost])

Total Profit = 
[Total Sales] - [Total Costs]

Profit % = 
DIVIDE([Total Profit], [Total Sales], 0)
```
### 2. Low-Box Shipment (LBS) Metrics
```dax
LBS Count = 
CALCULATE(
    COUNTROWS(Shipments),
    Shipments[Boxes] < 50
)

LBS % = 
DIVIDE([LBS Count], [Total Shipments], 0)
```
### 3. Time Intelligence & Dynamic Month-on-Month
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
### 4. Profit Target Benchmark & Status Indicator
```dax
Profit Target = 0.60

Profit Target Indicator = 
IF(
    [Profit %] >= [Profit Target],
    2, -- Green / Target Met
    IF([Profit %] >= 0.9 * [Profit Target], 1, 0) -- Amber / Below Target
)
```
