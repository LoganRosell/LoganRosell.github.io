---
title: "How Anxiety and Depression Levels Differed Between US States and Regions Following COVID-19"
excerpt: "An exploratory data analysis and longitudinal modeling project evaluating state- and regional-level mental health trends during the pandemic using CDC Household Pulse Survey data. Utilizing Python, Plotly, and scikit-learn, the project tracks biweekly symptom prevalence across 50 states to identify regional disparities and evaluate temporal fluctuations against major national events.<br/><img src='/images/regional_variations_post_covid_plots/fig_1_trends_over_time.png' width='500'>"
collection: portfolio
use_math: true
katex: true
---

## Overview
* **Authors:** Logan Rosell, Ian Walsh (Willamette University, School of Computing & Information Sciences)
* **Course:** DATA 501: Data Science With Python
* **Date:** August 19, 2026
* **Data Source:** CDC / NCHS Household Pulse Survey (PHQ-4 Mental Health Symptoms, biweekly from April 2020 to September 2023 across 50 states and DC)
* **Stack:** Python (`pandas`, `numpy`, `matplotlib`, `seaborn`, `plotly`, `scipy`, `scikit-learn`, `statsmodels`)

---

## Executive Summary & Outcomes

This project investigates how reported symptom rates of anxiety and depressive disorders varied across US states and Census regions following the onset of the COVID-19 pandemic. Using biweekly Household Pulse Survey data based on the four-item Patient Health Questionnaire (PHQ-4), we built data cleaning and transformation pipelines in Python, mapped regional shifts using animated Plotly choropleth maps, and evaluated temporal and regional linear models. Deliverables included an interactive Quarto report and a slide presentation.

### Key Outcomes:
* **Persistent Regional Disparities:** The South consistently reported the highest rates of anxiety and depressive symptoms throughout the study period, whereas the Midwest and Northeast maintained noticeably lower baseline symptom rates.
* **Prevalence Hierarchy:** Across all measured time periods and regions, symptoms of anxiety disorder were consistently more prevalent among state populations than symptoms of depressive disorder.
* **Event-Correlated Peaks:** Mental health symptoms peaked sharply nationwide during major public health and political milestones—most notably around the November 2020 general election and winter surge, followed by a trough in May 2021 as vaccines rolled out, and a secondary peak in September 2022.
* **Modeling Limitations:** Simple linear models across time explained only ~19% of the overall variation ($$R^2 \approx 0.19$$), demonstrating that pandemic mental health dynamics followed distinct non-linear waves rather than a steady linear decline.

<div style="text-align: center;">
<img src="/images/regional_variations_post_covid_plots/fig_1_trends_over_time.png" alt="Figure 1: Anxiety and Depression Trends Over Time with Events" width="800">
<p><em>Figure 1: Percentage of the population with anxiety or depression over time, annotated with major national events.</em></p>
</div>

---

## Research Focus
* **Research Question:** How did anxiety and depression levels differ between states and regions following the outbreak of COVID-19 in the United States?
* **Core Metrics:** 
  * **Symptom Prevalence:** Percentage of adult population reporting symptoms of anxiety disorder, depressive disorder, or either disorder occurring more than half the days or nearly every day during the past 7 days.
  * **Clinical Instrument:** CDC / Census implementation of the standard 4-item Patient Health Questionnaire (PHQ-4).
  * **Geographic Scope:** 50 states and the District of Columbia categorized into four standard US Census Regions (Northeast, Midwest, South, West).

---

## Data Transformation & Exploratory Analysis

### 1. Data Pipeline & Metric Definitions
The raw CDC dataset was filtered to state-level observations and reshaped from long to wide format to structure parallel time-series indicators for each state and Census region:

* **Anxiety Symptoms:** Based on PHQ-4 questions assessing feeling nervous, anxious, or on edge, and inability to control worrying.
* **Depression Symptoms:** Based on PHQ-4 questions assessing anhedonia (little interest or pleasure) and feeling down, depressed, or hopeless.
* **Combined Indicator:** Population percentage exhibiting symptoms of either anxiety or depressive disorders.

### 2. Longitudinal National Trends
Tracking national averages across 60+ biweekly survey phases revealed clear non-linear fluctuations coinciding with national milestones:

* **Initial Surge & 2020 Peak:** Symptom rates rose steeply through 2020, reaching an initial peak near the November 2020 election (~42% reporting combined symptoms).
* **Post-Vaccine Decline:** Rates dropped to their lowest point in May 2021 (~28% combined symptoms) following widespread vaccine availability.
* **Plateau & Secondary Waves:** Rates rebounded to a secondary peak in late 2022 (~36% combined) before stabilizing at elevated baselines compared to pre-pandemic estimates.

---

## Regional Modeling & Statistical Findings

### 1. Regional Comparisons
Across all Census regions, symptom rates moved in parallel temporal waves, but with distinct baseline separations:
* **South & West:** Exhibited the highest average symptom levels across all three indicators (depression, anxiety, and combined).
* **Midwest & Northeast:** Displayed the lowest symptom rates, with Midwestern states demonstrating greater stability during national surge periods.

<div style="text-align: center;">
<img src="/images/regional_variations_post_covid_plots/fig_4_linear_reg_by_region.png" alt="Figure 4: Regional Regression of Symptoms Over Time" width="800">
<p><em>Figure 4: Regression of the percentage of symptoms of depression or anxiety over time, faceted by US Census Region.</em></p>
</div>

### 2. Linear Regression Diagnostics
* **Anxiety vs. Depression Correlation:** A simple linear model evaluating state-level anxiety rates as a function of depression rates revealed a strong linear association ($$R^2 > 0.85$$), confirming that regional stressors impacted both conditions in tandem.
* **Temporal Model Fit:** Regressing symptom prevalence against linear time yielded an $$R^2$$ of approximately 0.19. Residual diagnostics and prediction error displays confirmed significant non-linear curvature caused by event-driven surges and seasonality.


[View Full Report (HTML)]({{ site.baseurl }}/project_reports/Ian%20and%20Logan%20Final%20Project.html)