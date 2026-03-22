# Copilot Instructions for Colorectal Cancer Survival Analysis

**Project Type**: Data Science - Survival Analysis  
**Domain**: Biostatistics & Oncology (Colorectal Cancer)  
**Language**: Python  
**Key Tools**: Jupyter Notebooks, pandas, scikit-survival, lifelines

---

## Project Overview

This workspace contains survival analysis models for colorectal cancer patients using Cox Proportional Hazards (CPH) regression. The analysis pipeline includes data preparation, exploratory analysis, model fitting, validation, and prediction.

### Directory Structure

```
IC/
├── Cox/                          # Main survival analysis project
│   └── COX.ipynb                 # Primary analysis notebook
├── Machine Learning/             # Secondary ML experiments
│   └── Ensinando_à_Máquina.ipynb
├── DataSet/                      # Patient data
│   ├── Colorretal_tratado_2025.csv
│   └── pacigeral_2025.csv
└── .venv/                        # Python virtual environment
```

---

## Code Conventions & Patterns

### Notebook Organization

Notebooks follow a consistent structure with clearly delineated sections:

1. **Initialization** - Library installations and imports
2. **Data Treatment** - Data preparation, cleaning, and feature engineering
3. **Encoding** - Categorical variable encoding (OneHotEncoder, OrdinalEncoder)
4. **Model Fitting** - Cox model training and calibration
5. **Validation** - Proportional hazards assumptions, Kaplan-Meier analysis, diagnostics
6. **Metrics & Evaluation** - C-Index, IBS, calibration curves
7. **Prediction** - Survival time estimation and model comparison

### Function Documentation

All functions include docstrings in Portuguese with:
- **Descrição**: Function purpose
- **Parâmetros**: Parameter descriptions and types
- **Retorno**: Return value description

Example pattern:
```python
def function_name(param1, param2):
    """
    Descrição:
      Brief description of what the function does.
    
    Parâmetros:
      param1: type
        Description of parameter 1.
    
    Retorno:
      type
        Description of return value.
    """
```

### Markdown Headers in Notebooks

Use HTML color-coded headers for visual hierarchy:
- `<font color = 56A5EC>**Title**</font>` - Main section (blue)
- `<font color = FFB90F>**Subtitle**</font>` - Subsection (gold)
- `<font color = #888B90>` - Explanatory text (gray)

### Data Processing Pipeline

#### Preparation (`data_preparing()`)
- Filter data by clinical criteria (topography, staging, diagnostic confirmation)
- Extract numeric DRS from string format
- Convert date columns and calculate time-to-event differences
- Categorize temporal variables (CONSDIAG_CAT, TRATCONS_CAT, DIAGTRAT_CAT)
- Select specific morphology types (adenocarcinoma, signet ring, mucinous, undifferentiated)
- Remove irrelevant columns

#### Encoding (`encoding()`)
- **Ordinal Encoding**: ECGRUP (I < II < III < IV ordered relationship)
- **One-Hot Encoding**: TOPO (topography), MORFO_CAT (morphology)
- Handle unknown values gracefully
- Return both encoded DataFrame and encoder objects for reuse

#### Model Preparation (`pred_cols()`)
- Convert ULTIDIAG (days) to 'time' (months): `ceil(days/30)`
- Create binary 'event' column (1=death, 0=censored)
- Censor observations beyond 60 months (5-year follow-up)

### Modeling & Validation

#### Cox Model Workflow
```python
cph = CoxPHFitter(penalizer=0.01)
cph.fit(Xt, duration_col='time', event_col='event', strata=strata_cols)
```

Key parameters:
- **strata**: Stratified analysis (DIAGPREV, TRATCONS_CAT, CONSDIAG_CAT) - relaxes proportional hazards assumption
- **penalizer**: L2 regularization (0.01) aids convergence

#### Validation Methods
- **Proportional Hazards Test**: `proportional_hazard_test()` with Schoenfeld residuals
- **Log-Log Plots**: Visual check for parallel survival curves
- **Martingale Residuals**: Assess linearity assumption
- **Kaplan-Meier by Group**: Log-rank tests for group differences
- **Calibration Curve**: Assess prediction accuracy (ICI, E50 metrics)

#### Model Selection
- Filter predictors by significance (p < 0.05)
- Compare full vs. reduced models via:
  - C-Index (concordance)
  - C-Index IPCW (Inverse Probability Censoring Weighting)
  - IBS (Integrated Brier Score)
  - MSE, RMSE, MAE

