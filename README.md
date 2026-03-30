# indonesia-socioeconomic-2024

Cross-sectional analysis of education, poverty, and socioeconomic indicators across 38 Indonesian provinces — BPS 2024

---

## Overview

This repository contains data, analysis code, and output for the paper:
**"The Association between Educational Attainment and Poverty across Indonesian Regions"**

The study examines how mean years of schooling relates to provincial poverty rates using OLS regression, controlling for GRDP per capita, unemployment, and internet access.

---

## Repository Structure
```
indonesia-socioeconomic-2024/
├── indonesia_provincial_socioeconomic_2024.csv   # Aggregated dataset (38 provinces)
├── education_poverty_analysis.do                 # Stata do-file (EDA + regression)
├── education_poverty_log.log                     # Stata log file (full output)
└── visualizations/
    ├── scatter_educ_poverty.png
    ├── scatter_grdp_poverty.png
    └── hist_poverty.png
```

---

## Data Sources

All raw data are from Badan Pusat Statistik (BPS) Indonesia, 2024:

| Variable | Dataset |
|---|---|
| Poverty headcount rate (P0) | Percentage of Poor Population by Province and Area |
| Mean years of schooling | Rata-Rata Lama Sekolah, Age 15+, by Province |
| GRDP per capita | PDRB per Kapita Atas Dasar Harga Berlaku, by Province |
| Open unemployment rate | Tingkat Pengangguran Terbuka, by Province |
| Household internet access | Percentage of Households Accessing Internet, by Province |
| Life expectancy at birth | Life Expectancy at Birth by Province and Gender |

Source: [https://bps.go.id](https://bps.go.id)

---

## Data Aggregation

Six separate BPS datasets were merged into a single cross-sectional dataset at the province level (n = 38). The aggregation logic is equivalent to the following SQL:
```sql
SELECT
    a.province,
    b.grdp_per_capita_thousand_rp_2024,
    c.life_exp_male_2024,
    c.life_exp_female_2024,
    d.internet_pct_total_2024,
    e.poverty_total_sep2024,
    f.mean_years_schooling_2024,
    g.unemployment_pct_aug2024

FROM provinces a
LEFT JOIN grdp         b ON a.province = b.province
LEFT JOIN life_exp     c ON a.province = c.province
LEFT JOIN internet     d ON a.province = d.province
LEFT JOIN poverty      e ON a.province = e.province
LEFT JOIN schooling    f ON a.province = f.province
LEFT JOIN unemployment g ON a.province = g.province

WHERE a.province != 'INDONESIA'
ORDER BY a.province;
```

In practice, the merge was performed in Python using `pandas.merge()` with `how='outer'` joins on the `province` key, after standardizing all province names to uppercase across the six source files.

---

## Analysis

All analysis was conducted in **Stata**. The do-file runs the following steps:

1. Load and clean data
2. Rename and label variables
3. Exploratory Data Analysis (summary stats, correlations, scatter plots, histogram)
4. OLS Regression — Model 1 (bivariate) and Model 2 (full controls)
5. Post-estimation diagnostics (VIF, residuals vs fitted)
6. Robustness check using log-transformed GRDP per capita

---

## Key Results

| Model | Education Coef. | R² |
|---|---|---|
| Model 1 — Bivariate | −3.025*** | 0.298 |
| Model 2 — Full controls | +2.168** | 0.705 |
| Model 3 — Log GRDP | +2.075** | 0.706 |

Internet access (β = −0.409, p < 0.001) is the strongest predictor of poverty in the full model.

---
