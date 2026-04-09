
# The GOAT INDEX for Modern era test batsmen (from 1970 – to early 2026 including the 5th Ashes test)
**Moving away from averages, 100's scored by batsmen to evalualte test batting performance and who the GOAT batsmen are**

## OVERVIEW
Came across a twitter debate who was the greatest test batsmen. There were some varying opinions in the twitter debate and within the twitter thread with an open data source (https://p.imgci.com/db/DOWNLOAD/100/0163/Ananth_Test_Indices_Primer coming from Anantha Narayanan). using the available base data I decided to do my own analysis and build out analysis and visulisation + story telling to find who are the GOAT test batsmens.   

Using a weighted 100-point system across six performance pillars, the model normalizes for era-specific difficulty, quality of bowling faced, and situational pressure.

---

## Data Pipeline & Architecture

* **Ingestion:** Raw career and match metrics gathered via **Google Sheets**.
* **Processing:** Data cleaning, player normalization, and feature engineering performed in **Google Colab (Python)**.
* **Data Warehouse:** Cleaned data hosted in **Google BigQuery** for high-speed SQL aggregations and Z-Score calculations.
* **Visualization:** Dynamic reporting and scatter plot analysis built in **Looker Studio**.

---

## Dimensions & Metrics Reference

### Core Dimensions
* **Player:** The unique identifier for each batsman in the dataset.
* **Country:** The international team represented by the player.
* **Era (Decade):** The specific time period used to calculate relative Z-Scores (e.g., 1990s, 2000s).
* **Span:** The start and end years of the player's international career.

### Performance Metrics
* **Mat / Inns:** Total matches played and innings batted.
* **Runs:** Total career runs scored in Test Cricket.
* **Ave:** The traditional batting average (Runs / Dismissals).
* **SR (Strike Rate):** The number of runs scored per 100 balls faced.
* **100s / 50s:** The frequency of centuries and half-centuries scored.
* **MOM Awards:** Total "Player of the Match" accolades received.
* **WBA (Weighted Batting Avg):** Average adjusted for opposition strength and team run contribution.
* **X-Factor Score:** A composite metric measuring scoring speed and match-winning impact.
* **Era Dominance (Z-Score):** The number of standard deviations a player sits above their decade's mean.
* **Consistency Rating:** Performance frequency against ICC Top-5 ranked bowling attacks.
* **Impact Profile (IPV):** A weighted score for 4th innings, series-deciders, and high-pressure chases.
* **Bowling Quality Resistance:** A metric measuring runs scored specifically against elite individual bowlers.

---

## The 100-Point Methodology (Calculations)

To ensure statistical parity, every pillar is relativized against the modern era's "Gold Standard" (the highest recorded value since 1970).

### 1. Opposition & Team Impact (35.0 Pts)
* **Formula:** `(Player_WBA / Max_Modern_WBA) * 35.0`
* **Goal:** Rewards players who "carried" their teams against strong opposition.

### 2. Match-Winner Score (25.0 Pts)
* **Formula:** `((Strike_Rate * 0.1) + (MOM_Awards * 2) / Max_XFactor) * 25.0`
* **Goal:** Rewards aggression and the ability to fundamentally alter match outcomes.

### 3. Elite Attack Resistance (20.0 Pts)
* **Formula:** `(Resistance_Score / Max_Resistance) * 20.0`
* **Goal:** Filters out "cheap runs" by prioritizing runs scored against top-tier bowling.

### 4. Situational Pressure Value (10.0 Pts)
* **Formula:** `(IPV_Score / Max_IPV) * 10.0`
* **Goal:** Rewards "Clutch" performances in 4th innings or series-defining moments.

### 5. Era Dominance (5.0 Pts)
* **Formula:** `(Era_ZScore / Max_ZScore) * 5.0`
* **Goal:** A prestige bonus for players who gapped their contemporaries by the widest margins.

### 6. Longevity & Volume (5.0 Pts)
* **Formula:** `(Total_Runs / 15921) * 5.0`
* **Goal:** A "marathon" reward for maintaining elite standards over a long career.

---

## Tiered Classification System

Players are mapped into tiers based on their **Final GOAT Score**:

* **The Immortal Tier (80.0+):** Outliers who dominated across all six pillars (e.g., Sangakkara, Lara, Smith).
* **The Elite Benchmark (70.0 - 79.9):** Hall of Fame legends with sustained world-class impact (e.g., Ponting, Kallis).
* **The Great Tier (<70.0):** Elite professionals who defined their era but lacked the statistical "Peak" of the top tiers.

---

## Limitations
* **Bowling Quality:** While Z-Scores normalize for era-means, they cannot fully capture the "fear factor" of specific batteries like the 90s West Indies pace attack.
* **Series Leverage:** Current logic weights match-winning innings equally; future iterations (v3.0) will include multipliers for series-clinching performances.

---
**Author:** Dipesh Shah  
**Tools:** SQL (BigQuery), Python (Colab), Looker Studio
