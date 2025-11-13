# 🚀 Quick Replication Guide

**Estimated Time:** 5-20 minutes (depending on machine)

**Target Audience:** Researchers wanting to replicate "Uruguay and the Fossilization Meme"

---

## ⚡ Quick Start (5 Minutes)

### 1. Clone Repository

```bash
git clone https://github.com/adrianlerer/uruguay-ultraactivity-reform.git
cd uruguay-ultraactivity-reform
```

### 2. Install R Dependencies

**Option A: Automatic (recommended)**
```bash
Rscript -e "install.packages(c('MatchIt', 'did', 'Synth', 'tidyverse', 'fixest', 'modelsummary', 'ggplot2', 'sandwich'))"
```

**Option B: Manual (R console)**
```r
install.packages(c(
  "MatchIt", "did", "Synth", "tidyverse", 
  "fixest", "modelsummary", "ggplot2", "sandwich"
))
```

### 3. Run All Analyses

```bash
Rscript code/run_all.R
```

**Output:** Watch console for progress messages (~20 minutes)

### 4. Check Results

```bash
# View generated tables
ls output/tables/

# View generated figures
ls output/figures/

# Check execution log
cat output/logs/replication_log_*.txt
```

---

## 📋 Step-by-Step Instructions

### Step 1: Data Preparation

**Purpose:** Load and clean raw data

**Command:**
```r
source("code/01_data_preparation.R")
```

**Expected Output:**
```
✓ Loaded Uruguay reforms (n=53)
✓ Loaded Argentina reforms (n=50)
✓ Loaded Chile reforms (n=129)
✓ Merged datasets successfully
✓ Created treatment indicator
✓ Saved processed data to data/processed/
```

**Verification:**
```r
library(tidyverse)
data <- readRDS("data/processed/uruguay_panel.rds")

# Check treatment distribution
table(data$post_1991, data$final_outcome)
#          Success Reversed Partial
#   0 (Pre)    2      4        1
#   1 (Post)  29     13        4

# Verify success rates
data %>%
  group_by(post_1991) %>%
  summarise(success_rate = mean(final_outcome == "Success"))
# Pre:  28.6% (2/7)
# Post: 63.0% (29/46)
```

---

### Step 2: Propensity Score Matching

**Purpose:** Estimate treatment effect controlling for observables

**Command:**
```r
source("code/02_psm_analysis.R")
```

**Expected Output:**
```
✓ Estimated propensity scores
✓ Performed nearest-neighbor matching (caliper=0.1)
✓ Checked covariate balance (all std.diff < 0.15)
✓ Estimated ATT: +0.41 (SE=0.15, p=0.006)
✓ Generated Table 9 → output/tables/table_09_psm.tex
✓ Generated balance plots → output/figures/psm_balance.pdf
```

**Verification:**
```r
# Check Table 9
cat(readLines("output/tables/table_09_psm.tex"))

# Expected ATT row:
# Post-1991 (Treatment) & 0.41*** & (0.15) & 0.006 \\
```

**What to Look For:**
- ✅ ATT = +0.41 (matches paper Table 9)
- ✅ Standard error = 0.15
- ✅ p-value < 0.01 (significant)
- ✅ Covariate balance plot shows all points near zero

---

### Step 3: Difference-in-Differences

**Purpose:** Estimate treatment effect using Argentina as control

**Command:**
```r
source("code/03_did_estimation.R")
```

**Expected Output:**
```
✓ Tested parallel trends: F(5,41)=0.83, p=0.534 ✓
✓ Estimated basic DiD: +0.35 (SE=0.12)
✓ Added year FE: +0.37 (SE=0.11)
✓ Added domain FE: +0.39 (SE=0.13)
✓ Added covariates: +0.42 (SE=0.14)
✓ Clustered SEs: +0.42 (SE=0.18)
✓ Generated Table 10 → output/tables/table_10_did.tex
✓ Generated Figure 3 → output/figures/figure_03_parallel_trends.pdf
```

