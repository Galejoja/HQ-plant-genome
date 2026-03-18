# HQ-plant-genome
This repository provides a reproducible R workflow to compute and visualize standardized quality metrics for plant genome assemblies as a radar plot.

## Description
As plant genomics continues to grow rapidly, clear guidelines and consistent approaches to evaluating genome quality are needed. This repository encourages researchers to report assembly contiguity and completeness, base-level accuracy, and the completeness and quality of genome annotations.

The following metrics are proposed for the evaluation of genome assemblies and annotations:

  - **CC (contig/chromosome ratio)**: Number of contigs divided by the number of chromosome pairs.

  - **QV (Quality Value)**: Phred-scale base accuracy of the assembly.

  - **BUSCO (assembly and annotation)**: Completeness scores computed using the most appropriate lineage dataset.

  - **PSAURON score**: Metric reflecting overall genome annotation quality.

### Recommended benchmarks

    CC ≤ 5

    QV ≥ 40

    BUSCO (assembly) ≥ 90%

    BUSCO (annotation) ≥ 90%

    PSAURON ≥ 90%

## Requirements

- R (≥ 4.0 recommended)
- The following R packages:
  - dplyr
  - fmsb
  - png
  - grid
 
## Installation

Install required R packages (if not already installed):

```r
install.packages(c("dplyr", "fmsb", "png", "grid"))
```

---

## Input file format

User must provide a two column table including quality metrics.

```markdown
## Input file format

The script expects a tab-delimited text file with two columns named `Metric` and `Value`.

The file should include the following entries:

- CC
- BUSCO_assembly
- BUSCO_annotation
- PSAURON
- QV
- Species

Numeric values are expected for all metrics except `Species`, which should be provided as text.

```

### Example:

| Metric | Value |
| --- | --- |
| CC | 33.4 |
| BUSCO_assembly | 99.0 |
| BUSCO_annotation | 98.8 |
| PSAURON | 95.8 |
| QV | 43.39 |
| Species | Arabidopsis thaliana |

### Assembly and annotation evaluation
#### **Contig / Chromosome ratio (CC)**

This metric consider contigs instead of scaffolds; it is necessary to identify Ns joined sequences 
```bash
#calculate the number of contigs using seqtk
seqtk cutN -n 1 assembly.fasta | grep -c '>'
```
Use the following formula to calculate **CC**

      CC = Number of contigs / Number of chromosome pairs

#### **BUSCO (assembly)**
Since higher-level datasets (e.g. eukaryote) can misestimate completeness, please use the most appropriate lineage dataset.
```bash
busco -i assembly.fasta -m genome -l lineage_path -o output_name
```

#### **BUSCO (annotation)**
The proteins or transcripts FASTA file can be used for annotation evaluation.
```bash
busco -i proteins.fasta -m prot -l lineage_dataset_path -o output_name
```
or
```bash
busco -i transcripts.fasta -m tran -l lineage_dataset_path -o output_name
```

#### **QV**
Count the ocurrence of canonical kmers in a high accuracy dataset (e.g., Illumina data) using Meryl
```bash
meryl k=31 count threads=<int> memory=<int>g data_path output my_data.meryl
```
k-mer based assembly evaluation using Merqury
```bash
merqury.sh my_data.meryl assembly.fasta output_name
```

## Usage

Run the script from the command line:

```bash
Rscript HQ_plant_genome.Rscript genome_metrics.txt
```

## Output

Tell the user exactly what they’ll get:

```markdown
## Output

The script generates:

- A radar plot summarizing genome quality metrics
```

## Citing this tool
If you use HQ-plant-genome for the visualization of genome quality metrics, please cite:

  - Alejo-Jacuinde, G, Albert, VA, Herrera-Estrella, L. **2026** Beyond Assembly: Standardizing Plant Genome Quality in the Age of Long Reads
