# SF_Civic_Service_Demand_Forecasting
Forecasting San Francisco 311 safety-related service demand at the neighborhood level using time-aware regression models.

---

- [Overview](#-overview)
- [Methodology](#-methodology)
- [Analytical Techniques and Tools](#-analytical-techniques-and-tools)
- [Feature Engineering](#-feature-engineering)
- [Modeling Approach](#-modeling-approach)
- [Results](#-results)
- [Project Setup](#-project-setup)
- [Repository Structure](#-repository-structure)



---

## Overview
City services routinely need to respond to safety events which range from disturbances, including noise, illegal parking, graffiti, and curb problems. This project will predict the number of different types of [311 events in San Francisco](https://www.sf.gov/departments--311-customer-service-center) per neighborhood and for a given date, based on historical data. Accurate models will help ensure that enough customer service agents and resources are available and able to respond promptly to these requests.

Our dataset is based on San Francisco 311 service call data ranging from 2016 to 2020. We develop and evaluate time-aware regression models to forecast aggregated San Francisco 311 safety-related service demand at the neighborhood level. These models assess whether historical call data, combined with temporal, weather, holiday, and operational schedule features, can produce reliable short-horizon demand forecasts suitable for planning and resource allocation. Forecasting is performed at a fixed 6-hour interval across selected neighborhoods and service categories.

---

## Methodology

* Analyze available events data to identify trends, impact of seasonality, and consistency of data.
* Build machine learning models to predict the number of events of a given type for a particular date and location.
* Use external datasets to improve model performance.
* Make predictions more explainable by using model interpretability tools.
* Prevent undesired biases or discrimination when models are put into production.
* Evaluate model performance during city-wide lockdowns during the COVID-19 pandemic.

---

## Analytical Techniques and Tools

* Cleaning, filtering and pre-processing the data loaded from Parquet files
    * [pandas](https://pandas.pydata.org/)
* Analysis and visualization of location-based and time-dependent data
    * [pandas](https://pandas.pydata.org/)
    * [matplotlib](https://matplotlib.org/)
* Machine Learning model training, evaluation, hyper-parameter tuning
    * [scikit-learn](https://pypi.org/project/scikit-learn/)
* Python package creation
    * [setuptools](https://pypi.org/project/setuptools/)
* Evaluating of bias risk and ethical considerations 
    * [deon](https://deon.drivendata.org/#data-science-ethics-checklist)


---

## Feature Engineering

### Target Construction

We have several different service categories for 311 records:

* Street and Sidewalk Cleaning
* Potentially Life-Threatening
* Graffiti
* Encampments
* Non Life-threatening

For each service category we:
1.  Filter events by category.
2. Aggregate counts by:
    a. Neighborhood
    b. 6-hour interval
3. Construct a complete neighborhood × time grid.
4. Fill missing time intervals with zero counts to ensure consistent time indexing and avoid missing data.


#### Temporal Features

* Year, month, day
* Day of week
* Hour of day (cyclical encoding using sine/cosine)

#### External Signals

* Daily weather variables (NOAA station data)
* Federal and California holiday indicators
* Street sweeping schedule intensity by neighborhood

## Modeling Approach
* Baseline
    * DummyRegressor (predict historical mean)

* Candidate Models
    * Ridge Regression
    * Poisson Regressor
    * HistGradientBoostingRegressor (primary model)

* Validation Strategy
    * Time-ordered train/test split
    * Forward-chaining cross-validation
    * Hyperparameter tuning using GridSearchCV / RandomizedSearchCV

Evaluation metric: [Root Mean Squared Error (RMSE)](https://en.wikipedia.org/wiki/Root_mean_square_deviation)

## Model Performance

Performance varies by service category and neighborhood.

Evaluation includes:

* RMSE
* Mean Absolute Error (MAE)
* Maximum Error
* Mean Residual (Directional Bias)
* Neighborhood-level residual time series

Key observations:

* Gradient boosting consistently outperformed baseline models.
* Some service categories are structurally more predictable than others.
* Neighborhood-level bias can occur even when overall RMSE appears acceptable.
* Model performance degraded in 2020 relative to prior periods, indicating measurable concept drift.

### Model Interpretation

Interpretability analysis includes:

* [Permutation feature importance](https://scikit-learn.org/stable/modules/permutation_importance.html)
* [Partial Dependence Plots (PDP)](https://scikit-learn.org/stable/modules/partial_dependence.html)
* Individual Conditional Expectation (ICE) plots


## Results

