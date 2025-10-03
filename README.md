# Thesis_R_codes

This repository hosts the working files for a thesis that studies the determinants of Foreign Direct Investment (FDI) with panel data econometric techniques. The project is built around a collection of R scripts that clean raw macroeconomic indicators, construct modelling datasets, and run a sequence of regression specifications that mirror the empirical chapters of the thesis.

## Repository layout

The repository currently contains the top-level documentation and licensing files. As you continue to develop the project, you will typically see (or create) the following folders and scripts:

- `R/`: R scripts for data cleaning, feature engineering, model estimation, and result visualisation. A conventional breakdown is to have separate scripts for ingesting data, preparing panel structures, estimating models (e.g., fixed effects, random effects, or dynamic panel models), and generating tables/plots for the thesis.
- `data_raw/` and `data_processed/`: Storage locations for the source macroeconomic data and the transformed datasets that feed the econometric models. Keeping these separate makes it easy to reproduce the pipeline from scratch.
- `outputs/`: Generated artefacts such as regression tables, diagnostic plots, and any intermediate CSV files used to build figures in the written thesis.
- `docs/` or `notes/`: Supplemental methodological notes, meeting summaries, or literature review excerpts that explain modelling choices.

If these directories are not yet present in your local checkout, create them as you begin adding code and data—structuring the repository early will keep subsequent work organised.

## Getting started

1. **Set up R**: Install R (version 4.2 or later is recommended) and RStudio or your preferred IDE. Use `renv` or a similar tool to manage package dependencies once the project scripts are in place.
2. **Collect the data sources**: Gather the macroeconomic indicators, governance metrics, or institutional variables referenced in the thesis. Document the provenance of each dataset so you can cite and update them consistently.
3. **Implement the pipeline**: Begin with a data ingestion script that harmonises country codes and calendar periods, followed by transformation scripts that compute lagged variables or moving averages required by the econometric models. Finally, add analysis scripts that fit the panel models and export tables/figures.
4. **Track results**: Version-control your outputs (or at least the code that generates them) so you can reproduce the thesis figures when data revisions arrive.

## Key considerations for newcomers

- **Reproducibility**: Aim to keep every derived dataset and figure reproducible from the raw inputs by running the scripts end-to-end. R Markdown documents can be useful for combining narrative explanations with executable code.
- **Econometric tooling**: Familiarise yourself with R packages such as `plm`, `lfe`, `fixest`, or `sandwich` for robust standard errors. These form the backbone of panel data econometrics in R.
- **Data management**: Be explicit about country/year coverage, treatment of missing values, and variable transformations. These details are critical when interpreting results in the thesis.

## Suggested next steps

- Draft a `PROJECT_PLAN.md` or similar document capturing hypotheses, model specifications, and milestones to keep the research roadmap visible.
- Set up automated checks—such as linting with `lintr` or lightweight tests with `testthat`—once scripts start to accumulate.
- Explore versioned data storage (e.g., using `git-lfs` or cloud buckets) if the raw datasets are too large for the Git repository.
- Continue expanding this README with concrete instructions (package lists, script entry points, and expected outputs) as the analysis matures.

By establishing the structure and practices above, newcomers can quickly understand where each piece of the FDI thesis analysis lives and how to extend it responsibly.
