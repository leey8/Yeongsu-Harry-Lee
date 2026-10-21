# Perfect Competition — Marginal Analysis & Optimization Report

**Author**: Yeongsu (Harry) Lee  
**Date**: October 20, 2026  
**Capability**: `marginal-analysis`  
**Engagement**: `perfect-competition`  
**Workbook Reference**: `capabilities/marginal-analysis/model.xlsx` (Audited Commit `c27726e`)  

---

## Executive Summary

This study analyzes the profit-maximizing crop allocation for a 1.5-acre market garden operating under binding land capacity (64 beds total) and labor capacity (6,480 seasonal hours) across a 36-week season. Utilizing an Excel-based non-linear optimization model (`model.xlsx`) governed by precise marginal analysis ($P = MC$) and compounding labor diminishing returns, the farm achieves a maximum seasonal net profit of **$42,761.66** (`NET_SEASON_PROFIT`, Cell `Farm_Model!B51`). The optimal planting mix is **10 tomato beds**, **20 carrot beds**, and **30 mesclun beds** (`BEDS_TOMATOES`, `BEDS_CARROTS`, `BEDS_MESCLUN`, Cells `Farm_Model!B42:B44`), utilizing 60 of the 64 available land beds and 5,277.22 labor hours.

---

## 1. Where Does the Money Crop Stop, and Why There?

Tomatoes represent the farm's primary "money crop," generating gross revenue of **$8,800.00 per bed** (`REV_PER_BED_TOMATOES`, Cell `Farm_Model!B9`), which is over three times higher than mesclun ($2,700.00) and four times higher than carrots ($2,094.00). Despite this overwhelming top-line revenue advantage and an individual land cap of 20 beds (`BED_CAP_TOMATOES`, Cell `Farm_Model!B6`), the model stops planting tomatoes at **exactly 10 beds** (`BEDS_TOMATOES`, Cell `Farm_Model!B42`), leaving 10 potential tomato beds unplanted.

![Tomato Marginal Cost vs Price](figures/tomato-mc.png)

The decision variable governing this stopping point is not revenue per bed, but the relationship between marginal revenue ($P$) and marginal cost ($MC$). Tomatoes suffer from a severe 10% compounding diminishing-returns rate (`DIM_RATE_TOMATOES`, Cell `Farm_Model!B18`) applied against a high weekly base labor requirement of 2.5 hours/bed/week (`BASE_HRS_TOMATOES`, Cell `Farm_Model!B12`). 

As shown in the bed-by-bed schedule (`Farm_Model!A81:N101`):
* At **Bed 10**, the marginal cost of the 10th tomato bed under the farm-wide blended labor rate ($19.73/hr, Cell `Farm_Model!B37`) is **$9,253.92** (`MARGINAL_COST(10)`, Cell `Farm_Model!I91`). On a standalone basis using actual step-wise labor pricing, $MC(10)$ equals **$8,248.59** (Cell `Farm_Model!N91`), which remains profitable under the $8,800.00 price line.
* At **Bed 11**, the marginal cost spikes to **$10,551.88** under the blended rate (`MARGINAL_COST(11)`, Cell `Farm_Model!I92`) or **$9,390.72** under standalone step-wise labor pricing (Cell `Farm_Model!N92`).

Because the marginal cost of producing the 11th bed ($9,390.72–$10,551.88) significantly exceeds the constant marginal revenue of $8,800.00 ($MC > P$), planting an 11th tomato bed would destroy between $590.72 and $1,751.88 of farm profit. Therefore, economic marginalism ($P = MC$), rather than an administrative land cap fence, stops tomato production at 10 beds.

---

## 2. Which Constraints Bind, and What Would Relaxing One Be Worth?

An evaluation of all six farm constraints in the optimization block (`Farm_Model!A53:D59`) reveals which constraints actively limit profitability and which possess idle capacity at the optimum:

