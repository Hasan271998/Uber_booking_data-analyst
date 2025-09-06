# 🚖 Uber Booking Data Analysis (NCR)

This project analyses **Uber-style ride bookings** for the **NCR (National Capital Region)** using **SQL, Google Sheets, and Power BI**.  
Purpose: clean, transform, and visualise booking data to extract actionable business insights (demand trends, cancellations, payment mix, peak hours, ride completions, driver & customer ratings). The dataset is synthetic and generated to mirror real-world booking behaviour for demonstration and reproducibility.

---

## 📂 Project Structure
- **Dataset**: `ncr_ride_bookings.csv` (Synthetic CSV used for the analysis)  
- **SQL Scripts**: `queries.sql` (Data cleaning, transformation, KPI calculations)  
- **Google Sheets**: `sheets/` (Pivot tables and ad-hoc summaries)  
- **Power BI Dashboard**: `Uber.pbix` – Interactive dashboard for business insights  
- **README.md** (this file)  
- **DATASET_PROMPT.txt** (generation prompt — included)

---

## 🔹 Dataset Source (Important)
**Dataset provenance:** This dataset is **synthetic** and was generated using **ChatGPT** for the purpose of this analysis and demonstration. The synthetic dataset contains ** (1.5 lac) rows** covering one month for the **NCR city**. The dataset structure, distributions and success-rate (~62%) were chosen to reflect realistic ride-booking behaviour.

**Generation Prompt (used verbatim with ChatGPT):**

> Please create a spreadsheet with 1.5 lac rows, for NCR city. Give the following columns.  
> The data will be for 1 month. Use the following column -  
> 1. Date  
> 2. Time  
> 3. Booking ID  
> 4. Booking Status  
> 5. Customer ID  
> 6. Vehicle Type  
> - Auto  
> - Prime Plus  
> - Prime Sedan  
> - Mini  
> - Bike  
> - eBike  
> - Prime SUV  
> 7. Pickup Location (Create dummy location points. Take any 50 areas from Bangalore)  
> 8. Drop Location (Take from dummy pickup locations)  
> 9. Avg VTAT (Time taken to arrive at the vehicle)  
> 10. Avg CTAT (Time taken to arrive at  the Customer)  
> 11. Cancelled Rides by Customer  
> 12. Reason for cancelling by Customer  
> - Driver is not moving towards the pickup location  
> - Driver asked to cancel  
> - AC is not working (Only for 4-wheelers)  
> - Change of plans  
> - Wrong Address  
> 13. Cancelled Rides by Driver  
> - Personal & Car related issues  
> - Customer-related issue  
> - The customer was coughing/sick  
> - More than the permitted people in there  
> 14. Incomplete Rides  
> 15. Incomplete Rides Reason  
> - Customer Demand  
> - Vehicle Breakdown  
> - Other Issue  
> 16. Booking Value  
> 17. Ride Distance  
> 18. Driver Ratings  
> 19. Customer Rating  
> Keep the overall booking status success for this data at 62%. If the booking status is successful, then only fare charge ratings, average VTAT, average CTAT, and other data will be there.

**Note about pickup locations:** Although prompt mentions “Take any 50 areas from Bangalore”, for the NCR project, these location points were adapted to **50 representative NCR/locality names** (e.g., Gurgaon sectors, Noida sectors, Connaught Place, Saket, Rohini, etc.) to align with the NCR scope.

**Analysis provenance:** The synthetic NCR dataset was generated and then **analysed/adjusted** using the earlier uploaded synthetic file (`ncr_ride_bookings.csv`) — distributions (success rate, peak hour patterns, cancellation reason frequencies, rating distributions) were aligned with the patterns observed in that file, so the final dataset behaves realistically for analysis and dashboarding.

---

## 🔹 Steps Performed

### 1. Data Generation & Ingestion
- Synthetic dataset (150,000 rows) created via ChatGPT prompt and saved as `ncr_ride_bookings.csv`.  
- CSV imported into SQL (MySQL) for ETL and analytics.  
- A copy loaded into Google Sheets for quick validation & pivot exploration.


##Dataset Overview (Key Columns)
/*
Booking Info: Date, Time, Booking_ID, Booking_Status (Completed, Cancelled, Incomplete, No Driver Found)
User & Ride: Customer_ID, Vehicle_Type, Pickup_Location, Drop_Location
Timings: Avg_VTAT (Vehicle time to arrive), Avg_CTAT (Customer time to arrive)
Cancellations: Cancelled_Rides_by_Customer, Reason_for_cancelling_by_Customer, Cancelled_Rides_by_Driver, Driver_Cancellation_Reason
Incomplete: Incomplete_Rides, Incomplete_Rides_Reason
Financials: Booking_Value, Ride_Distance, Payment_Method
Feedback: Driver_Ratings, Customer_Rating

*/

