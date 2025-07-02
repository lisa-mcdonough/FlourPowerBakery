Here's your document formatted in Markdown:

-----

# 🧁 Flour Power Bakery – Measures Reference

This document contains measures powering insights in the Flour Power Bakery Power BI dashboard. Measures are grouped by theme with brief descriptions for clarity and reusability.

-----

## 🛍️ Sales & Revenue

**TotalSales**
Core metric summing sales from `LineItems[Item Total]`.

```
TotalSales =
SUM(LineItems[Item Total])
```

**Average Order Value (AOV)**
Total sales divided by count of total orders.

```
AverageOrderValue =
DIVIDE([TotalSales], [CountTotalOrders])
```

**CategorySales**
Filters total sales by selected category.

```
CategorySales =
CALCULATE([TotalSales], Category[CategoryName])
```

**SalesByCategory**
Returns category-specific sales using `SELECTEDVALUE`.

```
SalesByCategory = CALCULATE([TotalSales], 'Category'[CategoryName] = SELECTEDVALUE('Category'[CategoryName]))
```

**SalesByMonth**
Filters sales to current calendar month.

```
SalesByMonth =
CALCULATE([TotalSales], MONTH('Date'[Date]) = MONTH(TODAY()))
```

**TopProductsBySales**
Ranks items by total sales in descending order.

```
TopProductsBySales =
RANKX(ALL('LineItems'[Item Name]), [TotalSales], , DESC, DENSE)
```

**SalesByQuarter**
Filters for current calendar quarter.

```
SalesByQuarter =
CALCULATE([TotalSales], QUARTER('Date'[Date]) = QUARTER(TODAY()))
```

**SalesDayOfWeek**
Sales on the same weekday as today.

```
SalesDayOfWeek =
CALCULATE([TotalSales], WEEKDAY('Date'[Date], 2) = WEEKDAY(TODAY(), 2))
```

**SalesByHour**
Filters sales occurring during operating hours.

```
SalesByHour =
CALCULATE (
    [TotalSales],
    FILTER ( LineItems, [IsWithinOperatingHours] = "Open" )
)
```

-----

## 📈 Time Intelligence

**MoMSalesGrowth**
Month-over-month growth using `PREVIOUSMONTH`.

```
MoMSalesGrowth =
DIVIDE(
    [TotalSales] -
    CALCULATE([TotalSales], PREVIOUSMONTH('Date'[Date])),
    CALCULATE([TotalSales], PREVIOUSMONTH('Date'[Date])),
    0
)
```

**YoYSalesGrowth**
Compares sales to same period last year.

```
YoYSalesGrowth =
DIVIDE(
    [TotalSales] -
    CALCULATE([TotalSales], SAMEPERIODLASTYEAR('Date'[Date])),
    CALCULATE([TotalSales], SAMEPERIODLASTYEAR('Date'[Date])),
    0
)
```

**3MMovingAVG**
3-month moving average using `DATESINPERIOD`.

```
ThreeMonthMovingAvg =
AVERAGEX(
    DATESINPERIOD('Date'[Date], MAX('Date'[Date]), -3, MONTH),
    [TotalSales]
)//3-month moving average
```

**CumulativeSalesbySeason**
Season-based accumulation of sales to current date.

```
CumulativeSalesbySeason =
CALCULATE(
    [TotalSales],
    FILTER(
        ALL('Date'),
        'Date'[Date] <= MAX('Date'[Date])
    ),
    'Date'[Season] = SELECTEDVALUE('Date'[Season])
)//running total of sales within each season
```

**MOM Sept 2023 - Jan 2024**
Manual date range, there was only 6 months of overlapping for YOY

```
MOM_Sept2023_Jan2024 =
CALCULATE(
    SUM('LineItems'[Item Total]),
    'Date'[Period] = "Sept 2023 - Jan 2024"
)
```

**Oct 2023 - Sept 2024**
Year-to-date simulation using `DATESBETWEEN`.

```
Sept 2023 - Jan 2024 =
CALCULATE(
    SUM('LineItems'[Item Total]),
    DATESBETWEEN('Date'[Date], DATE(2023, 9, 27), DATE(2024, 1, 31)),
    ALL('LineItems')
)
```

```
Sept 2024 - Jan 2025 =
CALCULATE(
    SUM('LineItems'[Item Total]),
    DATESBETWEEN('Date'[Date], DATE(2024, 9, 27), DATE(2025, 1, 31)),
   ALL('LineItems')
)
```

