---
type: spec
capability: marginal-analysis
engagement: perfect-competition
date: 2026-10-14
status: draft
built-by:
---

# Perfect Competition — Model Specification

## 1. Purpose
This model determines the optimal seasonal allocation of 64 available land beds across three crops (tomatoes, carrots, and mesclun) to maximize net seasonal profit for a 1.5-acre market garden over a 36-week season. It provides a quantitative decision framework under binding land capacity (64 beds total vs. 70 bed caps) and labor capacity (6,480 total hours max), incorporating crop-specific compounding diminishing returns to evaluate marginal cost versus marginal revenue ($P = MC$).

## 2. Inputs, the named contract

| Variable Name | Value | Unit | Source in Case Scenario |
| :--- | :--- | :--- | :--- |
| `BED_CAP_TOMATOES` | 20 | beds | Case Scenario Table 1 ("The Givens") |
| `BED_CAP_CARROTS` | 20 | beds | Case Scenario Table 1 ("The Givens") |
| `BED_CAP_MESCLUN` | 30 | beds | Case Scenario Table 1 ("The Givens") |
| `REV_PER_BED_TOMATOES` | 8,800 | USD / bed | Case Scenario Table 1 ("The Givens") |
| `REV_PER_BED_CARROTS` | 2,094 | USD / bed | Case Scenario Table 1 ("The Givens") |
| `REV_PER_BED_MESCLUN` | 2,700 | USD / bed | Case Scenario Table 1 ("The Givens") |
| `BASE_HRS_TOMATOES` | 2.5 | hrs / wk / bed | Case Scenario Table 1 ("The Givens") |
| `BASE_HRS_CARROTS` | =5/6 | hrs / wk / bed | Case Scenario Table 1 (Exact fraction for 0.833) |
| `BASE_HRS_MESCLUN` | 1.25 | hrs / wk / bed | Case Scenario Table 1 ("The Givens") |
| `FERT_PER_BED_TOMATOES` | 880 | USD / bed | Case Scenario Table 1 ("The Givens") |
| `FERT_PER_BED_CARROTS` | 440 | USD / bed | Case Scenario Table 1 ("The Givens") |
| `FERT_PER_BED_MESCLUN` | 880 | USD / bed | Case Scenario Table 1 ("The Givens") |
| `DIM_RATE_TOMATOES` | 0.10 | rate (10%) | Case Scenario Table 1 ("The Givens") |
| `DIM_RATE_CARROTS` | 0.025 | rate (2.5%) | Case Scenario Table 1 ("The Givens") |
| `DIM_RATE_MESCLUN` | 0.0125 | rate (1.25%) | Case Scenario Table 1 ("The Givens") |
| `FARM_BEDS_AVAIL` | 64 | beds | Case Scenario Table 2 ("The Givens") |
| `SEASON_WEEKS` | 36 | weeks | Case Scenario Table 2 ("The Givens") |
| `FIXED_COSTS` | 20,000 | USD / season | Case Scenario Table 2 ("The Givens") |
| `OWNER_MAX_HRS` | 720 | hours / season | Case Scenario Table 2 ("The Givens") |
| `OWNER_HOURLY_RATE` | 34.72 | USD / hour | Case Scenario Table 2 (implied rate stated in case) |
| `TEMP_WORKERS_MAX` | 4 | workers | Case Scenario Table 2 ("The Givens") |
| `TEMP_WORKER_MAX_HRS` | 1,440 | hours / worker | Case Scenario Table 2 ("The Givens") |
| `TEMP_HOURLY_RATE` | 17.36 | USD / hour | Case Scenario Table 2 ("The Givens") |
| `TOTAL_LABOR_CAP` | 6,480 | hours | Case Scenario Table 2 (720 + 4 × 1,440 hrs) |

## 3. Structure

The workbook `model.xlsx` consists of two worksheets:
1. `Farm_Model`: Contains five contiguous named regions on a single worksheet:
   * `inputs`: Table housing all constant scenario parameters listed in Section 2.
   * `cost_structure`: Farm-level labor cascade, total labor cost computation, and the farm-wide blended labor rate.
   * `optimization`: Decision cells ($q_{tomatoes}, q_{carrots}, q_{mesclun}$), summary farm P&L, Solver configuration block, and explicit constraint-check cells.
   * `marginal_cost`: Bed-by-bed schedules ($q = 1 \dots \text{cap}$) for each crop calculating marginal labor hours, fertilizer costs, allocated labor costs via the blended rate, marginal costs ($MC$), constant marginal revenue ($P$), and cumulative contribution margin.
   * `checks`: Automated acceptance criteria audit suite.
