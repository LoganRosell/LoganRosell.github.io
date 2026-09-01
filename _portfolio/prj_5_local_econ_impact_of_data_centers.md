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
* **Data Sources:** US Census Bureau (County Business Patterns NAICS 518 & Building Permits Survey), Bureau of Economic Analysis (BEA - Personal Income & GDP), Bureau of Labor Statistics (BLS - QCEW & LAUS Employment), EPA (AQS $$\text{PM}_{2.5}$$ Air Quality), CDC
* **Stack:** Python (`pandas`, `numpy`, `scikit-learn`, `PyTorch`, `statsmodels`, `geopandas`, `shapely`, `SQLAlchemy`, `matplotlib`, `seaborn`, `Quarto`)
* **Project Source Files:**
  * [Capstone Technical Write-Up Website](https://loganrosell.github.io/local-economic-impacts-of-data-centers/)
  * [Technical Write-Up Repository](https://github.com/LoganRosell/local-economic-impacts-of-data-centers)
  * [Project Data & Modeling Workbook Repository](https://github.com/LoganRosell/local-economic-impact-of-data-centers-workbook)
  * [Download Capstone Research Poster (PDF)](/images/local_econ_impact_of_data_centers/capstone_poster.pdf)

---

## Executive Summary & Outcomes

The recent expansion of data centers, accelerated by cloud computing and generative AI, has raised questions about their local economic and environmental footprints. While public discourse often focuses on power and water demands, their broader economic effects on surrounding communities have received less attention. Empirical research has also been hindered by proprietary datasets that restrict historical entry timing and facility locations.

This capstone project addresses that gap by building an open-source time-series pipeline using public federal data under NAICS 518 (Computing Infrastructure Providers, Data Processing, Web Hosting, and Related Services) across all contiguous U.S. counties from 2007 to 2022. We evaluate local economic effects by combining spatial exposure measures (50-mile buffers), unsupervised clustering (PCA and K-Means), staggered Difference-in-Differences (DiD) event-study regressions, and Conditional Variational Autoencoder (CVAE) counterfactual models.

<p align="center">
  <img src="/images/local_econ_impact_of_data_centers/did_clusters_poster.png" alt="Difference-in-Differences Event Study Estimates Across County Clusters" width="750" />
</p>

### Key Outcomes:
* Urban vs. Rural Distribution: Data centers are primarily concentrated in more populous, urban counties (Cluster 1 averages $$\approx 10$$ facilities versus $$\approx 1$$ in Cluster 0), which challenges the common idea that they are mainly built in remote rural areas. However, statistically significant Difference-in-Differences effects were concentrated in rural counties (Cluster 0), where smaller local economies are more sensitive to single facility additions.
* Regional Employment Spillovers: Counties with higher regional data center exposure ($$\ge 112$$ establishments within a 50-mile buffer) experienced steady, statistically significant reductions in local unemployment rates over time.
* Air Quality Measures ($$\text{PM}_{2.5}$$): In rural counties, the addition of $$\ge 3$$ in-county data centers correlated with modest, multi-year decreases in ambient $$\text{PM}_{2.5}$$ levels, which may reflect local transitions from legacy industrial land use toward electrified commercial infrastructure.
* Income and Employment Shifts: In-county data center additions were associated with lower average per-capita personal income and declines in traditional trade, transportation, and industrial employment shares, reflecting capital-intensive operations with limited on-site staffing requirements.
* Policy Takeaway: The findings point to local trade-offs rather than uniform economic benefits or downsides. Regional planners and policymakers need to weigh short-term construction activity and regional job gains against potential wage compression and industrial shifts.

---

## Research Focus

* Core Research Question: How does the deployment of a new data center relate to local economic conditions, including employment, income, environmental quality ($$\text{PM}_{2.5}$$), and business development patterns at the county level?
* Treatment & Exposure Definitions:
  * In-County Exposure: Number of NAICS 518 establishments operating within a county in a given year ($$\ge 3$$ establishments).
  * Regional Spatial Exposure: Inverse-distance and area-weighted counts of NAICS 518 establishments located within a 50-mile radius ($$\ge 112$$ nearby establishments).
  * Outcome Indicators: County unemployment rate, log per-capita personal income, single-family building permits, sector-level employment shares, and annual mean $$\text{PM}_{2.5}$$ particulate concentrations ($$\mu\text{g}/\text{m}^3$$).
* Geographic Scope: All 3,108 contiguous U.S. counties observed over a 16-year panel (2007–2022).

---

## Data Pipeline & Spatial Exposure

### 1. Data Ingestion & Harmonization
To ensure full reproducibility, we integrated public county-level records across five federal agencies into a unified relational schema:
* Infrastructure & Business Patterns: U.S. Census County Business Patterns (CBP) for NAICS 518 establishment counts and employment figures.
* Labor Market Dynamics: Bureau of Labor Statistics (BLS) QCEW and Local Area Unemployment Statistics (LAUS).
* Income & Output: Bureau of Economic Analysis (BEA) regional accounts for personal income and county GDP.
* Environmental Quality: EPA Air Quality System (AQS) monitor records aggregated to annual county $$\text{PM}_{2.5}$$ averages.
* Housing & Construction: U.S. Census Building Permits Survey for residential building activity.

<p align="center">
  <img src="/images/local_econ_impact_of_data_centers/naics_518_establishments_trend.png" alt="Growth Trend of NAICS 518 Establishments Over Time" width="650" />
</p>

### 2. Spatial Exposure Construction
Because county boundaries are administrative lines that do not restrict commuting patterns or supply chains, we constructed 50-mile Euclidean buffers around each county centroid. We then calculated area-weighted overlaps to estimate nearby infrastructure presence, allowing us to evaluate cross-county economic spillovers.

<p align="center">
  <img src="/images/local_econ_impact_of_data_centers/map_dcs_2022.png" alt="Geographic Distribution of Data Centers Across US Counties (2022)" width="750" />
</p>

---

## Unsupervised Structural Clustering (PCA & K-Means)

To account for baseline differences across counties without introducing circular bias from data center locations, we ran Principal Component Analysis (PCA) and K-Means clustering using strictly physical county characteristics: total population, land area, and water area.

<p align="center">
  <img src="/images/local_econ_impact_of_data_centers/pca_datacenter_plot.png" alt="PCA Projection of Counties Colored by Log Data Center Count" width="650" />
</p>

* Cluster 0 (Rural / Lower-Density Counties): Lower population and larger average land area, with an average of $$\approx 1$$ data center per county.
* Cluster 1 (Urban / Metropolitan Counties): Higher population density and smaller land areas, averaging $$\approx 10$$ data centers per county.
* Validation: Projecting log data center counts onto the PCA components shows a smooth gradient along the diagonal, confirming that data center deployments align naturally with urban infrastructure and population centers even when those features are omitted from the clustering model.

<p align="center">
  <img src="/images/local_econ_impact_of_data_centers/clusters.png" alt="K-Means Cluster Partitioning Across the United States" width="700" />
</p>

---

## Econometric Modeling: Difference-in-Differences

We estimated staggered, two-way fixed effects Difference-in-Differences (DiD) event-study specifications across both clusters to compare counties before and after data center entry:

$$
Y_{it} = \alpha_i + \lambda_t + \sum_{k=-5}^{5} \beta_k D_{i, t-k} + \varepsilon_{it}
$$

Where $$Y_{it}$$ represents the county outcome, $$\alpha_i$$ is the county fixed effect, $$\lambda_t$$ is the year fixed effect, and $$D_{i, t-k}$$ captures annual leads and lags relative to the treatment year to evaluate the parallel trends assumption ($$t < 0$$).

### Summary of Results:
* Personal Income: In rural counties (Cluster 0), adding $$\ge 3$$ data centers was associated with a statistically significant, multi-year decline in log personal income per capita. Urban counties showed no significant shift.
* Ambient Air Quality ($$\text{PM}_{2.5}$$): In rural counties, local data center additions were followed by a post-treatment decrease in $$\text{PM}_{2.5}$$ levels over time.
* Unemployment: Higher regional data center concentration ($$\ge 112$$ nearby establishments) was associated with a steady reduction in unemployment rates in rural areas.

---

## Machine Learning Counterfactuals (Conditional VAE)

To complement the DiD framework, we trained a Conditional Variational Autoencoder (CVAE) in PyTorch to explore non-linear counterfactual county trajectories:
* Architecture: Symmetric encoder-decoder network:
  $$\text{Input (136)} \rightarrow 128 \rightarrow 16 \rightarrow \text{Latent (8)} \rightarrow 16 \rightarrow 128 \rightarrow \text{Output (136)}$$
* Conditioning: Embedded vectors for year ($$16 \times 8$$) and log data center tier ($$6 \times 4$$).
* Evaluation: While the CVAE learned structured representations across 136 county features, perturbing the latent data center vector did not yield easily interpretable economic conclusions. The quasi-experimental DiD approach provided clearer, more reliable causal estimates for policy evaluation.

---

## Discussion & Limitations

### Limitations:
* NAICS 518 Scope: NAICS 518 is a helpful, standardized proxy, but it includes web hosting and data processing services alongside dedicated hyperscale campuses.
* Spatial Approximations: 50-mile centroid buffers provide a regional approximation, which can be less precise in large western counties with complex topography.

### Conclusions:
Overall, the project suggests that data centers bring measurable local trade-offs rather than unconditional windfalls. While regional presence supports lower unemployment and increased construction, local facility concentration can lead to lower average personal income and shifts in traditional employment sectors.

---

[View Full Technical Write-Up Website](https://loganrosell.github.io/local-economic-impacts-of-data-centers/) | [Download Capstone Poster (PDF)](/images/local_econ_impact_of_data_centers/capstone_poster.pdf)




