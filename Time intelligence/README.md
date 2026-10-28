# Power BI DAX Time Intelligence — Complete Guide

## 📌 Project Overview

This project focuses on learning and implementing DAX Time Intelligence functions in Microsoft Power BI to analyze sales performance over different time periods.

Time Intelligence helps compare current performance with previous periods, calculate cumulative sales, measure growth, and build dynamic business dashboards.

### 🎯 Objectives

- Understand DAX Time Intelligence functions.
- Calculate MTD, QTD, and YTD sales.
- Compare current sales with previous months, quarters, and years.
- Calculate Month-over-Month (MoM) and Year-over-Year (YoY) growth.
- Analyze rolling-period sales.
- Build KPI cards and interactive dashboards.
- Practice writing DAX measures instead of relying on static calculations.

---

## 🛠️ Tools and Technologies

- Microsoft Power BI Desktop
- DAX (Data Analysis Expressions)
- Date/Calendar Table
- Data Modeling and Relationships
- Power BI Table, Card, Line Chart, and Slicer Visuals

---

## 📂 Data Model Requirements

The project uses a sales table named `Sales` and a calendar table named `Date`.

### Sales Table

Example columns:

- `Sales[OrderDate]`
- `Sales[SalesAmount]`
- `Sales[OrderID]`
- `Sales[Product]`
- `Sales[Region]`

### Date Table

Example columns:

- `Date[Date]`
- `Date[Year]`
- `Date[Month]`
- `Date[Month Number]`
- `Date[Quarter]`

### Relationship Setup

1. Create a relationship between `Date[Date]` and `Sales[OrderDate]`.
2. Set the relationship as one-to-many, with `Date` on the one side.
3. Mark the `Date` table as a date table using `Date[Date]`.
4. Ensure the date table contains continuous dates covering the required analysis period.
5. Use fields from the `Date` table in time-based visuals and slicers.

Example calendar table:

```dax
Date =
ADDCOLUMNS(
    CALENDAR(
        MIN(Sales[OrderDate]),
        MAX(Sales[OrderDate])
    ),
    "Year", YEAR([Date]),
    "Month", FORMAT([Date], "MMM"),
    "Month Number", MONTH([Date]),
    "Quarter", "Q" & FORMAT([Date], "Q")
)
```

**Note:** For production reports, a calendar table should generally cover complete years, including any prior periods needed for comparisons.

---

## 📐 Base Measure

All sales calculations begin with a base measure.

```dax
Total Sales =
SUM(Sales[SalesAmount])
```

This measure calculates sales within the current filter context, including selected dates, years, products, and regions.

---

# 📚 The 21 DAX Time Intelligence Functions

## 1. TOTALYTD()

Calculates year-to-date sales from the beginning of the year through the current date context.

```dax
Sales YTD =
TOTALYTD(
    [Total Sales],
    'Date'[Date]
)
```

**Use case:** Track cumulative sales performance during the year.

## 2. TOTALQTD()

Calculates quarter-to-date sales.

```dax
Sales QTD =
TOTALQTD(
    [Total Sales],
    'Date'[Date]
)
```

**Use case:** Monitor sales during the current quarter.

## 3. TOTALMTD()

Calculates month-to-date sales.

```dax
Sales MTD =
TOTALMTD(
    [Total Sales],
    'Date'[Date]
)
```

**Use case:** Monitor sales from the beginning of the month through the current date context.

## 4. SAMEPERIODLASTYEAR()

Returns the corresponding date period from the previous year.

```dax
Previous Year Sales =
CALCULATE(
    [Total Sales],
    SAMEPERIODLASTYEAR('Date'[Date])
)
```

**Use case:** Compare sales for the current period against the equivalent period last year.

## 5. DATEADD()

Shifts the current date context forward or backward by a specified interval.

```dax
Previous Month Sales =
CALCULATE(
    [Total Sales],
    DATEADD('Date'[Date], -1, MONTH)
)
```

Other intervals include `DAY`, `MONTH`, `QUARTER`, and `YEAR`.

**Use case:** Compare the current period with a shifted period.

## 6. DATESYTD()

Returns the dates from the beginning of the year through the current date context.

```dax
Sales YTD Using Dates =
CALCULATE(
    [Total Sales],
    DATESYTD('Date'[Date])
)
```

**Use case:** Build a custom YTD measure with `CALCULATE()`.

## 7. DATESQTD()

Returns the dates from the beginning of the quarter through the current date context.

