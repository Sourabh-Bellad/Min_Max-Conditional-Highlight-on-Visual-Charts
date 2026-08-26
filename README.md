# Dynamic Min/Max Highlight — Power BI (DAX)

Power BI DAX solution to dynamically highlight the highest and lowest values in a bar chart (green/red) using conditional formatting. Built for a flat financial data model with no calendar table — uses CALCULATE + ALLSELECTED to isolate a specific metric (e.g. Sales) and compare it across all visible months automatically.

## 📊 What It Does

Given a bar chart of monthly values, this measure automatically:
- 🟢 Highlights the **highest** value in green
- 🔴 Highlights the **lowest** value in red
- ⚪ Leaves all other bars grey
- Updates dynamically when slicers/filters are applied

## 🗂️ Data Model

This solution is built for a **flat, single-table** structure — no separate Calendar or dimension table required.

| Column | Description |
|---|---|
| `Period` | Date (01-01-2022, 01-02-2022, ...) |
| `Financial` | Line item (Sales, COGS - Materials, OPEX - Selling Expenses, etc.) |
| `Value` | Numeric amount |
| `Month Name` | Text month (Jan, Feb, ...) |
| `Month No` | Numeric month index (1, 2, ...) |

Since multiple `Financial` line items are stacked in the same table, the measure explicitly filters to the metric you want to analyze (e.g. `"Sales"`) rather than relying on an external filter or slicer.

## 🧮 The DAX

**1. Highlight color measure (used in conditional formatting):**

Highlight Color = 
VAR CurrentValue = SUM('Financials'[Value])
VAR MonthContext = 
    ALLSELECTED('Financials'[Month Name], 'Financials'[Month No])
VAR MaxValue = 
    MAXX(MonthContext, CALCULATE(SUM('Financials'[Value])))
VAR MinValue = 
    MINX(MonthContext, CALCULATE(SUM('Financials'[Value])))
RETURN
    SWITCH(
        TRUE(),
        CurrentValue = MaxValue, "#00B050",
        CurrentValue = MinValue, "#FF0000",
        "#D3D3D3"
    )   
    

## ⚙️ How to Apply

1. Add both measures to your model.
2. On your bar chart, set the **Y-axis / Values** field to `Sales Value`.
3. Go to **Format visual → Columns → Colors**.
4. Click the **fx** icon next to the series.
5. Set **Format style** → `Field value`.
6. Set the field to base it on → `Highlight Color`.


## 📌 Notes

- Works with a single flat table — no Calendar or dimension table needed.
- Tested on a single-year (2022) dataset with unique month names; for multi-year data, swap `Month Name` for a proper date/index column to avoid cross-year collisions.
