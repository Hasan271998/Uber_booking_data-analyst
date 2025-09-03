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
- Synthetic dataset (100,000 rows) created via ChatGPT prompt and saved as `ncr_ride_bookings.csv`.  
- CSV imported into SQL (MySQL/Postgres) for ETL and analytics.  
- A copy loaded into Google Sheets for quick validation & pivot exploration.

### 2. Data Cleaning (SQL & Google Sheets)
- Removed duplicates and standardised date/time formats (`YYYY-MM-DD`, `HH:MM: SS`).  
- Set `Booking_Status` distribution to **~62% success** and ensured that VTAT, CTAT, fare, and  ratings fields exist only for successful bookings.  
- Handled missing or inconsistent values in `Booking_Value`, `Ride_Distance`.  
- Added derived columns:  
  - `Trip_Duration_minutes` (if start & end times available)  
  - `Revenue_after_discount` (if discounts exist)  
  - `Is_Successful` (boolean), `VTAT_seconds`, `CTAT_seconds`

### 3. Data Modeling (Star schema suggestion)
- `fact_rides` — main fact table (booking_id, date_id, time_id, customer_id, vehicle_type_id, pickup_id, drop_id, status, fare, distance, duration, vt_at, ct_at, driver_rating, customer_rating, ...)  
- `dim_date`, `dim_time`, `dim_vehicle_type`, `dim_location`, `dim_customer` — dimension tables for performant analytics.

### 4. Analysis (SQL Queries)
- Computed KPIs: Total rides, Completed rides, Revenue, Avg fare, Avg trip distance, Avg VTAT/CTAT, Cancellation rate, Top pickup/drop locations.  
- Identified peak hours and day-of-week demand patterns.  
- Cancellation reasons distribution (customer vs driver) and incomplete ride patterns.  
- Created segmentation by vehicle type and location to surface area-specific issues (e.g., higher cancellations in certain sectors).

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
