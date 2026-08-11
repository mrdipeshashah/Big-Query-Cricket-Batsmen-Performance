
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

### 1. Opposition & Team Impact (25.0 Pts) — [REDUCED from 35.0]
* **The Goal:** Measures "Carrying the Team" by rewarding players who dominated regardless of their teammates' failures or the era's bowling difficulty. Reduced by 10 points in V2 to balance relative peer weighting and prevent team context from over-dominating peer relativity.

### 2. Match-Winner Score (25.0 Pts) — [KEPT at 25.0]
* **The Goal:** Quantifies the "X-Factor"—the ability to take the match away from the opposition at high speed and convert matches into wins.

### 3. Elite Attack Resistance (20.0 Pts) — [KEPT at 20.0]
* **The Goal:** Filters out "cheap runs" by isolating performance frequency specifically against ICC Top-5 ranked bowling attacks and elite individual bowlers.

### 4. Era Dominance Index (15.0 Pts) — [UPGRADED from 5.0]
* **The Goal:** The primary shift in V2! Tripled in weight to prioritize relative peer dominance over simple volume. Evaluates standard deviations above decade peers using Z-Scores, locked to a player's primary debut era to isolate peak performance.

### 5. Situational Pressure Value (10.0 Pts) — [REDUCED from 15.0]
* **The Goal:** Rewards "Clutch" performances in high-stakes environments (4th innings chases, series deciders, and stopping team collapses).

### 6. Longevity & Volume (5.0 Pts) — [KEPT at 5.0]
* **The Goal:** A minimal career baseline reward that prevents pure run accumulation from overshadowing peak quality and era dominance.

The key changes from v1 v v2: 

### 1. Situational Pressure Value: Increased from 10.0 Pts $\rightarrow$ 15.0 Pts (+5.0 Pts)
### 2. Era Dominance Index: Increased from 5.0 Pts $\rightarrow$ 10.0 Pts (+5.0 Pts)

These changes ensures "Impact Over Accumulation." removing raw volume rewards ensures the model strictly measures peak quality, dominance over peers, and clutch performance rather than just career duration.

## MODEL COMPARISON: V1 vs V2 IN ACTION

With **V2 removing manual overrides to focus strictly on structural re-weighting** (capping longevity at 5% and re-balancing the 100-point total score across 25/25/20/15/10/5), global scores compressed uniformly across the dataset. However, relative rank shifts reveal how different player profiles responded once pure volume inflation was removed:

| Metric / Player | Jacques Kallis (Top Tier Anchor) | Allan Border (Volume vs Impact) | SM Katich (Peak Efficiency Climber) | S Chanderpaul (Accumulator Tax) |
| :--- | :---: | :---: | :---: | :---: |
| **Anchor Era** | 1990s | 1970s | 2000s | 1990s |
| **V1 Score -> V2 Score** | 79.56 -> 69.65 | 78.07 -> 67.02 | 68.10 -> 58.36 | 77.03 -> 65.35 |
| **Absolute Score Delta** | **-9.91** | **-11.05** | **-9.74** | **-11.68** |
| **V1 Rank -> V2 Rank** | **#7 -> #7** (0) | **#9 -> #9** (0) | **#107 -> #89** (+18) | **#11 -> #17** (-6) |
| **Calibration Insight** | **Top-Tier Elite Stability:** Maintained #7 spot despite global baseline compression. | **Structural Holding Power:** Retained #9 spot; elite rate metrics balanced volume loss. | **Efficiency Surge:** Less severe score drop relative to global average enabled an +18 rank surge. | **Removal of Volume Cushion:** Dropped 6 spots as V1's 35% run accumulation reward was removed. |

---

### Case Study Insights:

* **Jacques Kallis & Top 10 (Elite Tier Stability):** Demonstrates **Tier Retention Integrity**. Despite losing 9.91 points due to the baseline model compression, Kallis maintained his exact position at **#7**. In fact, the entire **Top 10 order remained 100% identical** (0 rank delta from Sangakkara at #1 down to Gavaskar at #10), proving that elite legends do not rely on volume inflation to justify their ranking.
* **SM Katich (The Efficiency Climber):** Highlights how **Rankings are Relative while Scores are Absolute**. Katich lost 9.74 points in raw score, yet **climbed 18 spots** (from #107 to #89). Under V2's 5% longevity cap, mid-tier players with high peak averages and efficient scoring rates surged past high-volume accumulators who lacked dominant rate metrics.
* **Shivnarine Chanderpaul (The Accumulator Tax):** Illustrates the direct impact of **capping longevity at 5%**. In V1, Chanderpaul's massive career total (11,867 runs across 164 Tests) provided a heavy volume cushion that elevated him to #11. In V2, stripping away raw accumulation rewards dropped him 6 spots to #17, aligning him with his true peer-relative impact tier.
* **Ricky Ponting (+4 Climber, #16 -> #12):** Demonstrates how lowering the volume tax allows peak dominance to shine. Ponting's peak period rate stats and match-winning contributions carried him closer to the Top 10 once raw duration was de-emphasized.
* **Harry Brook & Recent Debuts (The V3 Precursor):** Highlights the limits of a pure re-weighting model on active/recent players. With a score delta of -13.51 (-12 rank drop), recent players lack the career runway to build volume under V1 legacy rules. This N=1 sample distortion in the 2020s cohort establishes the exact business case for **V3's algorithmic era governance**.

---

## MODEL GOVERNANCE & VALIDATION SUMMARY

* **V2 Baseline Alignment:** The recalibration in V2 established a stronger analytical foundation by prioritizing genuine match impact over sheer run accumulation. Capping longevity at 5% shifted the model's focus toward peak efficiency over total career volume.
* **Top-End Preservation:** 100% of the Top 10 retained their exact rank positions (0 movement), confirming that V2 fixed structural volume biases without introducing arbitrary noise at the top of the leaderboard.
* **Bridge to V3:** V2 acted as an essential control variable by isolating the **weighting fix** (what we measure: Quality over Volume). This clean, rate-first canvas ensures that **V3** can safely introduce era governance and midpoint rules (when they played) without inflating raw run bulk.
