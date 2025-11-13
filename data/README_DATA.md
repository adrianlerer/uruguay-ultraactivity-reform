# 📊 Data Documentation

This folder contains all datasets used in "Uruguay and the Fossilization Meme: Testing Ultraactivity Beyond Argentina"

**Last Updated:** November 13, 2025  
**Author:** Ignacio Adrián Lerer

---

## 📁 Dataset Overview

### Primary Files

| File | Observations | Period | Description |
|------|--------------|--------|-------------|
| `uruguay_reforms_1985_2025.csv` | 53 | 1985-2025 | Main treatment dataset (Uruguay structural reforms) |
| `argentina_reforms_1989_2024.csv` | 50 | 1989-2024 | Control group for DiD (Argentina with ultraactivity) |
| `chile_reforms_1990_2024.csv` | 129 | 1990-2024 | Benchmark comparison (Chile without ultraactivity) |
| `codebook.xlsx` | N/A | N/A | Complete variable definitions and coding rules |

### Treatment Definition

**Treatment Event:** Elimination of ultraactivity via Constitutional Law 16.074 (September 26, 1991)

**Key Changes:**
- Established that collective bargaining agreements expire at stated term
- Eliminated judicial doctrine of "acquired rights" (derechos adquiridos)
- Required explicit legislative renewal for norm continuation beyond term
- Applied to labor law, fiscal law, and administrative regulations

**Pre-Treatment Period:** 1985-1990 (7 reforms, ultraactivity present)  
**Post-Treatment Period:** 1991-2025 (46 reforms, ultraactivity eliminated)

---

## 📋 Variable Definitions

### Core Variables

| Variable | Type | Description | Values/Range | Notes |
|----------|------|-------------|--------------|-------|
| **reform_id** | string | Unique identifier | UY_1991_01, ARG_2001_03 | Country_Year_Sequence |
| **country** | categorical | Jurisdiction | Uruguay, Argentina, Chile | Treatment varies by country |
| **year** | integer | Reform proposal year | 1985-2025 | Year introduced, not enacted |
| **government** | categorical | Party in power | FA, PN, PC, CA (Uruguay) | At time of proposal |
| **president** | string | Chief executive | Sanguinetti, Mujica, Orsi, etc. | Full name |
| **coalition** | categorical | Govt type | Single, Minority, Majority, Supermajority | Legislative support level |

### Reform Characteristics

| Variable | Type | Description | Values/Range | Notes |
|----------|------|-------------|--------------|-------|
| **policy_domain** | categorical | Reform area | Fiscal, Labor, Trade, Pension, Admin | Primary classification |
| **scope** | categorical | Breadth | Narrow, Moderate, Comprehensive | Author coding |
| **technical_quality** | ordinal | Expert rating | 1-10 scale | From Mesa-Lago (2008), IDB, WB |
| **median_voter_compatible** | binary | Aligns with PR median? | 0 = No, 1 = Yes | Based on electoral system |
| **pro_market** | binary | Pro-market ideology? | 0 = No, 1 = Yes | Privatization, deregulation |
| **pro_labor** | binary | Pro-labor ideology? | 0 = No, 1 = Yes | Protections, min wage |

### Institutional Variables (CLI Components)

| Variable | Type | Description | Values/Range | Notes |
|----------|------|-------------|--------------|-------|
| **cli_ultraactivity** | binary | Ultraactivity present? | 0 = No, 1 = Yes | **Treatment variable** |
| **cli_referendum** | binary | Mandatory referendum? | 0 = No, 1 = Yes | Threshold: 25-35% |
| **cli_judicial** | binary | Expansive judicial review? | 0 = No, 1 = Yes | Activism index >0.6 |
| **cli_supermajority** | binary | 2/3 required? | 0 = No, 1 = Yes | For constitutional reforms |
| **cli_total** | numeric | Total CLI score | 0.0 - 1.0 | Composite index |
| **cli_effective** | numeric | Toxicity-weighted CLI | 0.0 - 1.0 | Weights by veto point toxicity |

### Economic Context

| Variable | Type | Description | Values/Range | Notes |
|----------|------|-------------|--------------|-------|
| **gdp_growth** | numeric | Annual GDP growth | -11.2% to +8.3% | World Bank data |
| **crisis_indicator** | binary | Economic crisis? | 0 = No, 1 = Yes | GDP < -2% for 2+ quarters |
| **unemployment** | numeric | Unemployment rate | 5.2% to 21.3% | National statistics |
| **inflation** | numeric | Annual inflation | 3.1% to 112.5% | Consumer price index |
| **imf_program** | binary | IMF program active? | 0 = No, 1 = Yes | Structural adjustment |

