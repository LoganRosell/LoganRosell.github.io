---
title: "The Local Economic Impact of Data Centers: County-Level Measures in the Contiguous United States"
excerpt: "An econometric and machine learning capstone evaluating how data center deployment relates to county-level employment, wages, air quality (PM2.5), and industrial patterns across the contiguous U.S. (2007–2022). Combines staggered Difference-in-Differences, spatial 50-mile buffer spillovers, PCA clustering, and Conditional Variational Autoencoders (CVAEs) using public federal statistics (NAICS 518).<br/><img src='/images/local_econ_impact_of_data_centers/did_clusters_poster.png' width='500'>"
collection: portfolio
permalink: /portfolio/local_econ_impact_of_data_centers/
use_math: true
katex: true
---

## Overview
* **Authors:** Logan Rosell, Ian Walsh (Willamette University, School of Computing & Information Sciences)
* **Course:** DATA 510: Data Science Capstone
* **Date:** Summer 2026
* **Data Sources:** US Census Bureau (County Business Patterns NAICS 518 & Building Permits Survey), Bureau of Economic Analysis (BEA - Personal Income & GDP), Bureau of Labor Statistics (BLS - QCEW & LAUS Employment), EPA (AQS $\text{PM}_{2.5}$ Air Quality), CDC
* **Stack:** Python (`pandas`, `numpy`, `scikit-learn`, `PyTorch`, `statsmodels`, `geopandas`, `shapely`, `SQLAlchemy`, `matplotlib`, `seaborn`, `Quarto`)
* **Project Source Files:**
  * [Capstone Technical Write-Up Website](https://loganrosell.github.io/local-economic-impacts-of-data-centers/)
  * [Technical Write-Up Repository](https://github.com/LoganRosell/local-economic-impacts-of-data-centers)
  * [Project Data & Modeling Workbook Repository](https://github.com/LoganRosell/local-economic-impact-of-data-centers-workbook)
  * [Download Capstone Research Poster (PDF)](/images/local_econ_impact_of_data_centers/capstone_poster.pdf)

---

## Executive Summary & Outcomes

The rapid expansion of computing infrastructure—accelerated by cloud computing and the recent proliferation of generative AI and Large Language Models (LLMs)—has triggered intense debate over the local economic and environmental footprints of data centers. While municipal governments frequently offer aggressive tax incentives to attract data center developments, empirical research on their localized impacts has been constrained by expensive, proprietary data aggregators that obscure historical entry timing and facility locations.

This capstone project overcomes that bottleneck by developing an open-source, reproducible time-series data pipeline tracking federal statistics under NAICS 518 (Computing Infrastructure Providers, Data Processing, Web Hosting, and Related Services) across all **3,100+ contiguous U.S. counties from 2007 to 2022**. Combining unsupervised machine learning (PCA and K-Means), spatial exposure buffer modeling (50-mile radii), staggered Difference-in-Differences (DiD) event-study regressions, and Conditional Variational Autoencoder (CVAE) counterfactual simulations, we evaluate how data center growth affects local labor markets, personal income, housing permits, and fine particulate matter ($\text{PM}_{2.5}$).

<p align="center">
  <img src="/images/local_econ_impact_of_data_centers/did_clusters_poster.png" alt="Difference-in-Differences Event Study Estimates Across County Clusters" width="750" />
</p>

### Key Outcomes:
* **The Urban Clustering vs. Rural Impact Paradox:** Unsupervised clustering reveals that data centers congregate predominantly in populous, metropolitan counties (Cluster 1 averages $\approx 10$ facilities vs. $\approx 1$ in Cluster 0), refuting the common assumption that data centers are primarily built in remote areas. However, **statistically significant economic impacts are concentrated almost exclusively in rural counties (Cluster 0)**.
* **Regional Employment Spillovers:** Counties with substantial regional exposure ($\ge 112$ data centers within a 50-mile radius) experienced persistent, statistically significant **declines in local unemployment rates** over time, demonstrating that economic benefits spill across county borders.
* **Air Quality Improvements ($\text{PM}_{2.5}$):** Local data center deployment ($\ge 3$ establishments per county) is associated with noticeable, multi-year **reductions in ambient fine particulate matter ($\text{PM}_{2.5}$)** in rural counties, likely capturing industrial zoning transitions away from legacy heavy manufacturing toward modern, electrified computing facilities.
* **Wages & Sectoral Restructuring Trade-offs:** In-county data center concentration correlated with **lower average per-capita personal income** and contractions in trade, transportation, and traditional industrial employment, reflecting capital-intensive automation with relatively low on-site operational headcounts.
* **Policy Implications:** Data centers generate real, measurable local trade-offs rather than uniform economic gains. Policymakers must weigh the benefits of construction activity and regional employment against potential wage compression and industrial displacement.

---

## Research Focus & Problem Framing

* **Core Research Question:** How does the deployment and regional clustering of new data centers relate to county-level employment, wages, environmental quality ($\text{PM}_{2.5}$), and business development across the contiguous United States?
* **Core Metric Definitions:**
  * **Direct In-County Exposure:** Number of NAICS 518 establishments operating within a county in a given year (partitioned into threshold tiers, e.g., $\ge 3$ establishments).
  * **Regional Spatial Exposure:** Inverse-distance and area-weighted counts of NAICS 518 establishments located within a 50-mile radius of the county centroid (e.g., $\ge 112$ regional establishments).
  * **Socio-Economic & Environmental Outcomes:** County unemployment rate, log per-capita personal income, single-family building permits, sector-specific employment shares, and annual mean $\text{PM}_{2.5}$ particulate concentrations ($\mu\text{g}/\text{m}^3$).
* **Methodological Scope:** Contiguous U.S. counties (3,108 counties) observed annually across a 16-year panel (2007–2022).

---

## Data Pipeline & Spatial Exposure Architecture

### 1. Multi-Source Federal Ingestion
To ensure complete open-source reproducibility, we synthesized diverse federal statistical endpoints into a normalized relational database:
* **Infrastructure & Business Patterns:** U.S. Census County Business Patterns (CBP) extracting NAICS 518 establishment counts, payroll, and employee sizes.
* **Labor Market Dynamics:** Bureau of Labor Statistics (BLS) Quarterly Census of Employment and Wages (QCEW) and Local Area Unemployment Statistics (LAUS).
* **Macroeconomics & Income:** Bureau of Economic Analysis (BEA) regional accounts for county GDP and personal income per capita.
* **Environmental Quality:** EPA Air Quality System (AQS) monitor-level daily measurements aggregated to annual county-level $\text{PM}_{2.5}$ means.
* **Housing Activity:** U.S. Census Building Permits Survey capturing annual single- and multi-family residential construction permits.

<p align="center">
  <img src="/images/local_econ_impact_of_data_centers/naics_518_establishments_trend.png" alt="Growth Trend of NAICS 518 Establishments Over Time" width="650" />
</p>

### 2. Spatial Buffer & Spillover Construction
Data centers exert influence beyond artificial administrative boundaries through commuting patterns and regional supply chains. We engineered a spatial exposure metric by projecting 50-mile geodesic buffers around each county centroid and computing area-overlap weighted aggregations of neighboring NAICS 518 establishments.

<p align="center">
  <img src="/images/local_econ_impact_of_data_centers/map_dcs_2022.png" alt="Geographic Distribution of Data Centers Across US Counties (2022)" width="750" />
</p>

---

## Unsupervised Structural Clustering (PCA & K-Means)

To account for profound structural heterogeneity across U.S. counties without introducing circular bias from data center counts, we applied **Principal Component Analysis (PCA)** and **K-Means clustering** strictly on physical county characteristics (total population, land area, and water area).

<p align="center">
  <img src="/images/local_econ_impact_of_data_centers/pca_datacenter_plot.png" alt="PCA Projection of Counties Colored by Log Data Center Count" width="650" />
</p>

* **Cluster 0 (Rural / Lower-Density Counties):** Comprises lower-population counties with larger land areas. Average in-county data center count is $\approx 1$.
* **Cluster 1 (Urban / Metropolitan Counties):** Comprises dense, highly populated metropolitan counties. Average in-county data center count is $\approx 10$.
* **Validation:** Projecting log data center counts onto the PCA space reveals a smooth diagonal gradient—confirming that data centers organically track urban infrastructure and population density even when the unsupervised model has zero direct knowledge of data center locations.

<p align="center">
  <img src="/images/local_econ_impact_of_data_centers/clusters.png" alt="K-Means Cluster Partitioning Across the United States" width="700" />
</p>

---

## Econometric Modeling: Difference-in-Differences

We implemented staggered, two-way fixed effects Difference-in-Differences (DiD) event-study specifications to evaluate dynamic treatment trajectories five years before and five years after data center adoption, rigorously validating the **parallel trends assumption** ($t < 0$).

$$
Y_{it} = \alpha_i + \lambda_t + \sum_{k=-5}^{5} \beta_k D_{i, t-k} + \varepsilon_{it}
$$

Where $Y_{it}$ represents the county outcome, $\alpha_i$ is the county fixed effect, $\lambda_t$ is the year fixed effect, and $D_{i, t-k}$ captures leads and lags relative to the treatment year.

### Event-Study Findings by Outcome:

1. **Personal Income:**
   * In rural counties (Cluster 0), adding $\ge 3$ data centers leads to a statistically significant, multi-year decline in log personal income per capita.
   * Urban counties (Cluster 1) exhibit no statistically significant income effect, as high baseline economic diversity absorbs facility-level shifts.

2. **Ambient Air Pollution ($\text{PM}_{2.5}$):**
   * In rural counties, local data center additions correlate with a consistent post-treatment drop in $\text{PM}_{2.5}$ levels, supporting the hypothesis that data center developments frequently replace or preempt dirtier heavy industrial uses.

3. **Local Unemployment Rates:**
   * High regional data center concentration ($\ge 112$ within 50 miles) produces a steady, statistically significant reduction in county unemployment rates in rural areas over time.

---

## Machine Learning Counterfactuals (Conditional VAE)

To complement quasi-experimental econometric models, we designed a deep **Conditional Variational Autoencoder (CVAE)** in PyTorch to explore non-linear counterfactual county trajectories:
* **Architecture:** Symmetric encoder-decoder network `[136 $\rightarrow$ 128 $\rightarrow$ 16 $\rightarrow$ 8 $\rightarrow$ 16 $\rightarrow$ 128 $\rightarrow$ 136]`.
* **Conditioning Vectors:** Year embeddings (`[16, 8]`) and log data center count tier embeddings (`[6, 4]`).
* **Counterfactual Perturbation:** By holding latent socio-economic vectors constant while perturbing the data center conditioning embedding, the decoder reconstructs a synthetic "parallel universe" estimate for an identical county under altered infrastructure scenarios.
* **Methodological Takeaway:** While the CVAE successfully learned high-dimensional structural relationships across 136 multi-sector features, the quasi-experimental DiD framework proved superior for interpretable causal policy analysis.

---

## Discussion, Limitations & Real-World Impact

### Methodological Limitations:
* **NAICS 518 Aggregation:** While NAICS 518 provides an open, nationwide benchmark, it encompasses general web hosting and data processing services alongside hyperscale facilities.
* **Spatial Buffer Simplifications:** 50-mile centroid buffers assume isotropic commuting corridors, which may be less precise in geographically vast Western counties with heterogeneous topography.

### Practical Policy Takeaways:
* **Targeted Regional Planning:** Economic gains from data centers (unemployment reduction, construction activity) operate regionally, while specific trade-offs (income shifts, power and land demands) concentrate locally.
* **Informed Incentive Structuring:** Municipalities considering aggressive tax abatements must balance short-term construction windfalls against long-term operational headcount realities.

---

[View Full Technical Write-Up Website](https://loganrosell.github.io/local-economic-impacts-of-data-centers/) | [Download Capstone Poster (PDF)](/images/local_econ_impact_of_data_centers/capstone_poster.pdf)


