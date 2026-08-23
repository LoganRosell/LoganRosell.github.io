---
title: "Regional and Temporal Variation in Coal Power's Emission Intensity: A Global Analysis (2000–2024)"
excerpt: "A global statistical analysis evaluating whether the carbon intensity (mtCO2/TWh) of coal-fired electricity generation has varied across regions and over time. Utilizing Ember energy data and non-parametric tests alongside regional linear models in R, the project demonstrates that while regional differences and modest efficiency gains exist (notably in Asia and Oceania), phasing out coal capacity remains the only effective mechanism for substantial emissions reductions.<br/><img src='/images/dist_co2_per_twh_coal_power.png' width='500'>"
collection: portfolio
use_math: true
katex: true
---

## Overview
* **Author:** Logan Rosell (Willamette University, School of Computing & Information Sciences)
* **Course:** DATA 501: Data Science With R
* **Date:** December 10, 2025
* **Data Source:** Ember Yearly Electricity Data (2000–2024, covering 215 countries/economies)
* **Stack:** R (`tidyverse`, `ggplot2`, `car`, `lawstat`, `gtsummary`, `gt`, `sf`, `rnaturalearth`, `skimr`)

---

## Executive Summary & Outcomes

This project evaluates whether the carbon intensity (mtCO2 per TWh) of coal-fired electricity generation varies across global regions and over a 25-year period (2000–2024). Using historical generation, demand, and emissions data from Ember, I conducted exploratory data analysis, checked parametric regression assumptions, ran non-parametric hypothesis tests, and modeled regional linear trends over time. The primary deliverables were a research paper and slide presentation.

### Key Outcomes:
* **Statistically Significant Regional Disparities:** Median emissions intensity differed across all regional pairings ($$p < 0.01$$), ranging from 0.858 mtCO2/TWh in Africa to 0.976 mtCO2/TWh in Europe.
* **Isolated Temporal Declines:** Asia ($$R^2 = 0.936$$) and Oceania ($$R^2 = 0.861$$) were the only regions exhibiting consistent, statistically significant declines in carbon intensity over time.
* **Policy Implication:** In 2024, efficiency improvements avoided 14.68 mtCO2 in Asia and 0.045 mtCO2 in Oceania—representing only 0.18% and 0.039% of their respective coal power emissions. Incremental plant-level efficiency gains are negligible compared to gross generation volume; phasing out coal capacity remains the primary driver for lowering power sector emissions.

---

## Research Focus
* **Research Question:** Does coal power’s CO2 emission intensity vary across global regions and time between 2000 and 2024?
* **Core Metric:** Carbon Intensity (mtCO2 per TWh), calculated as million metric tons of CO2 produced per terawatt-hour of electricity generated from coal.
* **Hypotheses Tested:**
  * **Question 1 (Regional Variation):**
    * $$H_{0A}$$: Coal power CO2 emission intensity does not vary between global regions.
    * $$H_{1A}$$: Coal power CO2 emission intensity does vary between global regions.
  * **Question 2 (Temporal Trends):**
    * $$H_{0B}$$: Coal power CO2 emission intensity does not vary over time between global regions.
    * $$H_{1B}$$: Coal power CO2 emission intensity does vary over time between global regions.

---

## Exploratory Data Analysis & Statistical Testing

### 1. Regional Distribution & Assumption Checking
Initial visualization of the regional distributions showed visible separation across median intensity values, alongside very tight clustering within most regions.

<p align="center">
  <img src="/images/dist_co2_per_twh_coal_power.png" alt="Distribution of CO2 per TWh from Coal Power" width="700" />
</p>

* **Normality:** A Shapiro-Wilk test on overall regional intensity yielded $$W = 0.87914$$ ($$p = 1.117 \times 10^{-10}$$), rejecting the null hypothesis of normality.
* **Equal Variance:** A Brown-Forsythe test yielded a test statistic of $$36.197$$ ($$p < 2.2 \times 10^{-16}$$), rejecting the null of equal variance across regions.

### 2. Hypothesis A: Non-Parametric Regional Comparison
Because the data lacked both normality and equal variance, I used the Kruskal-Wallis test and follow-up pairwise Wilcoxon rank-sum tests:
* **Kruskal-Wallis Test:** $$\chi^2 = 167.33$$, $$\text{df} = 6$$, $$p < 2.2 \times 10^{-16}$$, rejecting the null hypothesis that distributions and medians are identical across regions.
* **Pairwise Comparisons:** Pairwise Wilcoxon rank-sum exact tests showed that every regional pairing exhibited statistically significant differences ($$p < 0.01$$).