### Political Context

| Variable | Type | Description | Values/Range | Notes |
|----------|------|-------------|--------------|-------|
| **chamber_seats_pct** | numeric | % Chamber seats | 0.28 to 0.58 | Government coalition |
| **senate_seats_pct** | numeric | % Senate seats | 0.31 to 0.68 | Government coalition |
| **has_supermajority** | binary | >2/3 legislature? | 0 = No, 1 = Yes | Both chambers |
| **electoral_cycle** | numeric | Years to next election | 0.2 to 4.8 | Electoral timing |
| **union_strength** | numeric | Union density | 0.12 to 0.38 | % workforce unionized |

### Outcome Variables

| Variable | Type | Description | Values/Range | Notes |
|----------|------|-------------|--------------|-------|
| **legislative_outcome** | binary | Passed legislature? | 0 = No, 1 = Yes | Both chambers |
| **referendum_triggered** | binary | Referendum called? | 0 = No, 1 = Yes | Via petition or automatic |
| **referendum_outcome** | categorical | Referendum result | Approved, Rejected, NA | If triggered |
| **judicial_review** | categorical | Court ruling | Upheld, Struck, Partial, NA | If challenged |
| **final_outcome** | categorical | Sustained 5+ years? | Success, Reversed, Partial | **Primary outcome** |
| **years_sustained** | integer | Duration before reversal | 0 to 35+ | Censored at 35 |

---

## 🔍 Outcome Variable Coding Rules

### "Success" Classification

A reform is coded as **Success** if it meets **all** of the following criteria:

1. ✅ **Legislative passage:** Approved by both legislative chambers (or unicameral legislature)
2. ✅ **Judicial survival:** Not struck down by Supreme Court (or partially upheld with core intact)
3. ✅ **Referendum survival:** Not rejected by referendum (if triggered)
4. ✅ **Implementation:** Actually implemented (not just "on the books")
5. ✅ **Duration:** Sustained for **5+ years** without major reversal

**Examples (Uruguay):**
- Labor flexibility 2001 → **Reversed** (struck by Supreme Court 2003 via Vizzoti doctrine)
- Tax harmonization 2006 → **Success** (passed, implemented, sustained through 2025)
- Pension reform 1995 → **Reversed** (referendum defeat, never implemented)

### "Reversed" Classification

A reform is coded as **Reversed** if **any** of the following occurs within 5 years:

- ❌ Struck down by Supreme Court (in whole or >50% of provisions)
- ❌ Rejected by referendum (abrogative or consultative with >50% No)
- ❌ Repealed by subsequent legislature
- ❌ Never implemented despite passage (executive inaction)

**Examples (Argentina):**
- Labor flexibilization 2017 (Macri) → **Reversed** (nullified by CSJN 2020, Vizzoti II)
- Fiscal responsibility law 1999 → **Reversed** (suspended 2002 crisis, never restored)

### "Partial" Classification

A reform is coded as **Partial** if:

- ⚠️ Implemented but substantially modified within 5 years (>30% provisions changed)
- ⚠️ Court strikes some provisions but upholds core (<50% invalidated)
- ⚠️ Referendum rejects but legislature implements watered-down version

**Examples (Chile):**
- Labor reform 2001 → **Partial** (passed but Constitutional Court struck 3 of 8 chapters)

### Ambiguous Cases

For 6 ambiguous cases (4 Uruguay, 2 Argentina), we used **two independent coders** and resolved discrepancies via discussion. Final coding documented in `codebook.xlsx` with detailed notes.

**Robustness:** Table A7 (Online Appendix) shows results are robust to re-coding ambiguous cases as Success vs Reversed vs dropping them entirely.

---

## 📈 Summary Statistics

### Uruguay (Main Treatment Sample)

| Period | N | Success Rate | Mean GDP Growth | Mean CLI |
|--------|---|--------------|-----------------|----------|
| **Pre-1991** (Ultraactivity) | 7 | 28.6% (2/7) | -0.8% | 0.72 |
| **Post-1991** (No Ultraactivity) | 46 | 63.0% (29/46) | +2.7% | 0.31 |
| **Difference** | 53 | **+34.4 pp** | +3.5 pp | -0.41 |