2. `Documentation`: Metadata, versioning log, and audit findings summary.

## 4. Calculation logic

All calculation logic must use named-range notation. Cell addresses (e.g., `D5`, `E10`) are strictly prohibited.

1. **Labor Hours for $q$ Beds of a Crop**:
   `LABOR_HRS(q) = q * HRS_PER_BED * SEASON_WEEKS * (1 + DIM_PCT)^q`

2. **Carrot Base Hours Formula**:
   `HRS_PER_BED_CARROTS = 5/6`

3. **Crop Revenue for $q$ Beds**:
   `CROP_REVENUE(q) = q * REV_PER_BED`

4. **Crop Fertilizer Cost for $q$ Beds**:
   `FERT_COST(q) = q * FERT_PER_BED`

5. **Total Beds Planted**:
   `TOTAL_BEDS_PLANTED = BEDS_TOMATOES + BEDS_CARROTS + BEDS_MESCLUN`

6. **Total Farm Labor Hours Required**:
   `TOTAL_FARM_LABOR_HRS = LABOR_HRS_TOMATOES + LABOR_HRS_CARROTS + LABOR_HRS_MESCLUN`

7. **Owner Field Hours Consumed**:
   `OWNER_HRS_USED = MIN(TOTAL_FARM_LABOR_HRS, OWNER_MAX_HRS)`

8. **Temporary Worker Labor Hours Required**:
   `TEMP_HRS_NEEDED = MAX(0, TOTAL_FARM_LABOR_HRS - OWNER_MAX_HRS)`

9. **Total Farm Labor Expenditure ($)**:
   `TOTAL_LABOR_COST = (OWNER_HRS_USED * OWNER_HOURLY_RATE) + (TEMP_HRS_NEEDED * TEMP_HOURLY_RATE)`

10. **Farm-Wide Blended Labor Rate ($/hr)**:
    `BLENDED_LABOR_RATE = IF(TOTAL_FARM_LABOR_HRS > 0, TOTAL_LABOR_COST / TOTAL_FARM_LABOR_HRS, OWNER_HOURLY_RATE)`
    * *Timing & Evaluation*: `BLENDED_LABOR_RATE` is evaluated dynamically based on the total farm labor hours required by the active decision cells ($q_{tomatoes}, q_{carrots}, q_{mesclun}$).

11. **Crop Allocated Labor Cost ($)**:
    `CROP_LABOR_COST(q) = LABOR_HRS(q) * BLENDED_LABOR_RATE`

12. **Crop Total Variable Cost ($TVC$)**:
    `CROP_TVC(q) = FERT_COST(q) + CROP_LABOR_COST(q)`

13. **Marginal Labor Hours for Bed $q$**:
    `MARGINAL_LABOR_HRS(q) = LABOR_HRS(q) - LABOR_HRS(q - 1)` *(where `LABOR_HRS(0) = 0`)*

14. **Marginal Cost ($MC$) for Bed $q$**:
    `MARGINAL_COST(q) = FERT_PER_BED + (MARGINAL_LABOR_HRS(q) * BLENDED_LABOR_RATE)`
    * *Note*: Locate and note the tomato MC dip around 6 beds for Stage 3 analysis.

15. **Marginal Revenue ($P$) for Bed $q$**:
    `MARGINAL_REVENUE(q) = REV_PER_BED` *(constant for all $q$)*

16. **Total Farm Revenue ($TR$)**:
    `TOTAL_FARM_REVENUE = CROP_REVENUE_TOMATOES + CROP_REVENUE_CARROTS + CROP_REVENUE_MESCLUN`

17. **Total Farm Fertilizer Cost**:
    `TOTAL_FERTILIZER_COST = FERT_COST_TOMATOES + FERT_COST_CARROTS + FERT_COST_MESCLUN`

18. **Total Farm Variable Cost ($TVC$)**:
    `TOTAL_FARM_TVC = CROP_TVC_TOMATOES + CROP_TVC_CARROTS + CROP_TVC_MESCLUN`

19. **Net Seasonal Profit ($)**:
    `NET_SEASON_PROFIT = TOTAL_FARM_REVENUE - TOTAL_FARM_TVC - FIXED_COSTS`

