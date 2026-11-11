# Replication report: "Does Scarcity Impede Cognitive Function? Five Preregistered Experiments and Meta-Analysis"

Prepared 11 November 2026  five CSV exports and five SPSS files (`St1` to `St5`). Everything below was computed from the raw exports with the code in `R/` and `python/`; the numbers can be regenerated with `Rscript R/run_all.R` and `python3 python/run_all.py`.

## 1. Summary

- **The CSV and SAV files match.** For each study the two files have the same number of rows and columns, the same column names in the same order, and the same values. The only differences are rounding (the CSV prints the two derived income variables with five decimals, maximum difference 0.00002), the storage format of three Prolific time stamps, and a handful of empty free-text cells that SPSS stores as ".". None of these touches an analysis variable.
- **Every result in the manuscript replicates from the raw data.** All 64 rows of Tables 2 to 6 (three Cohen's d values, three one-tailed p values, one interaction t and p each) are reproduced exactly by both the R and the Python code. The reliabilities, omnibus ANOVAs, income-group comparisons, manipulation checks, and the meta-analytic results in Tables 7 and 8, the equivalence tests, meta-regressions, leave-one-study-out ranges, the "7 of 192" and "4 of 96" counts, and the pooled income-group differences all match to the printed precision.
- **The Python implementation agrees with the R implementation** (which uses metafor, the package of the manuscript) to at least the fourth decimal in every meta-analytic table, and the two processed data sets are identical cell for cell.
- **Three things cannot be reproduced from these files**: the repeat-participant analyses of Studies 4 and 5 (omitted from the package by decision, see Section 6), the exact wording of some response options and demographic questions, and the order of the two Affective Slider items in Studies 4 and 5. Details in Section 6.

## 2. Do the CSV and SAV files match?

| Study | Rows (CSV = SAV) | Columns (CSV = SAV) | Same column order | Columns with any differing cell | What differs |
|---|---|---|---|---|---|
| 1 | 2,441 | 304 | yes | 2 | `Personal_income`, `Income_shah`: rounding to 5 decimals |
| 2 | 2,198 | 369 | yes | 6 | the same two; three Prolific time stamps (`started_datetime`, `completed_date_time`, `reviewed_at_datetime`) stored as times in SPSS and as numbers in the CSV; 7 empty comment cells (`Q121`) |
| 3 | 1,569 | 243 | yes | 7 | as Study 2, plus one `time_taken` cell |
| 4 | 2,242 | 361 | yes | 9 | as Study 2, plus three empty recall-text cells in one row |
| 5 | 2,148 | 345 | yes | 2 | `Income_shah` rounding; 4 empty comment cells |

Full results: `output/checks/csv_vs_sav_summary.csv`, `csv_vs_sav_differences.csv`, and the SPSS metadata in `sav_metadata.json`. The SPSS files carry no variable labels and value labels only for the condition code, so they add no documentation beyond the CSVs. The sample sizes of the exports equal the analysis samples of the manuscript, so the exclusions described in the paper (repeated attempts, invalid IDs, no consent, non-standard lottery bonus) were already applied.

Other things found in the raw exports (none affects the analyses, all matter for the published data set):

- Identifying information is present: Prolific IDs (two columns per study), IP addresses, latitude/longitude (Studies 4 and 5), and free-text answers from the recall, consequences, scenario and picture tasks. The processed files drop all of it and keep the demographic fields of the Prolific profiles (sex, gender identity, age, nationality, country of birth and residence, languages, employment and student status, ethnicity in Study 5, and the screening income bracket) for Studies 2 to 5; Study 1 has no Prolific export.
- Study 5's `ID` column is empty, and its Prolific block mixes two export formats pasted side by side: for the screened pools (September 2022) every field sits under its own header, whereas for the full-income-range pool (November 2022) the export lacked the "Total rejections" and "Approval rate" fields and every demographic value sits two columns to the left of its header. The preparation scripts detect the format from the "Approval rate" column; the recovered Prolific age equals the survey age in 95% of rows and lies within one year in 98%, as in Studies 2 to 4. Four Study 5 participants have no Prolific record in the export.
- Study 5's collection dates: the Prolific submissions of the screened pools run from 13 September to 2 October 2022 and those of the full-income-range pool from 14 to 30 November 2022, whereas the manuscript states that data were collected between 12 and 27 September 2022.
- Several column names carry an encoding artefact ("Â" prefix, e.g. `ÂPicture_Intro_T_First_Click`) and some text columns contain mojibake ("Â£", "â€“"). The analysis variables are unaffected.
- Study 3 was exported with option texts instead of numeric codes (CPT items, SCS items, gender, education, income, residency, hunger). Its residency question exported the "yes" option with the label "UK" although the sample is American.
- Study 4's `Income_High` column splits income with ties assigned to the low-income group (1,122 / 1,120); the manuscript's split is `gr_income` (1,096 / 1,146), which the code reproduces from effective income with ties to the high-income group, as the paper states.

## 3. From raw data to scores

Every score was recomputed from item responses and compared with the score stored in the SPSS file (`output/checks/derived_vs_spss.csv`): scarcity concerns (FCS, FCQ, SCS), cognitive reflection, fluid intelligence, the Reflection score, PANAS positive and negative affect, financial literacy, effective income and the income group agree in every one of the 10,598 rows. The scoring keys derived from the data (and recorded in the codebook) are: CRT items correct = option 1, belief-bias item correct = option 2 ("No"), base-rate and conjunction items correct = option 1, Raven E6/E8/E9 correct = options 5/6/3, FCS items 1, 2, 4 and FCQ items 1, 2, 4, 7, 8, 13 reverse-scored, income brackets 1 to 12 mapped to midpoints 5,000 ... 95,000, 125,000, 150,000, effective income = midpoint / sqrt(household size), median split with ties to the high-income group. The medians (25,000; 25,000; 37,500; 45,000; 43,301.27) and group sizes equal those in the manuscript.

## 4. Per-study results

`output/checks/replication_check_R.csv` and `replication_check_python.csv` compare each cell of Tables 2 to 6 with the recomputed value: **64 of 64 rows reproduce exactly in R and in Python.** The text statistics also match, for example:

| Study | Statistic | Manuscript | Replication |
|---|---|---|---|
| 1 | Concerns, one-way ANOVA | F(8, 2432) = 1.42, p = .185, eta2p = .005 | 1.415, .185, .005 |
| 1 | Income x condition on concerns | F(8, 2423) = 1.18, p = .308 | 1.179, .308 |
| 1 | Scale manipulation check | t(549) = -9.77, d = -0.83 | -9.77, -0.833 |
| 1 | Lottery attention checks | 96.3 / 98.5 / 99.6 / 99.6 % | identical |
| 2 | Concerns, condition effect | F(7, 2182) = 3.02, p = .004, eta2p = .010 | 3.016, .004, .010 |
| 2 | Income: concerns, CRS, FIS, Reflection | t = -20.21, 4.53, 6.28, 6.26; d = -0.86, 0.19, 0.27, 0.27 | identical |
| 3 | Concerns, condition effect | F(2, 1563) = 24.49, p < .001, eta2p = .030 | 24.491, < .001, .030 |
| 4 | Income: concerns ... Reflection | t = -21.07, 8.71, 4.55, 8.44 | -21.066, 8.713, 4.553, 8.445 |
| 5 | Concerns, condition effect / interaction | F(8, 2130) = 4.16 / 0.78, p = .618 | 4.164 / 0.783, .618 |
| all | Reliabilities (alpha) | .83/.67; .85/.70/.60/.73; .81/.73/.63/.74; .86/.75/.63/.76; .86/.71/.60/.74 | identical |
| all | Recall completions 856 vs 977 | binomial p = .005 | .005 |

Methodological notes confirmed in the replication: the contrasts are Student's t tests with equal variances (not Welch); the interaction tests are the condition x income term of an OLS regression on the two conditions of a technique, with one-tailed p in the direction of a larger effect among low-income participants; the ANOVAs use Type III sums of squares with sum-to-zero contrasts (the SPSS default); Cohen's d uses the pooled SD.

## 5. Meta-analysis

Both implementations reproduce Tables 7 and 8 (`output/tables/meta_table7_pooled_effects.csv`, `meta_table8_techniques.csv`, and the `_python` versions):

| Quantity | Manuscript | R (metafor 5.2) | Python |
|---|---|---|---|
| Concerns, all studies | 0.01 [-0.18, 0.20], p = .950; Q(17) = 82.62 | identical; Q = 82.62 | identical |
| Concerns, without Study 3 | 0.09 [0.03, 0.15], p = .004; Q(15) = 20.89, p = .140; techniques F(7, 8) = 2.51, p = .111 | identical | identical |
| Cognitive reflection | -0.02 [-0.06, 0.03], p = .482; low -0.05 [-0.11, 0.01], p = .100; b = -0.07 [-0.15, 0.02], p = .115 | identical | identical |
| Fluid intelligence | 0.00 [-0.05, 0.05], p = .996; b = -0.03 [-0.13, 0.07], p = .503 | identical | identical |
| Reflection score | -0.02 [-0.07, 0.03], p = .473; 90% CI [-0.06, 0.02]; b = -0.06 [-0.16, 0.04], p = .238 | identical | identical |
| COVID-19 pictures, low-income CRS | g = -0.13 [-0.25, -0.02], p = .024; b = -0.17 [-0.33, -0.01], p = .039; Holm p = .29; equivalence p = .123 | identical (.286, .123) | identical |
| Successful-check subset | CRS -0.04 [-0.13, 0.06]; Reflection (k = 5) -0.05 [-0.16, 0.06] | identical | identical |
| Meta-regression slopes | -0.05 [-0.28, 0.18], p = .672; -0.10 [-0.35, 0.15], p = .412; without Study 3: -0.20 [-0.67, 0.27], -0.01 [-0.80, 0.78] | identical | identical |
| Leave-one-study-out | CRS -0.025 to -0.003; Reflection -0.030 to -0.004 | identical | identical |
| Six-item Study 1 score; single-level Reflection | -0.01 [-0.06, 0.03]; -0.01 [-0.05, 0.03] | identical | identical |
| Ties to low-income group | 7 of 192 within-study tests change; no pooled estimate changes | 7 of 192; none | 7 of 192 |
| One-tailed cognitive tests | 4 of 96 (4.2 %) | 4 of 96 | 4 of 96 |
| Income groups pooled | -0.73 [-1.00, -0.46]; 0.25 [0.15, 0.35]; 0.17 [0.03, 0.32]; 0.27 [0.16, 0.39] | identical | identical |

Small differences, none of which changes a conclusion:

- I2 for the concerns models: 86 % and 26 % here versus 87 % and 28 % in the manuscript. Q and the variance components are identical, so the difference lies in the "typical sampling variance" formula used to express I2 for a multilevel model; the single-level I2 is 80 %. The authors may want to state which formula they used.
- The low-income Reflection estimate in the successful-manipulation subset: -0.06 [-0.19, 0.07] here versus [-0.22, 0.10] in the manuscript; the point estimate matches. The manuscript's wider interval suggests a slightly different model for that one sensitivity analysis (for example k - 1 rather than k - 2 degrees of freedom, or the comparisons entered separately). Worth checking against the original script.
- The Python and R meta-analyses differ by at most 0.0005 (Knapp-Hartung and pooled-effect tables) and otherwise by less than 1e-5, which is optimizer tolerance.

## 6. What cannot be reproduced from these files

1. **Repeat participants.** The manuscript reports 259 Study 4 participants who had taken part in Study 3 and 81 Study 5 participants who had taken part in Study 3 or 4, and reruns the analyses without them. The Prolific IDs in the exports are genuine 24-character IDs (their embedded account-creation dates run from 2014 to 2022 and they equal the IDs in the Prolific demographic export), yet **no ID occurs in more than one study**. IP addresses do overlap (187 Study 4 rows share an IP with Study 3; 29 Study 5 rows with Studies 3 or 4), so the overlap of people is real and the IDs must have been pseudonymised per study before the files were exported. The "without repeat participants" analyses are therefore not part of this package, by decision; the meta-analysis scripts print a note to that effect. If the flags become available later, a 0/1 column can be added to the processed files and the models rerun on the subset.
2. **Option texts and question wording.** Studies 1, 2, 4 and 5 exported numeric option codes. For the CPT the correct option is known from the derived columns and the intuitive option from the response frequencies, but the texts of options 3 and 4, the UK wording of the income and education questions, the two COVID-19 risk items, and the ideology and religiosity scales are not in the data. The codebook marks these; the Qualtrics survey files on OSF should be used to complete them.
3. **Affective Slider order (Studies 4 and 5).** The export names the items `Q1_1` and `Q2_1`; the codebook assumes the Study 3 order (arousal, pleasure). Confirm against the survey.
4. **Study 3 residency label.** The "yes" option of the residency question is exported as "UK" for a US sample; presumably a copied label. Harmless for the analyses, but the published codebook should say what the question asked.
5. **Study 5 collection dates.** The Prolific time stamps place the full-income-range pool in November 2022 (Section 2); the Participants section of Study 5 should state both collection periods.

## 7. Recommendations for the published package

- Publish the files in `data/public/` (the processed data with participant numbers reassigned after a seeded shuffle) rather than the raw exports: they contain no identifiers or free text, every variable is documented in `codebook/codebook.md`, and the analysis code starts from the same data. The Prolific profile demographics are kept at the authors' request; they are quasi-identifiers (rare nationalities or languages combined with age and sex), which is usual for published panel data but worth a sentence in the data statement. Keep the raw exports private or publish them only after removing Prolific IDs, IP addresses, locations and the free-text answers.
- Publish both pipelines or just the R one. The R code is the reference because the manuscript's meta-analysis was done with metafor and the R code calls the same functions (`rma.mv`, `vcalc`); the Python code is an independent re-implementation that agrees with it and is useful as a cross-check. Either `run_all` script regenerates every table, figure and check from the raw files in one command.
- State in the manuscript's Data Availability Statement that the published data do not identify repeat participants, so the analyses without them (Studies 4 and 5, meta-analysis) cannot be rerun from the public files.
- Record the software versions: the manuscript cites R 4.3.3 and metafor 4.4; this replication used R 4.6.2 with metafor 5.2.1 and Python 3.15 (pandas 3.1, scipy 1.18, statsmodels 0.15) and obtained the same results. `output/checks/r_sessionInfo.txt` is written by `R/run_all.R`.
- The Data Availability Statement still has a placeholder for the Study 5 preregistration link ("link (Study 5)").

## 8. Which application to use

- **R (RStudio)** for the published analysis code: it is what the manuscript used, metafor has no equivalent elsewhere, and the per-study tests, ANOVAs and figures are standard. This is the version to deposit on OSF.
- **Python** as an independent cross-check and for anyone who prefers it; the pipeline is complete, but the meta-analysis relies on a purpose-written REML routine rather than a maintained package, so it should be presented as a replication of the R results, not as the primary analysis.
- **JASP or jamovi** for verifying the per-study statistics by hand and for readers without programming: `jasp/JASP_replication_guide.md` gives the menu, settings and expected values for every test. JASP's meta-analysis module (which wraps metafor) can reproduce the pooled estimates approximately; it cannot model the shared control groups of Studies 2 to 4 or run the equivalence tests, so for Tables 7 and 8 the R code remains the reference.
