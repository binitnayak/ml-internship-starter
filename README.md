# FlyRank Refresh Opportunity Model

## 1. What It Does

This project helps content teams prioritize existing web pages that may need a content refresh.

The system uses page-level performance and content signals to identify pages that are more likely to show declining search performance. A machine learning model scores the pages and produces a ranked refresh queue.

The main goal is to help reviewers focus their limited time on pages with stronger signals of potential decline instead of manually reviewing every page.

The system is designed as a **decision-support tool**. A high-ranked page is a candidate for review and refresh; the prediction does not guarantee that refreshing the page will improve its future performance.

The model does not use titles, URLs, client names, domains, or keywords.

## 2. Who It Is For

This project is designed for:

* Content teams
* SEO teams
* Content reviewers
* Website owners
* Editorial teams

These users can use the ranked queue to identify pages that deserve further investigation and determine which pages may benefit from a content refresh.

The final decision to refresh a page remains with the human reviewer.

## 3. Setup

### Requirements

The project requires:

* Python 3.x
* Git
* pip

The required Python packages are listed in `requirements.txt`.

### Clone the Repository

```bash
git clone https://github.com/binitnayak/ml-internship-starterL
cd ml-internship-starter-main
```

### Install Dependencies

Install the required packages:

```bash
pip install -r requirements.txt
```

The project uses packages including:

* pandas
* numpy
* scikit-learn
* matplotlib
* reportlab
* duckdb
* huggingface_hub

### Dataset

The repository includes an anonymized starter dataset:

```text
data/raw/content_refresh_anonymized.csv
```

No external BigQuery export is required for the bundled dataset.

### Run the Complete Pipeline

The complete machine learning pipeline can be executed using:

```bash
python scripts/run_all.py
```

The pipeline performs the following steps:

1. Prepare features
2. Generate the baseline score
3. Train the machine learning models
4. Evaluate the models and export the refresh queue
5. Build the PDF report

### Individual Pipeline Steps

The individual scripts can also be run separately:

```bash
python scripts/01_prepare_features.py
python scripts/02_baseline_score.py
python scripts/03_train_model.py
python scripts/04_evaluate_and_export.py
python scripts/05_build_pdf_report.py
```

## 4. Usage Examples

After installing the dependencies, run:

```bash
python scripts/run_all.py
```

The pipeline processes the bundled page-level dataset and generates predictions and a ranked refresh queue.

The general workflow is:

```text
Input Page Data
      ↓
Feature Preparation
      ↓
Baseline Scoring
      ↓
Machine Learning Model
      ↓
Model Evaluation
      ↓
Predictions
      ↓
Ranked Refresh Queue
      ↓
Human Review
```

The generated queue can be used to identify pages that should be inspected first.

### Example Queue Output

The final queue contains actions such as:

* `monitor`
* `refresh`
* `refresh_and_review_ctr`
* `refresh_and_review_engagement`
* `expand_and_refresh`

The top-ranked pages contain a score, model probability, recommended action, and reason codes that help reviewers understand why a page was prioritized.

## 5. Architecture

The project follows a machine learning pipeline designed for content-refresh prioritization.

```text
┌──────────────────────────────────┐
│     Anonymized Page-Level Data   │
│  content + performance signals   │
└────────────────┬─────────────────┘
                 │
                 ↓
┌──────────────────────────────────┐
│        Feature Preparation       │
│ Cleaning + feature engineering   │
└────────────────┬─────────────────┘
                 │
                 ↓
┌──────────────────────────────────┐
│        Baseline Scoring          │
│ Transparent rule-based baseline  │
└────────────────┬─────────────────┘
                 │
                 ↓
┌──────────────────────────────────┐
│        Model Training            │
│ Logistic Regression              │
│ Decision Tree                    │
│ Random Forest                    │
└────────────────┬─────────────────┘
                 │
                 ↓
┌──────────────────────────────────┐
│          Evaluation              │
│ Client-holdout validation        │
└────────────────┬─────────────────┘
                 │
                 ↓
┌──────────────────────────────────┐
│       Ranked Refresh Queue       │
│ Score + Probability + Reasons    │
└────────────────┬─────────────────┘
                 │
                 ↓
┌──────────────────────────────────┐
│          Human Review            │
│ Final refresh decision           │
└──────────────────────────────────┘
```

## 6. v2 Evaluation Results

The model was evaluated using a **client-holdout validation strategy**.

The evaluation dataset contained:

* **Rows scored:** 30,000
* **Declining-label rows:** 16,262
* **Declining-label rate:** 0.542
* **Target:** `is_declining_label`

### Model Comparison

The Random Forest model was selected as the best model using **Precision@50**.

| Model               |   ROC AUC | Average Precision | Precision@50 |    Recall |        F1 |
| ------------------- | --------: | ----------------: | -----------: | --------: | --------: |
| Decision Tree       |     0.742 |             0.575 |        0.540 |     0.716 |     0.634 |
| Logistic Regression |     0.700 |             0.522 |        0.400 |     0.567 |     0.566 |
| **Random Forest**   | **0.750** |         **0.618** |    **0.740** | **0.744** | **0.640** |
| Baseline Rules      |     0.627 |             0.468 |        0.240 |         - |         - |

### Selected Model

**Best model: Random Forest**

Selection criterion:

**Precision@50**

The Random Forest achieved:

* **ROC AUC:** 0.750
* **Average Precision:** 0.618
* **Precision@50:** 0.740
* **Recall:** 0.744
* **F1 Score:** 0.640

An accuracy value was not reported in the project's final model report, so it is intentionally not presented here.

## 7. Final Queue Results

The generated refresh queue contains:

