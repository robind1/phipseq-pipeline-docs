# Input Requirements

The pipeline requires two primary input tables and raw sequencing data in FASTQ format.

## Sample Table
* **Format**: CSV file with a header row.
* **Required Columns**:
    * `fastq_filepath` — Path to the FASTQ file for each sample.
    * `sample_ID` — Human-readable sample identifier.
    * `control_status` — Classification of each sample. Required values:
        * `beads_only` — Negative control (bead-only immunoprecipitation). **At least 2 required** when `run_zscore_fit_predict = true`.
        * `library` — Library control (optional).
        * `empirical` — Experimental samples.
* **Note**: Do **not** include a column named `sample_id` (lowercase). The pipeline auto-generates integer `sample_id` indices.

### Example Sample Table

| sample_ID | fastq_filepath | control_status |
| :--- | :--- | :--- |
| Patient_1_B | reads/patient1_baseline.fastq.gz | empirical |
| Patient_1_E | reads/patient1_endline.fastq.gz | empirical |
| neg1 | reads/neg_control_1.fastq.gz | beads_only |
| neg2 | reads/neg_control_2.fastq.gz | beads_only |

## Peptide Table
* **Format**: CSV file with a header row.
* **Required Columns**:
    * `oligo` — The oligonucleotide sequence for each peptide tile. Uppercase characters are used for alignment; lowercase characters are trimmed.
* **Optional Columns**:
    * `peptide_id` — Integer index (auto-generated if not provided).
    * `Organism` — Species or virus name associated with the peptide (used for virus scoring and heatmaps).
    * `Species` — Species-level grouping.
    * `peptide` — Amino acid sequence of the peptide tile.
    * `pdb_id` — PDB structure identifier (used for 3D visualization).
    * `id` — Unique identifier for merging with oligo metadata.

### Example Peptide Table

| oligo | Organism | Species | peptide | pdb_id |
| :--- | :--- | :--- | :--- | :--- |
| ATGCGA... | Human coronavirus OC43 | Betacoronavirus 1 | MSDNGP... | 6OHW |
| GCTAGC... | Influenza A virus | Influenza A virus | DTLCIA... | 4LXV |


## Sequencing Data
* **Format**: FASTQ files (`.fastq.gz` or `.fastq`).
* **Platform**: Oxford Nanopore Technologies (ONT).
* **Compression**: Set `params.fastq_stream_func = 'zcat'` for gzipped files, or `'cat'` for uncompressed.
* **Location**: By default, paths in the sample table are resolved relative to `$baseDir`. If you provide a custom sample table, paths are resolved relative to `$launchDir`. Absolute paths are also supported.

## Optional Input Files

### IEDB Database
* **Path**: Configured via `params.iedb_database`.
* **Format**: TSV file from the [Immune Epitope Database](https://www.iedb.org/).
* **Usage**: Cross-referenced to annotate enriched peptides with known epitope information.

### PDB Structures
* **Path**: Configured via `params.pdb_dir`.
* **Format**: Directory containing `.pdb` or `.cif` structure files named by PDB ID.
* **Usage**: Used by the Streamlit 3D visualization dashboard to render protein structures with mapped epitopes.