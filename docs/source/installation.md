# Installation

## Prerequisites

* [Nextflow](https://www.nextflow.io/)
* [Docker](https://www.docker.com/) or [Singularity](https://sylabs.io/singularity/) / [Apptainer](https://apptainer.org/)
* [Python 3](https://www.python.org/) with the [phippery](https://matsen.group/phippery/introduction.html) library
* [Bowtie2](http://bowtie-bio.sourceforge.net/bowtie2/)
* [SAMtools](https://github.com/samtools/samtools)

### Python Dependencies
* `phippery` — Core PhIP-Seq data structures and normalization ([docs](https://matsen.group/phippery/introduction.html))
* `pandas`, `numpy`, `scipy` — Data manipulation and statistics
* `plotly` — Interactive heatmap visualizations
* `streamlit` — Interactive 3D dashboard application

## Setup

1. **Install Nextflow:**
    ```bash
    curl -s https://get.nextflow.io | bash
    ```

2. **Clone the repository:**
    ```bash
    git clone https://github.com/oucru-id/phip-seq-ont.git
    cd phip-seq-ont
    ```

3. **Verify installation:**
    ```bash
    nextflow -v
    ```