### Metrics & Interpretation

| Metric | Interpretation |
|--------|----------------|
| **C-Index** | Probability model correctly ranks event times (0.5=random, 1.0=perfect) |
| **C-Index IPCW** | Adjusted for censoring bias |
| **IBS** | Prediction error integrated over time (lower=better) |
| **MAE/RMSE** | Mean absolute/squared error in survival time prediction |

---

## Best Practices

### Data Management
- Always create backup copies with `.copy()` before transformations
- Reset indices after filtering: `df_aux.reset_index(drop=True)`
- Use `.fillna()` with explicit logic, not arbitrary values
- Validate data shapes after each major transformation

### Modeling
- Use **penalizer** to improve convergence (especially with many features)
- **Always stratify** when proportional hazards assumption is violated
- Compare models on **multiple metrics** (not just C-Index)
- Document all filtering criteria clearly in functions

### Visualization
- Add `plt.grid(True)` for readability
- Use descriptive titles: "Metric by Variable" format
- Include confidence intervals where appropriate (e.g., KM curves)
- Use `plt.tight_layout()` to prevent label overlap

### Comments & Clarity
- Write section headers as comments: `# === SECTION NAME ===`
- Explain **why** models diverge (e.g., "reduced model removes insignificant variables")
- Include expected ranges/clues for interpretation

---

## Common Workflows

### Adding a New Predictor Variable
1. Add to `data_preparing()` selection or creation logic
2. Update encoding function if categorical
3. Refit model and check p-value significance
4. Add to appropriate Kaplan-Meier analysis group if relevant

### Extending Follow-up Time
- Modify censoring threshold: `df_aux.loc[df_aux['time'] > <NEW_THRESHOLD>, ...] = [<NEW_THRESHOLD>, 0]`
- Update `t0` parameter in metric functions (default=60 months)
- Re-validate proportional hazards assumption with new temporal range

### Comparing Model Versions
- Use `cox_metrics_table()` function for side-by-side comparison
- Create visualization with `plt.subplots(1, 2)` for models
- Document which variables were removed and their p-values

---

## Environment Setup

### Virtual Environment
- Python version: Managed in `.venv/`
- Installation: `%pip install <package> --quiet` (in notebooks)

### Key Dependencies
```python
# Data manipulation
pandas, numpy

# Survival analysis
lifelines, scikit-survival

# Machine learning
scikit-learn (preprocessing only)

# Visualization
matplotlib, seaborn
```

---

## Useful Commands (PowerShell)

```powershell
# Activate environment
.\.venv\Scripts\Activate.ps1

# Run notebook (opens Jupyter)
jupyter notebook

# Check environment packages
pip list
```

---

## Writing New Code

### Function Template
```python
def meaningful_function_name(mandatory_param1, optional_param=default_value):
    """
    Descrição:
      What does this function accomplish?
    
    Parâmetros:
      mandatory_param1: type
        Explain this parameter.
      optional_param: type, default=...
        Explain this parameter.
    
    Retorno:
      return_type
        Describe the return value.
    """
    
    # === STEP 1 DESCRIPTION ===
    # Logic here
    
    # === STEP 2 DESCRIPTION ===
    # Logic here
    
    return result
```

### Analysis Cell Template
```python
# [MEANINGFUL DESCRIPTION]

# Step 1 - Prepare/calculate
variable = operation_on(df)

# Step 2 - Display/visualize
display(variable.head())
print(f"Summary: {variable.describe()}")

# Step 3 - Interpretation
# Explain what this means for the analysis
```

---

## Debugging & Troubleshooting

### Common Issues

**Model won't converge**: Increase penalizer (e.g., 0.01 → 0.1) or standardize features

**Proportional hazards violated**: Use stratification on problematic variable

**Calibration poor at high times**: Fewer events at tail of distribution; consider shorter t0

**Memory issues with large dataset**: Filter early in `data_preparing()`, use `.copy()` sparingly

---

## Next Steps for New Contributors

1. Review [Cox/COX.ipynb](Cox/COX.ipynb) to understand the analysis pipeline
2. Understand patient selection criteria in `data_preparing()`
3. Familiarize with encoding strategy (ordinal vs. one-hot)
4. Review proportional hazards diagnostics (Schoenfeld test, log-log plots)
5. Compare full vs. reduced model validation approaches

For questions about specific statistical methods, refer to comments in applicable cells.
