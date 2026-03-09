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
```
--INTERMEDIATE BUSINESS INSIGHT QUESTIONS
--which market segment produces the highest number of cancellaton?
SELECT
market_segment,
COUNT(*) total_cancellations
FROM hotel_bookings
WHERE is_canceled = 1
GROUP BY market_segment
ORDER BY total_cancellations DESC
--which country has the highest cancellation rate?
SELECT
country,
COUNT(*) total_cancellations
FROM hotel_bookings
WHERE is_canceled = 1
GROUP BY country
ORDER BY total_cancellations DESC
--which deposit type is associated with the lowest cancellation rate?
SELECT
deposit_type,
COUNT(*) total_bookings,
 SUM(is_canceled) AS total_cancellations,
    ROUND(100.0 * SUM(is_canceled) / COUNT(*),2) AS cancellation_rate
FROM hotel_bookings
GROUP BY deposit_type
ORDER BY cancellation_rate
--how do special request relate to cancellation?
SELECT
total_of_special_requests,
COUNT(*) total_bookings,
SUM(is_canceled) total_cancelation,
ROUND(100.0 * SUM(is_canceled) / COUNT(*),2)  cancellation_rate
FROM hotel_bookings
GROUP BY total_of_special_requests

-- which distribution channel brings the most repeated guests?
SELECT
distribution_channel,
COUNT(is_repeated_guest) repeated_guest
FROM hotel_bookings
WHERE is_repeated_guest =1
GROUP BY distribution_channel

      --ADVANCED SQL ANALYSIS
	 --1.Window function analysis
--Rank the top 10 countries by total bookings
SELECT TOP 10
country,
COUNT(*) total_bookings,
RANK() OVER(ORDER BY COUNT(*) DESC) booking_rank
FROM hotel_bookings
GROUP BY country
ORDER BY booking_rank
--Rank months by average ADR within each year
SELECT
arrival_date_month,
ROUND(AVG(adr),2) avg_adr,
RANK()OVER (ORDER BY AVG(adr))Rank_avg_adr
FROM hotel_bookings
GROUP BY arrival_date_month
--identify the top 1 highest ADR bookings per hotel type
SELECT TOP 1
hotel,
adr,
ROW_NUMBER()OVER(PARTITION BY hotel ORDER BY adr DESC)hotel_rank
FROM hotel_bookings

--2.customer behavior analysis
--identify guests who did multiple bookings but never canceled.
WITH guest_bookings AS(
SELECT
country,
COUNT(*)total_bookings,
SUM(is_canceled) cancellations
FROM hotel_bookings
GROUP BY country)
SELECT*
FROM guest_bookings
WHERE total_bookings > 1 AND cancellations =0 
ORDER BY total_bookings DESC
--Find repeated guests whose average stay duration increased over time.
WITH stay_data AS(
SELECT
country,
arrival_date_year,
AVG(stays_in_weekend_nights + stays_in_week_nights) AS avg_stay_duration
FROM hotel_bookings
WHERE is_repeated_guest = 1
GROUP BY country,arrival_date_year
),
stay_trend AS(
SELECT
country,
arrival_date_year,
avg_stay_duration,
LAG(avg_stay_duration) OVER (PARTITION BY country ORDER BY arrival_date_year) AS previous_avg_stay
FROM stay_data
)
SELECT*
FROM stay_trend
WHERE avg_stay_duration > previous_avg_stay

--REVENUE TREND ANALYSIS
--calculate the month-over-month ADR growth for each hotel
WITH monthly_adr AS(
SELECT
hotel,
arrival_date_month,
AVG(adr) avg_monthly_adr
FROM hotel_bookings
WHERE is_canceled = 0
GROUP BY hotel,arrival_date_month
),
adr_growth AS (
SELECT
hotel,
arrival_date_month,
avg_monthly_adr,
LAG(avg_monthly_adr) OVER(PARTITION BY hotel ORDER BY arrival_date_month) AS previous_month
FROM monthly_adr)
SELECT
hotel,arrival_date_month,avg_monthly_adr,previous_month,
ROUND(((avg_monthly_adr-previous_month)/previous_month)*100,2) MOM_growth_percent
FROM adr_growth
WHERE previous_month IS NOT NULL
ORDER BY hotel,arrival_date_month
--identify months where ADR uncreased compared to previous month.
WITH monthly_adr AS(
SELECT
hotel,
arrival_date_month,
AVG(adr) avg_monthly_adr
FROM hotel_bookings
WHERE is_canceled = 0
GROUP BY hotel,arrival_date_month
),
adr_growth AS (
SELECT
hotel,
arrival_date_month,
avg_monthly_adr,
LAG(avg_monthly_adr) OVER(PARTITION BY hotel ORDER BY arrival_date_month) AS previous_month
FROM monthly_adr)
SELECT
hotel,arrival_date_month,avg_monthly_adr,previous_month,
ROUND(((avg_monthly_adr-previous_month)/previous_month)*100,2) MOM_growth_percent
FROM adr_growth
WHERE avg_monthly_adr > previous_month
 ORDER BY hotel,arrival_date_month

 		--cancellation RISK Modeling

--Find bookings charcteristic that have the highest cancellation probability.
SELECT 
    market_segment,
    deposit_type,
    customer_type,
    COUNT(*) AS total_bookings,
    SUM(is_canceled) AS total_cancellations,
    ROUND(100.0 * SUM(is_canceled) / COUNT(*),2) AS cancellation_probability
FROM hotel_bookings
GROUP BY market_segment, deposit_type, customer_type
HAVING COUNT(*) > 50
ORDER BY cancellation_probability DESC;
--identify the top 5 combinaton of market segment and deposit type with the highest cancellations
SELECT TOP 5
    market_segment,
    deposit_type,
    COUNT(*) AS total_cancellations
FROM hotel_bookings
WHERE is_canceled = 1
GROUP BY market_segment, deposit_type
ORDER BY total_cancellations DESC

		--5.operaton efficiency analysis
--calculate the average waiting list dsys by hotel and customer type.
SELECT
hotel,
customer_type,
AVG(days_in_waiting_list)avg_waiting_days
FROM hotel_bookings
WHERE days_in_waiting_list > 0
GROUP BY hotel,customer_type
-- identify bookings where assigned room type differ from reserved room type
SELECT 
    hotel,
    arrival_date_year,
    arrival_date_month,
    reserved_room_type,
    assigned_room_type,
    ROUND(adr,2) adr
FROM hotel_bookings
WHERE reserved_room_type <> assigned_room_type

 --ADVANCED BUSINESS QUESTION(PORTFOLIO-LEVEL)

--Identify the booking factors that most influence cancellations(lead time,deposite type, market segment)
SELECT
deposit_type,
lead_time,
market_segment,
SUM(is_canceled) total_cancelation
FROM hotel_bookings
WHERE is_canceled = 1
GROUP BY deposit_type,lead_time,market_segment
ORDER BY total_cancelation DESC;
--Determione which customer segments generate the highest revenue with the lowest cancellation risks
SELECT
customer_type,
SUM(adr*(stays_in_weekend_nights + stays_in_week_nights))total_revenue,
SUM(is_canceled) total_cancelation
FROM hotel_bookings
WHERE is_canceled = 0
GROUP BY customer_type
ORDER BY total_revenue DESC

--Analyze seasonal demand pattern and identity the best months for hotel pricing optimization.
SELECT 
    arrival_date_month,
    COUNT(*) AS total_bookings,
    ROUND(AVG(adr),2) AS avg_adr
FROM hotel_bookings
GROUP BY arrival_date_month
ORDER BY 
CASE arrival_date_month
    WHEN 'January' THEN 1
    WHEN 'February' THEN 2
    WHEN 'March' THEN 3
    WHEN 'April' THEN 4
    WHEN 'May' THEN 5
    WHEN 'June' THEN 6
    WHEN 'July' THEN 7
    WHEN 'August' THEN 8
    WHEN 'September' THEN 9
    WHEN 'October' THEN 10
    WHEN 'November' THEN 11
    WHEN 'December' THEN 12
END;

--identify high-value customers define as those with high ADR ,long stay and no cancellations
SELECT 
    customer_type,
    COUNT(*) AS high_value_bookings,
    ROUND(AVG(adr),2) AS avg_adr,
    AVG(stays_in_weekend_nights + stays_in_week_nights) AS avg_stay_length
FROM hotel_bookings
WHERE 
    is_canceled = 0
    AND adr > (SELECT AVG(adr) FROM hotel_bookings)
    AND (stays_in_weekend_nights + stays_in_week_nights) >= 5
GROUP BY customer_type
ORDER BY high_value_bookings DESC

