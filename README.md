
# The GOAT INDEX for Modern era test batsmen (from 1970 – to early 2026 including the 5th Ashes test)
**Moving away from averages, 100's scored by batsmen to evalualte test batting performance and who the GOAT batsmen are**

## OVERVIEW
Came across a twitter debate who was the greatest test batsmen. There were some varying opinions in the twitter debate and within the twitter thread with an open data source (https://p.imgci.com/db/DOWNLOAD/100/0163/Ananth_Test_Indices_Primer coming from Anantha Narayanan). using the available base data I decided to do my own analysis, build out visulisation + story telling to find who are the GOAT test batsmens.   

Using a weighted 100-point system across six performance pillars, the model normalizes for era-specific difficulty, quality of bowling faced, and situational pressure.

---

## DATA PIPELINE & ARCHITECTURE

* **Ingestion:** Raw career metrics gathered via **Notepad** > **Excel** > **Google Sheets**.
* **Processing:** Data cleaning, player normalization, and feature engineering performed in **Google Colab (Python)**.
* **Data Warehouse:** Cleaned data hosted in **Google BigQuery** allowing to add in aggregations of data (to segment in the visulisation) and addtional calculations.
* **Visualization:** Story telling and analysis built in **Looker Studio**.

---

## DIMENSIONS & METRICS REFERENCE

The master Big Query table had a total of 39 dimensions and metrics. 

Summary of the core dimensions and metrics: 

### Core Dimensions
* **Player:** The unique identifier for each batsman in the dataset.
* **Country:** The international team represented by the player.
* **Era (Decade):** The specific time period used to calculate relative Z-Scores (e.g., 1990s, 2000s).
* **Span:** The start and end years of the player's international career.

### Core Performance Metrics
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

## THE 100 POINT MODEL + CALCULATIONS = GOAT INDEX

The GOAT INDEX was calculated from 6 pillars and each pillar weighted to its importance = 100

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

## DEEP DIVE INTO WEIGHTED BATTING AVERAGE (WBA) - CASE STUDY JACQUES KALLIS 

The Raw Data - Traditional Stats 

Averages are calculated by ignoring 'Not Out' 

* Total Career Runs - 13,289
* Total Innings - 280
* Not Outs - 40
* Traditional Denominator - 280 - 40 = 240
* Traditional Average - 13,289 / 240 = 55.37

The WBA logic every 'Not Out' innings is not ignored and a fractional value is added 

* Total Career Runs - 13,289
* Total Innings - 280
* Not Outs - 40
* Weighted Denominator - 262
* Traditional Average - 13,289 / 262 = 50.72

---

## THE MODELS PHILOSOPHY - IMPACT OVER ACCUMULATION

Most analysis on players will value longetivity, the model created focuses on the contributuon and the impact created 

---

## CASE STUDY - IMPACT V ACCUMULATION - BEN STOKES V JACQUES KALLIS

Comparing Ben Stokes and Jacques Kallis (the batsmen) looking at the batting average it shows a huge gulf between the two batsmen. The GOAT INDEX reduces the gap and credits Ben Stokes, but still Jacques Kallis is the greater batsmen of the two.

* Batting Average - 34.86 v 55.37
* 4th Innings Average - 34 v 44 
* Weighted Batting Average (WBA) - 34.12 v 50.72
* GOAT INDEX - 61.38 v 79.56

The model created is not valuing as high Kallis longer career where Stokes is rewarded for his match winning innings that is not truly reflected in his average but shows more fairly in the GOAT INDEX. 

---

