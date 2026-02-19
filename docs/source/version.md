# Version

## Pipeline Version
* **PhIP-Seq Analysis Pipeline Version:** 1.0.0
* **Adapted from:** [phip-flow](https://github.com/matsengrp/phip-flow) by the Matsen Group

## Software Dependencies

### Core Pipeline
| Tool | Purpose |
| :--- | :--- |
| [Nextflow](https://www.nextflow.io/) | Workflow orchestration |
| [phippery](https://matsen.group/phippery/) | PhIP-Seq data structures, normalization, and Z-score modeling |
| [Bowtie2](http://bowtie-bio.sourceforge.net/bowtie2/) | Short/long read alignment against peptide library |
| [SAMtools](https://github.com/samtools/samtools) | BAM processing, sorting, indexing, and counting |

### Statistical Analysis (Python)
| Library | Purpose |
| :--- | :--- |
| pandas | Data manipulation |
| numpy | Numerical computation |
| scipy | Statistical distributions (Z-score to p-value conversion) |
| statsmodels | Multiple testing correction (Benjamini-Hochberg FDR) |
| plotly | Interactive heatmap visualizations |

### Statistical Analysis (R, Optional)
| Library | Purpose |
| :--- | :--- |
| [PhIPData](https://bioconductor.org/packages/PhIPData/) | Bioconductor PhIP-Seq data class |
| [beer](https://bioconductor.org/packages/beer/) | Bayesian Enrichment Estimation in R |
| [edgeR](https://bioconductor.org/packages/edgeR/) | Empirical Bayes differential enrichment |

### Visualization
| Library | Purpose |
| :--- | :--- |
| [Streamlit](https://streamlit.io/) | Interactive web dashboard |
| [Mol*](https://molstar.org/) | 3D protein structure viewer (PDBe Web Component) |
| [Plotly](https://plotly.com/python/) | Interactive heatmaps |

## Reference Data

### IEDB Database
* **Source**: [Immune Epitope Database (IEDB)](https://www.iedb.org/)
* **Description**: Curated database of experimentally characterized immune epitopes.

### PDB Structures
* **Source**: [RCSB Protein Data Bank](https://www.rcsb.org/)
* **Description**: 3D protein structure files (PDB/CIF format) used for mapping enriched epitopes onto protein structures in the Streamlit dashboard.

## References
1. Mohan, D., et al. (2018). *PhIP-seq characterization of serum antibodies using oligonucleotide-encoded peptidomes*. [Nature Protocols](https://doi.org/10.1038/s41596-018-0025-6).
2. Larman, H. B., et al. (2011). *Autoantigen discovery with a synthetic human peptidome*. [Nature Biotechnology](https://doi.org/10.1038/nbt.1856).
3. Xu, G. J., et al. (2015). *Comprehensive serological profiling of human populations using a synthetic human virome*. [Science](https://doi.org/10.1126/science.aaa0698).
4. Galloway, J. G., et al. (2023) *phippery: a software suite for PhIP-Seq data analysis*. [Bioinformatics](https://doi.org/10.1093/bioinformatics/btad583).
5. Di Tommaso, P., et al. (2017). *Nextflow enables reproducible computational workflows*. [Nature Biotechnology](https://doi.org/10.1038/nbt.3820).