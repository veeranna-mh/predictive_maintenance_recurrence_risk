# Predictive Maintenance Through Temporal Problem Reformulation

### From Static Pre-Event Classification to Imminent-Event Recurrence-Risk Prediction

A temporal machine-learning project that demonstrates how an initially promising predictive-maintenance formulation failed on future data, how the event-generation process was investigated, and how the prediction problem was reformulated into a simpler and more temporally generalizable recurrence-risk model.

---

## Project Overview

Predictive-maintenance projects are often presented as a straightforward sequence:

> **sensor data → feature engineering → model training → failure prediction**

Real industrial data is rarely that simple.

This project began with a conventional objective:

> **Can static engineered sensor-window features distinguish normal operating windows from windows immediately preceding positive events?**

The first experiment showed promising development performance, but the frozen model failed on the untouched future test period.

Instead of tuning the failed model against the test set or hiding the negative result, the project investigated the temporal structure of the event process. This analysis showed that positive events were predominantly brief, frequently recurrent, and often organized into short-term bursts.

The prediction problem was therefore reformulated.

The second experiment asked a narrower question:

> **When an eligible positive event is already imminent, is it more likely to remain isolated or initiate a short-term recurrent burst?**

Under this formulation, a simple one-feature logistic regression model demonstrated promising discrimination on the untouched future test period.

The core analytical progression was:

> **model failure → event-process investigation → target reformulation → restrained modelling → chronological validation → uncertainty-aware interpretation**

---

## Why This Project Is Different

The main contribution of this project is not a single high model score.

It demonstrates a complete applied data-science reasoning process:

* preserving an unsuccessful future test result;
* distinguishing validation performance from true temporal generalization;
* investigating why a model failed instead of immediately increasing model complexity;
* examining the data-generating process;
* reformulating the prediction target;
* using training-only feature analysis;
* freezing model specifications before test evaluation;
* avoiding post-test tuning;
* and reporting uncertainty and production limitations explicitly.

Experiment 1 was not removed because it failed. Its failure became part of the analytical evidence that motivated Experiment 2.

---

## Business Problem

Unexpected machine instability can lead to:

* repeated operational disruptions;
* increased inspection and maintenance workload;
* unplanned downtime;
* production losses;
* and potentially higher safety and quality risks.

The original goal was to predict whether a machine was approaching a positive event using recent sensor behaviour.

After the original formulation failed to generalize reliably, the project shifted toward a more specific operational question:

> **Among events that are already imminent, which ones are more likely to initiate repeated short-term instability?**

The final model is therefore best interpreted as a **recurrence-risk decision-support model**, not as a complete machine-failure prediction system.

---

## Data and Analytical Unit

The dataset contains time-ordered sensor observations from three machines:

* `M01`
* `M02`
* `M03`

The analysis uses sensor and signal-derived information including variables related to:

* vibration;
* acoustic behaviour;
* temperature;
* electrical current;
* `IMF_1`;
* `IMF_2`;
* and `IMF_3`.

The project uses two different analytical units.

### Experiment 1

The analytical unit is a sensor window:

* control window;
* or pre-event window.

Frozen chronological partitions:

| Split      | Windows | Control | Pre-Event |
| ---------- | ------: | ------: | --------: |
| Training   |     158 |      86 |        72 |
| Validation |      33 |      14 |        19 |
| Test       |      36 |      13 |        23 |

All three machines were represented in every partition.

### Experiment 2

The analytical unit is an eligible positive-event onset.

The target distinguishes:

* isolated events;
* and burst-initiating events.

After final model selection, the frozen specification was refitted on 91 development events:

* 60 isolated events;
* 31 burst-initiating events.

The untouched chronological test set contained 23 events:

* 10 isolated events;
* 13 burst-initiating events.

---

## Methodological Principles

Several rules were maintained throughout the project:

1. chronological splitting rather than random splitting;
2. preprocessing fitted using development data only;
3. training-only feature analysis and feature selection;
4. explicit separation of model development and final test evaluation;
5. frozen model specifications before test evaluation;
6. no post-test tuning of failed models;
7. preservation of negative results;
8. explicit uncertainty analysis;
9. no production claims unsupported by the available evidence.

These principles are especially important in temporal predictive-maintenance problems, where random splitting can create unrealistic estimates of future performance.

---

## Project Workflow

