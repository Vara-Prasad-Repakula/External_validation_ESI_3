# Abdominal Pain in the Emergency Department

Research notebooks for predicting **discharge home versus non-ICU inpatient admission** among adults presenting with abdominal pain and assigned Emergency Severity Index (ESI) level 3. Model development and internal temporal evaluation use MIMIC data; external validation uses MC-MED.

This release contains four analysis notebooks and this README. It provides the analysis code, not a self-contained reproduction package: source datasets, frozen feature dictionaries, clinician-review inputs and generated model artifacts are not bundled.

## Notebooks

Run the notebooks in numerical order, and run cells within each notebook from top to bottom.

| Notebook | Purpose |
| --- | --- |
| [01_data_preparation.ipynb](notebooks/01_data_preparation.ipynb) | Builds and harmonises cohorts, defines the outcome and approximate-year temporal partitions, reconstructs features using frozen dictionaries, and saves the hand-off files. |
| [02_internal_modelling.ipynb](notebooks/02_internal_modelling.ipynb) | Compares Random Forest, XGBoost and LightGBM; selects the model, calibrates probabilities and locks a threshold; evaluates internal temporal performance and computes grouped SHAP values. |
| [03_external_validation.ipynb](notebooks/03_external_validation.ipynb) | Applies the locked model, calibrator and threshold to MC-MED; estimates performance uncertainty and calibration diagnostics; generates external grouped SHAP values. |
| [04_shap_clinician_analysis.ipynb](notebooks/04_shap_clinician_analysis.ipynb) | Assesses cross-dataset SHAP stability and compares model interpretation with the ten-clinician review. |

The feature stages in these notebooks are **S0: triage**, **S1: previous diagnoses and medications**, and **S2: current laboratory and imaging findings**. The full representation contains 2,495 grouped raw features. S2 uses information from the ED work-up, so the full model should not be described as an arrival-only triage model.

## Data and access

Obtain the datasets directly from their official PhysioNet pages. Access to this code does not grant access to the data.

| Dataset | Version / source | Use |
| --- | --- | --- |
| MIMIC-IV | [3.1](https://physionet.org/content/mimiciv/3.1/) | Hospital admissions, demographics, diagnoses, laboratory data and ICU linkage. |
| MIMIC-IV-ED | [2.2](https://physionet.org/content/mimic-iv-ed/2.2/) | ED encounters, triage, disposition and medication reconciliation. |
| MIMIC-IV-Note | [2.2](https://physionet.org/content/mimic-iv-note/2.2/) | Radiology notes and related information. |
| MC-MED | [1.0.1](https://physionet.org/content/mc-med/1.0.1/) | Independent external-validation source. |

Create a PhysioNet account, complete the credentialing and required training listed on each dataset page, and accept the applicable data-use agreement before downloading. Follow each source's current access instructions and citation requirements. Keep downloaded clinical data and patient-level derived files in an appropriately controlled environment; do not upload them to this repository.

## Open and run the files

Download this repository using GitHub's **Code → Download ZIP**, extract it, and open the `.ipynb` files in JupyterLab or VS Code with Jupyter support. GitHub can also display the notebooks for reading without executing them.

Use a dedicated Python environment. The notebooks include Linux-style environment checks, including an expected `bin/python` path in Notebook 2; use a Linux environment or adapt those checks for another operating system. The imported third-party packages include:

```text
numpy pandas scipy scikit-learn xgboost lightgbm shap
matplotlib plotly pyarrow joblib python-dateutil tqdm
ipykernel ipython
```

JupyterLab is needed if using that interface, and an Excel reader such as `openpyxl` is needed for the clinician `.xlsx` input. This release does not include a version-pinned environment; installing the latest packages is not a guarantee of reproducing the original results. Preserve the package versions from the study environment when reproducing the manuscript.

### Configure local paths

Set these environment variables in the environment that launches the notebook kernel. Use absolute paths. Set the project root explicitly even when opening a notebook from the `notebooks/` subdirectory.

| Variable | Meaning |
| --- | --- |
| `ABDOMINAL_PAIN_ED_PROJECT_ROOT` | Working project directory containing `notebooks/` and the separately supplied supporting inputs. |
| `ABDOMINAL_PAIN_ED_DATA_ROOT` | Base directory for downloaded source datasets; defaults to `PROJECT_ROOT/data_external`. |
| `MCMED_DATA_DIR` | Directory containing MC-MED `visits.csv`, `labs.csv`, `meds.csv`, `pmh.csv` and `rads.csv`. |
| `MIMIC_IV_ED_DIR` | MIMIC-IV-ED `ed` directory. |
| `MIMIC_IV_HOSP_DIR` | MIMIC-IV `hosp` directory; retain the associated ICU directory for ICU linkage. |
| `MIMIC_IV_NOTE_DIR` | MIMIC-IV-Note `note` directory. |
| `ABDOMINAL_PAIN_ED_PYTHON` | Expected Python executable used by the notebook checks. |
| `ABDOMINAL_PAIN_ED_VENV` | Expected Python environment directory used by Notebook 2. |

### Additional inputs required

**The four notebooks cannot reproduce the complete study from a fresh download on their own.** In addition to authorized source data, they depend on study-specific resources that are outside this minimal release:

- **Frozen feature dictionaries:** Notebook 1 expects `config/feature_vocabularies/`, including `frozen_s1_s2_feature_manifest.csv` and additional vocabulary, lookup and mapping files. It refers to an export step in `abd_event_log.ipynb`, which is not included here. The complete compatible dictionary set is needed; creating empty files will not satisfy the checks.
- **Clinician review:** Notebook 4 expects `data/clinical_validation/raw/external_clinician_feature_group_review_10_clinicians.xlsx`.
- **Sequential hand-offs:** Notebooks 2–4 consume cohort, feature, model, calibration, threshold, SHAP and checksum artifacts produced by preceding stages. These generated files are not included in this release.

Contact the study authors about the availability of these study-specific inputs and the original software environment. Their availability and any sharing conditions must be established separately; this README does not promise unrestricted access. Keep all generated patient-level files local.

## Release preparation and limitations

The distributed notebooks have empty code-cell outputs and null execution counts. Python comment tokens are absent. Markdown explanations and function docstrings are retained to document the analysis. Notebook metadata was reduced to essential kernel/language information, and cell metadata was cleared.

Static checks verified that all code cells parse as Python and that cleaning preserved their abstract syntax trees. The analysis was not rerun during release preparation: source datasets and supporting study inputs were not loaded. These checks do not establish numerical reproducibility or clinical validity.

The code supports retrospective research. SHAP associations are not causal effects, and this repository does not provide a validated clinical decision-support application.

## Citation

When using this work, cite the associated manuscript once its bibliographic details are available, the repository revision used, and the original datasets using the citations on their PhysioNet pages. No manuscript DOI or publication status is asserted in this release.
