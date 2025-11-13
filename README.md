# Uruguay and the Fossilization Meme: Testing Ultraactivity Beyond Argentina

[![SSRN](https://img.shields.io/badge/SSRN-Working%20Paper-blue)](https://ssrn.com/abstract=XXXXXXX)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![R](https://img.shields.io/badge/R-4.0%2B-blue)](https://www.r-project.org/)
[![Stata](https://img.shields.io/badge/Stata-17%2B-blue)](https://www.stata.com/)

**Author:** Ignacio Adrián Lerer  
**Affiliation:** Legal Evolution Lab  
**Last Updated:** November 2025

---

## 📄 Abstract

Why do democracies get stuck? This paper identifies **ultraactivity**—the indefinite persistence of legal norms beyond their stated term without democratic renewal—as a "fossilization meme" that locks institutions into their past. Using Uruguay's 1991 elimination of ultraactivity as a natural experiment, I show that structural reform success increased by **+34 to +42 percentage points** (robust across propensity score matching, difference-in-differences, and synthetic control methods). However, eliminating ultraactivity is necessary but not sufficient: Uruguay still fails 37% of reforms due to other "fossilization memes" (mandatory referendum, judicial activism, proportional representation). The 1995 pension reform—technically superior, fiscally sound, legislatively approved—collapsed via referendum because it clashed with median voter preferences under proportional representation.

---

## 🎯 Key Findings

- **Natural Experiment:** Uruguay eliminated ultraactivity in 1991 (Constitutional Law 16.074)
- **Causal Effect:** Reform success rate increased from 28.6% (1985-1990) to 63.0% (1991-2025)
- **Treatment Effect:** +34 to +42 percentage points (p<0.01) across three methods:
  - Propensity Score Matching: +41pp (SE=0.15)
  - Difference-in-Differences: +42pp (SE=0.14)
  - Synthetic Control: +34.8pp (95% CI: [12.3, 57.3])
- **Dataset:** 53 Uruguay reforms (1985-2025) + 50 Argentina reforms (1989-2024) + 129 Chile reforms (1990-2024)
- **Robustness:** 6 specification checks confirm results
- **Mechanism:** Referendum veto dominates post-1991 failures (65% of failed reforms)

---

## 📂 Repository Structure

```
uruguay-ultraactivity-reform/
├── README.md                          # This file
├── REPLICATION_INSTRUCTIONS.md        # Quick-start guide
├── LICENSE                            # MIT License
├── CITATION.cff                       # Citation metadata
├── .gitignore                         # Git ignore rules
│
├── data/                              # Raw and processed data
│   ├── README_DATA.md                 # Data documentation
│   ├── uruguay_reforms_1985_2025.csv  # Main dataset (Uruguay)
│   ├── argentina_reforms_1989_2024.csv # Comparison data (Argentina)
│   ├── chile_reforms_1990_2024.csv    # Comparison data (Chile)
│   └── codebook.xlsx                  # Variable definitions
│
├── code/                              # Replication scripts
│   ├── README_CODE.md                 # Code documentation
│   ├── 01_data_preparation.R          # Data cleaning
│   ├── 02_psm_analysis.R              # Propensity Score Matching
│   ├── 03_did_estimation.R            # Difference-in-Differences
│   ├── 04_synthetic_control.R         # Synthetic Control Method
│   ├── 05_robustness_checks.R         # Sensitivity analysis
│   ├── 06_figures.R                   # Generate all figures
│   └── run_all.R                      # Master script (run all)
│
├── output/                            # Generated outputs
│   ├── tables/                        # LaTeX and CSV tables
│   │   ├── table_09_psm.tex
│   │   ├── table_10_did.tex
│   │   ├── table_12_synth.tex
│   │   └── online_appendix_tables.tex
│   ├── figures/                       # PNG and PDF figures
│   │   ├── figure_01_timeline.pdf
│   │   ├── figure_02_cli_evolution.pdf
│   │   ├── figure_03_parallel_trends.pdf
│   │   └── figure_04_synthetic_control.pdf
│   └── logs/                          # Execution logs
│       └── replication_log_[date].txt
│
└── paper/                             # Paper and appendix (optional)
    ├── lerer_uruguay_ultraactivity.pdf
    └── online_appendix.pdf
```

---

## 📊 Data Availability

### Primary Dataset: Uruguay Reforms (1985-2025)

- **N = 53 structural reforms**
- **Period:** Pre-treatment (1985-1990, n=7), Post-treatment (1991-2025, n=46)
- **Treatment:** Elimination of ultraactivity (Constitutional Law 16.074, September 1991)
- **Outcome:** Reform success (sustained implementation 5+ years)

### Comparison Datasets

- **Argentina (1989-2024):** 50 labor/fiscal reforms with ultraactivity (control group for DiD)
- **Chile (1990-2024):** 129 structural reforms without ultraactivity (comparative benchmark)

### Variables Included

- **Reform characteristics:** Year, government, policy domain, technical quality
- **Institutional features:** CLI components (ultraactivity, referendum, judicial, supermajority)
- **Economic context:** GDP growth, crisis indicators, IMF programs
- **Political context:** Legislative strength, electoral cycle, median voter position
- **Outcomes:** Legislative passage, judicial review, referendum result, final implementation

All data sources are publicly available government records, World Bank statistics, and author's compilation from official archives.

---

## 🔧 Replication Instructions

### Prerequisites

**Software:**
- R 4.0+ or Stata 17+
- Git (for cloning repository)

**R Packages:**
```r
install.packages(c("MatchIt", "did", "Synth", "tidyverse", 
                   "fixest", "modelsummary", "ggplot2", "sandwich"))
```

**Stata Packages:**
```stata
ssc install psmatch2
ssc install synth
ssc install reghdfe
```

### Quick Start (5 minutes)

```bash
# 1. Clone repository
git clone https://github.com/adrianlerer/uruguay-ultraactivity-reform.git
cd uruguay-ultraactivity-reform

# 2. Install R dependencies
Rscript -e "install.packages(c('MatchIt', 'did', 'Synth', 'tidyverse', 'fixest'))"

# 3. Run all analyses
Rscript code/run_all.R

# 4. Check outputs
ls output/tables/
ls output/figures/
```

### Expected Results

After running `code/run_all.R`, verify key results match the paper:

| Table/Figure | Expected Result | Location |
|--------------|-----------------|----------|
| Table 9 (PSM) | ATT = +0.41 (SE=0.15) | `output/tables/table_09_psm.tex` |
| Table 10 (DiD) | Coefficient = +0.42 (SE=0.14) | `output/tables/table_10_did.tex` |
| Table 12 (Synth) | Gap = +34.8pp, Pre-RMSPE < 0.05 | `output/tables/table_12_synth.tex` |
| Figure 3 | Parallel trends (F-test p>0.5) | `output/figures/figure_03_parallel_trends.pdf` |
| Figure 4 | Synthetic control divergence post-1991 | `output/figures/figure_04_synthetic_control.pdf` |

**Runtime:** ~15-20 minutes on standard laptop (4GB RAM, 2.5GHz processor)

---

## 📖 Citation

If you use this dataset or replication code, please cite:

### BibTeX

```bibtex
@article{lerer2025uruguay,
  title={Uruguay and the Fossilization Meme: Testing Ultraactivity Beyond Argentina},
  author={Lerer, Ignacio Adri{\'a}n},
  journal={Available at SSRN},
  year={2025},
  url={https://ssrn.com/abstract=XXXXXXX}
}
```

### APA

Lerer, I. A. (2025). Uruguay and the fossilization meme: Testing ultraactivity beyond Argentina. *Available at SSRN*. https://ssrn.com/abstract=XXXXXXX

---

## 📬 Contact

**Ignacio Adrián Lerer**  
Email: adrian@lerer.com.ar  
ORCID: [0009-0007-6378-9749](https://orcid.org/0009-0007-6378-9749)  
GitHub: [@adrianlerer](https://github.com/adrianlerer)

For questions about:
- **Data:** See `data/README_DATA.md` for detailed variable definitions
- **Code:** See `code/README_CODE.md` for script documentation
- **Replication:** See `REPLICATION_INSTRUCTIONS.md` for step-by-step guide
- **Paper:** Contact via email for latest draft

---

## 📜 License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

**Data License:** All data are from publicly available sources. Original compilation and coding by author.

**Code License:** MIT License (free to use, modify, redistribute with attribution)

---

## 🌟 Acknowledgments

- **Data Collection:** Uruguayan Parliament Archives, Supreme Court of Uruguay, World Bank
- **Funding:** [Add if applicable]
- **Feedback:** [Add workshop/conference presentations if applicable]

---

## 📌 Version History

- **v1.0** (November 2025) - Initial public release with paper submission
- Pre-release versions available upon request

---

**Last Updated:** November 13, 2025  
**DOI:** [Will be added upon Zenodo archiving]
