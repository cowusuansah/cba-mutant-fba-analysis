# FBA Analysis of Phage-Resistant Mutants in *Cellulophaga baltica*

This repository contains analysis code, data, and models for investigating the metabolic mechanisms and ecological implications of phage resistance mutations in *Cellulophaga baltica* (strain 18, Cba18-WT) using Flux Balance Analysis (FBA).

## Project Overview

This research uses Flux Balance Analysis to investigate how a mutation in *C. baltica* mutant 184f1 affects its metabolism and contributes to phage resistance mechanisms.

### Key Research Questions Explored with FBA

- What are the predicted metabolic flux changes in mutant 184f1 compared to Cba18-WT?
- Can FBA modeling suggest metabolic bottlenecks potentially contributing to phage resistance in 184f1?

## Repository Structure

- `analysis.ipynb`: Jupyter notebook containing all FBA analysis code. Run interactively via Colab: [![Open in Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/cowusuansah/cba-mutant-fba-analysis/blob/main/analysis.ipynb)

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

## Methods

### Model Construction and Curation

The genome-scale metabolic model of *Cellulophaga baltica* 18 (Cba18-WT, NZ_CP009976) was constructed as follows:
1.  **Genome Annotation:** Using RASTtk within KBase.
2.  **Model Reconstruction:** Using the KBase MS2 tool with a Gram-negative template.
3.  **Gapfilling:** The model was gapfilled for growth on experimentally verified carbon sources (details in `models/curation/gapfilling_reactions.tsv`).
4.  **Manual Curation:** A threonine aldolase reaction (`rxn00541`) was added based on gene homology and experimental necessity for simulating mutant growth (details in `models/curation/threonine_aldolase.txt`).
5.  **Export:** The final model (`models/CBA18_RAST_defined.xml`) was exported for analysis with COBRApy.

### Simulation of Wild-Type and Mutant Phenotypes

-   **Mutant 184f1:** The effect of the mutation in gene `M666_RS17370` (phosphoglycerate dehydrogenase) was simulated by constraining the flux through the corresponding reaction (`rxn01101`) to zero.
-   **Mutants 184d1 & 184e1:** These mutants, affecting L-threonine degradation (`rxn00274`), were not simulated further as the reaction was predicted to be blocked in the WT model (see `models/model_diagnostics/blocked_reactions.tsv`).
-   **Media:** Simulations used defined single-carbon-source media and a complex LB medium formulation (definitions in `growth_media/`).
-   **Constraints:** Physiologically relevant constraints were applied to oxygen and carbon source uptake rates. Specific values and rationale can be found in publication.
-   **Analysis Methods:**
    -   **pFBA (Parsimonious FBA):** Used to predict Cba18-WT flux distributions.
    -   **MOMA (Minimization Of Metabolic Adjustment):** Used to predict mutant 184f1 flux distributions (quadratic MOMA best matched experiments).
    -   **Solver:** Gurobi.

### Analysis of Metabolic Changes

Metabolic differences between Cba18-WT (pFBA) and mutant 184f1 (quadratic MOMA) were assessed at multiple levels. See the `analysis.ipynb` notebook and the publication for detailed methods. Key analyses included:
1.  **Reaction Level:** Comparing flux distributions (results in `simulations/.../fluxes/`).
2.  **Pathway Level:** Aggregating reaction fluxes by KEGG and MetaCyc annotations.
3.  **Metabolite Level:** Calculating total steady-state production rates (results in `simulations/.../metabolite_production/`).