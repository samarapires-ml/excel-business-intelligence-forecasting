# Optimization Analysis

## Objective

The optimization model determines how a fixed marketing budget should be allocated across multiple marketing channels to maximize projected revenue.

The model was implemented using Excel Solver and demonstrates how optimization techniques can support business resource-allocation decisions.

## Marketing Channels

Three hypothetical marketing channels were evaluated:

| Channel | Cost per Order | Revenue per Order | Maximum Orders |
|---|---:|---:|---:|
| Email Marketing | 40 | 260 | 1,200 |
| Paid Search | 85 | 310 | 900 |
| Social Media | 60 | 285 | 1,000 |

Marketing Budget: 100,000

These values are modeling assumptions created for the optimization exercise rather than historical values from the Online Retail II dataset.

## Decision Variables

The decision variables represent the number of orders allocated to each marketing channel.

Solver determines these values while respecting the defined business constraints.

## Objective Function

The objective is to maximize:

Projected Revenue

where:

Projected Revenue = Orders × Revenue per Order

Total projected revenue is calculated as the sum of projected revenue across all marketing channels.

## Constraints

The optimization model includes the following constraints:

- Total Marketing Cost ≤ Marketing Budget
- Orders for each channel ≤ Maximum Orders
- Orders must be non-negative
- Orders must be integers

Because the model is linear, the Simplex LP solving method was used.

## Unrestricted Optimal Solution

Without requiring minimum investment in every marketing channel, Solver produced a maximum projected revenue of:

**558,860**

using the full marketing budget of:

**100,000**

The solution prioritized Email Marketing and Social Media because they generated greater revenue per unit of marketing spend than Paid Search.

## Business Constraint Scenario

A second optimization was performed requiring at least 100 Paid Search orders.

The resulting allocation was:

| Channel | Orders | Marketing Cost | Projected Revenue |
|---|---:|---:|---:|
| Email Marketing | 1,200 | 48,000 | 312,000 |
| Paid Search | 100 | 8,500 | 31,000 |
| Social Media | 725 | 43,500 | 206,625 |

Total Marketing Cost: **100,000**

Total Projected Revenue: **549,625**

## Opportunity Cost

Introducing the Paid Search requirement reduced projected revenue from:

558,860 → 549,625

Opportunity Cost:

558,860 - 549,625 = **9,235**

This represents approximately a **1.65% reduction in projected revenue**.

## Business Interpretation

The unrestricted model maximizes revenue by prioritizing the channels with the strongest revenue generated per unit of marketing spend.

However, businesses may intentionally maintain investment across multiple channels for reasons such as customer acquisition, brand visibility, channel diversification, or strategic positioning.

The constrained optimization demonstrates how these business requirements can be incorporated into an optimization model while quantifying their financial trade-offs.

## Excel Skills Demonstrated

- Excel Solver
- Linear optimization
- Integer constraints
- Decision-variable modeling
- Objective functions
- Budget constraints
- Capacity constraints
- Scenario comparison
- Opportunity-cost analysis
- Business interpretation of optimization results