### 2. Data Cleaning (MySQL)

#Data Cleaning Steps (SQL):
# Vehicle_type 
# Booking_Value, Ride_Distance	Replace NULL with 0
# Driver_Ratings, Customer_Rating, Avg_VTAT, Avg_CTAT	Replace NULL with average/median
# Reason_for_cancelling_by_Customer, Driver_Cancellation_Reason, Incomplete_Rides_Reason	Keep NULL (only valid when applicable)
# Cancelled_Rides_by_Customer / Driver, Incomplete_Rides	Replace NULL with 0
# Payment_Method	Replace NULL with "UNKNOWN"

#1. Removed quotes from Booking_ID and Customer_ID

UPDATE ride_bookings
SET Booking_ID = REPLACE(Booking_ID, '"', ''),
    Customer_ID = REPLACE(Customer_ID, '"', '');
    
#2. Standardised categorical values (status, vehicle type)

UPDATE ride_bookings
SET Booking_Status = CONCAT(
    UPPER(LEFT(TRIM(Booking_Status),1)),
    LOWER(SUBSTRING(TRIM(Booking_Status),2))
);

-- Vechile_type

UPDATE ride_bookings
SET Vehicle_Type = CONCAT(
    UPPER(LEFT(TRIM(Vehicle_Type),1)),
    LOWER(SUBSTRING(TRIM(Vehicle_Type),2))
);


/*3. Handled missing values (Vehicle_type,Avg_vtat,Avg_ctat,Cancelled_ride_by_Customer,reason_for_cancelling_by_customer,cancel_ride_by_driver,
driver_cancellation_reason,Incompete_rides,Incomplete_rides_reason,Booking_Value & Ride_Distance,Driver_Rating,Customer_Rating,Payments) */

-- Premier sedan replace with 'Prime Sedan' In the Vehicle_type Column

Update ride_bookings
set vehicle_type='Prime Sedan'
Where vehicle_type='Premier sedan';

-- Avg_VTAT replace NULL with overall average

UPDATE ride_bookings
SET Avg_VTAT = (SELECT AVG(t.Avg_VTAT) 
                FROM (SELECT * FROM ride_bookings) t
                WHERE t.Avg_VTAT IS NOT NULL)
WHERE Avg_VTAT IS NULL;

-- To remove decimal point 

UPDATE ride_bookings
SET Avg_VTAT = ROUND(Avg_VTAT, 2);

-- Avg_CTAT replace NULL with overall average

UPDATE ride_bookings
SET Avg_CTAT = (SELECT AVG(t.Avg_CTAT) 
                FROM (SELECT * FROM ride_bookings) t
                WHERE t.Avg_CTAT IS NOT NULL)
WHERE Avg_CTAT IS NULL;

-- To remove decimal point 

UPDATE ride_bookings
SET Avg_CTAT = ROUND(Avg_CTAT, 2);

-- update Cancelled Ride By Customer

UPDATE ride_bookings
SET cancelled_rides_by_customer = 0
WHERE Cancelled_rides_by_customer IS NULL;

-- update reason for cancel by customer, replace null with ' other'

UPDATE ride_bookings
SET reason_for_cancelling_by_customer = 'Other'
WHERE reason_for_cancelling_by_customer IS NULL;


-- Ride canceled by driver, replace Null value with 0

update ride_bookings
set cancelled_rides_by_Driver= 0 
Where Cancelled_rides_by_Driver is null;


-- driver_cancellation_reason is replaced Null Value with 'Other'

UPDATE ride_bookings
SET driver_cancellation_reason = 'Other'
WHERE driver_cancellation_reason IS NULL;

-- Incomplete Rides replace null with 0 

update ride_bookings
set Incomplete_Rides= 0 
Where Incomplete_Rides is null;

-- Incomplete_rides_reason replace Null Value with 'Other

UPDATE ride_bookings
SET Incomplete_rides_reason  = 'Other'
WHERE Incomplete_rides_reason  IS NULL;


--  Booking Value replace null Value with = 0

UPDATE ride_bookings
SET Booking_Value = 0
WHERE Booking_Value IS NULL;

-- Ride Distance replace null value with = 0