20. **Crop Cumulative Contribution Margin ($)**:
    `CROP_CUMULATIVE_CONTRIBUTION(q) = CROP_REVENUE(q) - CROP_TVC(q)` *(Variable profit before fixed overhead)*

21. **Temporary Workers Hired Equivalent (FTEs)**:
    `TEMP_WORKERS_HIRED_EQUIVALENT = TEMP_HRS_NEEDED / TEMP_WORKER_MAX_HRS` *(Unrounded decimal fraction)*

22. **Standalone $P \approx MC$ Calculation Logic**:
    Evaluated for each crop in isolation by setting other crops to 0 beds and ignoring the 64-bed farm capacity limit (`FARM_BEDS_AVAIL`). `STANDALONE_PMC_CROP` is the largest integer bed count $q$ where single-crop `MARGINAL_COST(q) <= REV_PER_BED` and `MARGINAL_COST(q + 1) > REV_PER_BED`, applying the owner-to-temp labor rate cascade for that single crop's hours.

## 5. Conventions

* **Owner Priority (Permanent Labor First)**: The owner's 720 field hours (implied rate of $34.72/hr) are always consumed first before hiring temporary workers. Temporary workers cover any remaining labor hours at $17.36/hr up to their maximum capacity of 5,760 total hours (4 workers $\times$ 1,440 hours).
* **Hourly Billing for Temporary Workers**: Temporary labor is billed strictly per hour worked ($17.36/hr). Workers are NOT hired in indivisible 1,440-hour block contracts. The "up to 4 temporary workers" constraint is enforced as `TEMP_HRS_NEEDED <= 5760`.
* **Blended Labor Rate Allocation**: P&L schedules and $MC$ tables allocate labor costs using the farm-wide blended hourly labor rate (`TOTAL_LABOR_COST / TOTAL_FARM_LABOR_HRS`).
* **Owner Hourly Cost Inclusion in P&L**: Owner labor hours are included as an explicit cash/opportunity labor expense in the P&L at the implied rate of $34.72/hr for 720 hours. This accounting treatment is strictly required for the target net season profit check figure of $42,762 to reconcile.
* **Integer Decision Variables**: All crop decision variables ($q_{tomatoes}, q_{carrots}, q_{mesclun}$) must be non-negative integers ($\mathbb{Z}_{\ge 0}$).
* **Precision & Exact Values**: Carrot base hours are defined as `=5/6` in Excel formulas to prevent rounding errors. Intermediate calculations preserve floating-point precision. Displayed currency values round to whole dollars.
* **Solver Configuration Parameters**:
  * **Objective Cell**: `NET_SEASON_PROFIT` (Set to Maximize)
  * **Changing Variable Cells**: `BEDS_TOMATOES`, `BEDS_CARROTS`, `BEDS_MESCLUN`
  * **Engine**: GRG Nonlinear
  * **Constraints**:
    1. `BEDS_TOMATOES <= 20` (`BED_CAP_TOMATOES`)
    2. `BEDS_CARROTS <= 20` (`BED_CAP_CARROTS`)
    3. `BEDS_MESCLUN <= 30` (`BED_CAP_MESCLUN`)
    4. `TOTAL_BEDS_PLANTED <= 64` (`FARM_BEDS_AVAIL`)
    5. `TOTAL_FARM_LABOR_HRS <= 6480` (`TOTAL_LABOR_CAP`)
    6. `BEDS_TOMATOES, BEDS_CARROTS, BEDS_MESCLUN` = Integer $\ge 0$
  * **Multi-Start Optimization Audit**: Solver must be run independently from two distinct initial starting states:
    * Start State A: `(0, 0, 0)`
    * Start State B: `(20, 0, 0)`
    Both runs must converge to the identical global optimal solution `(10, 20, 30)`.
* **Handling Check Figure Discrepancies**: If a generated model fails any validation rule, the builder MUST NOT manually edit cell values in Excel. The specification file (`spec.md`) must be corrected/clarified and re-submitted to the AI builder.

## 6. Validation rules

The model must include 7 explicit boolean check indicator cells, all displaying `TRUE` with Green cell formatting:

