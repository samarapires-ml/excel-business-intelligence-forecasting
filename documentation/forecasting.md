# Revenue Forecasting

## 1. Objective

The objective of this analysis was to forecast monthly Core Sales Revenue for Nexora Commerce and compare multiple forecasting approaches using out-of-sample performance.

Rather than selecting a forecasting method based only on how well it fit historical data, multiple models were evaluated on a holdout period using standard forecast accuracy metrics.

The forecasting workflow included:

1. Monthly time-series preparation
2. Naïve forecasting
3. Three-month moving average forecasting
4. Linear trend forecasting
5. Exponential Triple Smoothing (ETS)
6. Holdout validation
7. Forecast accuracy comparison
8. Final model selection
9. Confidence interval estimation
10. Forecast visualization and interpretation

---

## 2. Monthly Time Series

Monthly Core Sales Revenue was extracted from the Excel Data Model using a PivotTable.

The analysis period runs from:

- December 2009
- through December 2011

The original `YearMonth` field was converted into a true Excel date so that Excel's forecasting functions could correctly interpret the observations as a chronological time series.

A true date field is important because forecasting functions require an ordered numerical timeline rather than text labels.

---

## 3. Naïve Forecast

The first model created was a naïve forecast.

Under this method:

> Forecast for the current month = Actual revenue from the previous month

For example, January 2010 revenue was forecast using December 2009 revenue.

The naïve model provides an important baseline because a more sophisticated forecasting model should ideally outperform a simple assumption that the next period will resemble the previous period.

Forecast error was calculated as:

> Forecast Error = Actual Revenue - Forecast Revenue

Positive errors indicate that actual revenue exceeded the forecast, while negative errors indicate that the forecast exceeded actual revenue.

---

## 4. Three-Month Moving Average

A three-month moving average forecast was created to smooth short-term fluctuations.

The model forecasts each month using the average revenue from the previous three months.

Conceptually:

> Forecast(t) = Average of Actual(t-1), Actual(t-2), Actual(t-3)

Moving averages reduce short-term noise but may react slowly when the underlying business changes rapidly.

This became particularly relevant during periods of strong seasonal revenue growth.

---

## 5. Linear Forecast

A linear forecasting model was created using Excel's `FORECAST.LINEAR` function.

The model estimates a linear relationship between time and monthly revenue and extrapolates that trend into future periods.

Linear forecasting is useful when a time series follows a relatively stable upward or downward trend.

However, retail revenue often contains seasonality and nonlinear changes that cannot be represented well by a single straight-line trend.

---

## 6. ETS Forecast

The final advanced forecasting method used Excel's:

`FORECAST.ETS`

ETS stands for Exponential Triple Smoothing.

Unlike a simple linear trend or moving average, ETS can model patterns such as:

- level
- trend
- seasonality

This makes it particularly useful for business time series where recurring seasonal behavior may exist.

The historical revenue data showed substantial seasonal variation, including strong revenue increases during later months of the year.

---

## 7. Holdout Validation

Forecasting models should not be evaluated only on the same observations used to build them.

A holdout period was therefore created using:

**June 2011 through November 2011**

For each month in the holdout period, predictions from the four forecasting approaches were compared against actual Core Sales Revenue.

The evaluated models were:

- Naïve Forecast
- Three-Month Moving Average
- Linear Forecast
- ETS Forecast

December 2011 was intentionally excluded from holdout evaluation because the source dataset ends on December 9, 2011 and therefore does not contain a complete month of actual revenue.

---

## 8. Forecast Accuracy Metrics

Three standard forecasting metrics were calculated.

### Mean Absolute Error (MAE)

MAE calculates the average absolute difference between actual and forecast values.

> MAE = Average(|Actual - Forecast|)

It expresses forecast error in the same units as the target variable.

Lower MAE indicates better forecast accuracy.

### Root Mean Squared Error (RMSE)

RMSE squares forecast errors before averaging them and then takes the square root.

Conceptually:

> RMSE = SQRT(Average((Actual - Forecast)^2))

Because large errors are squared, RMSE penalizes large forecasting mistakes more heavily than MAE.