UPDATE ride_bookings
SET Ride_Distance = 0
WHERE Ride_Distance IS NULL;


-- Driver_Ratings replace NULL with average rating
UPDATE ride_bookings
SET Driver_Ratings = (SELECT AVG(t.Driver_Ratings)
                      FROM (SELECT * FROM ride_bookings) t
                      WHERE t.Driver_Ratings IS NOT NULL)
WHERE Driver_Ratings IS NULL;

UPDATE ride_bookings
SET Driver_Ratings = ROUND(Driver_Ratings, 1);

-- customer_Ratings replace NULL with average rating

UPDATE ride_bookings
SET customer_Rating = (SELECT AVG(t.customer_Rating)
                      FROM (SELECT * FROM ride_bookings) t
                      WHERE t.customer_Rating IS NOT NULL)
WHERE customer_Rating IS NULL;

UPDATE ride_bookings
SET customer_Rating = ROUND(customer_Rating, 1);

-- Payments Method replace Null value with 'Unknown'

update ride_bookings
set Payment_method='Unknown'
where Payment_method is null;

#4. Ensured valid ratings (1–5 range only)

UPDATE ride_bookings
SET Driver_Ratings = NULL
WHERE Driver_Ratings NOT BETWEEN 1 AND 5;

UPDATE ride_bookings
SET Customer_Rating = NULL
WHERE Customer_Rating NOT BETWEEN 1 AND 5;




#5. Remove outliers in Ride_Distance (more than 100 km inside NCR is unrealistic)
DELETE FROM ride_bookings
WHERE Ride_Distance > 100;

/*
##  Next Step: Exploratory Data Analysis
Now that the dataset is cleaned, the next phase is **Data Analysis**.  
In this phase, I will:
- Write SQL queries (Basic → Advanced) to extract insights
- Perform trend analysis (booking status, revenue, cancellations, peak hours)
- Customer & driver behavior study (ratings, cancellations, churn)
- Vehicle type & payment method insights
- Revenue contribution by different categories


#Basic
The analysis will help in answering key business questions like:
-- 1. Retrieve all successful bookings:
-- 2. Find the average ride distance for each vehicle type:
-- 3. Get the total number of cancelled rides by customers:
-- 4. List the top 5 customers who booked the highest number of rides:
-- 5. Get the number of rides cancelled by drivers due to personal and car-related issues:
-- 6. Find the maximum and minimum driver ratings for Prime Sedan bookings:
-- 7. Retrieve all rides where payment was made using UPI:
-- 8. Find the average customer rating per vehicle type:
-- 9. Calculate the total booking value of rides completed successfully:
-- 10. Average rating of driver and customer after a complete ride
*/


#1. Retrieve all successful bookings:

select * 
from ride_bookings 
where booking_status='Completed';



#2. Find the average ride distance for each vehicle type:

select 
	avg(ride_distance), vehicle_type
from ride_bookings
group by vehicle_type;

#3. Get the total number of cancelled rides by customers:

select 
		count(cancelled_rides_by_customer)
from ride_bookings;
	
#4. List the top 5 customers who booked the highest number of rides:

select 
	customer_id,count(booking_ID) highest_ride_booking
from ride_bookings
group by customer_id
order by highest_ride_booking desc
limit 5;


#5. Get the number of rides cancelled by drivers due to personal and car-related issues:


select 
	count(*)
From ride_bookings
where Driver_cancellation_reason='Personal & Car_related issues ';

#6. Find the maximum and minimum driver ratings for Prime Sedan bookings:

select 
	max(driver_ratings),
	min(Driver_ratings)
FROM ride_bookings
Where vehicle_type='Prime sedan';

#7. Retrieve all rides where payment was made using UPI:

Select 
		*
from ride_bookings
where payment_method='UPI'; 


#8. Find the average customer rating per vehicle type:

select 
	vehicle_type,
    avg(customer_rating)
from ride_bookings
group by vehicle_type;


#9. Calculate the total booking value of rides completed successfully:

select 
	sum(booking_value)
from ride_bookings
where booking_status='Completed';

#10. Average rating of driver and customer after complete ride

SELECT AVG(Driver_Ratings) AS avg_driver_rating,
       AVG(Customer_Rating) AS avg_customer_rating
FROM ride_bookings
WHERE Booking_Status = 'Completed';


