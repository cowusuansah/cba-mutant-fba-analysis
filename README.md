# FBA Analysis of Phage-Resistant Mutants in *Cellulophaga baltica*

This repository contains analysis code, data, and models for investigating the metabolic mechanisms and ecological implications of phage resistance mutations in *Cellulophaga baltica* (strain 18, Cba18-WT) using Flux Balance Analysis (FBA).

## Project Overview

This research explores how a mutation conferring phage resistance in *C. baltica* mutant 184f1 affects its metabolism compared to the wild-type (Cba18-WT), and the potential ecological significance of these changes. Using a genome-scale metabolic model and constraint-based analysis methods, we characterize the metabolic consequences of a mutation in L-serine biosynthesis and relate these changes to phage resistance mechanisms.

### Key Research Questions Explored with FBA

- What are the predicted metabolic flux changes in mutant 184f1 compared to Cba18-WT?
- How does the L-serine biosynthesis mutation impact related pathways and overall metabolism?
- Can FBA modeling suggest metabolic bottlenecks potentially contributing to phage resistance in 184f1?

## Repository Structure

*(Note: This structure reflects the repository before planned cleanup. Some files/folders might be removed before final publication.)*

- `analysis.ipynb`: Jupyter notebook containing all analysis code described in the methods.

- `models/`: Metabolic models and related files
  - `CBA18_RAST_defined.xml`: Genome-scale metabolic model of *C. baltica* 18 (Cba18-WT), manually curated.
  - `manual curation/`: Contains files related to model curation, specifically:
    - `threonine_aldolase.txt`: Details on the addition of the threonine aldolase reaction (`rxn00541`) to the model based on gene `M666_RS13845`.

- `growth_media/`: Media definitions used for simulations, downloaded from the ModelSEED database.

- `simulations/`: Simulation results for the curated model (`CBA18_RAST_defined_add_threonine_adolase`)
  - `fluxes/`: Flux distributions for WT (pFBA) and mutant 184f1 (quadratic MOMA) under various conditions, including differences, normalized differences, and ratios.
  - `metabolite_production/`: Total production flux (turnover) for each metabolite.
  - `other/`: Additional analysis results including:
    - `gapfilling_reactions.tsv`: List of 65 reactions added during gapfilling.
    - `blocked_reactions.tsv`: List of reactions predicted to carry no flux under any condition, including `rxn00274` (L-threonine degradation) relevant to mutants 184d1 and 184e1.

- `escher maps/`: Metabolic pathway visualizations.

- `growth_and_mutant_data/`: Experimental data on growth and mutant characterization.

## Methods

### Model Construction

The genome-scale metabolic model of *Cellulophaga baltica* 18 (Cba18-WT, NZ_CP009976) was constructed and curated as follows:
1.  **Genome Annotation:** The genome was imported into KBase and annotated using RASTtk (v1.073).
2.  **Model Construction:** A draft model was built using the KBase "MS2 - Build Prokaryotic Metabolic Models with OMEGGA" tool (v2.0.0), applying a Gram-negative template biomass composition.
3.  **Gapfilling:** The model was gapfilled to enable growth on experimentally verified carbon sources (D-glucose, lactose, D-maltose, sucrose, fructose, L-glutamic acid, salicin), adding 65 reactions listed in `simulations/CBA18_RAST_defined_add_threonine_adolase/other/gapfilling_reactions.tsv`.
4.  **Export:** The final model (`models/CBA18_RAST_defined.xml`, 1,295 reactions, 848 genes) was exported for analysis using COBRApy (v0.29.1).

### Simulation of Wild-Type and Mutant Phenotypes

-   **Manual Curation:** A threonine aldolase reaction (`rxn00541`), catalyzed by gene `M666_RS13845`, was added to the model based on homology to *E. coli* *itaE*. This curation was necessary to allow the simulated growth of mutant 184f1, consistent with experimental observations. Details can be found in `models/manual curation/threonine_aldolase.txt`.
-   **Mutant 184f1:** The mutation affects gene `M666_RS17370` (phosphoglycerate dehydrogenase, involved in L-serine biosynthesis, `rxn01101`). This was simulated by constraining the flux through the corresponding reaction to zero.
-   **Mutants 184d1 & 184e1:** These mutants affect gene `M666_RS13880` (L-threonine degradation, `rxn00274`). These mutants were *not* analyzed further using FBA because the affected reaction was found to be blocked (zero flux) in the wild-type model under all simulated conditions, preventing FBA-based phenotype prediction (see `simulations/CBA18_RAST_defined_add_threonine_adolase/other/blocked_reactions.tsv`).
-   **Media:** Simulations used defined single-carbon-source media and a complex LB medium formulation from the ModelSEED database (`growth_media/`).
-   **Constraints:** To ensure physiological relevance, uptake rates were constrained.
    - *Oxygen:* The maximum oxygen uptake rate was first determined for Cba18-WT (using pFBA) under each medium condition. The constraint was then set to 80% of this calculated optimal value. This specific numerical uptake rate limit was then applied to simulations of *both* Cba18-WT and mutant 184f1 for that medium, ensuring consistent environmental conditions for comparison.
    - *Carbon Sources:* Limited to 5 mmol gDW⁻¹ h⁻¹ for single-carbon-source media, and 1 mmol gDW⁻¹ h⁻¹ for each potential carbon source in the complex LB medium. Other media components were unconstrained (1000 mmol gDW⁻¹ h⁻¹).
-   **Analysis Methods:**
    -   **pFBA (Parsimonious FBA):** Used to predict Cba18-WT flux distributions, minimizing total flux while maximizing biomass.
    -   **MOMA (Minimization Of Metabolic Adjustment):** Used to predict mutant 184f1 flux distribution, minimizing the Euclidean distance (quadratic MOMA) to the Cba18-WT pFBA solution. Quadratic MOMA was chosen as it best matched experimental growth observations.
    -   **Solver:** Gurobi (v11.0.3)

### Analysis of Metabolic Changes

Metabolic differences between Cba18-WT (pFBA) and mutant 184f1 (quadratic MOMA) were quantified using:
1.  **Reaction Level:** Flux difference, normalized flux difference, and flux ratio (Mutant Flux / WT Flux). Results in `simulations/CBA18_RAST_defined_add_threonine_adolase/fluxes/`.
2.  **Pathway Level:** Reaction fluxes were aggregated using KEGG and MetaCyc pathway annotations. Pathway changes were assessed by the average flux difference and average absolute flux difference across reactions within each pathway.
3.  **Metabolite Level:** Total steady-state production (turnover rate) was calculated for each metabolite by summing production fluxes across reactions. Results in `simulations/CBA18_RAST_defined_add_threonine_adolase/metabolite_production/`.
4.  **Bottleneck Assessment:** FBA was used to assess if the production fluxes of key phage building blocks (amino acids, NTPs, dNTPs, cofactors, lipids) were limited in the mutant compared to WT.

## Installation and Usage

### Dependencies

This project requires Python and the following core packages:
- COBRApy (v0.29.1) for constraint-based modeling
- Gurobipy (v11.0.3) as the linear programming solver
- Pandas, NumPy, SciPy for data manipulation
- Matplotlib, Seaborn for visualization
- Additional dependencies are listed in the `analysis.ipynb` notebook.

### Running the Analysis

The primary analysis is contained within the Jupyter notebook `analysis.ipynb`. Ensure all dependencies are installed and the Gurobi solver is configured. 