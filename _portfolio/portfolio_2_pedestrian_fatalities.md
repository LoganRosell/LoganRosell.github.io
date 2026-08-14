---
title: "Pedestrian Fatalities in Vision Zero US Cities"
excerpt: "Short description of portfolio item number 1<br/><img src='/images/pedestrian_fatalities_dasbhoard_thumbnail.png' width='500'>"
collection: portfolio
permalink: /portfolio/pedestrian-fatalities/
redirect_from:
  - /portfolio/portfolio-1/
  - /portfolio/portfolio-1
  - /portfolio/portfolio_1_pedestrian_fatalities/
  - /portfolio/portfolio_2_pedestrian_fatalities/
---

# Pedestrian Fatalities in Vision Zero US Cities

## Project Overview
**Vision Zero** is a strategy aimed at eliminating traffic fatalities and severe injuries while promoting safe, equitable mobility. This data engineering and visualization project analyzed fatal pedestrian accidents across **61 US Vision Zero cities** over a 6-year study period (**2018–2023**) to evaluate key environmental, geographic, and temporal conditions.

**Core Question:** *Among cities that have adopted Vision Zero, what conditions are most commonly reported in fatal car accidents involving a pedestrian?*

---
## Data Engineering Architecture & Pipeline
* **Data Ingestion:** Automated Python pipelines querying the **NHTSA Fatality Analysis Reporting System (FARS)** API, **Open-Meteo Historical Weather** API, and **US Census Bureau (ACS 1-Year)** API.
* **Transformation & Normalization:** Python and SQL scripts handling schema design, fuzzy matching for coordinates/city names, 2020 Census imputation, and filtering cities with populations $\ge 65\text{k}$.
* **Serving Layer:** PostgreSQL database hosted via Railway serving structured views to an interactive **Grafana** dashboard.
---

## Dashboard Breakdown

### 1. High-Level Metrics & Geographic Overview
![Pedestrian Fatalities Dashboard 1](/images/pedestrian_fatalities_dashboard_1.png)
* **Key Features:** Top-level summary KPIs (total fatalities, per-capita rate), national map visualization highlighting city-by-city fatality distribution, and multi-variable filtering controls (year, city, speed limit).
* **Takeaways:** Demonstrates geographic disparities across participating Vision Zero cities, showing higher fatality concentrations across Sunbelt and Southern urban corridors.

### 2. Temporal & Environmental Conditions
![Pedestrian Fatalities Dashboard 2](/images/pedestrian_fatalities_dashboard_2.png)
* **Key Features:** Breakdown of fatal crashes by lighting conditions (daylight vs. dark/unlit), time of day, day of week, and daily weather patterns matched from Open-Meteo data.
* **Takeaways:** Fatalities peak disproportionately during low-light/nighttime hours and on higher-speed corridors, highlighting visibility and roadway lighting infrastructure as primary risk factors.

### 3. City-by-City Benchmarking & Demographics
![Pedestrian Fatalities Dashboard 3](/images/pedestrian_fatalities_dashboard_3.png)
* **Key Features:** Per-capita fatality rankings across all 61 analyzed Vision Zero cities normalized against ACS Census population data.
* **Takeaways:** Absolute crash numbers can be misleading without population weighting; normalization reveals that several smaller/mid-sized Southern cities (particularly in Florida) face significantly higher per-capita fatality rates compared to denser transit-heavy cities like New York or San Francisco.

### 4. Roadway Infrastructure & Speed Analysis
![Pedestrian Fatalities Dashboard 4](/images/pedestrian_fatalities_dashboard_4.png)
* **Key Features:** Correlation between posted speed limits, roadway classification, intersection proximity, and fatal collision severity.
* **Takeaways:** Reinforces the core Vision Zero premise—while traffic accidents may occur, high speeds on multi-lane arterial roads turn survivable incidents into fatal ones.