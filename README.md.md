# Electricity Consumption Time Series Forecasting

A comprehensive time series analysis project for forecasting building electricity consumption using multiple statistical and machine learning models with and without temperature data as a predictor.

## Project Overview

This project develops and compares various forecasting models to predict electricity consumption (kW) for a specific building on February 21, 2010. The analysis implements two distinct forecasting approaches:

1. **Univariate Forecasting**: Predictions based solely on historical electricity consumption patterns
2. **Multivariate Forecasting**: Predictions incorporating outdoor air temperature as an external regressor

The primary dataset includes electricity consumption and outdoor air temperature measurements at 15-minute intervals from January 1, 2010 to February 20, 2010, providing 96 observations per day.

## Objectives

- Develop accurate short-term electricity consumption forecasts
- Compare model performance with and without temperature data
- Identify the most effective forecasting technique for high-frequency energy data
- Generate 96 predictions (24 hours at 15-minute intervals) for February 21, 2010
- Evaluate the impact of environmental variables on prediction accuracy

## Dataset

**Source**: `2023-11-Elec-train.xlsx`

**Features**:
- Timestamp: Date and time of observations
- Power (kW): Electricity consumption
- Temp (C°): Outdoor air temperature

**Specifications**:
- Time period: January 1, 2010 - February 20, 2010
- Frequency: 15-minute intervals (96 readings/day)
- Total observations: 4,987 rows (after cleaning: 4,896 rows)
- Data cleaning: Removed incomplete first day and corrected anomalous zero values

## Methodology

### Data Preprocessing

1. **Data Cleaning**:
   - Removed first incomplete day (January 1, 2010)
   - Replaced anomalous zero values with 160 kW (based on data pattern analysis)
   - Created balanced dataset with complete 24-hour cycles

2. **Time Series Transformation**:
   - Converted data to ts objects with frequency = 96
   - Decomposed series to analyze trend and seasonality
   - Split data: 80% training (Jan 2 - Feb 10), 20% testing (Feb 11 - Feb 20)

### Univariate Models (Without Temperature)

#### 1. Simple Exponential Smoothing (SES)
- Basic level-based forecasting
- Alpha parameter: 0.9860426
- **RMSE**: 93.28

#### 2. Holt-Winters Seasonal Model
- Captures seasonal patterns without trend
- Alpha: 0.7831196, Gamma: 0.8904545
- **RMSE**: 21.24

#### 3. SARIMA (Seasonal ARIMA)
- Auto-ARIMA selected: ARIMA(1,0,0)(0,1,0)[96]
- Accounts for seasonal differencing
- **RMSE**: 15.76 (Best univariate model)

#### 4. Neural Network Autoregression (NNAR)
- NNAR(20,1,11)[96] architecture
- 21-11-1 network with 254 weights
- **RMSE**: 72.60

### Multivariate Models (With Temperature)

#### 1. Simple Linear Regression (SLR)
- Basic temperature-power relationship
- R² = 0.2076
- **RMSE**: 50.85

#### 2. Advanced Linear Regression (ALR)
- Incorporates trend and seasonal factors
- R² = 0.9598
- Temperature coefficient: 1.031 kW per °C
- **RMSE**: 15.75

#### 3. Dynamic Regression Model
- ARIMA(5,0,0)(0,1,1)[96] with temperature regressor
- Combines regression with SARIMA error modeling
- **RMSE**: 14.66 (Best overall model)

#### 4. Neural Network with External Regressor (NNAR)
- NNAR(20,1,12)[96] with temperature input
- 22-12-1 network with 289 weights
- **RMSE**: 74.79

## Key Findings

### Model Performance Comparison

**Univariate Models (Without Temperature)**:
1. SARIMA: 15.76 RMSE
2. Holt-Winters: 21.24 RMSE
3. NNAR: 72.60 RMSE
4. SES: 93.28 RMSE

**Multivariate Models (With Temperature)**:
1. Dynamic Regression: 14.66 RMSE (BEST)
2. ALR with SARIMA residuals: 15.75 RMSE
3. SLR: 50.85 RMSE
4. NNAR: 74.79 RMSE

### Key Insights

- **Temperature Impact**: Including temperature as a predictor improved forecast accuracy by approximately 7% (15.76 vs 14.66 RMSE)
- **Seasonality Dominance**: Strong daily seasonal patterns (96 periods) are the primary driver of consumption
- **No Clear Trend**: Decomposition revealed stable consumption levels without significant upward/downward trends
- **Model Selection**: Dynamic Regression combining SARIMA error structure with temperature regression achieved optimal performance
- **Temperature Coefficient**: Each 1°C increase in temperature corresponds to approximately 0.45-1.03 kW increase in consumption

## Technologies & Tools

**R Environment**:
- Base R 4.2+
- RStudio (recommended)

