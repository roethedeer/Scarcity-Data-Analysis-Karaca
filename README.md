# Does Scarcity Impede Cognitive Function? Five Preregistered Experiments and Meta-Analysis

Analysis code, processed data and codebook for the five studies reported in the paper (Karaca, Isler, Yilmaz, Dulleck & Dogruyol). The same pipeline is implemented twice, in R and in Python, and both reproduce every number in the paper's Results sections and Tables 2 to 8 from the raw survey exports. 

## Folder layout

```
Analysis/
  St1.csv ... St5.csv, St1.sav ... St5.sav   raw exports (one per study; CSV and SPSS versions are identical)
  Scarcity_Ak_JDM_26102026.docx              paper
  R/                                         R pipeline (run from the project root)
    00_setup.R          packages, paths, helper functions (t tests, Cohen's d, Hedges' g, Type III ANOVA)
    01_prepare_data.R   raw exports -> data/processed/*.csv, with verification against the SPSS-derived scores
    02_study_analyses.R per-study results: reliabilities, ANOVAs, income groups, Tables 2-6
    03_meta_analysis.R  meta-analyses with metafor: Tables 7-8, equivalence tests, sensitivity analyses
    04_figures.R        figures
    run_all.R           runs everything in order
  python/                                    Python pipeline (same steps, same file names with the suffix _python)
    setup.py, prepare_data.py, study_analyses.py, meta_analysis.py, figures.py, make_codebook.py,
    compare_with_paper.py, compare_text_statistics.py, anonymity_audit.py, build_site.py, run_all.py, requirements.txt
  data/processed/       study1.csv ... study5.csv, all_studies.csv (R output; *_python.csv from Python, identical)
  data/public/          the public release: same data, participant numbers reassigned after a seeded shuffle (anonymity_audit.py)
  output/tables/        every statistic as CSV (study<k>_*.csv, all_*.csv, meta_*.csv)
  output/figures/       PNG and PDF figures
  output/checks/        CSV-vs-SAV comparison, derived-vs-SPSS verification, replication checks, logs
  codebook/             codebook.md, codebook.csv, conditions_by_study.csv
  REPLICATION_REPORT.md what was checked and what was found
```

## Running the pipeline

R (4.6; packages: readr, dplyr, tidyr, purrr, stringr, car, psych, metafor, ggplot2):

```bash
Rscript R/run_all.R
```

Python (3.12 or later; `pip install -r python/requirements.txt`):

```bash
python3 python/run_all.py
```

Both must be run from the project root (the folder that contains `R/`, `python/` and the raw `St*.csv` files). In RStudio, open `Analysis.Rproj` and source `R/run_all.R`. The R pipeline takes about two minutes; the Python pipeline about ten (the multilevel models are fitted by a small REML implementation in `meta_analysis.py`, since Python has no metafor).

## What the code does

1. **Prepare.** Reads each raw export, maps the Qualtrics condition labels to the condition names of the paper, recomputes every score from the item responses (scarcity concerns, cognitive reflection, fluid intelligence, Reflection score, financial literacy, PANAS), computes effective income and the preregistered median split, and drops identifying columns. The recomputed scores are compared with the scores stored in the SPSS files; the script stops if any value differs (`output/checks/derived_vs_spss.csv`).
2. **Per-study analyses.** For each study: Cronbach's alpha, Type III ANOVAs with partial eta squared, two-tailed income-group comparisons, and the preregistered one-tailed contrasts with Cohen's d in the full sample and within income groups, plus the condition x income interaction for each pair of conditions (`output/tables/study<k>_table.csv` reproduce Tables 2 to 6).
3. **Meta-analysis.** Hedges' g for the 18 comparisons; shared-control covariance (metafor::vcalc); multilevel REML models (Table 7), technique models (Table 8), equivalence tests, meta-regressions, leave-one-study-out, alternative scoring and income split, single-level Knapp-Hartung models, and the pooled income-group differences.
4. **Figures.** Forest plots (Figure 1 of the paper and all 18 comparisons on all measures), condition means by income group, income-group differences, and the distribution of effective income.
5. **Checks.** `python/compare_with_paper.py` compares the 64 rows of Tables 2 to 6 (three d values, three one-tailed p values, and the interaction t and p each) with the values extracted from the paper; `python/compare_text_statistics.py` does the same for the statistics reported in the text (sample descriptives, income groups, reliabilities, ANOVAs, manipulation checks); `output/checks/meta_r_vs_python.csv` compares the R and Python meta-analyses.

## Data

The processed files contain one row per participant: the analysis variables, the survey demographics, and the demographic fields of the participants' Prolific profiles (Studies 2 to 5). Prolific IDs, IP addresses, locations, free-text answers and open comments are not included. See `codebook/codebook.md` for every variable, its coding, and the scoring keys, and `REPLICATION_REPORT.md` for what was checked and the open points. The paper's analyses without repeat participants (Studies 4 and 5) are not included: the exports do not identify repeat participants.
