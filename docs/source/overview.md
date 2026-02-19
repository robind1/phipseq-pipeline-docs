# Overview

[This pipeline](https://github.com/oucru-id/phip-seq-ont) is a Nextflow-based workflow for the analysis of Phage Immunoprecipitation Sequencing (PhIP-Seq) data, adapted from [phip-flow](https://github.com/matsengrp/phip-flow) by the Matsen Group. It processes raw nanopore sequencing reads through alignment, statistical enrichment analysis, virus scoring, IEDB epitope annotation, and generates FHIR-compliant immunological reports with interactive 3D protein structure visualizations.

PhIP-Seq is a high-throughput method for profiling antibody repertoires against thousands of viral peptides simultaneously. This pipeline uses the [phippery](https://matsen.group/phippery/introduction.html) Python library as its core data structure.

## Key Features
* **Nanopore Sequencing Support**: Alignment of nanopore reads using Bowtie2.
* **Statistical Enrichment Analysis**: Counts-per-million (CPM) normalization, size factor estimation, Z-score modeling against bead-only controls, and optional edgeR/BEER analysis.
* **Virus Scoring**: Calculates per-species virus exposure scores based on enriched peptide hits with novel epitope filtering.
* **FDR Analysis**: Benjamini-Hochberg FDR correction on Z-score-derived p-values for multiple testing control.
* **IEDB Epitope Annotation**: Cross-references enriched peptides against the Immune Epitope Database (IEDB) to identify known and novel epitopes.
* **FHIR Compliance**: Generates HL7 FHIR R4 bundles encoding PhIP-Seq observations for clinical data exchange.
* **3D Protein Visualization**: Interactive Streamlit dashboard with Mol* viewer for mapping enriched epitopes onto PDB structures.
* **Interactive Heatmaps**: Virus score and Z-score heatmaps for visual exploration.

## Key Outputs
* Normalized count matrices (CPM, size factors, Z-scores)
* Virus scores per sample per species
* IEDB epitope annotation reports
* FHIR genomics bundles
* Interactive heatmaps (virus scores, Z-scores)
* Streamlit 3D protein structure visualization