| Region | N | Mean (mtCO2/TWh) | Median (mtCO2/TWh) | SD | Min | Max |
| :--- | :---: | :---: | :---: | :---: | :---: | :---: |
| **Africa** | 25 | 0.855 | 0.858 | 0.005 | 0.846 | 0.858 |
| **Asia** | 25 | 0.917 | 0.918 | 0.013 | 0.896 | 0.934 |
| **Europe** | 25 | 0.976 | 0.976 | 0.001 | 0.974 | 0.978 |
| **Latin America & Caribbean** | 25 | 0.885 | 0.886 | 0.003 | 0.879 | 0.891 |
| **Middle East** | 25 | 0.859 | 0.859 | 0.002 | 0.851 | 0.861 |
| **North America** | 25 | 0.948 | 0.949 | 0.001 | 0.946 | 0.950 |
| **Oceania** | 25 | 0.883 | 0.883 | 0.003 | 0.880 | 0.890 |

---

## Modeling Emission Intensity Over Time

### 1. Global Interaction Model
* **Formula:** $$\text{mtCO}_2\text{ per TWh}_i = 1.87 - 0.505(\text{Year}_i) + \sum \alpha_j \text{Area}_{ji} + \sum \gamma_j (\text{Year}_i \times \text{Area}_{ji})$$
* **Diagnostics:** Despite an adjusted $$R^2 = 0.998$$ and statistically significant coefficients, the global model exhibited severe multicollinearity ($$\text{GVIF} > 10$$) and failed linear regression assumptions (linearity, normality, and homoscedasticity).

### 2. Region-Specific Linear Models

<p align="center">
  <img src="/images/models_of_reasonable_fit_coal_power_prj.png" alt="Models of Reasonable Fit" width="700" />
</p>

$$
\begin{aligned}
\text{\textbf{Asia:}} \quad &\widehat{\text{mtCO}}_2\text{ per TWh}_i = 4.30 - 0.00168(\text{Year}_i) \quad (R^2 = 0.9358, \; p = 2.02 \times 10^{-15}) \\
\text{\textbf{Oceania:}} \quad &\widehat{\text{mtCO}}_2\text{ per TWh}_i = 1.58 - 0.000347(\text{Year}_i) \quad (R^2 = 0.8610, \; p = 1.52 \times 10^{-11}) \\
\text{\textbf{Latin America:}} \quad &\widehat{\text{mtCO}}_2\text{ per TWh}_i = 0.381 + 0.000251(\text{Year}_i) \quad (R^2 = 0.3138, \; p = 0.00212) \\
\text{\textbf{Europe:}} \quad &\widehat{\text{mtCO}}_2\text{ per TWh}_i = 1.07 - 0.0000469(\text{Year}_i) \quad (R^2 = 0.0310, \; p = 0.1965)
\end{aligned}
$$

* **Asia & Oceania:** Demonstrated stable linear decreases in carbon intensity over time with strong model fits ($$R^2 = 0.94$$ and $$0.86$$, respectively).
* **Europe & Latin America:** Displayed weak linear fits and non-linear fluctuations across shorter multi-year intervals, resulting in wider uncertainty intervals.
* **Africa, Middle East, & North America:** Failed standard linear regression normality assumptions ($$p < 0.01$$).

### 3. Model Diagnostics

#### Asia
The data is nearly normally distributed, linear with minor departures prior to 2007, and roughly homoscedastic.

<p align="center">
  <img src="/images/asia_diagnostic_plots_coal_power_prj.png" alt="Asia Diagnostic Plots" width="650" />
</p>

#### Oceania
The data satisfies normality assumptions, but exhibits non-linear curvature and unequal variance across the time series.

<p align="center">
  <img src="/images/oceania_diagnostic_plots_coal_power_prj.png" alt="Oceania Diagnostic Plots" width="650" />
</p>

#### Europe
The regional series passes normality testing ($$p = 0.395$$), but shows clear non-linear patterning and poor linearity fit.

<p align="center">
  <img src="/images/europe_diagnostic_plots_coal_power_prj.png" alt="Europe Diagnostic Plots" width="650" />
</p>

#### Latin America & Caribbean
The data is close to normally distributed ($$p = 0.395$$), but exhibits moderate departures from linearity and equal variance.

<p align="center">
  <img src="/images/latin_america_diagnostic_plots_coal_power_prj.png" alt="Latin America Diagnostic Plots" width="650" />
</p>

---

## Discussion & Real-World Impact
* **Scale of 2024 Intensity Improvements:**
  * **Asia:** Generated 8,738 TWh from coal in 2024, saving an estimated 14.68 mtCO2 due to efficiency improvements (equivalent to the annual footprint of $$\approx 978,666$$ Americans).
  * **Oceania:** Generated 130 TWh from coal in 2024, saving an estimated 0.045 mtCO2 (equivalent to $$\approx 3,000$$ Americans).
* **In Sum:** These savings accounted for only **0.18%** of Asia's and **0.039%** of Oceania's 2024 coal emissions. Because incremental efficiency gains are vastly overshadowed by generation volume, reducing coal capacity remains the only effective mechanism for significant emissions cuts.