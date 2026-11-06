# HERA — Etch Recipe Advisor

HERA is an AI-assisted decision-support system for semiconductor Etch recipe evaluation.

The project was developed during the **SK mySUNI AI-Based Industry Problem-Solving Project** to explore how process data and machine-learning models could support engineers when evaluating Etch recipes.

Instead of reviewing recipe conditions and process-quality results separately, HERA connects **Recipe, Wafer, and Site-level data** and predicts key quality indicators such as **CD, Depth, and Defect**. The predicted results are then converted into quality scores so that different recipe conditions can be compared through an interactive dashboard.

> **Project Period:** Jun. 2026 – Aug. 2026  
> **Team:** 5 members  
> **Role:** Team Leader  
> **Result:** 3rd Place (Encouragement Award)  
> **Tech Stack:** Python · Streamlit · RandomForest · XGBoost · MLP

**Demo:** https://hera-simulator-sunic-team16.streamlit.app/

---

## 1. Project Background

In semiconductor Etch processes, multiple recipe parameters interact with equipment and process conditions, making it difficult to judge recipe performance from a single parameter alone.

The goal of HERA was to build a prototype that could organize this process into a more systematic flow:

```text
Recipe Conditions
       ↓
Quality Prediction
       ↓
Quality Evaluation
       ↓
Recipe Comparison
       ↓
Parameter Adjustment Support
```

Rather than automatically determining the optimal recipe, the system was designed as a **decision-support tool** that helps users compare recipe conditions and review predicted process outcomes.

---

## 2. What HERA Does

HERA supports four Etch process datasets:

- **Isolation Etch**
- **Trench Etch**
- **Gate PolySi Etch**
- **Metal Al Etch**

For each process, recipe parameters are used as model inputs to predict process-quality indicators.

### Prediction Targets

The system evaluates key wafer-quality metrics including:

- **CD (Critical Dimension)**
- **Depth**
- **Defect**

Predicted values are compared with target specifications and converted into quality scores.

This allows users to review both the predicted process result and the relative quality of a recipe condition.

---

## 3. System Workflow

HERA connects process data and ML models through the following workflow:

```text
Recipe / Wafer / Site Data
            ↓
      Data Processing
            ↓
 Process-Specific ML Model
            ↓
 CD / Depth / Defect Prediction
            ↓
       Quality Scoring
            ↓
 Recipe Evaluation & Comparison
            ↓
 Parameter Adjustment Support
            ↓
      Streamlit Dashboard
```

The dashboard provides two main ways to evaluate recipes.

### Recipe Evaluation

Users can enter recipe conditions and examine:

- predicted CD, Depth, and Defect values
- expected quality scores
- recipe revision performance
- wafer-level process results

### Target-Based Evaluation

Users can define target quality conditions and review how recipe parameters may be adjusted to move predicted results toward those targets.

The recommendation result is intended to support engineering judgment rather than replace process validation.

---

## 4. Machine-Learning Approach

HERA uses multiple supervised-learning models, including:

- **RandomForest**
- **XGBoost**
- **MLP**

Models are trained separately according to process and prediction target.

For each Etch process, the modeling pipeline performs:

```text
Process Data
    ↓
Feature / Target Configuration
    ↓
Model Training
    ↓
Model Evaluation
    ↓
Best Model Selection
    ↓
Prediction Pipeline
```

The selected models are then connected to the Streamlit application for inference.

---

## 5. Recipe-Version Holdout Validation

One of the key considerations during model development was how to divide training and validation data.

A random split at the Site level could place measurements originating from the **same Recipe revision** in both training and validation datasets.

This could cause the validation result to appear better than the model's actual ability to handle a new recipe condition.

To reduce this risk, **Recipe-version-level holdout validation** was applied.

```text
Training
Recipe Rev A
Recipe Rev B
Recipe Rev C

        ↓

Validation
Unseen Recipe Rev D
```

This approach was used to evaluate whether the model could maintain prediction performance when encountering recipe revisions that were not included in training.

---

## 6. Input Range Validation

Another issue appeared when testing recipe conditions outside the range observed during model training.

Tree-based models such as RandomForest and XGBoost do not naturally extrapolate beyond the values they have learned.

For example, even when an input parameter is far outside the training range, the model may still return a seemingly reasonable prediction.

This can make an unreliable prediction appear valid.

HERA therefore checks whether recipe parameters fall within the range observed during training before running inference.

### Validation Logic

- Inputs within the training range are evaluated normally.
- Inputs outside the range generate a warning.
- Users may still execute the prediction with an explicit override.
- Results generated outside the training range are marked as lower-confidence.
- Unsupported equipment combinations are blocked.

The same validation logic is applied across the supported Etch processes.

---

## 7. Process Dashboard

The Process Dashboard was designed to help users review both historical and predicted process behavior.

It includes:

- Recipe revision scoring
- Recipe quality ranking
- Wafer-level result visualization
- Wafer Map
- Edge → Center → Edge profile visualization
- Quality indicator comparison

Recipe revisions can therefore be compared not only through individual predicted values, but also through their overall quality score and wafer-level behavior.

---

## 8. My Role

As the **team leader of a five-member team**, I participated in both system development and project coordination.

My main contributions included:

- developing and evaluating ML-based quality prediction using linked Recipe, Wafer, and Site data
- applying Recipe-version holdout validation to test performance on unseen recipe revisions
- testing recipe and target-quality scenarios to verify whether recommendation results changed appropriately
- identifying recommendation logic that produced unchanged rankings under different target conditions and refining the logic
- implementing safeguards for recipe parameters outside the model training range
- supporting integration and deployment of the Streamlit prototype

The project received **3rd Place (Encouragement Award)** in the SK mySUNI AI-Based Industry Problem-Solving Project.

---

## 9. Repository Structure

| Path | Description |
|---|---|
| `app.py` | Streamlit UI and application flow |
| `model.py` | Prediction, quality scoring, and recommendation logic |
| `data_utils.py` | Data loading, unit conversion, and process configuration |
| `ml_engine/{process}_core.py` | Process-specific inference pipeline |
| `ml_engine/{process}_models/` | Trained model files and metadata |
| `ml_engine/training/` | Model-training pipelines |
| `data/` | Process datasets used by the prototype |
| `requirements.txt` | Python dependencies |

Supported process modules include:

```text
isolation
trench
gate
metal
```

For additional implementation details, see:

- [`PROJECT_OVERVIEW.md`](PROJECT_OVERVIEW.md)
- [`ml_engine/training/README.md`](ml_engine/training/README.md)

---

## 10. Run Locally

```bash
git clone https://github.com/ggoungse0/Etch-Recipe-Advisor.git
cd Etch-Recipe-Advisor

pip install -r requirements.txt
streamlit run app.py
```

The application will open locally at:

```text
localhost:8501
```

---

## 11. Deployment

The prototype is deployed using **Streamlit Community Cloud**.

The deployed application is intended for project demonstration and prototype evaluation.

Credentials and sensitive configuration values should be managed through Streamlit Secrets and should not be committed to the repository.
