# FBA Analysis of Phage-Resistant Mutants in *Cellulophaga baltica*

This repository contains analysis code, data, and models for investigating the metabolic mechanisms and ecological implications of phage resistance mutations in *Cellulophaga baltica* (strain 18, Cba18-WT) using Flux Balance Analysis (FBA).

## Project Overview

This research uses Flux Balance Analysis to investigate how a mutation in *C. baltica* mutant 184f1 affects its metabolism and contributes to phage resistance mechanisms.

### Key Research Questions Explored with FBA

- What are the predicted metabolic flux changes in mutant 184f1 compared to Cba18-WT?
- Can FBA modeling suggest metabolic bottlenecks potentially contributing to phage resistance in 184f1?

## Repository Structure

- `analysis.ipynb`: Jupyter notebook containing all FBA analysis code. Run interactively via Colab: [![Open in Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/cowusuansah/cba-mutant-fba-analysis/blob/main/analysis.ipynb)
  - **Note:** Simulating the mutant using MOMA requires the Gurobi solver. The `analysis.ipynb` notebook, by default, uses pre-existing data from this repository and does not require Gurobi. However, generating new simulation data within the notebook will likely require the Gurobi solver, which needs a license.

- `models/`: Metabolic models and related files
  - `CBA18_RAST_defined.xml`: Genome-scale metabolic model of *C. baltica* 18 (Cba18-WT).
  - `curation/`: Contains files related to model curation and gapfilling:
    - `threonine_aldolase.txt`: Details on the addition of the threonine aldolase reaction (`rxn00541`) to the model based on gene `M666_RS13845`.
    - `gapfilling_reactions.tsv`: List of 63 reactions added during gapfilling to enable growth on experimentally verified carbon sources.
  - `model_diagnostics/`: Contains structural analysis of the metabolic model:
    - `blocked_reactions.tsv`: List of reactions predicted to carry no flux under any condition, including `rxn00274` (L-threonine degradation) relevant to mutants 184d1 and 184e1.
    - `dead_end_metabolites.tsv`: List of metabolites that cannot be produced or consumed by the network.

- `growth_media/`: Media definitions used for simulations, downloaded from the ModelSEED database.

- `simulations/`: Simulation results for the curated model (`CBA18_RAST_defined_add_threonine_adolase`)
  - `fluxes/`: Flux distributions for WT (pFBA) and mutant 184f1 (quadratic MOMA) under various conditions, including differences, normalized differences, and ratios.
  - `metabolite_production/`: Total production flux (turnover) for each metabolite.

- `escher maps/`: Metabolic pathway visualizations.

- `growth_and_mutant_data/`: Experimental data on growth and mutant characterization.