-----

## 👥 Employee Performance

**EmployeeSales**
Total sales divided by number of employees on shift.

```
EmployeeSales =
DIVIDE([TotalSales], [EmployeesOnShift])
```

**EmployeeHours**

```
EmployeeHours =
CALCULATE(
    SUM(Shifts[Elapsed Hours]),
    FILTER(
        RELATEDTABLE(LineItems),
        LineItems[Item Total] > 0      )
)
```

**EmployeeHoursWithDateFilter**
Dynamically filters hours using full date granularity.

```
EmployeeHoursWithDateFilter =
VAR SelectedYear = SELECTEDVALUE('Date'[Year])
VAR SelectedMonth = SELECTEDVALUE('Date'[Month])
VAR SelectedWeek = SELECTEDVALUE('Date'[MonthWeekLabel])
VAR SelectedDay = SELECTEDVALUE('Date'[Day of Week])
VAR SelectedDate =
CALCULATE(
MAX('Date'[Date]),
'Date'[Year] = SelectedYear,
'Date'[Month] = SelectedMonth,
'Date'[MonthWeekLabel] = SelectedWeek,
'Date'[Day of Week] = SelectedDay
)
RETURN
CALCULATE(
SUM(Shifts[Elapsed Hours]),
//USERELATIONSHIP(Shifts[Clock In Date], 'Date'[Date]), -no longer need relationship is active
Shifts[Clock In Date] = SelectedDate
)
```

**TotalEmployeeCount**
Unique count of employees, excluding test entries.

```
TotalEmployeeCount =
CALCULATE(
DISTINCTCOUNT(Shifts[Employee ID]),
NOT(Shifts[Employee Name] IN {"Alexjandro Jesus", "Samuel Poole", "Julian AndersonRodriguez"})
)
```

**AvgEmployeeHours**
Average hours worked per employee.

```
AvgEmployeeHours = DIVIDE([EmployeeHours], [TotalEmployeeCount])
```

**ShiftsCount**
Number of overlapping shifts per hour.

```
ShiftsCount =
CALCULATE(
COUNTROWS(Shifts),
FILTER(
Shifts,
(Shifts[Formatted Clock In Hour] <= SELECTEDVALUE('HourTable'[Hour])) &&
(Shifts[Formatted Clock Out Hour] >= SELECTEDVALUE('HourTable'[Hour]))
)
)
```

-----

## 🕒 Hourly & Weekday Trends

**AvgHourlySalesbyDayofWeek**
Hourly sales normalized by days selected.

```
AvgHourlySalesbyDayofWeek = DIVIDE([TotalSalesByHour], [NumberDaysSelectedRange])
```

**NumberDaysSelectedRange**
Count of days in the selected date range.

```
NumberDaysSelectedRange =
CALCULATE(
COUNTROWS('Date'),
FILTER('Date', 'Date'[Date] >= MIN('Date'[Date]) && 'Date'[Date] <= MAX('Date'[Date]))
)
```

**CountSelectedDaysInDateRange**
How many times a specific day occurs in the date range.

```
CountSelectedDaysInDateRange =
VAR StartDate = MIN('Date'[Date])
VAR EndDate = MAX('Date'[Date])
VAR SelectedDayOfWeek = SELECTEDVALUE('Date'[Day of Week])
VAR DayCount =
CALCULATE(
COUNTROWS('Date'),
'Date'[Day of Week] = SelectedDayOfWeek,
'Date'[Date] >= StartDate && 'Date'[Date] <= EndDate
)
RETURN IF(ISBLANK(DayCount), 0, DayCount)
```

**DynamicAvgSalesbyHour**
Average hourly sales normalized by count of selected weekdays.

```
DynamicAvgSalesbyHour =
VAR DaysInRange = VALUE([CountSelectedDaysInDateRange])
RETURN
IF(
ISBLANK(DaysInRange) || DaysInRange = 0,
0,
DIVIDE([SalesByHour], DaysInRange)
)
```

**TotalSalesByHour**
Adjusts sales by expected operating hours.

