# # 🌌 Exoplanet Data Vault: ETL & Visualization Ready

## 🚀 Project Overview
This project builds a **mini Data Warehouse (DW)** for exoplanet and star system data.  
We take raw CSV data, clean and transform it, and load it into structured DW tables for analysis and future predictions.  
Our goal was to impute missing values, understand stellar influences on planet mass and radius, and detect the most “unusual” 
or “unicorn” exoplanets—planets whose mass and radius deviate significantly from what would be expected given their stellar environment.

We combined ML-based imputation, feature importance analysis, and story-driven visualizations to uncover quirky patterns in the galaxy.

The goal: **From raw data → Dimension & Fact Tables → Analysis/ML**.

---

### 1️⃣ Data Ingestion
- Loaded raw CSV data of exoplanets.  
- Cleaned it into a usable format (`exoplanets_cleaned.csv`).  
- Script: `ingest_exoplanets.py` ✅
- Python ingestion script is compatible with most ETL systems.
Airflow: wrap ingest_exoplanets.py and create_dw_tables.py as tasks in a DAG
Snowflake: load the CSVs into Snowflake tables using COPY INTO or snowflake-connector-python

### 2️⃣ Dimension Tables
- **Planet Dimension (`planet_dim.csv`)**: Unique planets with `planet_id`. 🌍  
- **Star Dimension (`star_dim.csv`)**: Unique stars with `star_id`. ⭐  
- **System Dimension (`system_dim.csv`)**: Star system info with `system_id`. 🪐  

### 3️⃣ Fact Table
- **Exoplanet Fact (`exoplanet_fact.csv`)**: Links planets, stars, and systems using foreign keys. 🔗  
- Stores key planetary metrics like `pl_orbper`, `pl_rade`, `pl_masse`. 📊  

### 4️⃣ Orchestration & DW Creation
- Automated table creation with `create_dw_tables.py`. ⚙️  
- Memory-efficient mapping avoids large merge operations. 💾  
- `check_tables.py` lets us preview DW tables quickly (`head()`). 👀  

### 5️⃣ Warehouse Storage
- All CSVs are stored in `Warehouse/`. 📂  
- Ready for further **analysis, visualization, or predictive modeling**. 🎯

### Analysis
1️⃣ Data Preparation & Merging
-Loaded and merged multiple exoplanet + stellar datasets (planet_id, star_id, system_id) into a unified table.

-Filled missing planet mass (pl_masse) & radius (pl_rade) using model-based imputation:

-Trained RandomForest regressors on known values.

Created pl_masse_filled & pl_rade_filled combining original + ML-predicted values.

2️⃣ Model-Based Imputation
-Objective: Predict missing planet mass & radius.

Models: XGBoost Regressor for high performance + feature importance interpretability.

Features:
pl_masse: pl_orbper, st_mass, st_rad, st_teff, sy_dist
pl_rade: pl_orbper, st_mass, st_rad, st_teff, sy_dist

Performance:

Mass: R² = 0.7285, MAE = 166.16

Radius: R² = 0.7211, MAE = 3.65

Feature Importance:

Mass → pl_orbper > st_rad > sy_dist > st_mass > st_teff

Radius → sy_dist > pl_orbper > st_mass > st_teff > st_rad

3️⃣ 🦄 Unicorn Planet Detection
-Concept: Planets deviating strongly from predictions are “unicorns”.

Method:
-Predict expected mass/radius using top features only.

Compute deviations:

mass_deviation = abs(pl_masse_filled - expected_mass)

radius_deviation = abs(pl_rade_filled - expected_radius)

-Flag unicorns using threshold: mean + 2×std

Compute unicorn_score = mass_deviation + radius_deviation

## Key Insights

-Stellar Influence: st_rad & sy_dist dominate planet mass/radius predictions; pl_orbper surprisingly impacts mass deviations.

-Unicorns: ~1,177 flagged; ~60 super-unicorns. Many are bloated or unusually massive planets.

-Patterns: Multi-planet systems show correlated deviations, but exceptions reveal unusual formation or evolution pathways.

## Conclusion

-Machine learning + feature importance enables discovery of hidden exoplanet anomalies. Achievements:

-Imputed missing mass & radius accurately

-Identified key stellar drivers of planet properties

-Detected unicorn planets, highlighting extreme planetary formation cases 🚀

-Extensions:

Predict new planet properties

Study correlations between stellar & planetary architectures

## Next Steps

-Multi-planet system correlation analysis

-SHAP values for feature interpretability 🔍

-Temporal trends in discoveries & unicorn planets

-Interactive dashboard for real-time unicorn exploration 💻
