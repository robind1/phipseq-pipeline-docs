# Data Processing

## Main Workflow
**File:** `main.nf`
The entry point controls all sub-workflows. It automatically determines read file locations based on whether the default example data or custom data is being used:
- If the sample table matches the default example, reads are resolved relative to `$baseDir`.
- If a custom sample table is provided, reads are resolved relative to `$launchDir`.

## 1. Alignment Workflow (ALIGN)
**File:** `workflows/alignment.nf`
Processes raw FASTQ reads into aligned count matrices.
### Steps:
1. **Validate Sample Table** (`validate-sample-table.py`)
   - Ensures `fastq_filepath` column is present.
   - Auto-generates integer `sample_id` indices.
   - Validates that `control_status` column exists with ≥2 `beads_only` samples (when Z-score analysis is enabled).
2. **Validate Peptide Table** (`validate-peptide-table.py`)
   - Ensures `oligo` column is present.
   - Auto-generates `peptide_id` indices if not provided.
3. **Generate FASTA Reference** (`generate-fasta.py`)
   - Converts peptide oligo sequences to FASTA format (uppercase characters only).
4. **Generate Bowtie2 Index** (`bowtie2-build`)
   - Builds a Bowtie2 index from the peptide FASTA reference.
5. **Nanopore Alignment** (`templates/nanopore_alignment.sh`)
   - Tool: **Bowtie2** with configurable options (default: `--local --very-sensitive-local`).
   - Aligns each sample's FASTQ reads against the peptide index.
   - Output: SAM files per sample.
6. **SAM to Counts** (`templates/sam_to_counts.sh`)
   - Tools: **SAMtools** (`view`, `sort`, `index`, `idxstats`).
   - Converts SAM → sorted BAM → indexed BAM → per-peptide read counts.
7. **SAM to Stats** (`templates/sam_to_stats.sh`)
   - Tool: **SAMtools** `stats`.
   - Extracts alignment statistics (raw total sequences, reads mapped, etc.).
8. **Collect PhIP Data** (`merge-counts-stats.py`)
   - Merges all per-sample counts and stats into a single `phippery` xarray dataset (`.phip` format).
   - Computes: `percent_mapped`, `percent_peptides_detected`, `percent_peptides_between_10_and_100`.
9. **Replicate Counts** (`replicate-counts.py`, optional)
   - When `replicate_sequence_counts = true`, aggregates counts for peptides sharing the same oligo sequence.

## 2. Statistics Workflow (STATS)
**File:** `workflows/statistics.nf`
Applies normalization and statistical enrichment modeling using `phippery`.
### Steps:
1. **Counts Per Million (CPM)**
   - Normalizes raw counts to counts per million using `phippery.normalize.counts_per_million()`.
2. **Size Factors**
   - Estimates size factors for each sample using `phippery.normalize.size_factors()`.
3. **CPM Fold Enrichment** (optional, `run_cpm_enr_workflow = true`)
   - Computes fold enrichment of each sample's CPM relative to library controls using `phippery.normalize.enrichment()`.
4. **Z-Score Fit-Predict** (optional, `run_zscore_fit_predict = true`)
   - Tool: `fit-predict-zscore.py` using `phippery.modeling.zscore()`.
   - Fits a regression model on bead-only controls and predicts Z-scores for all samples.
   - Parameters: `min_Npeptides_per_bin=300`, quantile limits `0.05–0.95`.
7. **Merge Binary Datasets**
   - Merges all enrichment layer outputs into a single consolidated `.phip` dataset using `phippery merge`.

## 3. Output Workflow (DSOUT)
**File:** `workflows/output.nf`
Exports the merged dataset in multiple formats.
| Output | Condition | Format |
| :--- | :--- | :--- |
| Binary pickle | `output_pickle_xarray = true` | `.phip` |
| Wide CSV | `output_wide_csv = true` | Gzipped CSV per enrichment layer |
| Tall CSV | `output_tall_csv = true` | Gzipped tall-format CSV |