| Queue Category                |  Count |
| ----------------------------- | -----: |
| High-confidence items         |  3,605 |
| Medium-confidence items       | 11,395 |
| Low-confidence items          | 15,000 |
| Monitor                       | 13,093 |
| Refresh                       |  8,178 |
| Refresh and review CTR        |  6,657 |
| Refresh and review engagement |  1,990 |
| Expand and refresh            |     82 |

### Top Features

The most important features in the Random Forest model were:

| Feature                 | Importance |
| ----------------------- | ---------: |
| `days_with_impressions` |     0.1578 |
| `log_impressions_90d`   |     0.1282 |
| `avg_position`          |     0.1090 |
| `content_age_days`      |     0.0955 |
| `char_count`            |     0.0426 |
| `word_count`            |     0.0397 |
| `log_clicks_90d`        |     0.0346 |
| `ctr`                   |     0.0330 |
| `scroll_rate`           |     0.0311 |
| `days_with_sessions`    |     0.0280 |

## 8. Limitations

The system has several important limitations:

* The model does not guarantee that every recommended page will improve after a content refresh.
* Model performance may vary when pages or clients differ significantly from the data used during development and validation.
* Predictions depend on the quality and availability of the input data.
* A high priority score does not automatically mean that a page must be refreshed.
* The model ranking should be used as a prioritization aid rather than an automatic publishing or content decision.
* Human review is still required before making a final refresh decision.
* The dataset is anonymized and does not include titles, URLs, client names, domains, or keywords, which limits the amount of contextual information available to reviewers.

## 9. AI Transparency

AI tools were used during development to assist with code, documentation, debugging, explanations, and development-related tasks.

I personally reviewed the implementation, checked the evaluation results, and verified the final project behavior.

The final model results and project outputs were reviewed before submission.

AI assistance was used as a development aid and not as a replacement for evaluation or human decision-making.

## 10. Project Structure

```text
ml-internship-starter-main/
│
├── data/
│   └── raw/
│       └── content_refresh_anonymized.csv
│
├── notebooks/
│   ├── 01_first_look_and_discovery.ipynb
│   ├── 02_your_first_readable_model.ipynb
│   └── 03_working_with_the_full_release.ipynb
│
├── outputs/
│   ├── charts/
│   │   ├── action_mix.svg
│   │   ├── confidence_mix.svg
│   │   ├── top_feature_importance.svg
│   │   ├── top_reason_codes.svg
│   │   └── trend_distribution.svg
│   ├── model_report.md
│   └── refresh_queue_sample.csv
│
├── scripts/
│   ├── 01_prepare_features.py
│   ├── 02_baseline_score.py
│   ├── 03_train_model.py
│   ├── 04_evaluate_and_export.py
│   ├── 05_build_pdf_report.py
│   ├── ml_utils.py
│   └── run_all.py
│
├── work/
│   └── notebooks/
│       ├── w01_research_question.ipynb
│       ├── w02_ml_task_framing.ipynb
│       ├── w03_data_contract.ipynb
│       ├── w03_feature_leakage_check.ipynb
│       ├── w04_baseline_score.ipynb
│       ├── w04_signal_audit.ipynb
│       ├── w05_model.ipynb
│       ├── w06_validation_audit.ipynb
│       └── w07_action_playbook.ipynb
│
├── requirements.txt
└── README.md
```

## 11. Generated Outputs

After running the pipeline, the project generates evaluation and reporting outputs.

Important outputs include:

```text
outputs/
├── model_report.md
├── refresh_queue.csv
├── model_results.json
├── summary.json
└── charts/
    ├── action_mix.svg
    ├── confidence_mix.svg
    ├── top_feature_importance.svg
    ├── top_reason_codes.svg
    └── trend_distribution.svg
```

These outputs provide the model evaluation results, ranked refresh queue, summary information, and visual analysis.

## 12. Demo

A 3–5 minute live demonstration accompanies this README.

The demo should show the actual project running rather than using presentation slides.

### Suggested Demo Flow

**0:00–0:30 — Introduction**

Explain the problem and the purpose of the project.

**0:30–1:15 — Design Decision**

Explain why the system uses a machine learning ranking approach and why the output is treated as a reviewer aid.

**1:15–3:15 — Live Demo**

Run the pipeline and show:

* Input data
* Feature preparation
* Model processing
* Predictions
* Ranked refresh queue
* Recommended actions

**3:15–4:15 — Evaluation**

Explain the Random Forest results, especially Precision@50, ROC AUC, Recall, and F1.

**4:15–4:45 — Limitation**

Explain that the model provides prioritization signals and does not guarantee that a content refresh will improve future performance.

### Demo Video

**Demo Link:** `https://github.com/binitnayak/ml-internship-starter/blob/main/README.md`

## 13. Repository

**GitHub Repository:** `https://github.com/binitnayak/ml-internship-starter`

## 14. Showcase Thread

**Showcase Thread:** `<ADD_SHOWCASE_THREAD_LINK>`

## 15. Practical Use

The ranked queue should be used as a reviewer aid rather than as an automatic publishing decision.

The safest production workflow is to first inspect high-confidence rows, verify the page manually, and compare the recommendation against editorial context.

The model helps answer:

> Which pages should we review first?

It does not automatically answer:

> Which pages should definitely be refreshed?

## 16. Conclusion

This project provides a machine learning-based approach for prioritizing content refresh opportunities.

The Random Forest model performed best among the evaluated models according to Precision@50 and produced a ranked queue containing model scores, probabilities, recommended actions, and reason codes.

The system is intended to make content review more focused and efficient while keeping the final decision with human reviewers.