| Constraint Name | Model Cell | Constraint Expression | Status at Optimum | Slack / Idle Capacity | Shadow Price (Value of +1 Unit) |
| :--- | :--- | :--- | :--- | :--- | :--- |
| **Carrot Bed Cap** | `Farm_Model!B55` | `BEDS_CARROTS <= 20` | **Binding** | 0 beds | **+$234.65 / bed** |
| **Mesclun Bed Cap** | `Farm_Model!B56` | `BEDS_MESCLUN <= 30` | **Binding** | 0 beds | **+$69.78 / bed** |
| **Tomato Bed Cap** | `Farm_Model!B54` | `BEDS_TOMATOES <= 20` | **Slack** | 10 beds | $0.00 |
| **Total Land Capacity**| `Farm_Model!B57` | `TOTAL_BEDS_PLANTED <= 64`| **Slack** | 4 beds | $0.00 |
| **Total Labor Hours** | `Farm_Model!B58` | `TOTAL_FARM_LABOR_HRS <= 6480`| **Slack** | 1,202.78 hours | $0.00 |
| **Temporary Workers** | `Farm_Model!B38` | `TEMP_HRS_NEEDED <= 5760` | **Slack** | 1,202.78 hours | $0.00 |

### Economic Analysis of Binding vs. Fence Constraints
* **Carrots & Mesclun (Fence Constraints)**: Both crops stop strictly because of administrative caps (`BED_CAP_CARROTS = 20`, `BED_CAP_MESCLUN = 30`). At Bed 20 for carrots, marginal cost is **$1,859.35** (`MARGINAL_COST(20)`, Cell `Farm_Model!I124`), well below the $2,094.00 market price ($P > MC$). At Bed 30 for mesclun, marginal cost is **$2,630.22** (`MARGINAL_COST(30)`, Cell `Farm_Model!I157`), below the $2,700.00 market price. Economics wants to plant more of both crops; only the fence stops them.
* **Relaxation Value (Shadow Prices)**:
  * Relaxing the Carrot Cap by 1 bed adds **+$234.65** to profit ($P - MC = \$2,094.00 - \$1,859.35$).
  * Relaxing the Mesclun Cap by 1 bed adds **+$69.78** to profit ($P - MC = \$2,700.00 - \$2,630.22$).
* **Slack Constraints**: Land (60/64 beds used) and labor (5,277.22/6,480 hours used) both have remaining capacity. Relaxing a slack constraint adds **$0.00** to profit. Spending even a single dollar to acquire more land or hire additional labor capacity would be entirely wasteful.

---

## 3. Does Any Marginal Cost Curve Ever Fall, and What Would Explain It?

When mapping bed-by-bed marginal costs, the standalone marginal cost curve for tomatoes exhibits a distinct non-monotonic downward "dip" before resuming its upward trajectory.

![Standalone Marginal Cost Curves Across All Crops](figures/standalone-mc.png)

As recorded in the tomato schedule (`Farm_Model!A80:N101`):
* At **Bed 5**, standalone tomato labor reaches 724.73 cumulative hours, crossing the owner's 720-hour capacity threshold (`OWNER_MAX_HRS`, Cell `Farm_Model!B24`). Standalone marginal cost for Bed 5 is **$7,660.86** (`MARGINAL_COST(5)`, Cell `Farm_Model!N86`), priced at the owner's implied rate of **$34.72/hr** (`OWNER_HOURLY_RATE`, Cell `Farm_Model!B25`).
* At **Bed 6**, standalone marginal cost **dips sharply to $4,906.28** (`MARGINAL_COST(6)`, Cell `Farm_Model!N87`), a drop of $2,754.58 per bed.

### Economic Explanation of the Dip
Physical diminishing returns do not reverse; the 10% compounding penalty continues to increase physical labor hours per bed (increasing from 197.65 marginal hours at Bed 5 to 231.91 marginal hours at Bed 6, Cells `Farm_Model!D86:D87`). Instead, the **input price of labor changes**.

