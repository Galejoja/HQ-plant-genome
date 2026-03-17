# HQ-plant-genome
This repository provides a reproducible R workflow to compute and visualize standardized quality metrics for plant genome assemblies as a radar plot.

# Description
As plant genomics continues to grow rapidly, clear guidelines and consistent approaches to evaluating genome quality are needed. This repository encourages researchers to report assembly completeness and contiguity, base-level accuracy, and the completeness and quality of genome annotations.

The following metrics are proposed for the evaluation of genome assemblies and annotations:

  - **CC (contig/chromosome ratio)**: Number of contigs divided by the number of chromosome pairs.

  - **QV (Quality Value)**: Phred-scale base accuracy of the assembly.

  - **BUSCO (assembly and annotation)**: Completeness scores computed using the most appropriate lineage dataset.

  - **PSAURON score**: Metric reflecting overall genome annotation quality.

Recommended benchmarks

    CC ≤ 5

    QV ≥ 40

    BUSCO ≥ 90%

    PSAURON ≥ 90%
