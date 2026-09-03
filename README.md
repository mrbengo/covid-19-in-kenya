# Predicting the Next Phase of COVID-19 in Kenya

A time-series forecasting study of the early COVID-19 pandemic in Kenya, originally developed in **May 2020** as part of a Master of Science in Data Science research project.

The project used publicly available COVID-19 time-series data from the **Johns Hopkins University Center for Systems Science and Engineering (JHU CSSE)** to model the progression of confirmed cases, deaths, and recoveries in Kenya using **ARIMA/SARIMA time-series forecasting techniques**.

In **2026**, the project was revisited to correct methodological and reproducibility issues in the original implementation and to conduct a retrospective evaluation of how the May 2020 forecasts compared with what subsequently occurred.

---

## Background

COVID-19 emerged in late 2019 and developed into a global pandemic during the first quarter of 2020. Kenya reported its first confirmed case in March 2020 and subsequently introduced a range of public-health measures intended to slow transmission.

At the time this research was conducted, relatively little historical data existed for Kenya and considerable uncertainty surrounded the future trajectory of the outbreak.

The project therefore explored whether statistical time-series forecasting techniques could use the limited observations available at the time to provide useful indications of the likely short- and medium-term trajectory of reported COVID-19 cases in Kenya.

---

## Research Context

This study was developed during the **early phase of the pandemic in May 2020**, when governments, public-health institutions, researchers, and communities were attempting to understand how rapidly COVID-19 might spread and what pressures health systems might face.

The original project should therefore be interpreted within that historical context.

The objective was not to construct a complete epidemiological transmission model, but rather to investigate whether established statistical forecasting techniques could extract useful temporal patterns from the available COVID-19 case data.

The analysis focused on three reported indicators:

- Confirmed COVID-19 cases
- Reported COVID-19 deaths
- Reported recoveries

Separate forecasting models were developed for each series.

---

## Original Research - May 2020

The original study was conducted using information available during **May 2020**.

Three Jupyter notebooks were created:

- [`covid_confirmed_forecast_in_kenya.ipynb`](original/covid_confirmed_forecast_in_kenya.ipynb) - confirmed cases
- [`covid_deaths_forecast_in_kenya.ipynb`](original/covid_deaths_forecast_in_kenya.ipynb) - reported deaths
- [`covid_recoveries_forecast_in_kenya.ipynb`](original/covid_recoveries_forecast_in_kenya.ipynb) - reported recoveries

The models used the observed historical trajectory of each series to estimate its likely future direction.

The original analysis concluded that Kenya was likely to experience substantial continued growth in confirmed cases, deaths, and recoveries during the subsequent months.

### Historical integrity

The original notebooks are retained as part of the repository because they represent the analysis as it was originally performed.

The 2026 methodological revision is deliberately separated from the original research.

No observations occurring after the original May 2020 information cutoff are used to improve or retrospectively tune the original forecast.

---

## Research Objectives

The revised formulation of the original research objectives is:

1. **To develop and evaluate statistical time-series models capable of forecasting reported COVID-19 trends in Kenya using information available during the early phase of the pandemic.**

2. **To examine the observed trajectory of reported COVID-19 cases, deaths, and recoveries in Kenya within the context of the public-health response during the study period.**

3. **To assess the potential usefulness and limitations of statistical forecasting as a decision-support tool during a rapidly evolving public-health emergency.**

The models do **not** attempt to estimate the causal effect of individual containment measures.

---

## Dataset

The project uses the global COVID-19 time-series datasets maintained by the **Johns Hopkins University Center for Systems Science and Engineering (JHU CSSE)**.

The repository contains the historical datasets used for:

- confirmed cases;
- deaths; and
- recoveries.

The principal files are:

```text
time_series_covid19_confirmed_global.csv
time_series_covid19_deaths_global.csv
time_series_covid19_recovered_global.csv
```

For each dataset, the Kenya observations are extracted and converted into a time-indexed series for analysis.

### Data provenance

For the methodological reconstruction, preference is given to the historical dataset available at the time the original analysis was undertaken.