1. `CHECK_OPTIMAL_MIX`: `AND(BEDS_TOMATOES = 10, BEDS_CARROTS = 20, BEDS_MESCLUN = 30)` -> `TRUE`
2. `CHECK_NET_PROFIT`: `ABS(NET_SEASON_PROFIT - 42762) <= 1` (Allows $1 tolerance for floating-point compounding precision) -> `TRUE`
3. `CHECK_STANDALONE_PMC`: `AND(ABS(STANDALONE_PMC_TOMATOES - 10) <= 1, ABS(STANDALONE_PMC_CARROTS - 10) <= 1, ABS(STANDALONE_PMC_MESCLUN - 6) <= 1)` (Allows ±1 bed tolerance aligning with published ~10 / ~10 / ~6 figures) -> `TRUE`
4. `CHECK_HAND_CALC_Q1`: `ABS(TOMATO_Q1_LABOR_HRS - 99.0) <= 0.001` (Allows 0.001 tolerance for floating-point calculation of $1 \times 2.5 \times 36 \times 1.10 = 99.0$ hours) -> `TRUE`
5. `CHECK_LABOR_CAP`: `TOTAL_FARM_LABOR_HRS <= 6480` -> `TRUE`
6. `CHECK_LAND_CAP`: `TOTAL_BEDS_PLANTED <= 64` -> `TRUE`
7. `CHECK_NO_ERRORS_AND_FORMULAS`: Zero `#REF!`, `#DIV/0!`, `#VALUE!`, `#NAME?` error cells AND all calculated cells contain dynamic formulas referencing named inputs -> `TRUE`

## 7. Outputs

* `OPTIMAL_BEDS_TOMATOES`: Optimal bed count for tomatoes (beds)
* `OPTIMAL_BEDS_CARROTS`: Optimal bed count for carrots (beds)
* `OPTIMAL_BEDS_MESCLUN`: Optimal bed count for mesclun (beds)
* `TOTAL_BEDS_PLANTED`: Total farm beds utilized (60 beds)
* `TOTAL_LABOR_HOURS_USED`: Total labor hours across all crops (hours)
* `OWNER_HOURS_UTILIZED`: Owner field labor hours used (720 hours)
* `TEMP_WORKER_HOURS_UTILIZED`: Temporary worker labor hours used (hours)
* `TEMP_WORKERS_HIRED_EQUIVALENT`: Temporary worker full-time equivalents used (`TEMP_HRS_NEEDED / 1440`, unrounded FTE)
* `FARM_BLENDED_LABOR_RATE`: Farm-wide blended labor hourly rate ($/hour)
* `TOTAL_FARM_REVENUE`: Gross seasonal revenue ($)
* `TOTAL_FERTILIZER_COST`: Total fertilizer expenditure ($)
* `TOTAL_LABOR_COST_DOLLARS`: Total labor expenditure ($)
* `TOTAL_VARIABLE_COST`: Combined labor and fertilizer cost ($)
* `FIXED_COST_DOLLARS`: Seasonal fixed overhead ($20,000)
* `NET_SEASON_PROFIT`: Final seasonal net profit ($42,762)
* `CROP_CUMULATIVE_CONTRIBUTION_TOMATOES`: Cumulative contribution margin for tomatoes ($)
* `CROP_CUMULATIVE_CONTRIBUTION_CARROTS`: Cumulative contribution margin for carrots ($)
* `CROP_CUMULATIVE_CONTRIBUTION_MESCLUN`: Cumulative contribution margin for mesclun ($)
* `STANDALONE_PMC_TOMATOES`: Standalone profit-maximizing bed count for tomatoes (10 beds)
* `STANDALONE_PMC_CARROTS`: Standalone profit-maximizing bed count for carrots (10 beds)
* `STANDALONE_PMC_MESCLUN`: Standalone profit-maximizing bed count for mesclun (6 beds)

## 8. Audit findings

*(To be completed post-build during review)*

| Check Executed | Target / Expectation | Result Observed | Pass / Fail | Action Taken / Notes |
| :--- | :--- | :--- | :--- | :--- |
| Hand calc ($q=1$ Tomato) | 99.0 hours | *[Pending]* | *[Pending]* | *[Pending]* |
| Farm Profit Lab Cross-check | Match intermediate MCs | *[Pending]* | *[Pending]* | *[Pending]* |
| Dual Solver Start Points | (0,0,0) vs (20,0,0) match | *[Pending]* | *[Pending]* | *[Pending]* |
| Check Figures Reconciled | 10/20/30 beds, $42,762 | *[Pending]* | *[Pending]* | *[Pending]* |
| Formula Integrity Audit | No hardcoded numbers in formulas | *[Pending]* | *[Pending]* | *[Pending]* |
