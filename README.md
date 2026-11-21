# EcoCampus – Campus Waste Generation Analysis

> **Campus Environmental Intelligence & Data Analysis Platform**  
> **Tagline:** *"Measure Waste Today. Create a Greener Tomorrow."*  
> **Hero Objective:** *"Understand Waste. Build a Cleaner Campus."*

---

## 1. Project Overview & Problem Statement

### Problem Statement
College campuses currently lack fine-grained, location-wise waste analytics. Facilities and administrative personnel cannot readily observe which specific areas generate the most waste, and manual observations are error-prone and unstandardized. Without rigorous quantitative tracking, it is difficult to isolate waste hotspots or common material streams.

### Project Objective & Scope
**EcoCampus** delivers an end-to-end data science platform that:
1. **Ingests & Collects** waste measurements across all major campus zones (Canteen, Hostels, Classrooms, Library, Playground, Labs, Admin Block, Auditorium).
2. **Standardizes & Cleans** raw entries using Pandas (handling missing values, deduplication, synonym mapping, data type coercion, positive weight sanity checks).
3. **Aggregates & Groups** data by location and material category to compute totals, daily moving averages, and exact percentages.
4. **Visualizes Findings** using Bar charts (location comparison), Donut charts (stream proportions), Line/Area charts (daily timeline with 7-day moving averages), and cross-tabulation matrices.
5. **Provides Academic Defense & Viva Readiness** with explicit formula proofs, Pandas code explanations, and methodological limitations analysis.

---

## 2. Core Dataset Fields

| Field Name | Type | Description |
| :--- | :--- | :--- |
| `date` | Date (`YYYY-MM-DD`) | Date when waste collection was measured |
| `campus_location` | String | Campus facility (e.g. Canteen, Hostel, Classrooms, Library, etc.) |
| `waste_category` | String | Waste classification (Organic/Food, Plastic, Paper, Metal, Glass, Other) |
| `weight_kg` | Float ($> 0$) | Net weight of waste in kilograms |
| `remarks` | String | Operational context or collection notes |
| `record_id` | String | Unique identifier (e.g., `ECO-DEMO-1001`, `ECO-REC-1002`) |

---

## 3. Illustrative Benchmark Sample (From Project Brief)

The project includes an instant one-click loader for the exact 5-row reference table from the academic brief:

| Location | Waste Type | Weight (kg) | Computed Campus Share (%) |
| :--- | :--- | :--- | :--- |
| **Canteen** | Food (Organic) | 15.0 kg | 32.6% |
| **Classroom** | Paper | 8.0 kg | 17.4% |
| **Hostel** | Plastic | 12.0 kg | 26.1% |
| **Library** | Paper | 5.0 kg | 10.9% |
| **Playground** | Plastic | 6.0 kg | 13.0% |
| **Grand Total** | — | **46.0 kg** | **100.0%** |

---

## 4. Pandas Data Cleaning & Preparation Pipeline

```python
# 1. Missing Values & Type Casting
df['weight_kg'] = pd.to_numeric(df['weight_kg'], errors='coerce').fillna(0.0)
df = df[df['weight_kg'] > 0]  # Sanity check: strictly positive weight
df['date'] = pd.to_datetime(df['date'], errors='coerce')
df = df.dropna(subset=['date'])

# 2. Duplicate Detection & Removal
df = df.drop_duplicates(subset=['date', 'campus_location', 'waste_category', 'weight_kg'])

# 3. Category & Location Synonym Standardisation
# e.g., 'food' -> 'Organic / Food Waste', 'dorm' -> 'Hostel'

# 4. Location Aggregations & Percentage Formulas
loc_group = df.groupby('campus_location')['weight_kg'].agg(['sum', 'mean']).reset_index()
loc_group['percentage'] = (loc_group['weight_kg'] / total_weight * 100).round(1)

# 5. Time-Series 7-Day Rolling Moving Average
daily_df['moving_avg_7d'] = daily_df['weight_kg'].rolling(window=7, min_periods=1).mean().round(1)
```

---

## 5. System Architecture & Available Pages

1. **Home (`/`)**: Hero banner with slide-in entrance, animated scroll-down indicator, continuous sliding ticker marquee, interactive live preview, and features.
2. **Dashboard (`/dashboard`)**: 4 KPI cards with animated counters, sliding temporal filter pill, location bar chart, category donut chart, daily line chart with 7-day moving average toggle, monthly area chart, and automated natural-language insights.
3. **Waste Records (`/records`)**: Full CRUD registry table, live search, sorting, filtering, and instant "Load Brief Benchmark (5 Rows)" action.
4. **Data Upload (`/upload`)**: Drag-and-drop CSV/Excel file ingestion, column mapping, pre-import hygiene diagnostics, and template download.
5. **Location Analysis (`/locations`)**: Multi-location head-to-head comparison engine with grouped charts and facility leaderboard rankings.
6. **Category Analysis (`/categories`)**: Material stream circularity, diversion radar index, and avoided GHG emissions ($kg\text{ CO}_2e$) modeling.
7. **Institutional Reports (`/reports`)**: Daily, weekly, monthly formal audit document with 5 data-driven policy recommendations and 6 methodological limitations.

---

## 6. How to Run Locally

### Start Backend (FastAPI on Port 8000)
```bash
cd backend
python -m venv venv
source venv/bin/activate
pip install -r requirements.txt
uvicorn main:app --host 127.0.0.1 --port 8000 --reload
```

### Start Frontend (Vite on Port 3000)
```bash
cd frontend
npm install
npm run dev
```

Open **http://localhost:3000** in your browser.
