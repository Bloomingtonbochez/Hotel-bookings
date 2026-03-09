# Hotel Booking Data Analysis Using SQL

## Project Overview

The hospitality industry generates vast amounts of booking data every day. Analyzing this data can help hotels understand customer behavior, optimize pricing strategies, reduce cancellations, and improve overall operational efficiency.

This project focuses on performing Exploratory Data Analysis (EDA) and advanced business analysis using SQL on a hotel booking dataset. The goal is to uncover insights about booking patterns, customer segments, seasonal demand, cancellation behavior, and revenue generation.

Using SQL queries, this project answers important business questions that hotel managers and analysts may ask when making data-driven decisions.

The analysis demonstrates how SQL can be used not only for querying databases but also for extracting meaningful insights that support real-world business strategies.

# Objectives of the Project

The main objectives of this project are:

   -Perform exploratory data analysis on hotel booking data

  -Identify booking trends across different hotel types

  -Analyze cancellation patterns and their influencing factors

  -Evaluate customer behavior and booking characteristics

  -Identify high-value customers

  -Analyze seasonal demand trends

  -Determine optimal pricing periods for hotels

  -Practice advanced SQL techniques including aggregation, window functions, ranking, and subqueries

By answering these questions, this project demonstrates the analytical capabilities required for data analyst and business intelligence roles.

## Dataset Overview

**Name**: Hotel Booking Demand  
**Source**: Kaggle – [Original Dataset Link](https://www.kaggle.com/datasets/jessemostipak/hotel-booking-demand)  
**File**: `hotel_bookings.csv`  
**Rows**: 119,390  
**Columns**: 32  
**Time Period**: Bookings with arrival dates from July 2015 to August 2017  
**Hotels Covered**: Two real hotels in Portugal:
- **City Hotel** (urban/business-oriented) – ~79,330 bookings
- **Resort Hotel** (leisure/vacation-oriented) – ~40,060 bookings
  ## Technologies & Tools
**Database**: SQL Server (SSMS)  
- **Language**: SQL server 
- **Data Cleaning & Import**: BULK INSERT + manual fixes ('NA' → 0, tinyint → int)  
- **Visualization** (optional next steps): Power BI, Tableau, Python (Matplotlib/Seaborn), Excel  
- **Version Control**: Git + GitHub

## Exploratory Data Analysis (EDA)

Exploratory Data Analysis helps analysts understand patterns within the dataset.

The goal of EDA in this project was to examine:

-Booking distribution

-Customer behavior

-Cancellation patterns

-Revenue trends

-Seasonal demand


DATASET OVERVIEW
--How many total hotel bookings are recorded in the dataset

```
SELECT
COUNT(*) total_bookings
FROM dbo.hotel_bookings
```

--how many bookings belong to each hotel type?

```
SELECT 
hotel,
COUNT(*) total_booking
FROM dbo.hotel_bookings
GROUP BY hotel

```

--what percentage of booking canceled

```
SELECT
SUM(is_canceled)*100/ COUNT(*) AS canceled_percentage
FROM dbo.hotel_bookings

```

-- what is the average lead time for booking

```
SELECT
AVG(lead_time) avg_lead_time
FROM dbo.hotel_bookings

```

--What is the distribution of bookings across years

```
SELECT
arrival_date_year,
COUNT(*) bookings
FROM dbo.hotel_bookings
GROUP BY arrival_date_year

```

TIME-BASED BOOKING TRENDS
    
--which months have the highest number of bookings
```
SELECT
arrival_date_month,
COUNT(*) bookings
FROM dbo.hotel_bookings
GROUP BY arrival_date_month

```

--which is the total number of bookings per week number 

```
SELECT
DISTINCT arrival_date_week_number,
COUNT(*) bookings
FROM dbo.hotel_bookings
GROUP BY  arrival_date_week_number

```

--How do bookings vary by day of the month
```
SELECT
arrival_date_day_of_month,
COUNT(*) bookings
FROM dbo.hotel_bookings
GROUP BY arrival_date_day_of_month
ORDER BY arrival_date_day_of_month

```

--which month has the highest cancelation rate

```
SELECT TOP 1
arrival_date_month,
COUNT(is_canceled) canceled_bookings
FROM hotel_bookings
GROUP BY arrival_date_month
ORDER BY COUNT(is_canceled) DESC

```

--What is the trend of bookings across years?

```
SELECT
arrival_date_year,
COUNT(*) bookings
FROM hotel_bookings
GROUP BY arrival_date_year
ORDER BY COUNT(*) DESC

```

STAY PATTERNS

--What is the average number of weekend nights per bookings?
```
SELECT
AVG(stays_in_weekend_nights) avg_stay_weekend
FROM hotel_bookings
```
--what is the average number of week nights per bookings?
```
SELECT
AVG(stays_in_week_nights) avg_stay_week
FROM hotel_bookings
```
--which hotel type has longer average stay?
```
SELECT
hotel,
AVG(stays_in_week_nights + stays_in_weekend_nights) avg_total_stay
FROM hotel_bookings
GROUP BY hotel
ORDER BY avg_total_stay DESC
```

--what is the total average total stay duration weekend plus weekday?
```
SELECT
AVG(stays_in_week_nights + stays_in_weekend_nights) avg_total_stay
FROM hotel_bookings
```
--which customer types stays the longest?
```
SELECT
customer_type,
AVG(stays_in_week_nights + stays_in_weekend_nights) avg_total_stay
FROM hotel_bookings
GROUP BY customer_type
ORDER BY avg_total_stay DESC
```
REVENUE AND PRICING

--what is the average ADR(average daily rate)per hotel?
```
SELECT
hotel,
adr
FROM hotel_bookings
```
--which months generate the highest ADR?
```
SELECT TOP 6
arrival_date_month,
adr 
FROM hotel_bookings
ORDER BY adr DESC
```
--What is the average ADR by market segment?
```
SELECT
market_segment,
round(AVG(adr),2) avg_adr
FROM hotel_bookings
GROUP BY market_segment
```
--which distribution channel generates the highest revenue?
```
SELECT
distribution_channel,
ROUND(SUM(adr *(stays_in_week_nights + stays_in_weekend_nights)),2) total_revenue
FROM hotel_bookings
WHERE is_canceled = 0
GROUP BY distribution_channel
ORDER BY total_revenue DESC
```
--what is the average ADR by customer type?
```
SELECT
customer_type,
ROUND(AVG(adr),2) avg_adr
FROM hotel_bookings
GROUP BY customer_type