**Verification:**
```r
# Check parallel trends plot
# Should show flat line pre-1991, jump upward at 1991
```

**What to Look For:**
- ✅ Parallel trends test: p > 0.5 (assumption satisfied)
- ✅ All 5 specifications: positive and significant
- ✅ Preferred spec (5): +0.42 (SE=0.18, p=0.020)
- ✅ Figure 3: No pre-trends, clear post-treatment divergence

---

### Step 4: Synthetic Control Method

**Purpose:** Construct counterfactual Uruguay

**Command:**
```r
source("code/04_synthetic_control.R")
```

**Expected Output:**
```
✓ Constructed synthetic Uruguay:
  - Argentina weight: 0.39
  - Paraguay weight: 0.14
  - Uruguay Pre-1991: 0.47
✓ Pre-treatment RMSPE: 2.1 (good fit)
✓ Post-treatment gap: +34.8 pp
✓ Permutation p-value: 0.03 (significant)
✓ Generated Table 12 → output/tables/table_12_synth.tex
✓ Generated Figure 4 → output/figures/figure_04_synthetic_control.pdf
```

**Verification:**
```r
# Check Figure 4
# Actual and synthetic should overlap 1985-1990
# Diverge sharply after 1991
```

**What to Look For:**
- ✅ Pre-treatment fit: RMSPE < 3
- ✅ Gap: +34.8 pp (95% CI: [12.3, 57.3])
- ✅ Permutation test: p < 0.05
- ✅ Figure 4: Clean divergence post-1991

---

### Step 5: Robustness Checks

**Purpose:** Sensitivity analysis (6 specifications)

**Command:**
```r
source("code/05_robustness_checks.R")
```

**Expected Output:**
```
✓ Check 1: Alternative time windows → +0.38 to +0.42 (stable)
✓ Check 2: Alternative controls → +0.35 to +0.42 (all positive)
✓ Check 3: Placebo tests → all p > 0.10 (no false effects)
✓ Check 4: Alternative outcomes → +0.38 to +0.44 (robust)
✓ Check 5: Subsample analysis → +0.29 to +0.61 (heterogeneity)
✓ Check 6: Judicial activism control → +0.42 (unchanged)
✓ Generated Online Appendix Tables → output/tables/online_appendix_tables.tex
```

**Verification:**
```r
# All treatment effects should be positive
# Most should be statistically significant (p < 0.05)
```

**What to Look For:**
- ✅ All 6 checks confirm positive treatment effect
- ✅ Estimates stable across specifications (range: +0.29 to +0.61)
- ✅ No spurious results in placebo tests

---

### Step 6: Generate Figures

**Purpose:** Create publication-quality figures

**Command:**
```r
source("code/06_figures.R")
```

**Expected Output:**
```
✓ Figure 1: Timeline → figure_01_timeline.pdf
✓ Figure 2: CLI evolution → figure_02_cli_evolution.pdf
✓ Figure 3: Parallel trends → figure_03_parallel_trends.pdf
✓ Figure 4: Synthetic control → figure_04_synthetic_control.pdf
✓ Appendix figures → 11 additional plots
```

**Verification:**
```bash
ls output/figures/*.pdf | wc -l
# Should output: 15 (4 main figures + 11 appendix)
```

---

## ✅ Verification Checklist

After running all scripts, verify these key results:

### Tables

- [ ] **Table 9 (PSM):** ATT = +0.41, SE = 0.15, p = 0.006
- [ ] **Table 10 (DiD):** Spec 5 = +0.42, SE = 0.18, p = 0.020
- [ ] **Table 12 (Synth):** Gap = +34.8pp, RMSPE = 2.1

### Figures

- [ ] **Figure 2:** Clear drop in CLI at 1991
- [ ] **Figure 3:** Flat pre-trends, divergence post-1991
- [ ] **Figure 4:** Synthetic and actual overlap pre-1991, diverge after

### Statistical Tests

- [ ] **Parallel trends:** F-test p > 0.5 (satisfied)
- [ ] **Common support:** 100% treated units matched
- [ ] **Permutation test:** p < 0.05 (synthetic control significant)

