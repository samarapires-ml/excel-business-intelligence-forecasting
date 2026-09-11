# What-If Analysis and Scenario Modeling

## 1. Objective

The purpose of this analysis was to extend the Business Intelligence model from descriptive and predictive analytics into decision-oriented analysis.

The model evaluates how changes in two important business drivers affect projected monthly revenue:

- Core Orders
- Average Order Value (AOV)

This allows business users to explore questions such as:

- What happens to revenue if order volume falls?
- What happens if customers spend more per order?
- What if both order volume and AOV change simultaneously?
- How much order growth is required to achieve a specific revenue target?

---

## 2. Base Scenario

The scenario model uses October 2011 as a representative recent full-month operating baseline.

| Metric | Base Value |
|---|---:|
| Base Core Orders | 2,097 |
| Base AOV | ₹528.11 |
| Order Change % | 0% |
| AOV Change % | 0% |
| Projected Orders | 2,097 |
| Projected AOV | ₹528.11 |
| Projected Revenue | ₹11,07,446.67 |

Projected revenue is calculated as:

Projected Revenue = Projected Orders × Projected AOV

The projected values are driven by adjustable assumptions:

Projected Orders = Base Core Orders × (1 + Order Change %)

Projected AOV = Base AOV × (1 + AOV Change %)

---

## 3. Scenario Analysis

Three business scenarios were evaluated.

| Scenario | Order Change | AOV Change | Projected Revenue |
|---|---:|---:|---:|
| Downside | -10% | -5% | ₹9,46,866.90 |
| Base | 0% | 0% | ₹11,07,446.67 |
| Upside | +10% | +5% | ₹12,79,100.90 |

### Interpretation

The downside scenario demonstrates the combined effect of weaker order volume and lower customer spending.

The upside scenario demonstrates that simultaneous improvement in transaction volume and average order value produces a compounding revenue effect.

Revenue therefore depends on both:

1. how many orders the business generates; and
2. how much revenue is generated per order.

---

## 4. One-Variable Sensitivity Analysis

A one-variable Data Table was created to measure the effect of changing order volume while holding AOV constant.

Order Change % was evaluated from -20% to +20%.

| Order Change | Projected Revenue |
|---|---:|
| -20% | ₹8,85,957.34 |
| -15% | ₹9,41,329.67 |
| -10% | ₹9,96,702.00 |
| -5% | ₹10,52,074.34 |
| 0% | ₹11,07,446.67 |
| +5% | ₹11,62,819.00 |
| +10% | ₹12,18,191.34 |
| +15% | ₹12,73,563.67 |
| +20% | ₹13,28,936.00 |

This analysis isolates the effect of order volume on projected revenue.

Because AOV remains constant, revenue changes proportionally with order volume.

---

## 5. Two-Variable Sensitivity Analysis

A two-variable Data Table was created to analyze simultaneous changes in:

- Order Change %
- AOV Change %

Both variables were tested from -10% to +10%.

Selected results include:

| Order Change | AOV Change | Projected Revenue |
|---|---:|---:|
| -10% | -10% | ₹8,97,031.80 |
| 0% | 0% | ₹11,07,446.67 |
| +10% | +10% | ₹13,40,010.47 |

The sensitivity matrix demonstrates that revenue responds to both business drivers simultaneously.

When both drivers improve, their effects compound rather than simply add.

For example:

1.10 × 1.10 = 1.21

Therefore, a 10% increase in both orders and AOV produces approximately a 21% increase in revenue.

---

## 6. Goal Seek

Excel Goal Seek was used to reverse the model.

Instead of asking:

"What revenue will result from a particular order-growth assumption?"

the analysis asked:

"What order growth is required to reach a target revenue?"

A monthly projected revenue target of:

₹12,00,000

was selected.

Holding AOV constant at ₹528.11, Goal Seek determined that approximately 8% growth in order volume would be required.

This corresponds to approximately:

2,272 projected orders.

Goal Seek demonstrates how the model can support target-based planning rather than only forward scenario simulation.

---

## 7. Excel What-If Analysis Tools Used

The analysis demonstrated several Excel decision-modeling tools:

- Scenario modeling
- Scenario Manager
- One-variable Data Tables
- Two-variable Data Tables
- Goal Seek

These tools answer different business questions.

### Scenario Manager

Used to compare a small number of named business cases such as:

- Downside
- Base
- Upside

### One-Variable Data Table

Used to test many possible values for one assumption while keeping other assumptions constant.

### Two-Variable Data Table

Used to evaluate combinations of two assumptions simultaneously.

### Goal Seek

Used to determine the input required to achieve a specified output.

---

## 8. Business Insights

The analysis produced several key insights.

### Revenue has two primary operating levers

Revenue can be increased through:

- higher order volume;
- higher average order value; or
- a combination of both.

### Combined improvements have a compounding effect

Increasing both orders and AOV creates a larger impact than changing either variable independently.

### Downside exposure can be quantified

The sensitivity analysis provides a structured way to estimate revenue risk under weaker business conditions.

### Revenue targets can be translated into operating requirements

Goal Seek converts a financial objective into an actionable operational target.

For example, reaching approximately ₹12 lakh in monthly revenue at the current AOV requires roughly 8% order growth.

---

## 9. QA Checks

The scenario model was validated using several consistency checks.

### Base-case validation

At:

- Order Change = 0%
- AOV Change = 0%

the model returns:

₹11,07,446.67

which matches the baseline projected revenue.

### Two-variable table validation

The 0% / 0% intersection returns:

₹11,07,446.67

confirming that the sensitivity table correctly links to the underlying model.

### One-variable table validation

The 0% order-change row also returns:

₹11,07,446.67.

### Directional validation

Increasing either orders or AOV increases projected revenue.

Decreasing either variable reduces projected revenue.

This confirms that the model behaves according to the expected business relationship.

---

## 10. Analytical Workflow

This stage extends the project from forecasting into business decision support.

The workflow can be summarized as:

Historical Performance → Forecasting → Business Assumptions → Scenario Modeling → Sensitivity Analysis → Target Planning → Decision Support