The wide CSV output produces files like `dataset_counts.csv.gz`, `dataset_cpm.csv.gz`, `dataset_zscore.csv.gz`, etc.

## 4. FHIR Report Workflow (FHIR)
**File:** `workflows/fhir_report.nf`

Generates HL7 FHIR R4 transaction bundles from PhIP-Seq Z-score data.
### Steps:
1. **CREATE_FHIR** (`bin/phipseq_to_fhir.py`)
   - Reads Z-score CSV and sample table.
   - For each sample, generates a FHIR Bundle containing:
     - **Patient** resource
     - **Specimen** resource (serum for PhIP-Seq)
     - **Organization** resource
     - **Practitioner** / **PractitionerRole** resources
     - **Observation** resources per peptide (Z-score values with positive/negative interpretation at threshold > 3.5)
   - Method coded as SNOMED CT `708049000` (Phage immunoprecipitation sequencing).

## 5. FDR Workflow (FDR)
**File:** `workflows/fdr.nf`
Performs false discovery rate analysis on Z-scores.
### Steps:
1. **Z-score FDR Analysis** (`zscore_fdr_analysis`)
   - Converts Z-scores to two-sided p-values using the normal distribution CDF.
   - Applies **Benjamini-Hochberg** FDR correction per sample.

## 6. Virus Score Workflow (VIRUSSCORE)
**File:** `workflows/virusscore.nf`
Calculates per-species virus exposure scores based on enriched peptide hits.
### Steps:
1. **Split Hits** — Extracts significant hit matrices per sample.
2. **Calculate Scores** (`bin/calc_scores_nofilter.py`)
   - Groups peptides by species.
   - Applies novel epitope filtering: a peptide is counted only if it does not share a subsequence of length ≥ `virus_score_epitope_len` (default: 7 amino acids) with any previously assigned peptide.
   - Output: Per-sample virus score CSV files.

## 7. IEDB Annotation Workflow (IEDB)
**File:** `workflows/iedb_annotation.nf`
Cross-references enriched peptides with the Immune Epitope Database.
### Steps:
1. **Extract Sample Names** — Parses column headers from Z-score file.
2. **IEDB Annotation** (per sample)
   - Loads IEDB TSV database and extracts valid epitope sequences.
   - For each peptide, performs substring matching against the IEDB epitope set.
   - Classifies peptides as:
     - **Known** — matches at least one IEDB epitope.
     - **Novel** — no IEDB match found.
     - **Significant** — Z-score ≥ threshold (default: 3.5).
   - Outputs per sample:
     - `{sample}_annotated_peptides.csv`
     - `{sample}_novel_peptides.csv`
     - `{sample}_significant_epitopes.csv`
     - `{sample}_annotation_summary.txt`

## 8. Visualization Workflow (VISUALIZE)
**File:** `workflows/visualization.nf`
Generates interactive Plotly heatmaps.
### Outputs:
1. **Virus Score Heatmap** (`virus_score_heatmap.html`)
   - Merges per-sample virus score files.
2. **Z-Score Heatmap** (`zscore_heatmap.html`)
   - Per-organism dropdown selector.
   - Heatmap of Z-scores: Peptide Oligos × Samples.
   - Hover shows peptide name, sample ID, and Z-score value.

## 9. Streamlit Dashboard Workflow (STREAMLIT)
**File:** `workflows/streamlit.nf`
Creates an interactive 3D protein visualization web application.
### Steps:
1. **Generate JSmol HTML** (`GENERATE_JSMOL_HTML`)
   - Parses Z-score data and peptide metadata with PDB IDs.
   - Copies local PDB/CIF structure files.
   - Generates per-structure HTML files with epitope annotations.
   - Exports `epitope_data.csv` for the Streamlit app.
2. **Create Streamlit App** (`CREATE_STREAMLIT_APP`)
   - Generates a complete Streamlit application with:
     - **Mol\*** 3D web component for interactive protein structure viewing.
     - Sequence-to-structure mapping.
     - Per-sample epitope selection with color-coded highlighting.

## Workflow Parameters
All parameters are defined in `nextflow.config`.