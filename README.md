Data Engineering Flow:

Extraction: Raw historical cricket data harvested into Google Sheets.

Transformation: Python/Pandas scripts in Google Colab used for cleaning, handling nulls, and initial feature engineering.

Loading: Data pushed via the google-cloud-bigquery library into BigQuery for high-performance SQL analysis.

Modeling: Final "Master" tables created in SQL using CTEs and normalization to feed Looker Studio.

Data Dictionary

Core Dimensions (The "Who & When")
Player / Country: The primary identifiers for the athlete and their national team.

Career: The total span of the player's active years (e.g., 1990-2006).

Career_Start / Career_End: Integer values extracted from the career span, used for timeline analysis and cohort grouping.

Era: Categorical classification based on debut year (e.g., '90s', '10s') to compare players across different technological and pitch-condition epochs.

Longevity_Tier: A qualitative bucket (Short Peak to Iron Man) based on the total number of years active.

Batting_Style: An "Intent Index" that categorizes players (Aggressor to Blocker) based on their career Strike Rate.

Primary Metrics (The "Raw Data")
Runs / Average / Rating: Traditional performance metrics including total volume, per-innings consistency, and normalized performance ratings.

Strike_Rate: Scoring speed; used to differentiate between anchors and accelerators.

Century_Frequency: The number of innings played per century scored (lower is better).

MOM (Man of the Match): Total count of match-winning individual performances.

WinsR / WinsS: Win-specific metrics; Win-Run percentage (Runs in wins / Total runs) and Strike Rate during winning matches.

Team_Runs: The total runs scored by the player's team during their career; used to calculate relative contribution.

Advanced Calculations (The "Analysis")
wba_raw: Weighted Batting Average; a combined metric looking at a player's performance relative to both their teammates and their opposition.

Cons_Raw: A consistency coefficient that penalizes high variance and rewards steady output.

Bow_Q_Raw: A "Bowling Quality" factor that adjusts a player's score based on the strength of the bowling attacks faced in their specific era.

IPV_Raw: Independent Player Value; a "Peak Impact" metric isolating a player's performance from team context during their best years.

The Final Output
Final_GOAT_Score: A composite, weighted index (0-100) that aggregates Longevity, Peak Impact, Consistency, and X-Factor. This is the primary sorting metric for the leaderboard.

The GOAT Index: 100-Point Distribution
To identify the greatest of all time, the model aggregates performance across six core "Pillars." Each pillar is normalized against the all-time statistical leader in that category to ensure a fair 0–100 scale.

1. Longevity & Volume (12.5% / 12.5 Pts)
Metric: Career Total Runs.

Logic: Recognizes the "Iron Men" of the game. While runs aren't everything, the ability to maintain world-class form over 10,000+ runs is a mandatory requirement for GOAT status.

2. Consistent Dominance (10% / 10 Pts)
Metric: ICC/Standardized Performance Rating.

Logic: Uses historical ratings to reward players who maintained the "World #1" or "Elite" status for the longest periods during their active years.

3. The X-Factor (20% / 20 Pts)
Metric: Combined Strike Rate, Century Frequency, and Man of the Match (MOM) Awards.

Logic: This separates "Accumulators" from "Match Winners." It rewards players who score fast, score big centuries often, and are the primary reason their team wins individual matches.

4. Weighted Batting Achievement - WBA (32.5% / 32.5 Pts)
Metric: Average Relative to Team + Average Relative to Opposition.

Logic: The "Hero" Metric. This is the heaviest weight in the model. It asks: "How much better were you than your teammates and the era you played in?" This ensures a player who averaged 50 on "minefield" pitches in the 90s is valued more than one who averaged 50 on "flat tracks" in the 2010s.

5. Reliability & Quality (15% / 15 Pts)
Metric: Consistency Raw + Bowling Quality Raw.

Logic: Rewards players who performed consistently against high-quality bowling attacks. It penalizes "stat-padding" against weak opposition and rewards those who stood up against the best in the world.

6. Peak Impact Value - IPV (10% / 10 Pts)
Metric: Independent Player Value (IPV).

Logic: Measures a player's "Peak" (their best 3–5 year window) to ensure that legends with shorter but legendary peaks (like Don Bradman or Viv Richards) aren't buried by those who simply played longer.

# The Math Behind the Ranking

To ensure a fair comparison across different eras and playing styles, every player’s raw statistics are converted into a **Weighted Pillar Score**. We use a **Max-Ratio Normalization** formula, which benchmarks every player against the "Gold Standard" (the all-time statistical leader) in each specific category.

### **The Core Formula**

This formula ensures that the "Best in Class" for any category receives 100% of the available points for that pillar, with all other players ranked proportionally behind them:

$$Score = \left( \frac{Player\ Metric}{Max\ Global\ Metric} \right) \times Pillar\ Weight$$

---

### **Calculation Examples**

To see how this works in practice, here are three examples of how points are awarded across different pillars:

#### **1. Longevity & Volume (Weight: 12.5 Pts)**
* **The Benchmark (Max):** Sachin Tendulkar (~15,921 runs)
* **Player Example:** Brian Lara (~11,953 runs)
* **The Math:** `(11,953 / 15,921) * 12.5`
* **Points Awarded:** **9.38 / 12.5**

#### **2. The X-Factor (Weight: 20 Pts)**
* **The Benchmark (Max):** A theoretical top score of **85.0** (combined Strike Rate + Centuries + MOM).
* **Player Example:** A modern aggressive batsman with a combined score of **72.0**.
* **The Math:** `(72.0 / 85.0) * 20`
* **Points Awarded:** **16.94 / 20**
* *Insight: This allows high-impact modern players to remain competitive with high-volume legends.*

#### **3. Weighted Batting Achievement - WBA (Weight: 32.5 Pts)**
* **The Benchmark (Max):** The highest "Era-Adjusted" score in the database (e.g., **2.8**).
* **Player Example:** A player with a WBA raw score of **2.1** (indicating they were significantly better than their peers).
* **The Math:** `(2.1 / 2.8) * 32.5`
* **Points Awarded:** **24.37 / 32.5**

---

### **Dynamic Benchmarking**
The "Max Global Metric" is not a fixed number; it is dynamically identified by the SQL script from the current dataset. As active players like Joe Root or Virat Kohli increase their career totals or improve their averages, the benchmarks adjust, ensuring the **Final GOAT Score** is always a reflection of the total history of the game.







