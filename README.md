# Aviation Accident Analysis

## Project Overview

This project analyses **88,889 aviation accident records (1948–2022)** compiled by the NTSB,
filtered to professionally built aircraft involved in accidents from 1983 onwards.
The goal is to identify aircraft makes/models and conditions associated with the lowest
rates of passenger injury and aircraft destruction - information directly useful to an
airline/airplane insurer.

---

## Repository Structure

```
AviationData.csv                      # Raw NTSB accident data
USState_Codes.csv                     # State abbreviation lookup
Aviation_Accidents_Cleaning.ipynb     # Data cleaning pipeline
Aviation_Accidents_Data_Analysis.ipynb # EDA, visualisation, and recommendations
aviation_cleaned.csv                  # Cleaned dataset (output of cleaning notebook)
README.md                             # This file
```

---

## Methodology

### Data Filtering
- **Investigation type:** Accidents only (excluded incidents/near-misses)
- **Amateur built:** Excluded (professional builds only)
- **Date:** 1983 onwards (40-year max model lifetime)
- **Category:** Airplane (Aircraft.Category == 'Airplane' or NaN - the category field has 64% missing values, and helicopter-specific makes are removed during Make cleaning)

**Cleaned dataset: 64,211 records**

### Key Derived Metrics
| Metric | Definition |
|---|---|
| `total_passengers` | Fatal + Serious + Minor + Uninjured (NaN injury cols → 0) |
| `injury_fraction` | (Fatal + Serious) / total_passengers |
| `is_destroyed` | 1 if Aircraft.damage = 'Destroyed', 0 otherwise, NaN for unknown |

### Aircraft Size Split
A threshold of **20 total on board** separates small from large aircraft:
- **Small (≤ 20):** 62,959 records
- **Large (> 20):** 1,252 records

All make/model comparisons require a minimum of **10 accident records** per group.

---

## Aircraft Recommendations

### Large Aircraft (> 20 passengers)

| Make | Mean Injury Fraction | Destruction Rate | n |
|---|---|---|---|
| Aerospatiale | 0.066 | 0.100 | 18 |
| Bombardier | 0.076 | 0.083 | 48 |
| McDonnell Douglas | 0.078 | 0.179 | 154 |
| Embraer | 0.110 | 0.159 | 73 |
| Boeing | 0.112 | 0.213 | 700 |
| Airbus | 0.120 | 0.268 | 144 |

**Top specific models (large):**

| Model | Mean Injury Fraction | n |
|---|---|---|
| Boeing 717-200 | 0.002 | 10 |
| Boeing 757-232 | 0.005 | 16 |
| Bombardier CL-600-2B19 | 0.006 | 12 |
| Boeing 757-222 | 0.007 | 11 |
| McDonnell Douglas MD-88 | 0.007 | 12 |

### Small Aircraft (≤ 20 passengers)

| Make | Mean Injury Fraction | Destruction Rate | n |
|---|---|---|---|
| Waco | 0.103 | 0.088 | 137 |
| Grumman-Schweizer | 0.112 | 0.017 | 127 |
| Maule | 0.154 | 0.093 | 569 |
| Aviat Aircraft Inc | 0.162 | 0.039 | 77 |

Note: Cessna (injury fraction ~0.244, n=25,442), Beech (~0.377, n=4,941), and Mooney (~0.372, n=1,249) rank outside the top 15 for injury fraction among small aircraft.

**Top specific models (small, from top-10 makes):**

| Model | Mean Injury Fraction | n |
|---|---|---|
| Maule MX-7-235 | 0.029 | 17 |
| Hiller UH12E | 0.045 | 11 |
| Maule M-7-235B | 0.050 | 10 |
| Grumman-Schweizer G-164A | 0.053 | 50 |
| Maule M-4 | 0.056 | 18 |

---

## Key Findings: Factors Affecting Safety Outcomes

### Factor 1: Weather Condition (VMC vs IMC)

Accidents in **Instrument Meteorological Conditions (IMC)** are nearly **3× more likely** to
result in fatal/serious injuries compared to accidents in **Visual Meteorological Conditions (VMC)**:

- Mean injury fraction: **IMC = 0.664** vs **VMC = 0.227**
- Aircraft destruction rate: **IMC = 0.599** vs **VMC = 0.177**

This reflects the higher prevalence of controlled-flight-into-terrain (CFIT) and spatial
disorientation events under low-visibility conditions.

**Implication:** Aircraft operated on routes with frequent IMC exposure represent substantially
elevated risk and should carry higher insurance premiums.

### Factor 2: Number of Engines

The relationship between engine count and safety outcomes is more nuanced than a simple redundancy gradient:

| Engines | Mean Injury Fraction | Mean Destruction Rate | n |
|---|---|---|---|
| 1 | 0.252 | 0.194 | 52,138 |
| 2 | 0.338 | 0.326 | 7,938 |
| 3 | 0.070 | 0.128 | 175 |
| 4 | 0.235 | 0.297 | 199 |

Three-engine widebodies show the best outcomes by a wide margin. Twin-engine aircraft have
**higher** aggregate injury and destruction rates than single-engine aircraft — likely due to
selection bias, as twins are more commonly used for commercial IFR operations under riskier
conditions with fuller passenger loads.

**Implication:** Engine count alone is an insufficient risk discriminator. Aircraft type and
operational context (commercial vs. private, IMC vs. VMC exposure) are essential co-variates.

---

## Visualisations

| File | Description |
|---|---|
| `fig_makes_injury_fraction.png` | Top 15 safest makes (injury fraction) for small & large |
| `fig_violin_small_makes.png` | Injury fraction distribution - top 10 small makes |
| `fig_strip_large_makes.png` | Injury fraction distribution - top 10 large makes |
| `fig_makes_destruction_rate.png` | Destruction rate - top 15 small & large makes |
| `fig_small_models.png` | Safest specific models for small aircraft |
| `fig_large_models.png` | Safest specific models for large aircraft |
| `fig_weather_condition.png` | Injury fraction & destruction rate by weather |
| `fig_weather_violin.png` | Injury fraction distribution VMC vs IMC |
| `fig_engine_count.png` | Injury fraction & destruction rate by engine count |
| `fig_engine_count_box.png` | Injury fraction distribution by engine count |

---

## Tools & Libraries

- **Python 3.9** - pandas, numpy, matplotlib, seaborn
- **Jupyter Notebook** - interactive analysis and documentation
