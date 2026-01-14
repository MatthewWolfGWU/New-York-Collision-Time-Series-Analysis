# NYC Daily Collision Forecasting Analysis

A comprehensive time series forecasting project analyzing daily motor vehicle collisions in New York City using advanced ARIMA modeling techniques with R.

## Overview

This project develops and compares multiple time series models to forecast daily traffic collisions in NYC from January 1, 2016, to December 31, 2017. The analysis incorporates meteorological variables and sophisticated seasonal patterns to provide predictive insights for public safety planning and traffic management.

## Project Objectives

- Model daily collision patterns using univariate and multivariate time series techniques
- Identify seasonal and cyclical trends in collision data
- Evaluate the impact of weather conditions on traffic accidents
- Develop robust ARIMA models for accurate forecasting
- Provide actionable insights for emergency response resource allocation

## Dataset

**Time Period**: January 1, 2016 - December 31, 2017 (731 days)

**Target Variable**:
- `COLLISIONS`: Daily count of reported motor vehicle collisions in NYC

**Meteorological Variables**:
- `AWND`: Average daily wind speed (m/s)
- `TMAX`: Daily maximum temperature (°C)
- `TMIN`: Daily minimum temperature (°C)
- `PRCP`: Daily precipitation (mm)

**Training/Test Split**: 600 days training, 131 days holdout

## Key Findings

### Temporal Patterns
- **Weekly Seasonality**: Strong 7-day cycle with highest collisions on weekdays (Friday peak: ~700 collisions)
- **Weekend Effect**: Significantly lower collision rates on Saturday and Sunday (~500-550 collisions)
- **Monthly Trends**: Lower collisions in winter months (Jan-Feb), peak in spring/summer (May-July)
- **No Long-term Trend**: Series exhibits stable mean over the observation period

### Weather Impact
- Temperature shows moderate correlation with collisions (TMAX: 0.257, TMIN: 0.229)
- Wind speed shows weak negative correlation (-0.13)
- Precipitation shows minimal linear association (0.025)

## Methodology

### 1. Deterministic Time Series Models

**Seasonal Dummy Variable Model**
- Created manual dummy variables for months (11 indicators) and weekdays (7 indicators)
- Reference: January/February and Friday
- Training R²: 0.498 | Holdout R²: 0.389
- Training MAPE: 8.75% | Holdout MAPE: 9.03%

**Cyclical Trend Model**
- Analyzed periodogram to identify dominant frequencies
- Top 6 periods: 6.976, 3.508, 3.488, 300, 7.058, 200
- Created cos/sin pairs for each period
- Training R²: 0.422 | Holdout R²: -0.803 (poor generalization)

### 2. Time Series Regression Models

**Regression with Weather Variables**
- Model 1: AWND + TMAX + TMIN + PRCP
- Model 2: AWND + TMAX + TMIN (excluded PRCP)
- Results: Low R² (~0.10), indicating weak predictive power of weather alone
- Residuals showed significant autocorrelation (non-white noise)

### 3. Stochastic Time Series Models (ARIMA)

#### Model 4.1: Pure ARIMA on Collisions
**ARIMA(1,1,2) × (0,1,1)₇**
- Seasonal differencing at lag 7 to remove weekly seasonality
- Nonseasonal differencing to stabilize trend
- **Best Performance**: Holdout RMSE: 78.43
- Training MAPE: 8.58% | Holdout MAPE: 9.44%
- Box-Pierce test p-value: 0.236 (residuals are white noise ✓)

#### Model 4.2: ARIMAX with Weather Regressors
**ARIMA(2,1,3) × (0,1,1)₇ with TMAX**
- Corrected time series regression residuals
- Only TMAX remained significant after feature selection
- Training MAPE: 8.44% | Holdout MAPE: 9.31%
- All coefficients statistically significant
- Box-Pierce test p-value: 0.38 (residuals are white noise ✓)

#### Model 4.3: Corrected Deterministic Model ⭐ **BEST MODEL**
**ARIMA(1,1,2) with Seasonal Dummies**
- Combined deterministic (monthly/daily dummies) with stochastic components
- **Top Performance Across Metrics**:
  - Training MAPE: **8.22%** (best)
  - Training RMSE: **66.92** (best)
  - Holdout MAPE: **9.12%** (best)
  - Holdout MAE: **57.84** (best)
  - Training R²: 0.547 | Holdout R²: 0.369
- Box-Pierce test p-value: 0.129 (residuals are white noise ✓)

## Model Comparison Summary

| Model | Holdout MAPE | Holdout RMSE | Holdout MAE | Holdout R² |
|-------|--------------|--------------|-------------|------------|
| Seasonal Dummy (uncorrected)* | 9.03% | 77.80 | 56.86 | 0.389 |
| Pure ARIMA(1,1,2)×(0,1,1)₇ | 9.44% | **78.43** | 58.11 | 0.379 |
| ARIMAX(2,1,3)×(0,1,1)₇ + TMAX | 9.31% | 80.65 | 59.31 | 0.344 |
| **Corrected Deterministic** | **9.12%** | 79.06 | **57.84** | **0.369** |

*Biased due to correlated residuals - not recommended for forecasting

## Technologies Used

- **R**: Primary programming language
- **RMarkdown**: Reproducible analysis and documentation
- **Libraries**:
  - `tseries`: Time series analysis and stationarity tests
  - `forecast`: ARIMA modeling and forecasting
  - `Metrics`: Performance evaluation (MAPE, RMSE, MAE)
  - `dplyr`: Data manipulation
  - `TSA`: Periodogram analysis

## Project Structure

```
.
├── project_4219_MASTER FILE.Rmd    # Complete R Markdown analysis
├── final_write_up.pdf               # Detailed project report
├── final_write_up-merged.pdf        # Comprehensive documentation
├── NYC_Daily_Collisions.csv         # Dataset
└── README.md                        # Project documentation
```

## Statistical Techniques

- **Seasonal Decomposition**: ACF/PACF analysis, periodogram
- **Differencing**: Seasonal (lag 7) and nonseasonal (lag 1) to achieve stationarity
- **Box-Pierce Test**: Residual autocorrelation testing
- **Model Selection**: Iterative refinement using AIC, BIC, and diagnostic plots
- **Validation**: Train/test split with robust holdout evaluation

## Key Insights for Stakeholders

1. **Predictable Patterns**: Daily collisions follow strong weekly cycles, enabling proactive resource allocation
2. **Weather Effects**: Temperature has measurable but modest impact; seasonal dummies capture patterns more effectively
3. **Model Recommendation**: Corrected deterministic ARIMA model provides best balance of accuracy and interpretability
4. **Forecast Reliability**: Achieving ~9% MAPE on holdout data demonstrates robust predictive capability

## Recommendations

1. **Proactive Staffing**: Increase emergency response resources on weekdays, especially Fridays
2. **Seasonal Preparedness**: Anticipate higher collision volumes during May-July period
3. **Weather Monitoring**: Monitor temperature trends for collision risk assessment
4. **Continued Modeling**: Integrate additional features (holidays, special events) for enhanced accuracy

## Contributors

- Jonathan Beck
- Henrique Cassol
- Jesse Mutamba
- Matthew Wolf

## Academic Context

**Course**: DNSC 4219 - Forecasting Analytics
**Institution**: George Washington University
**Date**: March 2025

## License

This project was completed as part of academic coursework at George Washington University.
