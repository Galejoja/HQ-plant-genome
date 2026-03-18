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

Clone from this repository:
```bash
git clone https://github.com/Galejoja/HQ-plant-genome.git
```

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
- Sample

Numeric values are expected for all metrics except `Sample`, which should be provided as text.
Sample name will be used for the plot legend, and corresponds to species scientific name, assembly name, etc.
```

### Example:

| Metric | Value |
| --- | --- |
| CC | 33.4 |
| BUSCO_assembly | 99.0 |
| BUSCO_annotation | 98.8 |
| PSAURON | 95.8 |
| QV | 43.39 |
| Sample | Arabidopsis thaliana |


## Assembly and annotation evaluation

These commands provide example workflows; parameters may require adjustment depending on data type, software version or configuration, and computational resources.
For software supporting information please see [seqtk](https://github.com/lh3/seqtk), [BUSCO](https://busco.ezlab.org/), [PSAURON](https://github.com/salzberg-lab/PSAURON), and [Merqury](https://github.com/marbl/merqury).


### **Contig / Chromosome ratio (CC)**

This metric considers contigs rather than scaffolds. If scaffolds contain stretches of Ns, they should be split to estimate the true number of contigs.

```bash
#Count contigs by splitting sequences at Ns using seqtk
seqtk cutN -n 1 assembly.fasta | grep -c '>'
```
Use the following formula to calculate **CC**

      CC = Number of contigs / Number of chromosome pairs

### **BUSCO (assembly)**
To assess assembly completeness, use BUSCO with the most appropriate lineage dataset. Higher-level datasets (e.g., eukaryota) may misestimate completeness.
```bash
busco -i assembly.fasta -m genome -l lineage_path -o output_name
```
Please use the ‘Complete BUSCO (%)’ value.

### **BUSCO (annotation)**
Annotation completeness can be evaluated using the prediceted proteins or transcripts.
```bash
busco -i proteins.fasta -m prot -l lineage_path -o output_name
```
or
```bash
busco -i transcripts.fasta -m tran -l lineage_path -o output_name
```
Please use the ‘Complete BUSCO (%)’ value.

### **PSAURON score**
Annotation quality can be evaluated using PSAURON based on protein sequences.
```bash
psauron -i proteins.fasta -o output_name
```
The overall PSAURON score is reported in the output file (top).


### **QV**
Quality value is estimated using k-mer-based methods with high-accuracy sequencing data (e.g., Illumina reads).

First, count the ocurrence of canonical kmers using Meryl
```bash
meryl k=31 count threads=<int> memory=<int>g data_path output my_data.meryl
```
Then, evaluate the assembly using Merqury
```bash
merqury.sh my_data.meryl assembly.fasta output_name
```
The QV value can be obtained from the fourth column of the `.qv` file. 


# Usage

Run the script from the command line:

```bash
Rscript HQ_plant_genome.Rscript input_file output_name
```
See [genome_metrics.txt](https://github.com/Galejoja/HQ-plant-genome/blob/main/docs/genome_metrics.txt) for an example of input file.

## Output

The R script will generate a radar plot as PDF file.

```markdown
## Output

The script generates:

- A radar plot summarizing genome quality metrics
```

Example of **Arabidopsis thaliana* TAIR10:
<p align="center">
<img src="https://github.com/Galejoja/HQ-plant-genome/blob/main/docs/TAIR10.pdf" title="TAIR10_example" />
</p>

## Citing this tool
If you use HQ-plant-genome for the visualization of genome quality metrics, please cite:

  - Alejo-Jacuinde, G, Albert, VA, Herrera-Estrella, L. **2026** Beyond Assembly: Standardizing Plant Genome Quality in the Age of Long Reads. (Submitted)