```text
Raw time-ordered sensor data
            ↓
Data validation and temporal exploration
            ↓
Sensor-window feature engineering
            ↓
Experiment 1:
Static pre-event classification
            ↓
Promising development performance
            ↓
Frozen chronological test failure
            ↓
Temporal stability investigation
            ↓
Event-process investigation
            ↓
Discovery of recurrence and burst structure
            ↓
Prediction-target reformulation
            ↓
Experiment 2:
Imminent-event recurrence-risk classification
            ↓
Restrained model comparison
            ↓
Frozen one-feature logistic regression
            ↓
Locked chronological test
            ↓
Uncertainty-aware proof-of-concept conclusion
```

---

# Experiment 1: Static Pre-Event Classification

## Objective

Experiment 1 asked:

> **Can static engineered sensor-window features distinguish normal control windows from windows immediately preceding positive events?**

The target was:

* `window_type = 0`: control window;
* `window_type = 1`: pre-event window.

---

## Feature Engineering and Predictor Reduction

After removing identifiers and metadata, Experiment 1 contained 63 numerical predictors.

Using training data only, highly correlated predictors were investigated using an absolute correlation threshold of `0.95`.

Twelve predictors were removed, reducing the predictor space:

* from 63 predictors;
* to 51 predictors.

The project then evaluated several baseline models.

---

## Baseline Validation Results

| Model               | ROC-AUC | PR-AUC | Recall |     F1 |
| ------------------- | ------: | -----: | -----: | -----: |
| Logistic Regression |  0.5226 | 0.6249 | 0.5263 | 0.5714 |
| Random Forest       |  0.5564 | 0.6057 | 0.5789 | 0.6111 |
| Regularized XGBoost |  0.5977 | 0.7033 | 0.5789 | 0.6111 |

Random Forest showed substantial overfitting.

Regularized XGBoost became the primary model family for restrained further investigation.

---

## Final Frozen Experiment 1 Model

The final model was a regularized XGBoost classifier using 30 training-derived predictors.

The model specification and classification threshold were frozen before final test evaluation.

Final validation performance:

| Metric    |  Score |
| --------- | -----: |
| Accuracy  | 0.6364 |
| Precision | 0.7059 |
| Recall    | 0.6316 |
| F1-score  | 0.6667 |
| ROC-AUC   | 0.6128 |
| PR-AUC    | 0.7378 |

The retained classification threshold was `0.50`.

---

## Untouched Future Test Result

The frozen model was evaluated once on the untouched chronological test partition.

| Metric            |  Score |
| ----------------- | -----: |
| Accuracy          | 0.4444 |
| Precision         | 0.5882 |
| Recall            | 0.4348 |
| F1-score          | 0.5000 |
| ROC-AUC           | 0.4415 |
| PR-AUC            | 0.6423 |
| Balanced Accuracy | 0.4482 |

Confusion matrix:

|                  | Predicted Control | Predicted Pre-Event |
| ---------------- | ----------------: | ------------------: |
| Actual Control   |                 6 |                   7 |
| Actual Pre-Event |                13 |                  10 |

The below-random ROC-AUC showed that the problem was not simply an inappropriate classification threshold.

The probability ranking itself failed to generalize.

The final XGBoost model was rejected as a deployment candidate.

> **No post-test tuning was performed.**

---

# From Model Failure to Problem Reformulation

The failed future test result triggered investigation of the underlying event-generation process rather than additional model tuning.

The analysis examined:

* event duration;
* recurrence behaviour;
* inter-event timing;
* short-term burst structure;
* chronological stability;
* event eligibility;
* and structural properties of candidate sensor signals.

The investigation showed that positive events were predominantly:

* brief;
* recurrent;
* and frequently organized into short-term bursts.

This changed the interpretation of the prediction problem.

The original target attempted to identify a broad static pre-event state. The observed process suggested that a narrower recurrence-risk question might be more coherent.

The key methodological decision was:

> **When a model fails to generalize, investigate whether the target itself is aligned with the data-generating process before increasing model complexity.**

---

# Experiment 2: Imminent-Event Recurrence-Risk Classification

## Reformulated Question

Experiment 2 asked:

> **When an eligible positive event is already imminent, is it more likely to remain isolated or initiate a short-term recurrent burst?**

This is a conditional prediction problem.

