# 🚗 America Car Sales Analysis – Power BI Dashboard

This project showcases an end-to-end data modeling and dashboard visualization using **Power BI**, based on real-world car sales data. The objective is to analyze dealership performance, product trends, customer demographics, and regional sales patterns using a clean **star schema** approach.

---

## 📥 Dataset

- **Source**: [Kaggle – Car Sales Report Dataset](https://www.kaggle.com/datasets/missionjee/car-sales-report)
- **Format**: `.xlsx` file containing raw transactional sales data

---

## 🧱 Data Modeling: Star Schema

The original flat table was transformed into a **star schema** using Power Query. The schema includes:

### 🔷 Fact Table
- `FactSales` (later named `fig_sales`): contains the main transactional records including Date, Car ID, Customer, Dealer, and Price.

### 🔶 Dimension Tables
- **Customer Dimension**
  - Customer ID (generated using index)
  - Customer Name
  - Gender
  - Phone
  - Annual Income

- **Dealer Dimension**
  - Dealer ID
  - Dealer Name
  - Dealer Region
  - Dealer No

- **Product Dimension**
  - Product ID
  - Company / Brand
  - Model
  - Body Style
  - Color
  - Engine
  - Transmission

- **Date Dimension**
  Created using a Power Query function that dynamically generates a date range between two given dates. Here’s the code used:

```m
// Create Date Dimension
(StartDate as date, EndDate as date) =>

let
    StartDate = #date(Date.Year(StartDate), Date.Month(StartDate), Date.Day(StartDate)),
    EndDate = #date(Date.Year(EndDate), Date.Month(EndDate), Date.Day(EndDate)),
    GetDateCount = Duration.Days(EndDate - StartDate),
    GetDateList = List.Dates(StartDate, GetDateCount, #duration(1,0,0,0)),
    DateListToTable = Table.FromList(GetDateList, Splitter.SplitByNothing(), {"Date"}, null, ExtraValues.Error),
    YearNumber = Table.AddColumn(DateListToTable, "Year", each Date.Year([Date])),
    QuarterNumber = Table.AddColumn(YearNumber , "Quarter", each "Q" & Number.ToText(Date.QuarterOfYear([Date]))),
    WeekNumber = Table.AddColumn(QuarterNumber , "Week Number", each Date.WeekOfYear([Date])),
    MonthNumber = Table.AddColumn(WeekNumber, "Month Number", each Date.Month([Date])),
    MonthName = Table.AddColumn(MonthNumber , "Month", each Date.ToText([Date], "MMMM")),
    DayOfWeek = Table.AddColumn(MonthName , "Day of Week", each Date.ToText([Date], "dddd"))
in
    DayOfWeek

Each dimension table was created by removing duplicates, generating unique keys (Customer ID, Dealer ID, Product ID), and merging those keys back into the fact table. All transformations were done inside Power Query before building the dashboard in Power BI.

📈 Dashboard Highlights
The final Power BI dashboard presents insights through visual storytelling. Below are some of the key metrics and visualizations:

📊 Overview Cards
Total Sales: 672 Million USD

Total Customers: 23.91K

🧩 Visual Insights
Visualization	Description
Sales by Dealer	Highlights top-performing dealerships like Rabun Used Car, U-Haul CO, etc.
Sales by Company	Shows contribution from brands like Chevrolet, Ford, Dodge, etc.
Sales Trend	Visualizes sales performance over time from Jan 2022 to Oct 2023.
Sales by Model	Top-selling car models (e.g., LS400, Jetta, Ram Pickup).
Sales by Body Style	Comparison across styles: Sedan, Hatchback, SUV, etc.
Sales by Gender	Customer demographics: Male (78.5%), Female (21.5%).
Sales by Income	Breakdown of customers by annual income ranges.
Sales by Region	Performance by location: Janesville, Aurora, Middletown, etc.
Sales by Engine	Analysis of engine types (Overhead Camshaft, Double Overhead, etc.).
Sales by Transmission	Auto vs. Manual sales comparison.

🛠 Tools Used
Power BI: Data modeling, DAX, dashboard creation
Power Query (M language): Data transformation and dimension building
