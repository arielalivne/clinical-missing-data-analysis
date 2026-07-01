# Clinical Missing Data Analysis

A modular R/Quarto pipeline for detecting and evaluating missing data 
in clinical datasets, with a focus on both clinical significance and 
statistical validity.

---

## Motivation

Clinical datasets are rarely complete. Before any statistical analysis 
can be conducted, missing data must be carefully examined — not only 
from a statistical perspective, but also from a clinical one: the 
pattern of what is missing may itself carry meaning.

This project was developed out of an interest in clinical research 
methodology. It addresses two core questions:

1. **Is the missingness clinically meaningful?** — Do specific 
variables tend to be missing together in ways that suggest 
non-random omission?
2. **Is listwise deletion statistically safe?** — If we remove 
participants with missing data, does the remaining sample still 
support valid inference?

---

## Project Structure

The analysis is split into two sequential pipelines, both implemented 
in Quarto (.qmd) and outputting styled HTML reports.

### Pipeline 1 — `clinical_missing_pipeline.qmd`
**Missing Data Detection & Clinical Significance**

This pipeline should be run first. It examines whether missingness 
is random or potentially meaningful.

Steps:
1. **Data loading** — Adapt the upload section to your file format; 
from that point the pipeline runs on any dataset.
2. **Missing data summary** — Identifies which variables contain 
missing values and quantifies the proportion of missingness per 
variable.
3. **Co-missingness patterns** — Visualizes whether missing values 
across different variables tend to occur together (via UpSet plot), 
which may indicate non-random missingness.
4. **Dependency analysis** — Tests whether missingness in one 
variable is statistically associated with other variables:
   - **Fisher's Exact Test** for binary categorical variables
   - **Point-Biserial Correlation** for continuous variables

> **Note:** Dependency analysis is currently implemented for binary 
categorical variables and continuous variables. Extension to 
multi-category variables via Kruskal-Wallis is a potential future 
direction.

**Output:** A styled HTML report with color-coded tables and 
dynamic summary callouts.

---

### Pipeline 2 — `clinical_deletion_assessment.qmd`
**Listwise Deletion Assessment**

This pipeline should only be run if Pipeline 1 suggests that 
missingness is random. As a general rule, if missing data is below 
5%, listwise deletion is typically acceptable without further 
assessment. For cases where the decision is less straightforward, 
this pipeline provides a structured framework.

Steps:
1. **Analysis type selection** — Select the intended statistical 
test (t-test, logistic regression, or correlation) and manually 
set effect size, alpha, and desired power according to your 
research question.
2. **MCAR testing** — Little's MCAR Test formally tests whether 
data is Missing Completely At Random. A result of p > 0.05 
provides statistical support for the assumption that missingness 
is random, making listwise deletion legitimate.
3. **Data loss quantification** — Calculates how many observations 
would be lost under listwise deletion, with traffic-light verdicts 
to guide interpretation.
4. **Distribution shift testing** — Assesses whether the 
distributions of key variables change after deletion, examining 
whether the remaining sample still represents the same population 
as the original.
5. **Power analysis** — Calculates the statistical power that 
would remain after deletion, given the effect size and alpha 
defined in Step 1. If power drops below 0.80, listwise deletion 
compromises the statistical validity of the analysis.

**Output:** A styled HTML report summarizing all assessments with 
interpretive verdicts.

---

## Methods Summary

| Method | Purpose |
|---|---|
| UpSet Plot | Visualize co-missingness patterns |
| Fisher's Exact Test | Dependency: binary categorical variables |
| Point-Biserial Correlation | Dependency: continuous variables |
| Little's MCAR Test | Formally test randomness of missingness |
| Distribution Shift Testing | Assess representativeness after deletion |
| Power Analysis | Evaluate statistical validity after deletion |

---

## Requirements

R packages:
- `naniar`
- `ggplot2`
- `gtsummary`
- `broom`
- `pwr`

---

## How to Run

1. Clone this repository
2. Open the `.qmd` files in RStudio
3. In Pipeline 1: adapt the data loading section to your dataset
4. Render each file using the **Render** button in RStudio or via:

```r
quarto::quarto_render("clinical_missing_pipeline.qmd")
quarto::quarto_render("clinical_deletion_assessment.qmd")
```

---

## License

MIT License — see `LICENSE` file for details.

---

## Acknowledgements

This project was developed with AI-assisted coding tools.