Because owner labor is priced at $34.72/hr while temporary labor is billed at $17.36/hr (`TEMP_HOURLY_RATE`, Cell `Farm_Model!B28`), the cost of the next hour of work drops by 50% once owner hours are exhausted. This structural wage rate decrease temporarily offsets physical diminishing returns. By Bed 7, compounding labor hour growth (271.05 hrs) overcomes the lower wage rate, driving $MC$ back up to $5,585.71.

This phenomenon proves that a Marginal Cost curve does not merely measure physical engineering productivity; it measures **economic opportunity cost**—the dollar cost of marginal output, combining both physical returns and input factor prices.

---

## 4. Would Each Crop Make Money on Its Own, and Does That Matter?

To evaluate standalone viability, we test whether each crop covers the farm's $20,000.00 fixed overhead (`FIXED_COSTS`, Cell `Farm_Model!B23`) when grown in isolation at its optimal acreage:

| Crop | Optimal Beds | Cumulative Contribution (`CROP_CUMULATIVE_CONTRIBUTION`) | Standalone Net Profit (Less $20,000 Fixed Overhead) | Standalone Viable? |
| :--- | :--- | :--- | :--- | :--- |
| **Tomatoes** | 10 beds | **$33,143.42** (Cell `Farm_Model!H42`) | **+$13,143.42** | **Yes** |
| **Carrots** | 20 beds | **$13,682.27** (Cell `Farm_Model!H43`) | **-$6,317.73** | **No** |
| **Mesclun** | 30 beds | **$15,935.98** (Cell `Farm_Model!H44`) | **-$4,064.02** | **No** |

### Relevant Costs in Planting Decisions
If evaluated against Average Total Cost ($ATC$) or full overhead allocation, carrots and mesclun appear to be "losing money" and candidate crops for elimination. However, applying full fixed costs to individual bed planting decisions is a fundamental accounting error.

Fixed costs ($20,000.00) are sunk and owed regardless of planting choices. The relevant decision rule for planting an incremental bed is **Marginal Analysis ($P \ge MC$)** and **Average Variable Cost ($P \ge AVC$)**:
* Carrots generate $13,682.27 in positive contribution margin above variable labor and fertilizer expenses.
* Mesclun generates $15,935.98 in positive contribution margin.

Together, carrots and mesclun contribute **$29,618.25** toward covering the farm's fixed costs. Eliminating them because they fail a standalone $ATC$ test would reduce total farm variable contribution to $33,143.42 (tomatoes only), causing seasonal net profit to collapse from **$42,761.66** down to **$13,143.42**—a severe profit loss of $29,618.24. Price exceeding Marginal Cost ($P > MC$) and Average Variable Cost ($P > AVC$) settles the planting decision, completely independent of fixed overhead.

---

## 5. Hypothesis Comparison & Self-Audit

In the Stage 1 Brief, I hypothesized an optimal allocation of **30 mesclun beds, 20 carrot beds, and 14 tomato beds**, predicting that severe compounding labor penalties would cause tomato marginal cost to overtake marginal revenue past 14 beds.

The model falsified my specific tomato prediction: the true optimal allocation is **10 tomato beds** (4 beds lower than hypothesized), producing a maximum net profit of **$42,762.00**.

### What My Prior Got Wrong
My Stage 1 hypothesis calculated tomato marginal cost in isolation using the temporary worker rate of $17.36/hr, finding that single-crop $MC$ crossed $8,800.00 between Bed 14 ($8,800 vs. $13,826 $MC$) and Bed 15. What my prior failed to account for was **farm-wide labor aggregation across multiple crops**.

When 20 carrot beds (983.17 hrs) and 30 mesclun beds (1,959.68 hrs) are planted alongside tomatoes, total farm labor reaches 5,277.22 hours. Because labor is an aggregated resource, the farm-wide blended labor rate climbs to **$19.73/hr** (`BLENDED_LABOR_RATE`, Cell `Farm_Model!B37`). Under this higher blended wage rate, tomato marginal cost reaches $9,253.92 at Bed 10 and $10,551.88 at Bed 11, causing marginal cost to overtake price 4 beds earlier than my standalone calculation predicted. Precision in modeling farm-wide resource interactions corrected my initial intuition.
