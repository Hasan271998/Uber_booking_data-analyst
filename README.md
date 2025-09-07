# 🚖 Uber Booking Data Analysis (NCR)

This project analyses **Uber-style ride bookings** in the **NCR (National Capital Region)** using **SQL, Google Sheets, and Power BI**.  

The purpose of this project is to **clean, transform, and visualize ride booking data** to extract **actionable business insights** such as demand trends, cancellations, payment mix, peak hours, ride completions, and customer/driver ratings.  

The dataset is **synthetic**, generated to mirror real-world booking behavior for demonstration and reproducibility.

---

## 📂 Project Structure
- **Dataset**: `ncr_ride_bookings.csv` – Synthetic dataset (150k rows for NCR bookings)  
- **SQL Scripts**: `queries.sql` – Cleaning, transformation, KPI calculations  
- **Excel/Sheets**: `sheets/` – Pivot tables and ad-hoc summaries  
- **Power BI Dashboard**: `Uber.pbix` – Interactive dashboard for business insights  
- **README.md** – Documentation (this file)  
- **DATASET_PROMPT.txt** – Dataset generation prompt (for reproducibility)  

---

## 📊 Dataset Source
- The dataset (`ncr_ride_bookings.csv`) is **synthetic**, generated using **ChatGPT**.  
- Rows: ~150,000 (1 month of rides in NCR).  
- Success Rate: ~62% of rides completed (to reflect realistic behavior).  
- Vehicle Types: Auto, Bike, eBike, Mini, Prime Sedan, Prime SUV, Prime Plus.  
- Pickup & Drop Locations: 50 NCR dummy localities (e.g., Noida Sector 18, Gurugram Cyber Hub, Connaught Place, Saket, Rohini).  

👉 Dataset includes **bookings, cancellations, incomplete rides, ratings, payment methods, timings, and trip values**.  

---

## 🔹 Steps Performed

### 1. Data Generation & Ingestion
- Created a synthetic dataset via **ChatGPT prompt**.  
- Imported CSV into **MySQL** for ETL & analysis.  
- Loaded dataset into **Google Sheets** for quick pivots & validation.  

### 2. Data Cleaning (SQL)
- Removed unwanted quotes in IDs.  
- Standardized categorical values (e.g., *premier sedan → Prime Sedan*).  
- Handled NULLs (replaced with 0, average, “Other”, or “Unknown” depending on column).  
- Rounded numeric columns (ratings, CTAT, VTAT).  
- Ensured ratings within **1–5 range** only.  
- Deleted outliers (ride distance > 100 km within NCR).  

### 3. Exploratory Data Analysis (SQL)
Designed queries from **basic → advanced** to answer:  
- Ride completion vs cancellations.  
- Revenue by vehicle type.  
- Peak & off-peak booking hours.  
- Cancellation breakdown by reason.  
- Top customers & locations.  
- Payment method preferences.  
- Driver ratings vs cancellation rate.  
- **Lowest trends** (lowest driver ratings, lowest demand areas, lowest demand hours).  

### 4. Reporting (Power BI)
Built an **interactive dashboard**:  
- **KPIs**: Total rides, completion %, avg fare, total revenue.  
- **Demand Analysis**: Hourly & weekly ride patterns.  
- **Geographical Analysis**: Top pickup/drop hotspots (maps & bar charts).  
- **Cancellation Analysis**: Customer vs driver cancellations with reasons.  
- **Operational Metrics**: VTAT, CTAT distributions, driver & customer ratings.  

---

## 📈 Sample Insights

### ✅ Key Highlights
- Overall completion rate ≈ **62%**.  
- Evening (**6–9 PM**) is the **highest demand window**.  
- ~40% of bookings concentrated in **top 5 NCR areas**.  
- ~25% of rides cancelled (split between customer & driver).  
- Bikes/eBikes have the fastest arrival times (low VTAT/CTAT).  
- Prime SUVs and Prime Plus generate the **highest revenue per ride**.  

### 🔻 Lowest Trends
- **Lowest Driver Ratings**: Found mostly in Auto & Mini categories.  
- **Lowest Peak Areas**: Outskirts like Greater Noida Extension & remote Gurugram sectors.  
- **Lowest Peak Hours**: Very late night/early morning (**2–5 AM**) with minimal rides.  

---

## 🔧 Recommendations for Improvement
- **Driver Quality**  
  - Training programs for low-rated drivers.  
  - Incentives for consistently high-rated drivers.  
  - Penalties for repeat offenders with poor ratings.  

- **Low-Demand Areas**  
  - Targeted **discount campaigns**.  
  - Encourage **driver relocation** via heatmaps.  
  - Tie-ups with offices, malls, and societies to increase demand.  

- **Off-Peak Hours**  
  - Apply **dynamic (surge) pricing**.  
  - Introduce **night-shift incentives** for drivers.  
  - Promote **safety features** (SOS, live tracking) to increase late-night bookings.  

---

## 🛠 Tools & Technologies
- **SQL (MySQL)** → Cleaning, transformations, analysis  
- **Microsoft Excel** → Validation, pivots  
- **Power BI Desktop** → Interactive dashboards  

---

## 📜 How to Reproduce
1. Clone repository:  
   ```bash
   git clone https://github.com/Hasan271998/Uber-Booking-Analysis.git
   cd Uber-Booking-Analysis