**Core Packages**:
- `forecast` - Time series forecasting methods
- `fpp2` - Forecasting principles and practice
- `tseries` - Time series analysis utilities
- `tidyverse` - Data manipulation and visualization
- `ggplot2` - Advanced plotting
- `readxl` - Excel file import
- `writexl` - Excel file export
- `openxlsx` - Excel file handling

## Project Structure

```
├── data/
│   └── 2023-11-Elec-train.xlsx
├── scripts/
│   ├── 01_data_preparation.R
│   ├── 02_univariate_models.R
│   ├── 03_multivariate_models.R
│   └── 04_model_comparison.R
├── outputs/
│   ├── forecasts/
│   │   ├── forecast_without_temp.xlsx
│   │   └── forecast_with_temp.xlsx
│   └── figures/
├── report/
│   └── TimeSeriesAnalysis_Report.pdf
└── README.md
```

## Getting Started

### Prerequisites

```r
# Install required packages
install.packages(c(
  "readxl", "openxlsx", "tidyverse", "ggplot2", 
  "forecast", "fpp2", "writexl", "tseries"
))
```

### Running the Analysis

1. Clone the repository
```bash
git clone https://github.com/yourusername/electricity-forecasting-timeseries.git
cd electricity-forecasting-timeseries
```

2. Place the dataset in the `data/` folder

3. Run the analysis scripts sequentially
```r
source("scripts/01_data_preparation.R")
source("scripts/02_univariate_models.R")
source("scripts/03_multivariate_models.R")
source("scripts/04_model_comparison.R")
```

4. View forecasts in the `outputs/` directory

## Model Implementation Details

### Best Model: Dynamic Regression

**Model Specification**:
```r
ARIMA(5,0,0)(0,1,1)[96] with temperature regressor
```

**Parameters**:
- AR(5): Autoregressive order of 5
- Seasonal MA(1): Seasonal moving average of order 1
- Seasonal differencing: 1
- External regressor: Temperature (°C)

**Performance Metrics**:
- RMSE: 14.66 kW
- Captures both seasonal patterns and temperature influence
- Residuals show minimal autocorrelation

### Model Selection Criteria

Models were evaluated using:
- Root Mean Square Error (RMSE)
- Akaike Information Criterion (AIC)
- Bayesian Information Criterion (BIC)
- Residual diagnostics (Ljung-Box test)
- Visual inspection of forecasts vs. actual values

## Results

### Final Forecasts for February 21, 2010

Two sets of 96 predictions were generated:

1. **Without Temperature**: SARIMA model forecast
   - File: `forecast_without_temp.xlsx`
   - Model: ARIMA(1,0,0)(0,1,0)[96]

2. **With Temperature**: Dynamic Regression forecast
   - File: `forecast_with_temp.xlsx`
   - Model: ARIMA(5,0,0)(0,1,1)[96] with temperature

### Practical Applications

- **Energy Management**: Short-term load forecasting for grid operators
- **Demand Response**: Predicting peak consumption periods
- **Cost Optimization**: Planning energy procurement strategies
- **Building Efficiency**: Identifying consumption anomalies
- **HVAC Control**: Temperature-aware consumption patterns

## Limitations & Future Work

### Current Limitations

- Single building analysis (limited generalizability)
- Short training period (50 days)
- No weekend/holiday indicators
- Limited external variables (only temperature)
- 15-minute granularity may mask sub-interval variations

### Potential Improvements

1. **Extended Features**:
   - Day of week and holiday effects
   - Humidity, wind speed, solar radiation
   - Occupancy data
   - Building operational schedules

2. **Advanced Models**:
   - Ensemble methods combining multiple models
   - LSTM/GRU neural networks for sequence modeling
   - Prophet for handling multiple seasonalities
   - VAR models for multivariate dependencies

3. **Longer Time Horizons**:
   - Multi-day ahead forecasting
   - Weekly and monthly aggregations
   - Cross-validation with rolling windows

4. **Real-time Applications**:
   - Online learning with streaming data
   - Automated model retraining
   - Anomaly detection integration

## Contributors

Zakaria Bouhia

## Project Timeline

**Completion Date**: August 15, 2024

## References

- Hyndman, R.J., & Athanasopoulos, G. (2021). Forecasting: principles and practice, 3rd edition, OTexts: Melbourne, Australia.
- Box, G.E.P., & Jenkins, G.M. (1976). Time Series Analysis: Forecasting and Control.
- Holt, C.C. (2004). Forecasting seasonals and trends by exponentially weighted moving averages.
- Brockwell, P.J., & Davis, R.A. (2016). Introduction to Time Series and Forecasting.

## License

This project is available under the MIT License.

## Contact

For questions or collaboration opportunities, please open an issue in this repository.

---

**Note**: This project demonstrates practical applications of time series forecasting techniques for energy consumption prediction, combining classical statistical methods with modern machine learning approaches.
