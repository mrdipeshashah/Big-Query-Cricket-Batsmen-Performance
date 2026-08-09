
# The GOAT INDEX for Modern era test batsmen (from 1970 – to early 2026 including the 5th Ashes test)
**Moving away from averages, 100's scored by batsmen to evalualte test batting performance and who the GOAT batsmen are**

## OVERVIEW
Came across a twitter debate around test batsmen performance. There were some varying opinions in the twitter debate and within the twitter thread there was a shared open data source (which came from Anantha Narayanan - https://p.imgci.com/db/DOWNLOAD/100/0163/Ananth_Test_Indices_Primer). Using the available base data I decided to do my own analysis, build out visulisation + story telling to find who are the GOAT test batsmens.   

Using a weighted 100-point system across six performance pillars, the model normalizes for era-specific difficulty, quality of bowling faced, and situational pressure.

## DASHBOARD 

https://lookerstudio.google.com/reporting/b54e0997-5cc7-4865-9fa5-9a37f50b1f7e

## DATA PIPELINE & ARCHITECTURE

* **Ingestion:** Raw career metrics gathered via **Notepad** > **Excel** > **Google Sheets**.
* **Processing:** Data cleaning, player normalization, and feature engineering performed in **Google Colab (Python)**.
* **Data Warehouse:** Cleaned data hosted in **Google BigQuery** allowing to add in aggregations of data (to segment in the visulisation) and addtional calculations.
* **Visualization:** Story telling and analysis built in **Looker Studio**.


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
* **Actual Batting Average:** The traditional way ot calculating batting average (Runs / Innings).
* **SR (Strike Rate):** The number of runs scored per 100 balls faced.
* **100s / 50s:** The frequency of centuries and half-centuries scored.
* **MOM Awards:** Total "Player of the Match" accolades received.
* **WBA (Weighted Batting Avg):** Average adjusted for opposition strength and team run contribution (Runs/Weighted Denominator).
* **NotOutTax:** The delta between - Weighted Batting Average - Actual Batting Average
* **X-Factor Score:** A composite metric measuring scoring speed and match-winning impact.
* **Era Dominance (Z-Score):** The number of standard deviations a player sits above their decade's mean.
* **Consistency Rating:** Performance frequency against ICC Top-5 ranked bowling attacks.
* **Impact Profile (IPV):** A weighted score for 4th innings, series-deciders, and high-pressure chases.
* **Bowling Quality Resistance:** A metric measuring runs scored specifically against elite individual bowlers.

## THE 100 POINT MODEL + CALCULATIONS = GOAT INDEX

Google Doc - https://docs.google.com/document/d/1cUR987UmY0--9syn5hxvyI_08NLR2GwzTnQjT-_JYxE/edit?usp=sharing

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
* Weighted Average - 13,289 / 262 = 50.72

This helps calculate the Not Out Tax - Weighted Batting Average - Actual Batting Average. This normalize a player's average by treating 'Not Out' innings as fractional events rather than complete survivors."

## THE MODELS PHILOSOPHY - IMPACT OVER ACCUMULATION

Most analysis on players will value longetivity, the model focuses on the contributuon and the impact created 

## CASE STUDY - IMPACT V ACCUMULATION - BEN STOKES V JACQUES KALLIS

Comparing Ben Stokes and Jacques Kallis (the batsmen) looking at the batting average it shows a huge gulf between the two batsmen. The GOAT INDEX reduces the gap and credits Ben Stokes, but still Jacques Kallis is the greater batsmen of the two.

* Batting Average - 34.86 v 55.37
* 4th Innings Average - 34 v 44 
* Weighted Batting Average (WBA) - 34.12 v 50.72
* GOAT INDEX - 61.38 v 79.56

The model created is not valuing as high Kallis longer career where Stokes is rewarded for his match winning innings that is not truly reflected in his average but shows more fairly in the GOAT INDEX. 

---

## MODEL EVOLUTION & V2 CALIBRATION

Following initial findings in V1, the model underwent a comprehensive **V2 Calibration** to refine baseline scoring, tighten global inflation, and address specialized role interactions. 

### Key Structural Updates in V2:
1. **Universal Deflation Alignment:** Applied stricter global ceiling normalization, resulting in an average system-wide compression of **~8.0 points/runs**. **100% of players** experienced a negative absolute score delta, establishing a tougher "Immortal Status" benchmark threshold (75.0+ pts).
2. **Era Normalization Refinement:** Recalibrated era Z-Score weighting. Modern eras (2020s) experienced higher compression (-8.95 avg delta), whereas resilient historical eras like the 1980s (-7.20 avg delta) gained relative ground.
3. **Role Discipline Isolation:** Separated primary top-order batting contributions from secondary skill baselines (wicket-keeping and lower-order all-rounder workloads), resulting in heavier relative score drops (-10 to -12 pts) for hybrid roles.

---

## THE 100 POINT MODEL + CALCULATIONS = GOAT INDEX v2

Google Doc - https://docs.google.com/document/d/1cUR987UmY0--9syn5hxvyI_08NLR2GwzTnQjT-_JYxE/edit?usp=sharing

The GOAT INDEX was calculated from 5 pillars and each pillar weighted to its importance = 100

### 1. Opposition & Team Impact (35.0 Pts)
### 2. Match-Winner Score (20.0 Pts)
### 3. Elite Attack Resistance (20.0 Pts)
### 4. Situational Pressure Value (15.0 Pts)
### 5. Era Dominance (10.0 Pts)

Longevity & Volume was removed from v2 as a scoring pillar. This ensures "Impact Over Accumulation." removing raw volume rewards ensures the model strictly measures peak quality, dominance over peers, and clutch performance rather than just career duration.

The key changes from v1 v v2: 

### 1. Situational Pressure Value: Increased from 10.0 Pts $\rightarrow$ 15.0 Pts (+5.0 Pts)
### 2. Era Dominance Index: Increased from 5.0 Pts $\rightarrow$ 10.0 Pts (+5.0 Pts)

## MODEL COMPARISON: V1 vs V2 IN ACTION

While global scores compressed across the board, relative rank shifts revealed how different player profiles responded to the V2 logic:

| Metric / Player | Jacques Kallis (Top Tier Anchor) | Allan Border (Volume vs Impact) | Navjot Singh Sidhu (Top Climber) | Ian Healy (Hybrid / Keeper) |
| :--- | :---: | :---: | :---: | :---: |
| **Era** | 1990s / 2000s | 1970s / 1980s | 1980s / 1990s | 1980s / 1990s |
| **V1 Score -> V2 Score** | 79.56 -> 74.44 | 78.07 -> 71.30 | 61.20 -> 55.56 | 58.10 -> 46.10 |
| **Absolute Score Delta** | **-5.12** | **-6.77** | **-5.64** | **-12.00** |
| **V1 Rank -> V2 Rank** | **#8 -> #9** (-1) | **#11 -> #13** (-2) | **#140 -> #116** (+24) | **#110 -> #142** (-32) |
| **Calibration Insight** | **Top-Tier Elite Stability:** Maintained top-10 tier retention despite a 5.12-point compression. | **Removal of Volume Cushion:** Lost rank due to removal of raw run accumulation rewards (11k+ runs). | **Era Resilience Jump:** Milder drop relative to the dataset allowed a huge +24 rank surge. | **Specialist Discipline Adjustment:** Heavy penalty for lower-order keeping baseline. |

### Case Study Insights:

* **Jacques Kallis (Stability At The Top):** Demonstrates **Tier Retention Integrity**. Despite losing 5.12 points in raw score under V2 tightening, Kallis only slipped 1 rank (from #8 to #9). The elite tier remained 90% stable overall.
* **Allan Border (Volume vs. Peak Impact):** Illustrates the impact of **removing the 5-point Longevity pillar**. Border's high career accumulation (11,174 runs) gave him a volume cushion in V1. In V2, stripping raw volume rewards caused his 45.68 average to drop him behind pure peak-impact players like **Everton Weekes** (who surged into the Top 10 with a 56.77 average).
* **NS Sidhu (The Relative Resilient Climber):** Shows how **Rankings are Relative while Scores are Absolute**. Sidhu lost 5.64 score points, yet **climbed 24 spots** (from #140 to #116). Because his 80s/90s era profile held up better than the global average deflation (-8.0+), he surpassed modern competitors whose scores dropped much harder.
* **Ian Healy (Role Baseline Penalization):** Illustrates V2’s stricter handling of non-specialist top-order batters. Hybrid roles lost an average of 10–12 points, pushing pure top-order runs to the top of the leaderboard.

---

## MODEL GOVERNANCE & VALIDATION SUMMARY

* **Tier Integrity:** 9 out of the Top 10 players maintained their Top 10 status in V2, proving the model recalibrated absolute values without creating arbitrary noise at the top.
* **Core Takeaway:** Under V2, **winning meant losing the fewest points**. Rank surges were driven by score resilience against global deflation.


