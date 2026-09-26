---
type: brief
engagement: perfect-competition
capability: marginal-analysis
date: 2026-09-26
status: committed
hypothesis: "Mesclun & carrot-heavy mix; minimizing compounding labor penalties yields higher net profit"
---

# Perfect Competition — engagement brief

## The problem
The farmer must decide how to allocate 64 available land beds across three crops (tomatoes, carrots, and mesclun) for the upcoming 36-week season. 
Once committed, the planting decision cannot be taken back in July. 
A bad decision will result in severe labor bottlenecks, exponential labor cost spikes caused by diminishing returns, and substantial financial losses or lost profit opportunities.

Market prices (revenues per bed), season length (36 weeks), fixed costs ($20,000), crop bed caps, base weekly labor hours per bed, fertilizer costs, diminishing-return rates, and available worker capacity (owner's 720 hours plus up to 4 temporary workers) are all fixed givens handed to the farmer. 
The only decision variable under direct control is the number of beds allocated to each crop. 
Choices are constrained by total farm land capacity (64 beds, which is less than the sum of individual crop caps at 70 beds), individual crop bed caps, and total available temporary labor hours.

## What I am assuming
The farm is a price taker in a perfectly competitive market and cannot move market prices regardless of volume produced. 
Total labor hours for `q` beds of a crop follow the compounding labor curve Labor(q) = q × hrs/wk/bed × 36 × (1 + dim)^q.
Temporary workers can be hired incrementally as needed at $17.36/hr up to their capacity limit.

The assumptions I would most want to test if I had more time
Risk of a market price collapse during periods of high-volume shipments.
Potential to reduce the rate of diminishing returns through increased operational proficiency and process improvements.
Risk of fluctuations in yield and costs for individual crops due to factors such as weather, pests, and diseases.

## Hypothesis
I expect 30 mesclun beds, 20 carrot beds, and 14 tomato beds.
Because mesclun (1.25%) and carrots (2.5%) have drastically lower diminishing-returns rates than tomatoes (10%), ensuring that avoiding the severe compounding labor penalty will keep marginal costs low and deliver a higher seasonal net profit.

## How I would know I was wrong
This hypothesis will be falsified if the Solver model shows that the optimal tomato allocation significantly exceeds 14 beds (or reaches its 20-bed cap) while scaling back mesclun or carrot beds.
