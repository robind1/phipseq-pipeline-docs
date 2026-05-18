# Overview

[This pipeline](https://github.com/oucru-id/phip-seq-ont) is a Nextflow-based workflow for the analysis of Phage Immunoprecipitation Sequencing (PhIP-Seq) data, adapted from [phip-flow](https://github.com/matsengrp/phip-flow) by the Matsen Group. It processes raw nanopore sequencing reads through alignment, statistical enrichment analysis, virus scoring, IEDB epitope annotation, and generates FHIR-compliant immunological reports with interactive 3D protein structure visualizations.

PhIP-Seq is a high-throughput method for profiling antibody repertoires against thousands of viral peptides simultaneously. This pipeline uses the [phippery](https://matsen.group/phippery/introduction.html) Python library as its core data structure.

## Key Features
* **Nanopore Sequencing Support**: Alignment of nanopore reads using Bowtie2.
* **Statistical Enrichment Analysis**: Counts-per-million (CPM) normalization, size factor estimation, Z-score against bead-only controls, and optional edgeR/BEER analysis.
* **Virus Scoring**: Calculates per-species virus exposure scores based on enriched peptide hits with novel epitope filtering.
* **FDR Analysis**: Benjamini-Hochberg FDR correction on Z-score-derived p-values for multiple testing control.
* **IEDB Epitope Annotation**: Cross-references enriched peptides against the Immune Epitope Database (IEDB) to identify known and novel epitopes.
* **Neutralizing prediction**: Identify potential neutralizing peptides (In development).
* **FHIR Compliance**: Generates HL7 FHIR R4 bundles encoding PhIP-Seq observations for clinical data exchange.
* **3D Protein Visualization**: Interactive Streamlit dashboard with Mol* viewer for mapping enriched epitopes onto PDB structures.
* **Interactive Heatmaps**: Virus score and Z-score heatmaps for visual exploration.

## Key Outputs
* Normalized count matrices (CPM, size factors, Z-scores)
* Virus scores per sample per species
* IEDB epitope annotation reports
* Neutralizing prediction (in development)
* FHIR genomics bundles
* Interactive heatmaps (virus scores, Z-scores)
* Streamlit 3D protein structure visualization

## Directory Structure

```
phip-seq-ont
├── main.nf                          # Main workflow entry point
├── nextflow.config                  # Configuration and parameters
├── workflows/
│   ├── alignment.nf                 # Read alignment and count collection
│   ├── statistics.nf                # Normalization and Z-score calculation
│   ├── output.nf                    # dataset export
│   ├── fhir_report.nf               # HL7 FHIR R4 bundle generation
│   ├── fdr.nf                       # Benjamini-Hochberg correction
│   ├── virusscore.nf                # Per-species exposure scoring
│   ├── iedb_annotation.nf           # IEDB Epitope annotation and novelty classification
│   ├── visualization.nf             # Interactive heatmaps
│   ├── streamlit.nf                 # 3D protein visualization dashboard
│   ├── neutralization_score.nf      # Neutralization scoring (in development)
│   ├── aggregate.nf                 # result aggregation
│   └── edgeR_BEER.nf                # Optional edgeR/BEER analysis
├── bin/
│   ├── calc_scores_nofilter.py      # Virus score calculation
│   ├── fit-predict-zscore.py        # Z-score calculation
│   ├── generate-fasta.py            # Peptide FASTA generation
│   ├── merge-counts-stats.py        # Count/stats merging into phippery dataset
│   ├── phipseq_to_fhir.py           # PhIP-Seq to FHIR converter
│   ├── replicate-counts.py          # Replicate sequence count aggregation
│   ├── run_BEER.Rscript             # BEER Bayesian enrichment (optional)
│   ├── run_edgeR.Rscript            # edgeR differential enrichment (optional)
│   ├── validate-peptide-table.py    # Peptide table validation
│   └── validate-sample-table.py    # Sample table validation
├── templates/                       # Shell script templates (alignment, SAM processing)
└── data/                            # dataset (sample table, peptide table, FASTQs)
```