Lower RMSE is better.

### Mean Absolute Percentage Error (MAPE)

MAPE expresses the average absolute forecasting error as a percentage of actual revenue.

> MAPE = Average(|Actual - Forecast| / Actual)

MAPE makes forecast accuracy easier to communicate because it is expressed as a percentage.

Lower MAPE indicates better performance.

---

## 9. Model Comparison

The holdout evaluation produced approximately the following results:

| Model | MAE | RMSE | MAPE |
|---|---:|---:|---:|
| Naïve | 136,793 | 194,230 | 12.17% |
| 3-Month Moving Average | 204,010 | 272,497 | 17.85% |
| Linear | 229,322 | 286,998 | 21.22% |
| ETS | 43,904 | 63,004 | 4.30% |

ETS produced the lowest error across all three evaluation metrics.

Its approximately 4.3% MAPE was substantially lower than the errors produced by the naïve, moving-average, and linear models.

Therefore, ETS was selected as the final forecasting model.

---

## 10. December 2011 Forecast

The selected ETS model produced a December 2011 monthly revenue forecast of approximately:

**₹1,234,273**

or approximately:

**₹12.34 lakh**

The point forecast represents the model's best estimate of full-month Core Sales Revenue.

---

## 11. Forecast Uncertainty

A point forecast alone does not communicate the uncertainty surrounding a prediction.

Excel's `FORECAST.ETS.CONFINT` function was therefore used to estimate a confidence interval around the ETS forecast.

The approximate 95% forecast interval was:

- Lower Bound: ₹1,047,111
- Point Forecast: ₹1,234,273
- Upper Bound: ₹1,421,436

Therefore, the forecast can be communicated as approximately:

> ₹12.34 lakh, with a 95% forecast interval of approximately ₹10.47 lakh to ₹14.21 lakh.

Custom Excel error bars were used to visualize this uncertainty around the forecast point.

---

## 12. Business Interpretation

The forecasting analysis demonstrates that the revenue series cannot be adequately represented by a simple linear trend.

The strong performance of ETS suggests that recent patterns and recurring temporal structure contain useful predictive information.

The comparison also demonstrates why model complexity alone does not guarantee better forecasts.

For example, the simple naïve baseline outperformed both the three-month moving average and linear forecast during the holdout period.

Model selection was therefore based on measured out-of-sample performance rather than assumptions about which forecasting technique should perform best.

---

## 13. Important Data Limitation

The Online Retail II dataset ends on:

**December 9, 2011**

Therefore, December 2011 actual revenue represents only a partial month.

The observed December 2011 revenue should not be directly compared with the full-month ETS forecast as though both represented equivalent periods.

For this reason:

- December 2011 was excluded from holdout model evaluation.
- June through November 2011 was used for model comparison.
- The December ETS result is interpreted as a forecast for a complete month rather than a prediction of the available nine days of December data.

This distinction prevents an incomplete observation from creating a misleading forecast-performance conclusion.

---

## 14. Key Excel Skills Demonstrated

This forecasting workflow demonstrates practical use of:

- PivotTables
- Excel Data Model measures
- Date conversion and time-series preparation
- Relative and absolute cell references
- Naïve forecasting
- Moving averages
- `AVERAGE`
- `FORECAST.LINEAR`
- `FORECAST.ETS`
- `FORECAST.ETS.CONFINT`
- Forecast error calculations
- MAE
- RMSE
- MAPE
- Holdout validation
- Model comparison
- Confidence intervals
- Custom chart error bars
- Forecast visualization
- Business interpretation

---

## 15. Forecasting Outcome

Four forecasting approaches were developed and evaluated rather than relying on a single forecasting method.

ETS achieved the strongest holdout performance with approximately **4.3% MAPE** and was selected as the final model.

The resulting December 2011 forecast was approximately **₹12.34 lakh**, with a 95% interval of approximately **₹10.47–₹14.21 lakh**.

Most importantly, the forecasting process demonstrates an end-to-end analytical workflow:

> Prepare → Forecast → Validate → Compare → Select → Quantify Uncertainty → Communicate