### File Counts

- [ ] **12 tables** in `output/tables/`
- [ ] **15 figures** in `output/figures/`
- [ ] **1 log file** in `output/logs/`

---

## 🐛 Troubleshooting

### Problem: "Cannot open file" error

**Symptom:**
```
Error: cannot open file 'data/uruguay_reforms_1985_2025.csv'
```

**Solution:**
```r
# Ensure working directory is correct
setwd("/path/to/uruguay-ultraactivity-reform")

# Or use here package
library(here)
setwd(here())
```

---

### Problem: Package not found

**Symptom:**
```
Error: package 'MatchIt' is not available
```

**Solution:**
```r
# Update R to latest version (4.3+)
# Then reinstall packages
install.packages("MatchIt")
```

---

### Problem: Memory limit exceeded

**Symptom:**
```
Error: cannot allocate vector of size 2.3 GB
```

**Solution:**
```r
# Increase memory limit (Windows)
memory.limit(size = 8000)

# Use 64-bit R version (not 32-bit)
# Close other programs to free RAM
```

---

### Problem: Parallel trends test fails

**Symptom:**
```
Warning: Parallel trends assumption violated (p = 0.03)
```

**Solution:**
- This is expected for some subsamples (trade reforms)
- Check Figure 3 visually
- Use alternative control (Brazil instead of Argentina)
- See paper Section 4.4 for discussion

---

### Problem: Script runs but no output

**Symptom:**
Scripts execute without errors but no files in `output/`

**Solution:**
```r
# Manually create output directories
dir.create("output/tables", recursive = TRUE)
dir.create("output/figures", recursive = TRUE)
dir.create("output/logs", recursive = TRUE)

# Re-run scripts
source("code/run_all.R")
```

---

## ⏱️ Expected Runtime

On standard laptop (4GB RAM, 2.5GHz Intel i5):

| Script | Runtime | Bottleneck |
|--------|---------|------------|
| 01_data_preparation | 30 sec | File I/O |
| 02_psm_analysis | 2 min | Bootstrap (1,000 reps) |
| 03_did_estimation | 3 min | Event study grid |
| 04_synthetic_control | 5 min | Permutation tests (100 placebos) |
| 05_robustness_checks | 8 min | Multiple specifications |
| 06_figures | 2 min | PDF rendering |
| **Total** | **~20 min** | |

**Faster machines** (16GB RAM, multi-core): ~10-12 minutes

**Slower machines** (2GB RAM, older CPU): ~30-40 minutes

---

## 📧 Support

If replication fails after following this guide:

1. **Check versions:**
   ```r
   R.version.string
   packageVersion("MatchIt")
   packageVersion("did")
   packageVersion("Synth")
   ```

2. **Review log file:**
   ```bash
   cat output/logs/replication_log_*.txt
   ```

3. **Contact author:**
   - Email: adrian@lerer.com.ar
   - Include: error message, R version, OS
   - Attach: log file

**Typical response time:** 24-48 hours

---

## 🎯 Success Criteria

Replication is successful if:

✅ All 6 scripts run without fatal errors  
✅ Table 9 ATT ≈ +0.41 (±0.05)  
✅ Table 10 DiD ≈ +0.42 (±0.05)  
✅ Table 12 Synth gap ≈ +34.8pp (±5pp)  
✅ Figure 3 shows no pre-trends  
✅ Figure 4 shows clear post-1991 divergence  

**Minor variations** (±0.03 in treatment effects) are acceptable due to:
- R version differences
- Random seed variation in bootstraps
- Floating-point precision

---

## 📚 Next Steps

After successful replication:

1. **Explore data:** See `data/README_DATA.md` for variable definitions
2. **Modify analyses:** See `code/README_CODE.md` for script documentation
3. **Extend research:** Try alternative specifications, additional countries
4. **Cite properly:** Use BibTeX in main `README.md`

---

**Last Updated:** November 13, 2025  
**Replication Package Version:** 1.0
