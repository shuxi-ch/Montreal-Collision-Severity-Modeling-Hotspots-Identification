# Montreal Car Collision Severity Modeling and Hotspot Identification for Policy Insights

### Overview

This project analyzes 218 K+ traffic collisions in Montréal (2012–2019) to uncover when, where, and why severe crashes occur—and to equip city planners and policy makers with data‐driven tools for targeted interventions. We employed advanced EDA, class rebalancing with SMOTE-ENN, and spatial clustering to identify key risk factors, rebalance rare outcomes, and map high-severity hotspots.

### Directory layout

   ```bash
.
├── README.md
├── .gitignore
├── data
│   ├── raw
│   │   └── Dataset.xlsx
│   └── processed
│       └── processed_df.xlsx
├── notebooks                          
│   ├── EDA&DataPreprocessing.ipynb
│   ├── SMOTE Re-balancing&Collision Severity Prediction.ipynb             
│   └── Spatial Clustering for Hotspots Identification.ipynb               
├── reports
│   ├── INSY662 Group7_Presentation Report.pdf
│   ├── EDA&Data Preprocessing.pdf
│   ├── SMOTE Re-balancing&Collision Severity Prediction.pdf
│   └── Spatial Clustering for Hotspots Identification.pdf
└── docs
    ├── Data Dictionary & Processing Logic.xlsx 
    └── Appendix_Target Encoded Severity Mappings.xlsx
 ```

----

### Problem Statement

Severe and fatal collisions impose high human and economic costs, yet their rarity (0.82% serious, 0.12% fatal) makes them difficult to predict. Montréal’s road‐safety teams need precise, location-specific insights to prioritize infrastructure upgrades, enforcement, and public education. Our goals:

1. **Diagnose** temporal, environmental, and road‐use factors driving crash severity
2. **Enable** robust modeling of rare severe or fatal outcomes through oversampling to address extreme class imbalance, supporting more reliable multi-class prediction.
4. **Localize** spatial clusters for targeted policy actions

----

### Data Sources

