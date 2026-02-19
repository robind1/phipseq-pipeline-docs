# Output Files

The pipeline produces a set of outputs organized under the `results/` directory.

## Wide Data Outputs

The primary analytical outputs are gzipped CSV matrices where rows are peptides and columns are samples.

### Counts Matrix (`dataset_counts.csv.gz`)
Raw aligned read counts per peptide per sample.

### CPM Matrix (`dataset_cpm.csv.gz`)
Counts-per-million normalized values. Accounts for sequencing depth differences between samples.

### Z-Score Matrix (`dataset_zscore.csv.gz`)
Statistical enrichment Z-scores computed by fitting a regression model on bead-only negative control samples using `phippery.modeling.zscore()`. Higher Z-scores indicate stronger antibody binding signal above background.

## Virus Scores

Per-sample CSV files containing virus exposure scores aggregated at the species level. The scoring algorithm:
1. Groups peptides by species.
2. Sorts viruses by total hit count (descending).
3. For each virus, counts only novel peptides that do not share a 7-amino-acid subsequence with any previously assigned peptide.

### Merged Scores (`merged_scores.csv`)
Combined matrix: Species (rows) × Samples (columns).

## FHIR Output

### FHIR Transaction Bundle (`*.fhir.json`)
HL7 FHIR R4 compliant bundles containing:

| Resource | Description |
| :--- | :--- |
| **Patient** | Patient resource per sample |
| **Specimen** | Serum specimen for PhIP-Seq analysis |
| **Organization** | Testing facility |
| **Practitioner** | Lab personnel |
| **PractitionerRole** | Practitioner's role at the organization |
| **Observation** | Per-peptide Z-score observations with interpretation |

### Observation Interpretation
Each peptide observation includes:
* **Z-score value** as `valueQuantity`
* **Interpretation**:
  * `POS` (Positive) — Z-score > 3.5
  * `NEG` (Negative) — Z-score ≤ 3.5
* **Method**: SNOMED CT `708049000` (Phage immunoprecipitation sequencing)

## IEDB Annotation Outputs
Per-sample annotation results from cross-referencing with the Immune Epitope Database.

| File | Description |
| :--- | :--- |
| `*_annotated_peptides.csv` | All peptides with IEDB match counts, Z-scores, and novelty flags |
| `*_novel_peptides.csv` | Peptides with no IEDB epitope match |
| `*_significant_epitopes.csv` | Matched epitopes from peptides with Z-score ≥ threshold |
| `*_annotation_summary.txt` | Human-readable summary with match rates and statistics |


## Visualization Outputs

### Virus Score Heatmap (`virus_score_heatmap.html`)
Interactive heatmap showing virus exposure scores across all samples.

### Z-Score Heatmap (`zscore_heatmap.html`)
Interactive heatmap with a dropdown menu to select virus organisms. Displays Z-scores per peptide oligo per sample with hover details.

## Streamlit Dashboard

A Streamlit web application for interactive 3D protein visualization.

### Features:
* **Mol\* Viewer**: Renders PDB/CIF structures with epitope mapping.
* **Epitope Highlighting**: Red for all enriched epitopes; green for selected.
* **Sequence Mapping**: Sequence-to-structure alignment.
* **Controls**: PDB selection, sample selection, background color, auto-spin.

### Deployment:
```bash
cd results/streamlit_app/
chmod +x deploy_streamlit.sh
./deploy_streamlit.sh
```