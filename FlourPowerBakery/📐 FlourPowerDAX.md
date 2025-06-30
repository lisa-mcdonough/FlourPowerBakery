📐 DAX Measures – Flour Power Bakery
This document contains curated DAX measures powering insights in the Flour Power Bakery Power BI dashboard. Measures are grouped by theme with brief descriptions for clarity and reusability.
🛍️ Sales & Revenue
Measure	Description
TotalSales	Core metric summing sales from LineItems[Item Total].
Average Order Value (AOV)	Total sales ÷ count of total orders.
CategorySales	Filters total sales by selected category.
SalesByCategory	Returns category-specific sales using SELECTEDVALUE.
SalesByMonth	Filters sales to current calendar month.
TopProductsBySales	Ranks items by total sales in descending order.
SalesByQuarter	Filters for current calendar quarter.
SalesDayOfWeek	Sales on the same weekday as today.
SalesByHour	Filters sales occurring during operating hours.
📈 Time Intelligence
Measure	Description
MoMSalesGrowth	Month-over-month growth using PREVIOUSMONTH.
YoYSalesGrowth	Compares sales to same period last year.
3MMovingAVG	3-month moving average using DATESINPERIOD.
CumulativeSalesbySeason	Season-based accumulation of sales to current date.
MOM Sept 2023 - Jan 2024	Manual date-range total for a specific period.
Oct 2023 - Sept 2024	Year-to-date simulation using DATESBETWEEN.
👥 Employee Performance
Measure	Description
SalesByEmployeeByDay2	Uses TREATAS to link employees and sales by day.
EmployeeSales	Total sales ÷ number of employees on shift.
EmployeeHours	Total hours worked (relationship activated with USERELATIONSHIP).
EmployeeHoursWithDateFilter	Dynamically filters hours using full date granularity.
TotalEmployeeCount	Unique count of employees, excluding test entries.
AvgEmployeeHours	Average hours worked per employee.
ShiftsCount	Number of overlapping shifts per hour.
🕒 Hourly & Weekday Trends
Measure	Description
AvgHourlySalesbyDayofWeek	Hourly sales normalized by days selected.
TotalSalesByHour	Adjusts sales by expected operating hours.
NumberDaysSelectedRange	Count of days in the selected date range.
DynamicAvgSalesbyHour	Average hourly sales normalized by count of selected weekdays.
CountSelectedDaysInDateRange	How many times a specific day occurs in the date range.
📊 Visual UX Enhancers
Measure	Description
MonthWeekLabelMeasure	Label format: “MMM YYYY - Week X”.
MonthWeekNumber	Calculates week number within the month.
Total Sales Matrix	Custom matrix logic for totals based on scope.
Items_Category15	Concatenated item names under category 15.
SelectedCat & CatName	Interactive feedback when no category is selected.
📦 Cost & Profitability
Measure	Description
Total COGS	Total cost of goods sold.
GrossProfit	Total sales - COGS.
Margin	Gross profit as a percentage of total sales.
AvgItemMargin	Average gross margin per item.
🧠 Context-Aware Filters
Measure	Description
SelectedYear	Displays selected year(s) or “All Years” if none.
EmployeeDay/Hours	Filters employee hours by selected weekday.
Employees Working	Calculates active staff based on weekday + hour.
EmployeesWorking	Alternate method using HourTable and date alignment.