This is important because COVID-19 datasets were occasionally revised retrospectively as reporting errors were corrected or historical cases were redistributed.

Using a later version of the dataset while simply truncating it at May 2020 could therefore unintentionally introduce information that was not present when the original forecast was produced.

---

# Methodology

The study follows a conventional statistical time-series forecasting workflow.

## Data preprocessing

The original global JHU datasets are filtered to obtain observations for Kenya.

The revised preprocessing pipeline:

1. explicitly selects Kenya using the `Country/Region` field;
2. converts date columns into a chronological time-series index;
3. retains the cumulative reported totals;
4. derives daily changes where appropriate;
5. calculates moving averages for exploratory analysis; and
6. enforces the original historical information cutoff.

The revised implementation also removes machine-specific file paths and uses relative repository paths to improve reproducibility.

---

## Exploratory analysis

Exploratory analysis is performed on both:

### Cumulative counts

\[
C_t
\]

and daily changes:

\[
\Delta C_t = C_t - C_{t-1}
\]

The cumulative series shows the overall progression of reported cases, while daily incidence provides greater visibility into changes in the rate of reported infections.

A seven-day moving average may also be used to reduce short-term reporting noise.

Additional diagnostics include:

- trend visualization;
- first differences;
- autocorrelation analysis;
- partial autocorrelation analysis; and
- stationarity testing.

---

## ARIMA/SARIMA modelling

The original research used **Autoregressive Integrated Moving Average (ARIMA)** and seasonal ARIMA-family models.

A general ARIMA model is represented as:

\[
ARIMA(p,d,q)
\]

where:

- `p` = autoregressive order;
- `d` = degree of differencing;
- `q` = moving-average order.

Seasonal models extend this to:

\[
SARIMA(p,d,q)(P,D,Q)_s
\]

where `s` represents the assumed seasonal period.

The original implementation explored combinations of ARIMA and seasonal parameters using the `statsmodels` Python library.

---

## Model selection

The original research relied substantially on the **Akaike Information Criterion (AIC)** when comparing candidate model specifications.

The 2026 revision strengthens this process.

Candidate models are evaluated using a combination of:

- AIC;
- Bayesian Information Criterion (BIC);
- out-of-sample Mean Absolute Error (MAE);
- Root Mean Squared Error (RMSE);
- Mean Absolute Percentage Error (MAPE), where appropriate; and
- residual diagnostics.

The objective is therefore not simply to identify the model that best fits the historical observations, but the model that provides the strongest performance on observations it has not seen during training.

---

## Temporal validation

A significant methodological correction introduced in the 2026 revision is the use of a genuine **temporal holdout set**.

For the confirmed-cases model:

```text
Training period
        │
        └──── observations through 30 April 2020

Test period
        │
        └──── 1 May 2020 – 14 May 2020
```

The model is fitted using only the training period.

It must then forecast the observations in the May test period without having been trained on them.

This prevents **data leakage** between the training and evaluation periods.

Only after the preferred model has been selected is it refitted using all observations available through the original historical cutoff.

---

## Forecast evaluation

Forecast accuracy is evaluated using several complementary metrics.

### Mean Absolute Error

\[
MAE = \frac{1}{n}\sum_{t=1}^{n}|y_t-\hat{y}_t|
\]

MAE provides an intuitive indication of the average absolute forecasting error.

### Root Mean Squared Error

\[
RMSE =
\sqrt{
\frac{1}{n}
\sum_{t=1}^{n}
(y_t-\hat{y}_t)^2
}
\]

RMSE penalizes larger forecasting errors more strongly.

### Mean Absolute Percentage Error

\[
MAPE =
\frac{100}{n}
\sum_{t=1}^{n}
\left|
\frac{y_t-\hat{y}_t}{y_t}
\right|
\]

MAPE is reported only where the observed values make the metric meaningful.

### Baseline comparison

The revised methodology also introduces a **naïve persistence model** as a benchmark.

This allows the research to answer an important question:

> Does an ARIMA/SARIMA model provide materially better forecasting performance than a simple baseline?

---

# Results

The original May 2020 analysis projected continued substantial growth in Kenya's:

- confirmed COVID-19 cases;
- reported deaths; and
- reported recoveries.

The individual notebooks contain the original model outputs and forecast visualizations.

The 2026 revision does not alter these historical results retrospectively.

Instead, the corrected methodology first determines whether the original modeling approach remains statistically defensible when subjected to:

- genuine temporal holdout validation;
- corrected forecast-error calculations;
- baseline comparison;
- stationarity testing;
- alternative seasonal specifications; and
- residual diagnostics.

Updated validated results will be documented as the methodological revision progresses.

---

# 100-Day Projection

The original project generated forecasts extending approximately **100 days beyond the historical observation period**.

The revised project retains this horizon because it is an important part of the original research question.

However, its interpretation is changed.

Rather than describing it as a deterministic **100-day prediction**, the revised study refers to it as a:

> **100-day statistical projection**

The distinction is important.

A long-range ARIMA/SARIMA projection assumes that statistical relationships observed in the historical series remain sufficiently stable into the future.

During an epidemic, those relationships can change significantly because of factors including:

- changes in human behaviour;
- government interventions;
- testing capacity;
- reporting practices;
- geographic spread;
- changing transmission dynamics; and
- subsequent biological developments in the disease.

The revised analysis therefore distinguishes among:

| Forecast Horizon | Primary Interpretation |
|---|---|
| 7 days | Short-term operational forecast |
| 14 days | Short-term planning forecast |
| 30 days | Medium-term planning forecast |
| 100 days | Long-range statistical projection |

---

# Limitations

Several limitations should be considered when interpreting the study.

### 1. Confirmed cases are not equivalent to total infections

The model forecasts **reported laboratory-confirmed cases**, not the total number of people infected with SARS-CoV-2.

Observed confirmed cases depend partly on:

- testing availability;
- testing eligibility;
- case-detection practices; and
- reporting systems.

---

### 2. Limited historical observations

When the original research was undertaken, Kenya had only a relatively short history of reported COVID-19 transmission.

Long-range forecasting from such a limited time series necessarily involves substantial uncertainty.

---

### 3. Statistical rather than epidemiological modelling

ARIMA/SARIMA models identify temporal statistical relationships.

They do not explicitly model epidemiological mechanisms such as:

- susceptible populations;
- exposure;
- infectious periods;
- recovery;
- reproduction numbers; or
- population contact patterns.

The project should therefore not be interpreted as a substitute for mechanistic models such as SIR or SEIR.

---

### 4. Changing epidemic dynamics

Time-series forecasting assumes some persistence in the underlying data-generating process.

During the COVID-19 pandemic, that process could change rapidly because of interventions, mobility, behavioural responses, testing changes, and other factors.

---

### 5. Containment measures are not causal model variables

Although the study considers the epidemic within the context of containment measures, the original ARIMA/SARIMA models do not include intervention variables.

The research therefore cannot establish that an observed change in the epidemic trajectory was **caused** by a particular intervention.

---

### 6. Reporting effects

Daily COVID-19 statistics may contain administrative and reporting artefacts unrelated to actual transmission patterns.

These may appear as short-term temporal structure in the data.

---

### 7. Historical dataset revisions

JHU CSSE periodically corrected and revised historical COVID-19 data.

For rigorous reconstruction of the May 2020 forecasting experiment, the preferred input is therefore the historical dataset available at that time rather than a later retrospectively corrected version.

---

# 2026 Methodological Review

In 2026, the original project was revisited to assess the methodology using the benefit of subsequent data-science experience while preserving the historical integrity of the original experiment.

The review identified several areas for improvement.

### Key corrections

- Correct the forecast-error calculation in the original notebooks.
- Eliminate overlap between training and validation observations.
- Introduce a genuine temporal train/test split.
- Evaluate forecasts using MAE, RMSE, and MAPE rather than relying on a single metric.
- Introduce a naïve benchmark model.
- Add formal stationarity testing.
- Improve ACF/PACF analysis.
- Reassess the assumed seasonal period rather than adopting it without justification.
- Supplement AIC with out-of-sample performance and BIC.
- Add formal residual diagnostics.
- Improve data preprocessing and reproducibility.
- Separate daily incidence analysis from cumulative case analysis.
- Clarify the interpretation of the 100-day forecast.
- Explicitly document the statistical and epidemiological limitations of the approach.