```dax
Sales QTD Using Dates =
CALCULATE(
    [Total Sales],
    DATESQTD('Date'[Date])
)
```

**Use case:** Calculate cumulative quarterly performance.

## 8. DATESMTD()

Returns the dates from the beginning of the month through the current date context.

```dax
Sales MTD Using Dates =
CALCULATE(
    [Total Sales],
    DATESMTD('Date'[Date])
)
```

**Use case:** Calculate cumulative monthly performance.

## 9. PREVIOUSMONTH()

Returns the dates belonging to the previous month relative to the current date context.

```dax
Previous Month Sales =
CALCULATE(
    [Total Sales],
    PREVIOUSMONTH('Date'[Date])
)
```

**Use case:** Compare sales with the previous calendar month.

## 10. PREVIOUSQUARTER()

Returns the dates belonging to the previous quarter.

```dax
Previous Quarter Sales =
CALCULATE(
    [Total Sales],
    PREVIOUSQUARTER('Date'[Date])
)
```

**Use case:** Compare quarterly business performance.

## 11. PREVIOUSYEAR()

Returns the dates belonging to the previous year.

```dax
Previous Year Sales =
CALCULATE(
    [Total Sales],
    PREVIOUSYEAR('Date'[Date])
)
```

**Use case:** Calculate previous-year sales for annual comparisons.

## 12. NEXTMONTH()

Returns the dates belonging to the next month relative to the current date context.

```dax
Next Month Sales =
CALCULATE(
    [Total Sales],
    NEXTMONTH('Date'[Date])
)
```

**Use case:** Analyze the next calendar month's dates where the data contains them. This is not a sales forecast.

## 13. NEXTQUARTER()

Returns the dates belonging to the next quarter.

```dax
Next Quarter Sales =
CALCULATE(
    [Total Sales],
    NEXTQUARTER('Date'[Date])
)
```

**Use case:** Analyze sales in the next quarter when those dates exist in the dataset.

## 14. NEXTYEAR()

Returns the dates belonging to the next year.

```dax
Next Year Sales =
CALCULATE(
    [Total Sales],
    NEXTYEAR('Date'[Date])
)
```

**Use case:** Retrieve next-year sales where the date table and sales data support it. It does not predict future sales.

## 15. PARALLELPERIOD()

Returns a parallel period shifted by a specified number of months, quarters, or years.

```dax
Previous Whole Month Sales =
CALCULATE(
    [Total Sales],
    PARALLELPERIOD('Date'[Date], -1, MONTH)
)
```

**Use case:** Compare complete parallel calendar periods. Unlike `DATEADD()`, it returns the complete shifted period at the chosen interval granularity.

## 16. DATESBETWEEN()

Returns dates between a specified start date and end date, inclusive.

```dax
Sales Date Range =
CALCULATE(
    [Total Sales],
    DATESBETWEEN(
        'Date'[Date],
        DATE(2025, 1, 1),
        DATE(2025, 12, 31)
    )
)
```

**Use case:** Calculate sales for a custom date range.

## 17. DATESINPERIOD()

Returns dates within a specified interval relative to an anchor date.

```dax
Sales Last 12 Months =
CALCULATE(
    [Total Sales],
    DATESINPERIOD(
        'Date'[Date],
        MAX('Date'[Date]),
        -12,
        MONTH
    )
)
```

**Use case:** Analyze rolling 12-month sales. The result depends on the anchor date and available calendar and sales data.

## 18. STARTOFMONTH()

Returns the first date of the month represented by the current date context.

```dax
Start of Month =
STARTOFMONTH('Date'[Date])
```

**Use case:** Identify the start of a month. This returns a date table, not a sales amount.

## 19. ENDOFMONTH()

Returns the last date of the month represented by the current date context.

```dax
End of Month =
ENDOFMONTH('Date'[Date])
```

**Use case:** Identify the end of a month.

## 20. STARTOFYEAR()

Returns the first date of the year represented by the current date context.

```dax
Start of Year =
STARTOFYEAR('Date'[Date])
```

**Use case:** Identify the beginning of the year.

## 21. ENDOFYEAR()

Returns the last date of the year represented by the current date context.

```dax
End of Year =
ENDOFYEAR('Date'[Date])
```

**Use case:** Identify the end of the year.

**Important:** The `STARTOF...` and `ENDOF...` functions return dates, not sales values. For display in a card, create an appropriate scalar date measure or use a date-based visual.

---

# 📈 Advanced Business Calculations

## Year-over-Year (YoY) Growth %

