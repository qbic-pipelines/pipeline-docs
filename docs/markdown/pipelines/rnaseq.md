# RNAseq analysis

## RNAseq pipeline

Start by running the [nf-core/rnaseq](https://github.com/nf-core/rnaseq) pipeline.

### Quick start

Example command (change `cfc` for `binac` on binac cluster):

```bash
#!/usr/bin/bash
nextflow run nf-core/rnaseq -r 1.4.2 -profile cfc \
--reads "Data/*{R1,R2}.fastq.gz" \
--genome 'GRCh37'
```

Normally, using all default values should be fine.

### Known issues

- There is an incompatibility problem between `GRCh38` and the default `GTF` file, so running with `GRCh37` is still preferred.

## Differential expression analysis

For differential expression analysis we use the [qbic-pipelines/rnadeseq](https://github.com/qbic-pipelines/rnadeseq) pipeline.

### Quick start

Example command (change `cfc` for `binac` on binac cluster):

```bash
#!/usr/bin/bash
nextflow run qbicsoftware/rnadeseq -r 2.1 -profile docker \
--gene_counts 'merged_gene_counts.txt' \
--input 'QXXXX_sample_preparations.tsv' \
--model 'linear_model.txt' \
--contrast_matrix 'contrasts.tsv' \
--project_summary 'QXXXX_summary.tsv' \
--multiqc 'MultiQC.zip' \
--software_versions 'software_versions.csv' \
--genome 'GRCh37'
```

### Known issues

- No known issues

## Reporting

The reporting is taken care of as part of the [qbic-pipelines/rnadeseq](https://github.com/qbic-pipelines/rnadeseq) pipeline. Check the previous section.