The model does **not** independently detect that an event is approaching. It assumes that an upstream mechanism has already identified an imminent eligible event.

---

## Predictor Construction

Features were calculated from the five-minute sensor history preceding each eligible event.

Training-only feature analysis identified several candidate signals.

A six-feature logistic regression baseline was initially evaluated, but it generalized poorly on validation data.

A hypothesis-driven model using only:

`IMF_2_mean`

performed substantially better.

A final restrained comparison tested whether adding `vibration_change` improved the model. It did not.

---

## Validation-Stage Model Comparison

| Model                             |     PR-AUC |    ROC-AUC |     Recall |         F1 |
| --------------------------------- | ---------: | ---------: | ---------: | ---------: |
| Six-feature logistic regression   |     0.4680 |     0.4405 |     0.5714 |     0.5000 |
| `IMF_2_mean` only                 | **0.7071** | **0.7381** | **0.8571** | **0.6667** |
| `IMF_2_mean` + `vibration_change` |     0.6360 |     0.6548 |     0.7143 |     0.5556 |

The simplest formulation performed best.

No additional feature combinations were tested after this comparison.

---

## Final Frozen Experiment 2 Model

The final specification was frozen as:

* **Predictor:** five-minute pre-event `IMF_2_mean`
* **Preprocessing:** `StandardScaler`
* **Model:** `LogisticRegression`
* **Class weighting:** `balanced`
* **Classification threshold:** `0.50`

Before final test evaluation, no additional:

* features;
* model families;
* threshold optimization;
* or hyperparameter search

was performed.

The frozen specification was then refitted using the combined training and validation data.

---

## Locked Future Test Results

The final model was evaluated once on the untouched chronological test set of 23 events.

| Metric    |      Score |
| --------- | ---------: |
| PR-AUC    | **0.8292** |
| ROC-AUC   | **0.7615** |
| Precision | **0.7500** |
| Recall    | **0.6923** |
| F1-score  | **0.7200** |

Confusion matrix:

|                         | Predicted Isolated | Predicted Burst-Initiating |
| ----------------------- | -----------------: | -------------------------: |
| Actual Isolated         |                  7 |                          3 |
| Actual Burst-Initiating |                  4 |                          9 |

The model:

* detected 9 of 13 burst-initiating events;
* missed 4 burst-initiating events;
* correctly classified 7 of 10 isolated events;
* falsely flagged 3 isolated events.

The positive-class prevalence in the test set was `56.5%`.

The test PR-AUC of `0.8292` was therefore substantially above the prevalence-based no-skill reference.

> **No post-test tuning was performed.**

---

## Model Interpretation

The standardized logistic-regression coefficient for `IMF_2_mean` was:

`-0.5446`

The direction of the relationship was:

> **Lower five-minute pre-event `IMF_2_mean` values → higher predicted probability of burst initiation**

A one-standard-deviation increase in `IMF_2_mean` multiplied the estimated odds by approximately `0.58`.

Equivalent interpretation:

* one-standard-deviation increase → approximately 42% lower estimated odds;
* one-standard-deviation decrease → approximately 1.72 times the estimated odds.

The relationship was monotonic and directly interpretable.

---

## Comparative Result

| Aspect                | Experiment 1                     | Experiment 2                          |
| --------------------- | -------------------------------- | ------------------------------------- |
| Prediction problem    | General pre-event classification | Recurrence risk among imminent events |
| Analytical unit       | Sensor window                    | Eligible event onset                  |
| Final model           | 30-feature XGBoost               | One-feature logistic regression       |
| Test PR-AUC           | 0.6423                           | 0.8292                                |
| Test ROC-AUC          | 0.4415                           | 0.7615                                |
| Future generalization | Failed                           | Promising                             |
| Final status          | Rejected                         | Proof of concept                      |

The project therefore did not establish a validated long-horizon failure-prediction system.

Instead, it established that:

1. the original static pre-event classification problem was chronologically unstable;
2. investigation of the event process revealed meaningful recurrence and burst structure;
3. reformulating the target around imminent-event recurrence risk produced a simpler, more interpretable, and more temporally generalizable proof of concept.

---

## Business Interpretation

The final model should be interpreted as a **recurrence-risk decision-support model**.

A potential operational workflow is:

> **sensor stream → upstream imminent-event trigger → five-minute pre-event feature calculation → recurrence-risk score → maintenance or monitoring response**

