# FlussiMTS — Road Traffic & Air Pollution Analysis in Emilia-Romagna

> An exploratory data science project combining regional road-traffic monitoring data
> with air-quality measurements (NO₂, PM10) across the provinces of Emilia-Romagna, Italy.

---

## Table of Contents

1. [Project Overview](#project-overview)
2. [Scientific Framing](#scientific-framing)
3. [Data Sources](#data-sources)
4. [Repository Structure](#repository-structure)
5. [Notebooks](#notebooks)
6. [Key Findings](#key-findings)
7. [Installation & Requirements](#installation--requirements)
8. [Usage](#usage)
9. [Limitations & Future Work](#limitations--future-work)

---

## Project Overview

This project investigates **road traffic patterns** across Emilia-Romagna and their
relationship with **atmospheric pollutant concentrations**, with a focus on the period
2019–2020. The year 2020 provides a natural quasi-experiment: COVID-19 lockdowns caused
a sharp reduction in vehicle traffic, making the 2019–2020 comparison a useful
lens for understanding how mobility affects local air quality.

The traffic data come from the **MTS (Sistema di Monitoraggio del Traffico Stradale)**,
the regional road-traffic flow monitoring system of the Emilia-Romagna Region, operated
jointly by the Region, its Provinces, and the national road authority ANAS. The network
consists of **281–283 microwave sensors** installed on state and provincial roads,
operating 24 hours a day and classifying vehicles into 10 categories (motorcycles, cars,
vans, light and heavy trucks, articulated lorries, buses, and others).

Air-quality data (NO₂ and PM10) were collected from ARPAE (Regional Agency for
Environmental Protection) monitoring stations distributed across the regional provinces.

---

## Scientific Framing

The project follows a five-step scientific method:

| Step | Description |
|------|-------------|
| **1. Question** | Does road traffic volume correlate with atmospheric pollution levels (NO₂, PM10) in Emilia-Romagna? Did the COVID-19 lockdown of 2020 produce a measurable improvement in air quality? |
| **2. Hypothesis** | Higher traffic volume is positively correlated with NO₂ and PM10 concentrations; a significant drop in traffic during the 2020 lockdown should be accompanied by a reduction in pollutant levels. |
| **3. Data Collection** | MTS hourly/daily/monthly sensor readings for 2019 and 2020; ARPAE hourly NO₂ and daily PM10 measurements for nine provincial capitals. |
| **4. Analysis** | Time-series visualisation, weekday vs. weekend traffic breakdowns, STL decomposition, Pearson correlation between traffic flows and pollutant concentrations per province and monitoring station. |
| **5. Conclusions** | Correlation coefficients are computed per province and station; results are visualised in ranked bar charts to identify which areas show the strongest traffic–pollution relationship. |

---

## Data Sources

### Traffic — MTS System

| File | Description | Granularity |
|------|-------------|-------------|
| `MTS_2019.csv` | Full 2019 daily traffic readings (~11 MB) | Daily |
| `MTS_2020.csv` | Full 2020 daily traffic readings (~2 MB) | Daily |
| `MTS_2019_Mese.csv` | Monthly aggregates 2019 | Monthly |
| `MTS_2020_Mese.csv` | Monthly aggregates 2020 | Monthly |
| `MTS_2019_3mesi.csv` | First 3 months of 2019 | Daily |
| `MTS_AA_2019_3mesi.csv` | First 3 months of 2019, alternative format | Daily |
| `RilevazioniPerPostazionev1/v2.csv` | Per-sensor readings (two versions) | Daily |

**Traffic columns include:** total transits, light vehicles, heavy vehicles, unclassified,
daytime/night-time traffic, weekday/weekend traffic, and Average Daily Traffic (TGM)
for each category.

### Sensor Locations — GIS

The `MTS/` subfolder contains a **shapefile** (`PostazioniSensoriWGS84.*`) with the
geographic coordinates (WGS84) and names of all monitoring stations, used to render
interactive Folium maps.

### Air Quality — ARPAE

| File / Folder | Pollutant | Coverage |
|---|---|---|
| `NO2.csv` | NO₂ (Nitrogen Dioxide), µg/m³ | Hourly, Jan–Apr 2020 |
| `pm10.csv` | PM10 (Particulate Matter), µg/m³ | Daily, Jan–Apr 2020 |
| `inquinamento/NO2/` | Per-province NO₂ CSVs | Hourly — Bologna, Ferrara, Forlì-Cesena, Modena, Parma, Piacenza, Ravenna, Reggio Emilia, Rimini |
| `inquinamento/PM10/` | Per-province PM10 CSV | Daily — Bologna |
| `inquinamento/Pollen/` | Pollen concentration notebook | Historical archive via ARPAE |

---

## Repository Structure

```
FlussiMTS-master/
│
├── README.md
│
├── # Traffic data
├── MTS_2019.csv
├── MTS_2020.csv
├── MTS_2019_Mese.csv
├── MTS_2020_Mese.csv
├── MTS_2019_3mesi.csv
├── MTS_AA_2019_3mesi.csv
├── RilevazioniPerPostazionev1.csv
├── RilevazioniPerPostazionev2.csv
│
├── # Pollution data
├── NO2.csv
├── pm10.csv
│
├── # GIS sensor locations
└── MTS/
│   ├── PostazioniSensoriWGS84.shp
│   ├── PostazioniSensoriWGS84.dbf
│   ├── PostazioniSensoriWGS84.shx
│   └── ... (supporting shapefile components)
│
├── # Analysis notebooks
├── RoadTraffic.ipynb
├── SummaryStats.ipynb
├── Analisi_2019_Mese.ipynb
├── Inquinamento.ipynb
│
└── inquinamento/
    ├── NO2/          ← per-province NO₂ CSVs
    ├── PM10/         ← per-province PM10 CSV
    └── Pollen/       ← pollen concentration notebook
```

---

## Notebooks

### 1. `RoadTraffic.ipynb` — Road Traffic Exploration
An introductory notebook that loads and merges the two per-sensor CSV files
(`RilevazioniPerPostazionev1/v2.csv`), renames columns to clear English labels, and
produces time-series line charts for individual roads. The example road visualised is
the *NSA 313 complanare sud alla A14*, showing heavy-vehicle traffic over time.

**Key steps:** data loading → column renaming → road-level time-series plotting.

---

### 2. `SummaryStats.ipynb` — Regional Traffic Summary Statistics
The most comprehensive traffic analysis notebook. It aggregates all daily sensor
readings across the monitored road network and produces:

- **Daily total traffic** time series (total, light, heavy vehicles)
- **Weekday vs. weekend** traffic comparison over the full year
- **Day-of-week bar charts** (Monday–Sunday) broken down by vehicle category
- **Interactive Folium map** of all 281 sensor positions, colour-coded by traffic
  volume percentile (green = low, orange = moderate, red = high)

**Key libraries:** `pandas`, `numpy`, `matplotlib`, `folium`, `geopandas`

---

### 3. `Analisi_2019_Mese.ipynb` — Monthly Traffic Analysis 2019
Focused on monthly aggregated data (`MTS_2019_Mese.csv`). Iterates over all monitored
roads and plots monthly TGM (Traffico Giornaliero Medio — Average Daily Traffic) for
light vehicles. Also includes STL time-series decomposition to separate trend, seasonal,
and residual components.

**Key libraries:** `pandas`, `matplotlib`, `stldecompose`, `folium`, `geopandas`

Vehicle categories classified by the MTS system:
1. Motorcycles
2. Cars and MPVs
3. Cars with trailers
4. Vans and light trucks
5. Medium trucks (up to 7.5 m)
6. Large trucks
7. Truck-trailer combinations
8. Articulated lorries
9. Buses
10. Other (special vehicles)

---

### 4. `Inquinamento.ipynb` — Traffic–Pollution Correlation Analysis
The main scientific notebook. It joins traffic flow data (aggregated to the provincial
level) with NO₂ measurements from ARPAE monitoring stations across nine provinces, then:

- Plots NO₂ and PM10 time series for Jan–Apr 2020, with a vertical marker at
  the approximate start of the lockdown (≈ day 60 of the year)
- Computes **Pearson correlation coefficients** between daily traffic volume and
  NO₂ concentration for each monitoring station
- Produces a **histogram** of all correlation values across stations
- Produces a **ranked horizontal bar chart** of correlations per station and per province

**Provinces covered:** Bologna, Ferrara, Forlì-Cesena, Modena, Parma, Piacenza,
Ravenna, Reggio Emilia, Rimini.

**Key libraries:** `pandas`, `numpy`, `matplotlib`, `scipy.stats`, `seaborn`,
`sklearn.preprocessing`, `stldecompose`

---

## Key Findings

- **Traffic shows strong weekly seasonality**: heavy vehicles peak mid-week
  (Tuesday–Thursday) and drop sharply on weekends, while light vehicle traffic is
  more evenly distributed.
- **The COVID-19 lockdown (March 2020)** is clearly visible as a structural break
  in both traffic and pollutant time series.
- **Pearson correlations between traffic and NO₂** vary substantially across provinces
  and monitoring stations, reflecting differences in local road network density, urban
  morphology, and background pollution sources.
- **PM10 and NO₂ drop** noticeably after the lockdown marker, consistent with the
  hypothesis that road traffic is a meaningful contributor to local air pollution in
  Emilia-Romagna.

---

## Installation & Requirements

```bash
# Clone the repository
git clone https://github.com/<your-username>/FlussiMTS.git
cd FlussiMTS

# Create a virtual environment (recommended)
python -m venv venv
source venv/bin/activate   # on Windows: venv\Scripts\activate

# Install dependencies
pip install pandas numpy matplotlib seaborn scipy scikit-learn \
            folium geopandas stldecompose jupyter
```

> **Note:** `geopandas` may require additional system-level dependencies
> (`libgdal`, `libproj`). On macOS with Homebrew: `brew install gdal`.
> On Ubuntu/Debian: `sudo apt install gdal-bin libgdal-dev`.

---

## Usage

Launch Jupyter and open any of the four notebooks:

```bash
jupyter notebook
```

The recommended reading order is:

1. `RoadTraffic.ipynb` — get familiar with the sensor data structure
2. `SummaryStats.ipynb` — explore regional traffic patterns
3. `Analisi_2019_Mese.ipynb` — monthly/seasonal decomposition
4. `Inquinamento.ipynb` — correlation with air pollution

Data files are expected to be in the **project root** directory (same level as the
notebooks). The provincial NO₂ CSVs should be placed in `inquinamento/NO2/` and
PM10 data in `inquinamento/PM10/`.

---

## Limitations & Future Work

- **Incomplete pollution coverage**: provincial NO₂ CSVs are available for only 5 of 9
  provinces in the current dataset; PM10 data covers Bologna only.
- **Temporal mismatch**: traffic data spans all of 2019 and part of 2020, while
  pollution data currently covers Jan–Apr 2020 only.
- **Correlation ≠ causation**: Pearson correlation captures linear association but does
  not control for confounders (wind speed, temperature, industrial emissions, etc.).
- **Potential extensions:**
  - Include meteorological covariates (wind, rain, temperature) to partial out
    weather effects
  - Extend the pollution dataset to the full year and all provinces
  - Apply causal inference methods (difference-in-differences) to formally quantify
    the lockdown effect on air quality
  - Integrate pollen concentration data (notebook available in `inquinamento/Pollen/`)
    for a more comprehensive environmental analysis
  - Build an interactive dashboard (e.g. with Streamlit or Dash) combining the map,
    traffic charts, and pollution correlations

---

## Data License

Traffic data are publicly available via the
[Emilia-Romagna Open Data Portal](https://dati.emilia-romagna.it).
Air quality data are published by
[ARPAE Emilia-Romagna](https://www.arpae.it).
Please refer to each source for the applicable terms of use.