```
TotalSalesByHour =
VAR DayOfWeek = WEEKDAY(MAX('LineItems'[Order Date]), 2)
VAR Hours = SWITCH(
TRUE(),
DayOfWeek IN {1, 2}, 10,    // Mon & Tue (86)
DayOfWeek IN {3, 4, 5, 6}, 12, // Wed-Sat (88)
DayOfWeek = 7, 8            // Sun (106)
)
RETURN DIVIDE([TotalSales], Hours)
```

-----

## 📊 Visual UX Enhancers

**MonthWeekLabelMeasure**
Label format: “MMM YYYY Week X”.

```
MonthWeekLabelMeasure = FORMAT(MAX('Date'[Date]), "MMM YYYY") & "  Week " & MAX('Date'[MonthWeekLabel])
```

**MonthWeekNumber**
Calculates week number within the month.

```
MonthWeekNumber =
VAR CurrentMonthStart = STARTOFMONTH('Date'[Date])
VAR WeekNumberWithinMonth = WEEKNUM(MAX('Date'[Date]), 2) - WEEKNUM(CurrentMonthStart, 2) + 1
RETURN WeekNumberWithinMonth
```

**Total Sales Matrix**
Custom matrix logic for totals based on scope.

```
TotalSalesMatrix =
VAR IsTotalRow = ISINSCOPE('Category'[CategoryName])
VAR IsTotalColumn = ISINSCOPE('Date'[Season])
RETURN SWITCH(
TRUE(),
IsTotalRow && IsTotalColumn, [TotalSales],
NOT IsTotalRow && IsTotalColumn, [TotalSales],
IsTotalRow && NOT IsTotalColumn, [TotalSales],
BLANK()
)
```

**Items\_Category15**
Concatenated item names under category 15.

```
Items_Category15 = CONCATENATEX(FILTER(SubCategory, SubCategory[Category ID] = 15), SubCategory[Item], ", ")
```

**SelectedCat & CatName**
Interactive feedback when no category is selected.

```
SelectedCat = SELECTEDVALUE(Category[CategoryName])
CatName = IF([SelectedCat] = BLANK(), "Click on a Tile", [SelectedCat])
```

-----

## 📦 Cost & Profitability

**Total COGS**
Total cost of goods sold.

```
TotalCOGS = SUM(LineItems[Total COGS])
```

**GrossProfit**
Total sales minus COGS.

```
GrossProfit = [TotalSales] - [TotalCOGS]
```

**Margin**
Gross profit as a percentage of total sales.

```
Margin = DIVIDE([GrossProfit], [TotalSales])
```

**AvgItemMargin**
Average gross margin per item.

```
AvgItemMargin =
AVERAGEX(
VALUES(SubCategory[Item]),
DIVIDE([GrossProfit], [TotalSales])
)
```

-----

## 🧠 Context-Aware Filters

**SelectedYear**
Displays selected year(s) or “All Years” if none.

```
SelectedYear =
VAR SelectedYearCount =
VAR TotalYearCount =
CALCULATE (
    COUNTROWS ( VALUES ( 'Date'[Year] ) ),
    ALLSELECTED ( 'Date'[Year] )
)
RETURN
IF(
NOT ISFILTERED('Date'[Year]) || SelectedYearCount = TotalYearCount,
"All Years",
CONCATENATEX(VALUES('Date'[Year]), 'Date'[Year], ", ", 'Date'[Year], ASC)
)
```

**EmployeeDay/Hours**
Filters employee hours by selected weekday.

```
EmployeeDayHours =
VAR SelectedDayOfWeek = SELECTEDVALUE('Date'[Day of Week])
RETURN
CALCULATE(
SUM(Shifts[Elapsed Hours]),
USERELATIONHIP(Shifts[Clock In Date], 'Date'[Date]),
FORMAT(WEEKDAY(Shifts[Clock In Date], 2), "0") = FORMAT(SelectedDayOfWeek, "0")
)
```

**Employees Working**
Calculates active staff based on weekday + hour.

```
EmployeesWorking =
VAR SelectedDayOfWeek = FORMAT(SELECTEDVALUE('Date'[Day of Week]), "0")
VAR SelectedHourOfDay = FORMAT(SELECTEDVALUE(HourTable[Hour]), "0")
RETURN
CALCULATE(
DISTINCTCOUNT(Shifts[Employee ID]),
TREATAS(VALUES(LineItems[Order Employee ID]), Shifts[Employee ID]),
FORMAT(WEEKDAY(Shifts[Clock In Date], 2), "0") = SelectedDayOfWeek
)
```