The dataset focuses on traffic accidents in Montreal, containing variables that track accident location, severity, and other contextual factors like weather and road conditions, with initially 218,128 rows and 50 raw columns. The dataset comes from official sources SAAQ (Société de l'assurance automobile du Québec), and it includes geographic coordinates, quality, and precision scores for accident locations on Montreal’s road network. 

----

### Methodology & Key Techniques

#### 1. EDA

* **Data Cleaning & Imputation:** Dropped >50%-missing features (e.g., `Work_Zone`), forward/backward–filled key categorical/time variables, and converted counts into binary indicators for presence/absence.&#x20;
* **Temporal Patterns:** Mapped `Acc_Time`, `Weekday`, `Month`, `Year` to reveal rush-hour peaks (3–5 PM), Friday spikes, and seasonal January/February and June/July bumps.
* **Variable Distributions & Transformations:**
  * **Zero-inflated** count features (e.g. vehicle types) were binarized to capture involvement vs. count.
  * Continuous imaging (latitude/longitude, credibility score) and casualty counts highlighted `Total_Victims` and `Num_Veh_Invld` as strongest severity correlates.
* **Target Encoding**: Converted high-cardinality factors—`Acc_Type`, `Street_Name`, `Road_Config`—into severity-weighted continuous features, preserving rare but dangerous categories without inflating dimensionality.
* **Key Predictors Identified:** Time of day, accident type, total victims, road configuration, vehicle mix, and location code emerged as top signals.&#x20;

#### 2. Class Rebalancing with SMOTE-ENN

* **Original Imbalance:** Five-class distribution (0 “below threshold” → 4 “fatal”) exhibited a 330 : 1 imbalance ratio, with only 263 fatal cases.
* **SMOTE-ENN Application:** Combined synthetic oversampling of minority classes with Edited Nearest Neighbors cleaning of majority classes.
  * **Result:** Resampled dataset of \~289 k rows achieved near-parity (19 k–87 k per class), reducing imbalance ratio to 4.6 : 1.
  * **Implication:** Enables classifiers to learn severe categories without overwhelming bias toward low-severity crashes.&#x20;
* **Policy Value**: Oversampling ensures that predictive models can accurately identify severe crashes, enabling data-backed deployment of resources (e.g., emergency response readiness) where they matter most.&#x20;
  
#### 3. Spatial Clustering

* **Location-Only Clustering:** Standardized latitude/longitude, determined $k=10$ via elbow and silhouette analysis, and assigned each collision to one of 10 spatial clusters.
* **Cluster Profiling:** For each cluster, computed center coordinates, average severity, victim counts, and modal road/surface/weather conditions.
  * Identified “damage‐only” zones (Clusters 1 & 7) in northern/commercial corridors (avg ≈ 0.91 severity), and “low‐impact” residential pockets (e.g., Cluster 6, avg ≈ 0.76).
* **Actionable Maps:** Generated both folium‐based interactive maps and static scatter plots to visualize cluster extents, guiding local safety audits.&#x20;

----

### Key Findings & Policy Implications

* **Friday Rush-Hour (3 – 5 PM)**
  Both crash counts and severity peak during this window—deploy targeted speed enforcement and upgrade street lighting on Fridays between 3 PM and 5 PM.

* **High-Severity Road Types**
  Multi-lane arterials and numbered routes see a disproportionate share of serious collisions—implement traffic-calming measures (medians, reduced speed limits) and geometric improvements along these corridors.

* **Vulnerable Road Users**
  Pedestrian and cyclist collisions carry elevated fatality risk—install protected bike lanes, raised crosswalks, and advanced pedestrian signals in hotspot zones.

* **Spatial Hotspots**
  Serious/fatal clusters concentrate in Central Island, Plateau, and West Island arterials—prioritize engineering upgrades and stepped-up enforcement in these districts.

* **Emergency Response Optimization**
  Clusters with frequent multi-victim crashes (e.g., Clusters 2 & 5) warrant pre-positioning EMS units nearby to slash response times.

* **Modeling Accuracy via SMOTE-ENN**
  Oversampling lifted minority-class recall for “serious” and “fatal” outcomes from <1% to >93%, proving essential for balanced multi-class crash prediction.

* **Key Severity Predictors**
  Total victims, collision type (especially pedestrian/cyclist), time of day, and heavy-vehicle involvement are the strongest signals—use these for real-time risk alerts and resource dispatch.

* **Seasonal Campaigns**
  Winter and summer both see spikes in serious crashes—align public safety messaging (e.g., winter tire checks, glare-reduction tips) with these seasonal peaks.

* **Road Geometry Trumps Conditions**
  Adverse weather and poor lighting play a secondary role; road design, speed, and traffic controls are the primary drivers of crash severity—focus investments accordingly.

----

### Key Learnings & Challenges

* **Balancing Rare Events:** Combining oversampling with noise reduction (SMOTE-ENN) is critical for multi‐severity classification.
* **Encoding High-Cardinality Categorical Data:** Target‐encoding severity scores preserved rare road/accident‐type nuances without exploding feature space.
* **Geospatial Nuance:** Pure lat/long clustering reveals non‐linear local risk patterns that improve upon raw coordinate features.
* **Data Quality Weighting:** Converting geolocation precision metrics into a single “credibility score” allowed simplification while retaining imprecision signal.

----

### Future Enhancements

1. **Predictive Modeling**: Train XGBoost/Random Forest on SMOTE-ENN data and include cluster labels as features for dynamic risk scoring.
2. **Micro-Hotspot Analysis**: Within top clusters, sub-cluster serious/fatal cases to pinpoint street-level danger spots.
3. **Simulation of Countermeasures**: Model impact of speed-limit reductions or signal timing changes on predicted severity scores.
4. **Interactive Dashboard**: Deploy a web-based GIS dashboard for city engineers and policy makers to explore clusters, filter by time/conditions, and plan interventions in real time.
