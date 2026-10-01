# Weather Data ETL Pipeline
> A Python ETL pipeline that extracts real-time weather data from the OpenWeather API for five Nigerian cities, transforms it with Pandas, and loads it into CSV and SQLite for analysis — built to practice the core data engineering pattern behind most analytics work.

---

## ⚙️ Project Type Flags
> *Check what applies. This helps reviewers and collaborators understand the nature of the work at a glance. Delete this block before publishing.*

- [ ] Exploratory Data Analysis (EDA)
- [ ] SQL Analysis / Querying
- [ ] Dashboard / Data Visualization
- [x] Data Pipeline / ETL
- [ ] Predictive Modelling / Machine Learning
- [x] Data Cleaning / Wrangling
- [ ] End-to-End (multiple of the above)
- [ ] Other: ___________

---

## Table of Contents
1. [Project Overview](#1-project-overview)
2. [Objectives](#2-objectives)
3. [Project Scope & Tools](#3-project-scope--tools)
4. [Repository Structure](#4-repository-structure)
5. [Data Workflow](#5-data-workflow)
6. [Data Model & Schema](#6-data-model--schema)
7. [Analysis & Metrics](#7-analysis--metrics)
8. [Key Insights](#8-key-insights)
9. [Recommendations](#9-recommendations)
10. [Assumptions & Limitations](#10-assumptions--limitations)
11. [Future Enhancements](#11-future-enhancements)
12. [Deliverables](#12-deliverables)
13. [Author](#13-author)

---

## 1. Project Overview

**Context:** Data analysts often work with data from APIs, databases, and spreadsheets, none of which arrives ready for analysis — it has to be extracted, cleaned, transformed, and stored first. This project was Week 7 of the AnalystLab Africa Data Analytics Internship, focused on that exact process (ETL).
**Problem Statement:** Build a basic ETL pipeline that pulls live weather data for multiple cities, structures it cleanly, and stores it in a reusable format for analysis.
**Approach:** Used Python's Requests library to call the OpenWeather API for five Nigerian cities, transformed the nested JSON responses into a flat table with Pandas, and loaded the result into both a CSV file and a SQLite database.
**Outcome:** A working, reusable ETL script plus a cleaned dataset covering current weather conditions across Lagos, Abuja, Port Harcourt, Kano, and Owerri, with a short comparative analysis of temperature, humidity, wind speed, and weather conditions.

---

## 2. Objectives

**Primary Objective:** Build a working ETL pipeline that extracts, transforms, and loads real-time weather data using Python.
**Secondary Objective 1:** Practice working with a real external API, including authentication and error handling.
**Secondary Objective 2:** Produce a clean, structured dataset ready for comparative analysis across cities.

> 💡 *Every analysis decision in this project traces back to one of these objectives.*

---

## 3. Project Scope & Tools

### Scope
| Dimension | Details |
|-----------|---------|
| **In Scope** | Current weather snapshot (temperature, humidity, weather condition, wind speed) for 5 Nigerian cities, pulled once via the OpenWeather Current Weather API |
| **Out of Scope** |Historical weather data and 16-day forecasts (not available on the free OpenWeather tier); automated/scheduled re-runs |
| **Time Period** | Single point-in-time snapshot, collected August 2026 |
| **Granularity** | One row per city per extraction run |

### Tools & Technologies

| Category | Tool(s) Used |
|----------|-------------|
| Data Storage | CSV file, SQLite database |
| Data Processing | Python, Pandas |
| Analysis |Pandas (aggregate comparisons: max/min by column) |
| Visualization | None (tabular output only) |
| Version Control |Git / GitHub |
| Documentation |Markdown |
| Other | Requests (API calls), Google Colab (development environment |

---

## 4. Repository Structure

```
weather-etl-pipeline/
├── weather_etl.py          # Full ETL pipeline script (extract, transform, load, analyze)
├── weather_data.csv        # Processed dataset (output)
├── weather_data.db         # SQLite database version of the same data (output)
├── notebooks/
│   └── weather_etl.ipynb   # Google Colab notebook used for development
└── README.md               # You are here
```
---

## 5. Data Workflow

```
5. Data Workflow
OpenWeather API (5 cities)
        ↓
Extraction via Python Requests
        ↓
Cleaning & Transformation (Pandas)
        ↓
Load to CSV + SQLite
        ↓
```

**Source:** OpenWeather Current Weather Data API — live JSON response per city, accessed via a free-tier API key.
**Ingestion:** extract_weather() sends a GET request per city with the API key and unit settings; extract_all() loops through the city list and skips any city that fails, logging the error instead of crashing the pipeline.
**Cleaning:** Renamed raw API field names to clear, analysis-friendly column names (e.g. temp → temperature_c); title-cased weather descriptions for consistency.
**Transformation:** Converted temperature and wind speed to floats, humidity to an integer, and the retrieval time to a proper datetime; added a retrieved_at_utc timestamp column.
**Analysis:** Compared temperature, humidity, and wind speed across cities using Pandas idxmax()/idxmin(), and compared weather conditions city by city.
**Output:** A tidy DataFrame saved to both weather_data.csv and a weather table inside weather_data.db.

---

## 6. Data Model & Schema

### Dataset / Table: `weather`


| Field Name | Data Type | Description | Example Value |
|------------|-----------|-------------|---------------|
| `city` | string | City name as returned by OpenWeather | Lagos |
| `country` | string | ISO country code | NG |
| `temperature_c` | float | Current temperature in Celsius | 29.28 |
| `humidity_pct` | int | Relative humidity percentage| 64 |
| `weather_condition` | string | High-level weather category | Rain |
| `wind_speed_mps` | float | Wind speed in meters per second | 3.96|
| `retrieved_at_utc` | datetime | Timestamp the record was pulled | 2026-08-12 11:55:42 |

> **Row count (approx.):** Row count: 5 rows (one per city). No multi-table joins — single flat table.

---

## 8. Analysis & Metrics

### Analytical Approach

Analytical Approach: This was exploratory, single-snapshot comparison work rather than hypothesis testing — the goal was to practice extracting and structuring data cleanly, then compare a few straightforward metrics across cities.

### Key Metrics Defined

| Metric | Plain-Language Definition | Why It Matters |
|--------|--------------------------|----------------|
| `Temperature (°C)` |How hot or cold each city was at extraction time | Basic comparative weather signal across regions |
| `Humidity (%)` |How much moisture was in the air | Indicates likelihood of rain/discomfort, complements temperature |
| `Wind Speed (m/s)` | How fast the wind was blowing |Adds context to "feels like" conditions |
| `[Metric 3]` | [What it measures, in one sentence] | [What decision or question it answers] |

### Methods Used
- Descriptive comparison (max/min) across cities for temperature, humidity, and wind speed
- Categorical comparison of weather conditions city by city

---

## 9. Key Insights

**Insight 1: Kano was the hottest and driest city.** Kano recorded the highest temperature (33.17°C) and the lowest humidity (43%), and was the only city with cloudy rather than rainy conditions — suggesting drier air in the north at the time of collection.

**Insight 2: Abuja was the coolest and most humid city.** Abuja recorded the lowest temperature (26.9°C) and the highest humidity (76%), the opposite pattern from Kano.

**Insight 3: Lagos had the strongest winds.** Lagos recorded the highest wind speed at 3.96 m/s among the five cities.

**Insight 4: A regional rain/cloud split emerged.** Four of five cities (Lagos, Abuja, Port Harcourt, Owerri — all southern) were experiencing rain at extraction time, while Kano (northern) alone showed cloudy skies, hinting at a broader south-vs-north weather pattern worth checking against a larger sample.

---

## 9. Recommendations

| Priority | Recommendation | Based On | Suggested Owner |
|----------|---------------|----------|-----------------|
| High | Automate the pipeline to run on a schedule (e.g. daily) to build a real time-series instead of a single snapshot | Current pipeline only captures one point in time  |
| Medium | Add more cities across different Nigerian regions to test the south/north weather pattern more rigorously| Insight 4 | Project owner] |
| Low |Add basic charts (temperature/humidity trend lines) once historical data accumulates |Limitations below | Project owner |

---

## 11. Assumptions & Limitations

Assumptions

Limitations
Single point-in-time snapshot — no historical trend data (16-day forecast and history are not available on the free tier).
Only 5 cities, all in Nigeria — not a statistically robust sample for broader climate claims.
No automated scheduling — the pipeline must be re-run manually to get updated data.

### Assumptions
- Treated each API response as accurate at the moment of retrieval, without independently verifying against another weather source.
- Assumed the free-tier OpenWeather "current weather" endpoint is representative enough for a basic comparison exercise.

### Limitations
- Single point-in-time snapshot — no historical trend data (16-day forecast and history are not available on the free tier).
- Only 5 cities, all in Nigeria — not a statistically robust sample for broader climate claims.
- No automated scheduling — the pipeline must be re-run manually to get updated data.

---

## 12. Future Enhancements
- [ ] Schedule the pipeline to run daily and append to a growing historical table
- [ ] Add a simple visualization layer (line/bar charts for temperature and humidity trends)
- [ ] Expand to more cities across different regions/climates for a stronger comparison

---

## 13. Deliverables

| Deliverable | Description | Location |
|-------------|-------------|----------|
| ETL Script | Full extract/transform/load/analysis pipeline | `weather_etl.py` |
| Processed Dataset | Cleaned weather data | `weather_data.csv, weather_data.db` |
| Notebook| Development notebook (Google Colab)| `notebooks/weather_etl.ipynb` |
| Documentation| This README| `README.md` |


---

## 14. Author

**Vivian Okwara**
Data Analyst | Lagos, Nigeria 

- 🔗 LinkedIn: https://linkedin.com/in/okwara-vivian
- 💼 https://Vivian-Portfolio. github.io
- 📧 Email: okwaravivian26@gmail.com
---

*Last updated: August 2026*