YoY Growth measures the percentage change in sales compared with the corresponding period in the previous year.

### Step 1: Previous Year Sales

```dax
Previous Year Sales =
CALCULATE(
    [Total Sales],
    SAMEPERIODLASTYEAR('Date'[Date])
)
```

### Step 2: YoY Growth %

```dax
YoY Growth % =
DIVIDE(
    [Total Sales] - [Previous Year Sales],
    [Previous Year Sales]
)
```

Format this measure as **Percentage** with one decimal place.

### Example

| Year | Total Sales | Previous Year Sales | YoY Growth |
|---|---:|---:|---:|
| 2023 | 100,000 | — | — |
| 2024 | 120,000 | 100,000 | 20.0% |
| 2025 | 150,000 | 120,000 | 25.0% |

The first year may have no comparison if prior-year data is unavailable.

## Month-over-Month (MoM) Growth %

MoM Growth measures the percentage change compared with the previous month.

### Step 1: Previous Month Sales

```dax
Previous Month Sales =
CALCULATE(
    [Total Sales],
    PREVIOUSMONTH('Date'[Date])
)
```

### Step 2: MoM Growth %

```dax
MoM Growth % =
DIVIDE(
    [Total Sales] - [Previous Month Sales],
    [Previous Month Sales]
)
```

Format as Percentage with one decimal place.

## Sales Growth Amount

```dax
Sales Growth Amount =
[Total Sales] - [Previous Year Sales]
```

This measure returns the absolute difference in sales rather than the percentage change.

---

# 📊 Dashboard Visualizations

Recommended visuals for a Time Intelligence dashboard:

### 1. KPI Cards

- Total Sales
- YoY Growth %
- MoM Growth %
- Previous Year Sales

### 2. Line Chart

Show monthly sales trends over time.

- X-axis: `Date[Date]` or a month-year field
- Y-axis: `[Total Sales]`

Use a chronological month-year field to avoid sorting month names alphabetically.

### 3. Matrix or Table

Recommended fields:

- `Date[Year]`
- `[Total Sales]`
- `[Previous Year Sales]`
- `[YoY Growth %]`

A single dynamic measure can calculate the previous-year comparison for each year in the table.

### 4. Slicers

Useful slicers include:

- Year
- Month
- Region
- Product

### 5. KPI Card with YoY Growth

A Card (new) visual may support Reference labels, depending on the Power BI version. Use `[Total Sales]` as the main value and `[YoY Growth %]` as a reference label if supported.

Otherwise, use two separate cards or a carefully formatted combined display.

---

# ⚠️ Common Errors and Troubleshooting

### 1. Previous Year Sales returns blank

Possible causes:

- Previous-year dates are not present in the calendar.
- The sales data has no records for the corresponding period.
- The date relationship is missing or incorrect.
- The visual's filters restrict the required dates.

### 2. YoY Growth shows a very large percentage

Check that:

- The formula uses `DIVIDE()`.
- `[Total Sales]` and `[Previous Year Sales]` are measures, not unintended raw columns.
- The growth measure is formatted as Percentage only once.
- The previous-year measure returns the expected sales amount.

### 3. MTD, QTD, or YTD returns unexpected results

Check the date table, active relationship, date coverage, visual filters, and whether the visual uses fields from the calendar table.

### 4. Growth is blank for the first year

This is expected when there is no previous-year sales value. `DIVIDE()` returns blank by default when the denominator is zero or blank.

### 5. Date values are not sorted correctly

Sort `Date[Month]` by `Date[Month Number]`. For visuals spanning multiple years, use a unique chronological month-year field.

---

# 🧠 Key Learning Outcomes

After completing this project, I practiced:

- Creating and using a proper Date table.
- Writing DAX measures using `CALCULATE()`.
- Understanding filter context in time-based calculations.
- Calculating MTD, QTD, and YTD sales.
- Comparing current and previous periods.
- Measuring YoY and MoM growth.
- Analyzing rolling 12-month sales.
- Building KPI cards and interactive Power BI visuals.
- Troubleshooting date relationships and formatting issues.

---

# 🚀 Conclusion

DAX Time Intelligence is an essential skill for business intelligence and data analytics. It allows analysts to move beyond basic totals and understand how business performance changes over time.

This project builds a foundation for developing more advanced Power BI reports, including financial analysis, sales performance tracking, trend analysis, and executive dashboards.

**Skills:** Power BI | DAX | Time Intelligence | Data Modeling | KPI Development | Sales Analysis | Business Intelligence
