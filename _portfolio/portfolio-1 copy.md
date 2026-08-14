---
title: "Regional and Temporal Variation in Coal Power's Emission Intensity: A Global Analysis (2000–2024)"
excerpt: "A global statistical analysis evaluating whether the carbon intensity (mtCO2/TWh) of coal-fired electricity generation has varied across regions and over time. Utilizing Ember energy data and non-parametric tests alongside regional linear models in R, the project demonstrates that while regional differences and modest efficiency gains exist (notably in Asia and Oceania), phasing out coal capacity remains the only effective mechanism for substantial emissions reductions.<br/><img src='/images/dist_co2_per_twh_coal_power.png' width='500'>"
collection: portfolio
use_math: true
mathjax: true
---


## Overview
* **Author:** Logan Rosell (Willamette University, School of Computing & Information Sciences)
* **Course:** DATA 501: Data Science With R
* **Date:** December 10, 2025
* **Data Source:** Ember Yearly Electricity Data (2000–2024, covering 215 countries/economies)
* **Stack:** R (`tidyverse`, `ggplot2`, `car`, `lawstat`, `gtsummary`, `gt`, `sf`, `rnaturalearth`, `skimr`, `performance`, `rsq`)

---

## Research Focus
* **Research Question:** Does coal power’s CO2 emission intensity vary across global regions and time between 2000 and 2024?
* **Core Metric:** Carbon Intensity (mtCO2 per TWh), calculated as million metric tons of CO2 produced per terawatt-hour of electricity generated from coal.
* **Hypotheses Tested:**
  * **Question 1 (Regional Variation):**
    * $H_{0A}$: Coal power CO2 emission intensity does not vary between global regions.
    * $H_{1A}$: Coal power CO2 emission intensity does vary between global regions.
  * **Question 2 (Temporal Trends):**
    * $H_{0B}$: Coal power CO2 emission intensity does not vary over time between global regions.
    * $H_{1B}$: Coal power CO2 emission intensity does vary over time between global regions.

---

## Exploratory Data Analysis & Statistical Testing

### 1. Assumption Checking
* **Normality:** A Shapiro-Wilk test on overall regional intensity yielded $W = 0.87914$ ($p = 1.117 \times 10^{-10}$), rejecting the null hypothesis of normality.
* **Equal Variance:** A Brown-Forsythe test yielded a test statistic of $36.197$ ($p < 2.2 \times 10^{-16}$), rejecting the null of equal variance across regions.

### 2. Hypothesis A: Non-Parametric Regional Comparison
* **Kruskal-Wallis Test:** $\chi^2 = 167.33$, $\text{df} = 6$, $p < 2.2 \times 10^{-16}$. The null hypothesis was rejected, confirming statistically significant differences in median emission intensities across regions.
* **Pairwise Comparisons:** Pairwise Wilcoxon rank-sum exact tests demonstrated that all regional pairings exhibited statistically significant differences ($p < 0.01$).

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
* **Formula:** $\text{mtCO}_2\text{ per TWh}_i = \beta_0 + \beta_1 \text{Year}_i + \sum \alpha_j \text{Area}_{ji} + \sum \gamma_j (\text{Year}_i \times \text{Area}_{ji})$
* **Diagnostics:** Despite an adjusted $R^2 = 0.998$ and statistically significant coefficients, the global model exhibited severe multicollinearity ($\text{GVIF} > 10$) and violated linearity, normality, and homoscedasticity assumptions.

### 2. Region-Specific Linear Models

$$
\begin{aligned}
\text{\textbf{Asia:}} \quad &\widehat{\text{mtCO}}_2\text{ per TWh}_i = 4.30 - 0.00168(\text{Year}_i) \quad (R^2 = 0.9358, \; p = 2.02 \times 10^{-15}) \\
\text{\textbf{Oceania:}} \quad &\widehat{\text{mtCO}}_2\text{ per TWh}_i = 1.58 - 0.000347(\text{Year}_i) \quad (R^2 = 0.8610, \; p = 1.52 \times 10^{-11}) \\
\text{\textbf{Latin America:}} \quad &\widehat{\text{mtCO}}_2\text{ per TWh}_i = 0.381 + 0.000251(\text{Year}_i) \quad (R^2 = 0.3138, \; p = 0.00212) \\
\text{\textbf{Europe:}} \quad &\widehat{\text{mtCO}}_2\text{ per TWh}_i = 1.07 - 0.0000469(\text{Year}_i) \quad (R^2 = 0.0310, \; p = 0.1965)
\end{aligned}
$$

* **Asia & Oceania:** Displayed stable linear decreases in carbon intensity over time.
* **Europe & Latin America:** Displayed weak linear fit and non-linear patterns across smaller multi-year intervals.
* **Africa, Middle East, & North America:** Failed standard linear regression normality assumptions.

---

## Practical Impact & Takeaways
* **Regional Disparities:** Median coal emission intensity varies significantly by region, ranging from 0.858 mtCO2/TWh in Africa to 0.976 mtCO2/TWh in Europe.
* **Scale of Efficiency Gains in 2024:**
  * **Asia:** Generated 8,738 TWh from coal in 2024, saving an expected 14.68 mtCO2 due to yearly intensity improvements (equivalent to the annual footprint of $\approx 978,666$ Americans).
  * **Oceania:** Generated 130 TWh from coal in 2024, saving an expected 0.045 mtCO2 (equivalent to $\approx 3,000$ Americans).
* **The Bottom Line:** These efficiency savings accounted for only **0.18%** of Asia's and **0.039%** of Oceania's 2024 coal power emissions. Incremental gains in plant efficiency and fuel quality are negligible compared to total output; phasing out coal generation remains the primary driver for lowering power sector emissions.