### Methodological principle

The 2026 revision follows one central rule:

> **No post-May-2020 observations may be used to improve the model that represents the original May 2020 forecast.**

This ensures that the revised model can subsequently be subjected to a legitimate retrospective test.

---

# Retrospective Evaluation

**Phase 2 — planned**

Once the corrected May 2020 model has been finalized and frozen, its forecasts will be compared with the observations that actually occurred during the subsequent 100 days.

The retrospective evaluation will examine performance at approximately:

- 7 days;
- 14 days;
- 30 days;
- 60 days; and
- 100 days.

Forecast error will be assessed using measures including:

- MAE;
- RMSE;
- MAPE; and
- forecast bias.

The analysis will seek to answer:

> **How accurately could a statistical model constructed using only information available in May 2020 anticipate the subsequent trajectory of reported COVID-19 cases in Kenya?**

A subsequent extension may compare the original ARIMA/SARIMA approach with alternative forecasting techniques such as:

- exponential-growth models;
- Prophet;
- machine-learning models;
- recurrent neural networks; and
- compartmental epidemiological models such as SIR/SEIR.

These models will form a **new retrospective experiment** and will not be presented as part of the original May 2020 research.

---

# Repository Structure

The repository is being reorganized to distinguish clearly between the original research and the methodological revision.

Proposed structure:

```text
covid-19-in-kenya/
│
├── README.md
├── LICENSE
│
├── original/
│   ├── covid_confirmed_forecast_in_kenya.ipynb
│   ├── covid_deaths_forecast_in_kenya.ipynb
│   └── covid_recoveries_forecast_in_kenya.ipynb
│
├── notebooks/
│   ├── 01_data_preparation.ipynb
│   ├── 02_confirmed_cases_model_v2.ipynb
│   ├── 03_deaths_model_v2.ipynb
│   ├── 04_recoveries_model_v2.ipynb
│   └── 05_retrospective_evaluation.ipynb
│
├── data/
│   ├── original/
│   └── retrospective/
│
├── src/
│   └── forecasting.py
│
└── requirements.txt
```

Until the restructuring is complete, the original notebooks and datasets remain available in the repository root.

---

# Reproducing the Analysis

## Requirements

The revised analysis uses Python and Jupyter.

Core dependencies include:

```text
pandas
numpy
matplotlib
statsmodels
scikit-learn
jupyter
```

Install the dependencies using:

```bash
pip install -r requirements.txt
```

Then start Jupyter:

```bash
jupyter notebook
```

For strict historical reproducibility, the original May 2020 JHU dataset snapshot should be placed within:

```text
data/original/
```

The notebooks should then be executed sequentially.

---

# References

1. Dong, E., Du, H., & Gardner, L. (2020). *An interactive web-based dashboard to track COVID-19 in real time.* The Lancet Infectious Diseases, 20(5), 533–534. DOI: 10.1016/S1473-3099(20)30120-1.

2. Johns Hopkins University Center for Systems Science and Engineering (JHU CSSE). *COVID-19 Data Repository.*

3. Box, G. E. P., Jenkins, G. M., Reinsel, G. C., & Ljung, G. M. *Time Series Analysis: Forecasting and Control.* Wiley.

4. Hyndman, R. J., & Athanasopoulos, G. *Forecasting: Principles and Practice.* OTexts.

Additional references relating to the retrospective analysis and epidemiological modeling will be added as the project progresses.

---

# License

This project is licensed under the **GNU General Public License v3.0 (GPL-3.0)**.

See [`LICENSE`](LICENSE) for details.

---

## Project Status

**Original research:** Completed — May 2020  
**Methodological revision:** In progress — 2026  
**Retrospective 100-day evaluation:** Planned — Phase 2

---

## Author

**Alex Bengo**

Originally developed as part of a **Master of Science in Data Science** research project.