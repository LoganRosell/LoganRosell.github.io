---
title: "Pedestrian Fatalities in Vision Zero US Cities"
excerpt: "An end-to-end data pipeline and interactive Grafana dashboard analyzing 7,538 fatal pedestrian crashes across 61 Vision Zero cities from 2018 to 2023. Integrates federal crash records (FARS), historical weather, and US Census data using Python, PostgreSQL, and SQL to identify key roadway, temporal, and environmental risk factors.<br/><img src='/images/pedestrian_fatalities_dasbhoard_thumbnail.png' width='500'>"
collection: portfolio
permalink: /portfolio/pedestrian-fatalities/
redirect_from:
  - /portfolio/portfolio-1/
  - /portfolio/portfolio-1
  - /portfolio/portfolio_1_pedestrian_fatalities/
  - /portfolio/portfolio_2_pedestrian_fatalities/
---

## Overview
* **Authors:** Logan Rosell, Hayden Coke, Georgia Kirkpatrick (Willamette University, School of Computing & Information Sciences)
* **Course:** DATA 503: Fundamentals of Data Engineering
* **Date:** May 17, 2026
* **Data Sources:** NHTSA Fatality Analysis Reporting System (FARS) API, US Census Bureau ACS 1-Year API, Open-Meteo Historical Weather API, Census Gazetteer, FHWA Vision Zero Action Plans
* **Stack:** Python, PostgreSQL, Railway, SQL, Grafana
* **Project Source Files** [Pedestrian Fatalities Analysis GitHub Repo](https://github.com/LoganRosell/data_eng_pedestrian_project)

---

## Executive Summary & Outcomes

This data engineering project investigated environmental, geographic, and temporal factors associated with fatal vehicle-pedestrian crashes across 61 US Vision Zero cities from 2018 to 2023. We developed automated Python extraction pipelines, normalized the multi-source data into a PostgreSQL relational star schema hosted on Railway, handled pandemic-era 2020 Census missingness via linear time-spline imputation, and implemented spatial nearest-neighbor joins to link daily weather records. We then built analytical SQL views to power an interactive multi-panel Grafana dashboard and delivered a final technical report and code repository.

### Key Outcomes:
* **Regional Disparities (Latitude & Temperature):** Fatality rates exhibited a strong geographic gradient ($$r = -0.613$$, $$R^2 = 0.37$$), where a 1-degree decrease in latitude (moving south) correlated with an additional 1.4 pedestrian fatalities per 100,000 residents annually. Florida cities accounted for 4 of the top 10 deadliest per-capita municipalities.
* **Corridor Clumping on High-Crash Networks:** Crash locations clustered heavily along high-speed, multi-lane arterial roads and major intersections across virtually all 61 cities, confirming that fatalities concentrate on a narrow fraction of urban street networks.
* **Temporal and Lighting Drivers:** Incidents peaked sharply during nighttime hours (reaching their highest point at 9:00 PM) and over weekends, whereas the daytime window (7:00 AM–3:00 PM) saw the fewest fatalities.
* **Weather vs. Risk:** Average precipitation showed no meaningful correlation with fatality rates ($$r = 0.22$$, $$R^2 = 0.05$$), indicating that regional road design, speed, and visibility are far more critical safety determinants than wet weather conditions.

---

## Research Focus
* **Research Question:** Among US cities with at least 65,000 residents that have committed to a Vision Zero strategy, what factors are most commonly associated with fatal car accidents involving a pedestrian between 2018 and 2023?
* **Population Threshold:** Filtered to municipalities with $$\ge 65,000$$ residents to ensure single-year ACS Census sampling stability.
* **Core Study Scope:** 61 qualifying FHWA-registered Vision Zero cities across 6 years (7,538 fatal pedestrian crash records).

---

## Data Pipeline Architecture & Engineering

### 1. Ingestion & Transformation
* **Crash Records:** Ingested pedestrian crash endpoints via the NHTSA FARS API into normalized relational incident tables ($$N = 7,538$$).
* **Population Normalization:** Extracted annual city populations from the US Census ACS 1-Year API. Imputed missing 2020 pandemic census data using linear interpolation between 2019 and 2021 baselines.
* **Meteorological Context:** Extracted daily weather observations ($$N = 133,651$$) from Open-Meteo and resolved city center-to-station mapping via nearest-neighbor spatial joins on Census Gazetteer coordinates.

### 2. Database Schema & Storage
The staging and production tables were designed in a star-schema structure hosted in a cloud PostgreSQL instance on Railway, optimized for analytical view generation.
<p align="center">
  <img src="/images/db_schema_pedestrian_fatalities_prj.png" alt="Database Entity Relationship Diagram" width="700" />
</p>
---

## Dashboard Breakdown

### 1. High-Level Metrics & Geographic Overview
<p align="center">
  <img src="/images/pedestrian_fatalities_dashboard_1.png" alt="Pedestrian Fatalities Dashboard 1" width="700" />
</p>

* **Core Views:** Top-level summary KPIs (total fatalities, per-capita rates), national map visualization highlighting city-by-city fatality distribution, and multi-variable filtering controls (year, city, speed limit).
* **Key Finding:** Highlights stark regional disparities across participating Vision Zero cities, with elevated per-capita fatality rates concentrated in the Sunbelt and Southern urban corridors.

### 2. Temporal & Environmental Conditions
<p align="center">
  <img src="/images/pedestrian_fatalities_dashboard_2.png" alt="Pedestrian Fatalities Dashboard 2" width="700" />
</p>

* **Core Views:** Crash breakdowns by lighting condition (daylight vs. dark/unlit), time of day, day of week, and daily weather parameters.
* **Key Finding:** Fatalities concentrate disproportionately during nighttime hours (peaking at 9:00 PM) and drop significantly during daylight hours (7:00 AM–3:00 PM), underscoring ambient visibility and street lighting infrastructure as critical risk factors.

### 3. City-by-City Benchmarking & Demographics
<p align="center">
  <img src="/images/pedestrian_fatalities_dashboard_3.png" alt="Pedestrian Fatalities Dashboard 3" width="700" />
</p>

* **Core Views:** Per-capita fatality rankings across all 61 analyzed Vision Zero cities normalized against ACS Census population data.
* **Key Finding:** Absolute crash counts misstate risk without per-capita normalization; several mid-sized Southern cities face substantially higher per-capita fatality rates than larger, transit-dense metros like New York or San Francisco.

### 4. Roadway Infrastructure & Speed Analysis
<p align="center">
  <img src="/images/pedestrian_fatalities_dashboard_4.png" alt="Pedestrian Fatalities Dashboard 4" width="700" />
</p>

* **Core Views:** Cross-analysis of posted speed limits, roadway functional classification, intersection proximity, and collision outcomes.
* **Key Finding:** Fatalities cluster along high-crash networks of multi-lane arterial corridors, illustrating that high vehicle speeds on arterial streets are the primary driver of fatal outcomes.