# Business Questions Business Questions
-- How many rides were completed vs. cancelled, vs. incomplete?
-- Which vehicle type is the most popular among customers?
-- Which vehicle type generates the highest revenue?
-- What are the peak booking hours in NCR?
-- What are the top reasons for customer cancellations?
-- Which are the top 5 pickup locations by booking volume?
-- What is the relationship between driver ratings and cancellation rate?
-- Which customers have cancelled more than 3 rides (churn risk customers)?
-- Which payment method is most preferred by customers?
-- What are the main reasons for incomplete rides?

#1. How many rides were completed vs. cancelled vs. incomplete?

select 
	booking_status,
    count(*) as total_rides
from ride_bookings
group by booking_status
order by total_rides;

#2. Which vehicle type is the most popular among customers?

select 
	vehicle_type,
    count(*) as popular_vehicle
from ride_bookings
group by vehicle_type
order by popular_vehicle desc;


#3. Which vehicle type generates the highest revenue?

select 
	vehicle_type,
    sum(booking_value) as highest_revenue_by_vehicle
from ride_bookings
group by vehicle_type
order by highest_revenue_by_vehicle desc;

#4. What are the peak booking hours in NCR?

SELECT 
		HOUR(STR_TO_DATE(Time, '%H:%i:%s')) AS booking_hour,
       COUNT(*) AS total_bookings
FROM ride_bookings
GROUP BY booking_hour
ORDER BY total_bookings DESC;

#5. What are the top reasons for customer cancellations?

select 
		reason_for_cancelling_by_customer,
        count(*) as top_cancel_reason
from ride_bookings
group by reason_for_cancelling_by_customer
order by top_cancel_reason;

select * from ride_bookings;

#6. Which are the top 5 pickup locations by booking volume?

select 
		pickup_location,
        count(*) as high_volume
from ride_bookings
group by pickup_location
order by high_volume desc
limit 5;
		
#7. What is the relationship between driver ratings and cancellation rate?

SELECT Vehicle_Type,
       ROUND(AVG(Driver_Ratings),2) AS avg_driver_rating,
       SUM(
         CASE 
           WHEN Booking_Status IN ('Cancelled by driver', 'Cancelled by customer') 
           THEN 1 ELSE 0 END
       ) * 100.0 / COUNT(*) AS cancel_rate
FROM ride_bookings
GROUP BY Vehicle_Type;


#8. Which customers have cancelled more than 1 rides (churn risk customers)?
select 
		Customer_id,
        count(*) as Cancel_rides
from ride_bookings
where booking_status = 'cancelled by customer'
group by customer_id
having cancel_rides > 1
order by cancel_rides desc;


#9. Which payment method is most preferred by customers?

select
		payment_method,
        count(*) as most_payment_mode
from ride_bookings
group by payment_method
order by most_payment_mode desc;

#10. What are the main reasons for incomplete rides?

select 
		incomplete_rides_reason,
        count(*) as reason_for_incomplete_rides
from ride_bookings
where booking_status ='Incomplete'
group by incomplete_rides_reason
order by reason_for_incomplete_rides desc;
		



### 5. Reporting (Power BI)
- Created `Uber.pbix` with the following visuals:  
  - **Top KPIs**: Total Rides, Completed Rides, Revenue, Avg Fare, Cancellation %  
  - **Temporal Analysis**: Hourly heatmap, day-of-week patterns  
  - **Location Analysis**: Top pickup & drop areas (bars/maps)  
  - **Operational Metrics**: VTAT/CTAT distributions, Driver & Customer Rating trends  
  - **Cancellation Insights**: Reason breakdown, correlation with time/area/vehicle type

---

## 📈 Sample Insights (from the synthetic dataset)
- Overall completion rate ≈ **62%** (as enforced during generation).  
- Evening peak (18:00–21:00) shows the highest demand across NCR.  
- Certain NCR areas (top 5) contribute ~35–45% of bookings.  
- Cancellations by customers peak during early morning and late-night hours; driver cancellations correlate with vehicle type and distance.  
- Average VTAT and CTAT vary by vehicle type and area (e.g., bike/eBike faster arrival, Autos variable).

---

## 🛠 Tools & Technologies
- **SQL** (MySQL / PostgreSQL) – Data cleaning, aggregation, and analysis (`queries.sql`)  
- **Google Sheets** – Validation checks, pivot analysis, quick summaries  
- **Power BI Desktop** – Dashboard development (`Uber.pbix`)  

---

## 📜 How to reproduce
1. Clone repository:  
   ```bash
   git clone https://github.com/Hasan271998/Uber-Booking-Analysis.git
   cd Uber-Booking-Analysis
