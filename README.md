# IT Incident SLA Prediction

## IST 660 – Data Mining & Business Intelligence

Machine learning project predicting IT incidents that are likely to miss their Service Level Agreement (SLA), enabling service desk managers to identify high-risk incidents and intervene earlier.

---

## Project Overview

IT service desks handle large numbers of incidents, and some incidents take longer to resolve than their defined SLA allows. Identifying these incidents early can help service desk teams prioritize resources and take corrective action before an SLA breach occurs.

This project develops a machine learning classification approach to predict whether an IT incident will miss its SLA based on information available around the time the incident is opened.

### Business Question

> At ticket open time, can we identify incidents that are likely to miss SLA so that service desk managers can intervene earlier?

---

## Dataset

The dataset is an event-level IT incident log where a single incident can appear across multiple rows as it moves through different lifecycle states.

- **Raw event records:** 141,712
- **Unique incidents:** 24,918
- **Average events per incident:** 5.7
- **Original features:** 36

The project transforms the event-level data into an incident-level analytical dataset with one row per incident.

### Data Flow

Raw Event Log → Incident-Level Aggregation → Feature Engineering → Machine Learning Models → SLA Risk Prediction

---

## Data Preparation

Several data-quality and modeling considerations were addressed before model development.

### Missing Values

The dataset uses the literal `"?"` as a missing-value placeholder rather than standard `NaN` values.

Fields with extremely high missingness were excluded from the modeling process, including:

- `caused_by`
- `vendor`
- `cmdb_ci`
- `rfc`
- `problem_id`

These fields contained insufficient usable information to support reliable modeling.

### Incident-Level Target

The `made_sla` field is not consistent across all event records belonging to an incident.

- **63.4%** of incidents had a stable SLA outcome across their event history.
- **36.6%** had varying SLA values across event records.

Because SLA performance is ultimately determined by the incident outcome, the final event row was used to define the incident-level SLA outcome.

The prediction target was represented as:

`miss_sla = 1` → SLA missed

`miss_sla = 0` → SLA achieved

---

## Feature Engineering

The event-level dataset was transformed into an incident-level analytical table.

Predictors were taken from the earliest available event for each incident to approximate information available near ticket opening.

Features considered included:

- Incident priority
- Impact
- Urgency
- Assignment group
- Category
- Subcategory
- Contact type
- Location
- Knowledge usage
- Assignment information
- Incident timing information
- Time-to-close transformations

The project also documents the limitation that the earliest available event is not guaranteed to represent the perfectly complete original opening record.

---

## Exploratory Data Analysis

The analysis examines patterns associated with SLA performance, including:

- SLA success vs. SLA failure
- Incident priority
- Impact and urgency
- Assignment groups
- Incident categories
- Missing-value patterns
- Incident resolution time
- Distribution of the target variable

These analyses were used to understand the dataset before applying machine learning models.

---

## Machine Learning Approach

Multiple classification approaches were evaluated:

1. Dummy Classifier
2. Logistic Regression
3. Random Forest
4. XGBoost
5. Calibrated XGBoost

The Dummy Classifier provides a baseline against which the machine learning models can be compared.

---

## Model Evaluation

The models were evaluated using metrics appropriate for an imbalanced classification problem:

- Accuracy
- Precision
- Recall
- F1 Score
- ROC-AUC
- Average Precision
- Confusion Matrix
- Precision-Recall Curve

Recall is particularly important for this problem because failing to identify an incident that is likely to miss its SLA can prevent service desk managers from intervening early.

---

## Model Selection

XGBoost provided the strongest overall predictive performance among the evaluated models.

The final evaluation included:

- **ROC-AUC:** 0.8416
- **Recall:** 0.7724
- **F1 Score:** 0.6988

Rather than relying only on the default classification threshold, the project also evaluated alternative probability thresholds to better align model predictions with the operational objective of identifying SLA-risk incidents.

A threshold of approximately **0.472** increased recall to approximately **0.800**, while flagging roughly 40% of incidents for potential intervention.

---

## Data Leakage Analysis

Data leakage was treated as an important modeling risk.

Features that could contain information unavailable at incident-open time were reviewed and classified separately from valid predictors.

The project specifically examined variables such as:

- `reassignment_count`
- `reopen_count`

A leakage sensitivity analysis was performed to determine whether these variables materially improved model performance.

The analysis supports excluding information that would not realistically be available when the prediction is supposed to be made.

---

## Business Application

The model can be used as an early-warning mechanism for service desk operations.

A potential workflow is:

New Incident → Collect Available Ticket Information → ML Risk Prediction → Identify High-Risk Incidents → Early Intervention

Potential interventions include:

- Prioritizing the incident
- Reviewing assignment
- Escalating when necessary
- Allocating additional resources
- Monitoring the incident more closely

The goal is not to automatically resolve incidents, but to help service desk managers identify tickets that may require attention before an SLA breach occurs.

---

## Technologies

- Python
- Pandas
- NumPy
- Scikit-learn
- XGBoost
- Matplotlib
- Jupyter Notebook

---

## Project Structure

incident-sla-prediction/
├── README.md
├── IST660_Fixed.ipynb
├── data/
│   └── README.md
└── report/
    └── IST660_Final_Report.pdf

---

## Limitations

This project has several important limitations:

1. The original dataset is an event-level log rather than a clean incident-level dataset.
2. The earliest available event is used as an approximation of the ticket-open state.
3. The earliest available event may not always represent the complete original opening record.
4. Several variables contain substantial missingness and were excluded.
5. The dataset represents historical service-desk activity and may not generalize directly to other organizations.
6. Model predictions should be treated as decision-support signals rather than definitive outcomes.

---

## Key Takeaways

This project demonstrates an end-to-end data mining workflow:

**Data Cleaning → Incident-Level Transformation → Exploratory Analysis → Feature Engineering → Baseline Modeling → Classification → Model Comparison → Threshold Optimization → Leakage Analysis → Business Recommendations**

The project demonstrates how machine learning can be used not only to maximize predictive performance, but also to support practical operational decisions where identifying high-risk incidents early is the primary objective.

---

## Academic Project

**Course:** IST 660 – Data Mining & Business Intelligence

**Institution:** Cleveland State University

**Project:** IT Incident SLA Prediction
