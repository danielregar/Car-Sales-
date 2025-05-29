# 🚗 America Car Sales Analysis – Power BI Dashboard

This project showcases an end-to-end data modeling and dashboard visualization using Power BI, based on real-world car sales data. The objective is to analyze dealership performance, product trends, customer demographics, and regional sales patterns using a clean **star schema** approach.

---

## 📥 Dataset

- **Source**: [Kaggle – Car Sales Report Dataset](https://www.kaggle.com/datasets/missionjee/car-sales-report)
- **Format**: `.xlsx` file containing raw transactional sales data  

---

## 🧱 Data Modeling: Star Schema

The original flat table was transformed into a **star schema** using Power Query. The schema includes:

### 🔷 Fact Table: `fig_sales`
Contains the main transactional records including:
- Date
- Car ID (unique for each transaction)
- Customer
- Dealer
- Price
- Product-related info (e.g., transmission, engine type — because variations exist at the transaction level)

### 🔶 Dimension Tables

**Customer Dimension**
- Customer ID (generated using index)
- Name
- Gender
- Phone
- Annual Income

**Dealer Dimension**
- Dealer ID (generated using index)
- Dealer Name
- Dealer No

> **Note: Dealer Region is kept in the fact table since a single dealer name/number may appear in different regions.**

**Product Dimension**
- Product ID (generated using index)
- Company / Brand
- Model
- Body Style
- Color

> **Note: Transmission and Engine Type were excluded from this table and placed in the fact table, since they're tied to the sale, not the general product catalog.**

**Date Dimension**  
Created using a Power Query function that dynamically generates a date range and related fields.

```powerquery
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
````
Each dimension table was created by removing duplicates, generating unique keys, and merging those keys into the fact table — all done in Power Query before visualizing in Power BI.


**🧠 Why I Used a Star Schema**

At first, the dataset included a car_id field labeled as a **"unique identifier for each car"**, but it wasn’t clear whether it was tied to the customer, car model, or transaction.

Later, I found out it represents each sale, so it functions as a purchase ID.

That’s why I decided to restructure the dataset myself using **a star schema**, to clarify these relationships and make dashboarding easier.

Also… not gonna lie, shoutout to YouTube tutorials for teaching me data modeling in the first place 😄. Sometimes learning by doing just clicks better.

**📈 Dashboard Highlights**

The final Power BI dashboard presents insights through visual storytelling. Below are some of the key findings:

**📊 Summary Cards:**
- Total Sales: $672 million
- Total Customers: 23,910
- Avg Annual Income: $830,000

**📍 Regional Trends**

- Regions Covered: **Aurora, Austin, Greenville, Janesville, Middletown, Pasco, and Scottsdale**

- Top Region by Sales: **Austin with $117 million (~17.4% of total sales)**

**🚘 Company & Product Highlights**

- Top Company: **Chevrolet — $47.65 million**
- Top Model: **Lexus LS400 — $14.26 million**
- Top Body Style: **SUV — $170.6 million**
- Top Color: **Pale White — preferred by 11,256 customers**

**💬 Customer Insights**

**Gender Breakdown:**

- Male: 79
- Female: 21%

**Transmission Preference**: **Automatic: $355.1 million**

**🏢 Dealer Performance**

Top Dealer: Rabun Used Car Sales — $37+ million in total sales

**📈 Sales Trend Over Time**

From January 2022 to October 2023, the sales pattern repeats annually:

**Sales rise from Q1 to Q3**. Then **drop in Q4**

**🛠 Tools Used**

 - **Power BI** – for modeling, DAX, and dashboard creation
 - **Power Query (M)** – for data transformation and building dimensions
 - **Excel** – original data format