A higher recurrence-risk score could support:

* increased short-term monitoring;
* prioritized machine inspection;
* maintenance-resource preparation;
* review of recent operating conditions;
* and escalation of events that may initiate repeated instability.

The model is most relevant to **maintenance prioritization and escalation**, not autonomous shutdown or automatic maintenance decisions.

The appropriate operating threshold should eventually be selected using:

* the cost of unnecessary inspection;
* the cost of missed recurrent events;
* maintenance-team capacity;
* downtime consequences;
* and safety requirements.

---

## Limitations

The current result is promising, but several limitations remain.

### Small Event Sample

The untouched Experiment 2 test set contained only 23 events.

Performance estimates therefore remain uncertain and should not be interpreted as precise estimates of future production performance.

### Limited Machine Population

The dataset contains only three machines.

The result does not establish transferability across different equipment types, sites, or operating environments.

### Limited Chronological Coverage

A single future holdout period is important, but it does not establish long-term temporal stability.

### Missing Upstream Event Detector

The final model assumes that an eligible event is already imminent.

The project does not provide a validated upstream mechanism for independently detecting that event.

### Association, Not Causation

`IMF_2_mean` is an informative predictive signal within this dataset.

The project does not establish that it is a universal physical cause of recurrent instability.

### Probability Calibration

The model's probability calibration has not yet been established.

### Business Threshold Optimization

The classification threshold has not been optimized using maintenance economics or operational costs.

### Drift Monitoring

A production system would require monitoring for:

* predictor drift;
* sensor-quality changes;
* event-prevalence changes;
* model-performance degradation;
* and calibration drift.

---

## Production Requirements

Before production consideration, the complete system would require:

* substantially more chronological data;
* more positive events;
* more burst-initiating events;
* additional machines;
* repeated future holdout periods;
* independent validation where possible;
* validated upstream event detection;
* probability-calibration assessment;
* sensor and process drift monitoring;
* and economic evaluation of false positives and false negatives.

> **The final model is a promising and interpretable proof of concept, not a deployment-ready predictive-maintenance system.**

---

## Repository Structure

```text
predictive-maintenance-recurrence-risk/
│
├── README.md
├── predictive_maintenance_recurrence_risk.ipynb
├── requirements.txt
├── .gitignore
├── .gitattributes
│
├── data/
│   └── README.md
│
└── images/
```

---

## Technologies Used

* Python
* Jupyter Notebook
* pandas
* NumPy
* scikit-learn
* XGBoost
* SciPy
* Matplotlib
* Seaborn

Key modelling and analytical techniques include:

* chronological train-validation-test splitting;
* time-window feature engineering;
* correlation analysis;
* feature selection;
* logistic regression;
* random forest;
* gradient-boosted trees;
* precision-recall analysis;
* ROC analysis;
* threshold evaluation;
* machine-level diagnostics;
* temporal stability investigation;
* bootstrap uncertainty analysis;
* and probability interpretation.

---

## How to Run

1. Clone the repository.
2. Create and activate a Python environment.
3. Install the required dependencies:

```bash
pip install -r requirements.txt
```

4. Follow the dataset instructions in `data/README.md`.
5. Open:

```text
predictive_maintenance_recurrence_risk.ipynb
```

6. Restart the kernel and run all cells from top to bottom.

---

## Key Methodological Lessons

This project demonstrates several broader lessons for applied machine learning:

* future generalization matters more than development performance;
* a failed model may reveal a problem with the target rather than the algorithm;
* understanding the data-generating process can be more valuable than adding model complexity;
* simple models can be preferable when the evidence base is small;
* leakage prevention must cover the full analytical pipeline;
* the test set should remain a final evaluation mechanism;
* predictive performance should be interpreted together with uncertainty;
* and honest negative results can strengthen a data-science project.

The central lesson is:

> **Strong applied data science is not only the search for a better model. It is the disciplined process of testing whether the prediction problem, data, validation design, and resulting evidence support the claim being made.**

---

## Final Project Status

* Experiment 1: **completed and rejected as a deployment candidate**
* Experiment 2: **completed as a promising proof of concept**
* Final notebook: **frozen**
* Post-test tuning: **none**
* Production readiness: **not yet established**

---

## Author

**Veeranna Hanamashetti**

Data Science | Machine Learning | AI | Analytics | Operational Excellence
