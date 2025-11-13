# 🔧 Code Documentation

This folder contains all replication scripts for "Uruguay and the Fossilization Meme: Testing Ultraactivity Beyond Argentina"

**Last Updated:** November 13, 2025  
**Author:** Ignacio Adrián Lerer

---

## 💻 Software Requirements

### Primary: R Version 4.0+

**Recommended:** R 4.3.0 or higher

**Installation:** Download from [CRAN](https://cran.r-project.org/)

### Alternative: Stata 17+

**Note:** Stata scripts provided for robustness, but R is primary implementation

---

## 📦 Required R Packages

### Core Analysis Packages

```r
# Install all required packages
install.packages(c(
  # Matching and causal inference
  "MatchIt",        # v4.5.0+ - Propensity Score Matching
  "did",            # v2.1.0+ - Difference-in-Differences
  "Synth",          # v1.1-5+ - Synthetic Control Method
  
  # Data manipulation
  "tidyverse",      # v2.0.0+ - Data wrangling (dplyr, tidyr, etc.)
  "data.table",     # v1.14.0+ - Fast data operations
  
  # Regression and inference
  "fixest",         # v0.11.0+ - Fixed effects estimation
  "sandwich",       # v3.0-2+ - Robust standard errors
  "lmtest",         # v0.9-40+ - Hypothesis testing
  
  # Tables and figures
  "modelsummary",   # v1.4.0+ - Regression tables
  "ggplot2",        # v3.4.0+ - Data visualization
  "patchwork",      # v1.1.3+ - Combine plots
  "scales",         # v1.2.1+ - Scale functions for ggplot2
  
  # Utilities
  "here",           # v1.0.1+ - Path management
  "glue",           # v1.6.2+ - String interpolation
  "lubridate"       # v1.9.0+ - Date handling
))
```

### Version Verification

```r
# Check installed versions
packageVersion("MatchIt")
packageVersion("did")
packageVersion("Synth")
```

**Minimum versions:**
- MatchIt: 4.5.0
- did: 2.1.0
- Synth: 1.1-5

---

## 📄 Script Execution Order

Run scripts in this exact sequence for full replication:

### 1. `01_data_preparation.R` → Data Cleaning

**Purpose:** Load raw CSVs, clean, merge, create derived variables

**Inputs:**
- `data/uruguay_reforms_1985_2025.csv`
- `data/argentina_reforms_1989_2024.csv`
- `data/chile_reforms_1990_2024.csv`

**Outputs:**
- `data/processed/uruguay_panel.rds` (main analysis dataset)
- `data/processed/comparison_panel.rds` (Uruguay + Argentina + Chile)
- `data/processed/summary_stats.csv`

**Runtime:** ~30 seconds

**Key Steps:**
1. Load raw data
2. Standardize variable names
3. Handle missing values (GDP growth: linear interpolation)
4. Create treatment indicator (post_1991 × uruguay)
5. Calculate CLI components
6. Create crisis indicators
7. Merge cross-national data
8. Save processed datasets

**Verification:**
```r
library(tidyverse)
data <- readRDS("data/processed/uruguay_panel.rds")
table(data$final_outcome, data$post_1991)  # Should show 2/7 vs 29/46
```

---

### 2. `02_psm_analysis.R` → Propensity Score Matching

**Purpose:** Estimate treatment effect using PSM (replicates Table 9)

**Method:** Nearest-neighbor matching with caliper

**Inputs:**
- `data/processed/uruguay_panel.rds`

**Outputs:**
- `output/tables/table_09_psm.tex` (LaTeX format)
- `output/tables/table_09_psm.csv` (CSV format)
- `output/figures/psm_balance.pdf` (covariate balance plot)
- `output/figures/psm_common_support.pdf` (propensity score distribution)

**Runtime:** ~2 minutes

**Key Steps:**
1. Estimate propensity scores (logit model)
2. Check common support
3. Perform nearest-neighbor matching (caliper = 0.1)
4. Assess covariate balance (standardized differences)
5. Estimate ATT (Average Treatment Effect on Treated)
6. Bootstrap standard errors (1,000 replications)

**Expected Results:**
- **ATT = +0.41** (SE = 0.15, p = 0.006)
- All covariates balanced (std. diff < 0.15)
- Common support: 100% of treated units

**Verification:**
```r
source("code/02_psm_analysis.R")
# Check console output for ATT estimate
# Inspect output/tables/table_09_psm.tex
```

---

### 3. `03_did_estimation.R` → Difference-in-Differences

**Purpose:** Estimate treatment effect using DiD with Argentina as control (replicates Table 10)

**Method:** Two-way fixed effects with robust SEs

**Inputs:**
- `data/processed/comparison_panel.rds` (Uruguay + Argentina)

**Outputs:**
- `output/tables/table_10_did.tex` (main results, 5 specifications)
- `output/figures/figure_03_parallel_trends.pdf` (event study)
- `output/figures/did_placebo_tests.pdf` (robustness)

**Runtime:** ~3 minutes

**Key Steps:**
1. Test parallel trends assumption (pre-treatment F-test)
2. Estimate basic DiD model
3. Add year fixed effects
4. Add reform domain fixed effects
5. Add time-varying covariates (GDP, crisis, etc.)
6. Cluster standard errors at country level
7. Event study specification (leads and lags)
8. Placebo tests (false treatment years)

**Expected Results:**
- **DiD coefficient = +0.42** (SE = 0.14, p = 0.003)
- Parallel trends: F(5, 41) = 0.83, p = 0.534 ✓
- All specifications: +0.35 to +0.42, all p < 0.02

**Verification:**
```r
source("code/03_did_estimation.R")
# Check parallel trends plot (should be flat pre-1991)
# Verify Table 10 coefficients match paper
```

---

### 4. `04_synthetic_control.R` → Synthetic Control Method

**Purpose:** Construct counterfactual Uruguay using synthetic control (replicates Table 12, Figure 4)

**Method:** Synthetic control with permutation-based inference

**Inputs:**
- `data/processed/comparison_panel.rds`

**Outputs:**
- `output/tables/table_12_synth.tex` (synthetic weights and RMSPE)
- `output/figures/figure_04_synthetic_control.pdf` (main synth plot)
- `output/figures/synth_placebo_tests.pdf` (placebo distribution)

**Runtime:** ~5 minutes (permutation tests)

**Key Steps:**
1. Define predictor variables (GDP, unemployment, CLI components)
2. Optimization period: 1985-1990 (pre-treatment)
3. Construct synthetic Uruguay from:
   - Argentina (weight ≈ 0.39)
   - Paraguay (weight ≈ 0.14)
   - Uruguay Pre-1991 (weight ≈ 0.47)
4. Calculate pre-treatment fit (RMSPE)
5. Compute post-treatment gap
6. Permutation inference (100 placebos)
7. Calculate p-value

**Expected Results:**
- **Synthetic control gap = +34.8 pp** (95% CI: [12.3, 57.3])
- Pre-treatment RMSPE: 2.1 (good fit)
- Permutation p-value: 0.03 (reject null of no effect)

**Verification:**
```r
source("code/04_synthetic_control.R")
# Check Figure 4: actual and synthetic should overlap pre-1991
# Check Table 12 for weights and RMSPE
```

---

### 5. `05_robustness_checks.R` → Sensitivity Analysis

**Purpose:** 6 robustness checks (replicates Online Appendix Tables A5-A10)

**Inputs:**
- `data/processed/uruguay_panel.rds`
- `data/processed/comparison_panel.rds`

**Outputs:**
- `output/tables/online_appendix_tables.tex` (all robustness tables)
- `output/figures/robustness_plots.pdf` (sensitivity curves)

**Runtime:** ~8 minutes

**Key Steps:**

**Check 1: Alternative Time Windows**
- Narrow (1985-2000), Medium (1985-2010), Full (1985-2025)
- Expected: Stable treatment effects (±0.03)

**Check 2: Alternative Control Groups**
- Argentina (baseline), Brazil, Paraguay, Synthetic
- Expected: All positive, range +0.35 to +0.42

**Check 3: Placebo Tests**
- False treatment years: 1987, 1989, 1993, 1995
- Expected: No significant effects (all p > 0.10)

**Check 4: Alternative Outcome Definitions**
- Fully implemented 3 years, 5 years (baseline), Not reversed 10 years
- Expected: Similar magnitudes (+0.38 to +0.44)

**Check 5: Subsample Analysis**
- By reform domain (Fiscal, Labor, Trade, Pension)
- Expected: Larger for pensions (+0.61), labor (+0.51)

**Check 6: Controlling for Judicial Activism**
- Add judicial activism index as covariate
- Expected: Treatment effect unchanged (+0.42 vs +0.42)

**Verification:**
```r
source("code/05_robustness_checks.R")
# All treatment effects should be positive and significant
# Check Online Appendix tables for detailed results
```

---

### 6. `06_figures.R` → Generate All Figures

**Purpose:** Create all paper figures (1-4) and appendix figures

**Inputs:**
- `data/processed/uruguay_panel.rds`
- `data/processed/comparison_panel.rds`
- Results from previous scripts

**Outputs:**
- `output/figures/figure_01_timeline.pdf` (Uruguay institutional timeline)
- `output/figures/figure_02_cli_evolution.pdf` (CLI over time 1985-2025)
- `output/figures/figure_03_parallel_trends.pdf` (event study)
- `output/figures/figure_04_synthetic_control.pdf` (synth control)
- Appendix figures (balance plots, placebo tests, etc.)

**Runtime:** ~2 minutes

**Key Features:**
- Publication-quality PDF and PNG formats
- Consistent theme (Economist-style)
- Color-blind friendly palettes
- Properly labeled axes and legends

**Verification:**
```r
source("code/06_figures.R")
# Check output/figures/ for all PDFs
# Verify Figure 2 shows CLI drop in 1991
```

---

### 7. `run_all.R` → Master Script

**Purpose:** Run all scripts in sequence with progress tracking

**Usage:**
```r
source("code/run_all.R")
```

**Runtime:** ~20 minutes total

**Features:**
- Progress messages for each step
- Error handling (stops if any script fails)
- Timestamp logging
- Memory usage tracking
- Creates log file in `output/logs/`

**Log Output:**
```
[2025-11-13 14:23:15] Starting replication...
[2025-11-13 14:23:16] Step 1/6: Data preparation... ✓ (0.5 min)
[2025-11-13 14:23:46] Step 2/6: PSM analysis... ✓ (2.1 min)
[2025-11-13 14:25:52] Step 3/6: DiD estimation... ✓ (2.9 min)
...
[2025-11-13 14:43:08] Replication complete! Total time: 19.9 minutes
```

---

## 🛠️ Troubleshooting

### Common Issues

#### Issue 1: Package Installation Fails

**Error:**
```
package 'MatchIt' is not available for R version 4.0.0
```

**Solution:**
```r
# Update R to latest version
# Or install from GitHub:
devtools::install_github("kosukeimai/MatchIt")
```

#### Issue 2: Memory Limit Exceeded

**Error:**
```
Error: cannot allocate vector of size 2.3 GB
```

**Solution:**
```r
# Increase memory limit (Windows)
memory.limit(size = 8000)

# Or use data.table for large datasets
library(data.table)
data <- fread("data/uruguay_reforms_1985_2025.csv")
```

#### Issue 3: Path Not Found

**Error:**
```
Error: cannot open file 'data/uruguay_reforms_1985_2025.csv'
```

**Solution:**
```r
# Ensure working directory is repo root
setwd("/path/to/uruguay-ultraactivity-reform")

# Or use here package
library(here)
data <- read_csv(here("data", "uruguay_reforms_1985_2025.csv"))
```

#### Issue 4: Synth Optimization Fails

**Error:**
```
Warning: Optimization did not converge
```

**Solution:**
```r
# Increase iterations
synth_out <- synth(
  data.prep.obj = dataprep_out,
  optimxmethod = "BFGS",
  control = list(maxit = 10000)  # Increase from default 1000
)
```

#### Issue 5: Parallel Trends Test Fails

**Warning:**
```
Parallel trends assumption may be violated (p = 0.03)
```

**Solution:**
- This is expected for certain subsamples (e.g., trade reforms)
- Check Figure 3 visually for pre-trends
- Use alternative control group (Brazil instead of Argentina)
- See Section 4.4 of paper for discussion

---

## 📊 Expected Outputs Summary

After running all scripts, verify these key outputs exist:

### Tables (LaTeX + CSV)

- [ ] `table_09_psm.tex` - ATT = +0.41 (SE = 0.15)
- [ ] `table_10_did.tex` - DiD = +0.42 (SE = 0.14)
- [ ] `table_12_synth.tex` - Gap = +34.8pp
- [ ] `online_appendix_tables.tex` - 6 robustness checks

### Figures (PDF + PNG)

- [ ] `figure_01_timeline.pdf` - Uruguay institutional history
- [ ] `figure_02_cli_evolution.pdf` - CLI 1985-2025 (drop in 1991)
- [ ] `figure_03_parallel_trends.pdf` - Event study (flat pre-1991)
- [ ] `figure_04_synthetic_control.pdf` - Synth vs actual (diverge post-1991)

### Logs

- [ ] `replication_log_[date].txt` - Complete execution log

**Total Files:** 12 tables + 15 figures + 1 log = 28 files

---

## 🔍 Code Quality Standards

All scripts follow these standards:

✅ **Reproducibility**
- Set seed for random processes (`set.seed(20251113)`)
- Clear variable names (snake_case)
- Commented code explaining logic

✅ **Error Handling**
- Check for missing files before loading
- Validate data structure after processing
- Graceful failure messages

✅ **Documentation**
- Header with purpose, inputs, outputs
- Inline comments for complex operations
- Version control (Git)

✅ **Performance**
- Vectorized operations where possible
- Efficient data structures (data.table)
- Parallel processing for bootstraps

---

## 📧 Code Questions

For questions about:
- **Script errors:** Check Troubleshooting section above
- **Methodology:** See paper Section 3 (Methods)
- **Extensions:** Contact adrian@lerer.com.ar
- **Contributions:** Pull requests welcome on GitHub

---

## 📝 Version History

- **v1.0** (November 2025) - Initial release
  - R scripts for all main analyses
  - Stata scripts for robustness (alternative)
  - Master replication script
  - Comprehensive documentation

---

**Code Version:** 1.0  
**Last Updated:** November 13, 2025  
**License:** MIT (free to use, modify, redistribute with attribution)