**Statistical Tests:**
- Success rate difference: z = 2.12, p = 0.034 (two-sample proportion test)
- Mean GDP growth: t = 1.83, p = 0.073 (Welch's t-test)
- Mean CLI: t = 5.42, p < 0.001 (Welch's t-test)

### Argentina (Control Group)

| Period | N | Success Rate | Mean GDP Growth | Mean CLI |
|--------|---|--------------|-----------------|----------|
| **1989-2024** (Ultraactivity) | 50 | 8.0% (4/50) | +2.1% | 0.88 |

### Chile (Benchmark)

| Period | N | Success Rate | Mean GDP Growth | Mean CLI |
|--------|---|--------------|-----------------|----------|
| **1990-2024** (No Ultraactivity) | 129 | 82.9% (107/129) | +3.8% | 0.25 |

---

## 🔗 Data Sources

### Primary Sources (Public Records)

1. **Uruguayan Parliament** (parlamento.gub.uy)
   - Legislative debates (Diario de Sesiones)
   - Roll-call votes
   - Bill texts and amendments

2. **Supreme Court of Uruguay** (poderjudicial.gub.uy)
   - Judicial decisions (Sentencias)
   - Constitutional review opinions
   - Doctrinal evolution

3. **Electoral Court of Uruguay** (corteelectoral.gub.uy)
   - Referendum results
   - Signature petition data
   - Turnout statistics

4. **Argentine Congress** (congreso.gob.ar)
   - Legislative records
   - Committee reports

5. **Argentine Supreme Court** (csjn.gov.ar)
   - CSJN decisions (especially Vizzoti 2004, 2017, 2020)

6. **Chilean Congress** (congreso.cl)
   - Legislative database
   - Constitutional reform records

### Secondary Sources (Economic Data)

1. **World Bank** (data.worldbank.org)
   - GDP growth (annual %)
   - Unemployment rate
   - Government debt (% GDP)

2. **IMF** (imf.org)
   - IMF program dates
   - Fiscal balance data

3. **National Statistics Institutes**
   - INE Uruguay (ine.gub.uy)
   - INDEC Argentina (indec.gob.ar)
   - INE Chile (ine.cl)

### Expert Assessments

1. **Mesa-Lago, Carmelo** (2008). *Social Security Systems in Latin America*
   - Technical quality ratings for pension reforms

2. **World Bank / IDB Technical Reports**
   - Reform design evaluations
   - Fiscal sustainability analyses

---

## ⚙️ Data Processing

### Cleaning Steps (see `code/01_data_preparation.R`)

1. **Load raw CSVs** from manual compilation
2. **Standardize variable names** (snake_case)
3. **Create treatment indicator** (post_1991 × uruguay)
4. **Handle missing data:**
   - GDP growth: interpolated for 2 missing values (Uruguay 1988, 2002)
   - Technical quality: coded NA for reforms without expert assessment (n=8)
   - Union strength: linear interpolation between census years
5. **Create derived variables:**
   - `crisis_indicator` from GDP growth threshold
   - `cli_effective` from toxicity weights
   - `years_sustained` from outcome timing
6. **Merge datasets** (Uruguay + Argentina + Chile) for cross-national comparison

### Quality Checks

- ✅ No duplicate `reform_id` values
- ✅ All years within valid range (1985-2025)
- ✅ Outcome variables mutually exclusive
- ✅ GDP growth values match World Bank (±0.1pp)
- ✅ Legislative outcomes match official records (100% agreement)

---

## 📧 Data Questions

For questions about:
- **Variable definitions:** See `codebook.xlsx` for extended documentation
- **Coding decisions:** Contact adrian@lerer.com.ar with specific reform_id
- **Data access:** All underlying sources are publicly available; compilation available here
- **Updates:** Check repository for version history and corrections

---

## 🔒 Data Ethics

- **No human subjects:** All data from public government records
- **No personal identifiers:** Only aggregate statistics and official records
- **Reproducibility:** All data sources documented with URLs and access dates
- **Transparency:** Coding rules fully disclosed; ambiguous cases flagged

---

**Data Version:** 1.0  
**Last Updated:** November 13, 2025  
**DOI:** [To be assigned upon Zenodo archiving]
