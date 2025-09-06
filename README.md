# 🚖 Uber Booking Data Analysis (NCR)

This project analyses **Uber-style ride bookings** in the **NCR (National Capital Region)** using **SQL, Google Sheets, and Power BI**.  

The purpose of this project is to **clean, transform, and visualize ride booking data** to extract **actionable business insights** such as demand trends, cancellations, payment mix, peak hours, ride completions, and customer/driver ratings.  

The dataset is **synthetic**, generated to mirror real-world booking behavior for demonstration and reproducibility.

---

## 📂 Project Structure
- **Dataset**: `ncr_ride_bookings.csv` – Synthetic dataset (150k rows for NCR bookings)  
- **SQL Scripts**: `queries.sql` – Cleaning, transformation, KPI calculations  
- **Google Sheets**: `sheets/` – Pivot tables and ad-hoc summaries  
- **Power BI Dashboard**: `Uber.pbix` – Interactive dashboard for business insights  
- **README.md** – Documentation (this file)  
- **DATASET_PROMPT.txt** – Dataset generation prompt (included for transparency)

---

## 📊 Dataset Source
- The dataset (`ncr_ride_bookings.csv`) is **synthetic** and generated using **ChatGPT**.  
- Rows: ~150,000 (1 month of rides in NCR).  
- Success Rate: ~62% of rides completed, to reflect realistic behavior.  
- Vehicle Types: Auto, Bike, eBike, Go Sedan, Go Mini, Prime Sedan, Prime SUV.  
- 50 Pickup & Drop Locations: NCR dummy localities (Noida, Gurugram, CP, Rohini, Saket, etc.).  

👉 Dataset includes bookings, cancellations, incomplete rides, ratings, payment method, and trip details.  

---

## 🔹 Steps Performed

### 1. Data Generation & Ingestion
- Created a synthetic dataset using a ChatGPT prompt.  
- Imported CSV into **MySQL** for ETL & analytics.  
- Loaded dataset into **Google Sheets** for quick validation & pivots.  

### 2. Data Cleaning (MySQL)
Key steps:
- Removed unwanted quotes in `Booking_ID` and `Customer_ID`.  
- Standardized values (e.g., “premier sedan” → “Prime Sedan”).  
- Handled NULLs (replaced with 0, average, or "Other" depending on column).  
- Rounded ratings & timing values.  
- Ensured valid ranges for ratings (1–5 only).  
- Removed outliers (ride distance > 100 km inside NCR).  

### 3. Exploratory Data Analysis (SQL)
Wrote queries (Basic → Advanced) to answer business questions:
- Completion vs cancellation trends.  
- Revenue by vehicle type.  
- Peak booking hours.  
- Cancellation reasons.  
- Top customers & locations.  
- Payment method preferences.  
- Relationship between ratings & cancellations.  

### 4. Reporting (Power BI)
Developed an **interactive dashboard** with:
- **KPI cards**: Total rides, completion %, revenue, avg fare.  
- **Temporal analysis**: Hourly/weekly demand patterns.  
- **Location insights**: Top pickup & drop areas.  
- **Cancellation analysis**: Breakdown by reason, customer vs driver.  
- **Operational metrics**: VTAT, CTAT, ratings distribution.  

---

## 📈 Sample Insights
- Overall completion rate ≈ **62%**.  
- Evening (18:00–21:00) shows **peak demand**.  
- ~40% of bookings come from top 5 NCR locations.  
- ~25% of rides cancelled (customer or driver).  
- Bikes/eBikes show faster arrival (low VTAT/CTAT).  

---

## 🛠 Tools & Technologies
- **SQL (MySQL)** – Cleaning, transformation, KPI queries  
- **Google Sheets** – Validation, pivot summaries  
- **Power BI Desktop** – Dashboard & data visualization  

---

## 📜 How to Reproduce
1. Clone the repository:  
   ```bash
   git clone https://github.com/Hasan271998/Uber-Booking-Analysis.git
   cd Uber-Booking-Analysis
