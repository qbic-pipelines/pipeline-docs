# RNAseq analysis

## RNAseq pipeline

Start by running the [nf-core/rnaseq](https://github.com/nf-core/rnaseq) pipeline.

### Quick start

Example command (change `cfc` for `binac` on binac cluster):

```bash
#!/usr/bin/bash
module purge
module load devel/singularity/3.4.2
nextflow run nf-core/rnaseq -r 1.4.2 -profile cfc \
--reads "Data/*{R1,R2}.fastq.gz" \
--genome 'GRCh37'
```

Normally, using all default values should be fine.

### Known issues

* There is an incompatibility problem between `GRCh38` and the default `GTF` file, so running with `GRCh37` is still preferred.

## Differential expression analysis

For differential expression analysis we use the [qbic-pipelines/rnadeseq](https://github.com/qbic-pipelines/rnadeseq) pipeline.

### Quick start

Example command (change `cfc` for `binac` on binac cluster):

```bash
#!/usr/bin/bash
module purge
module load devel/singularity/3.4.2
nextflow run qbicsoftware/rnadeseq -r 1.3.2 -profile docker \
--rawcounts 'merged_gene_counts.txt' \
--metadata 'QXXXX_sample_preparations.tsv' \
--model 'linear_model.txt' \
--contrast_matrix 'contrasts.tsv' \
--project_summary 'QXXXX_summary.tsv' \
--multiqc 'MultiQC.zip' \
--quote 'QXXXX_signed_offer.pdf' \
--versions 'software_versions.csv' \
--report_options 'report_options.yml' \
--species Hsapiens
```

### Known issues

* No known issues

## Reporting

The reporting is taken care of as part of the [qbic-pipelines/rnadeseq](https://github.com/qbic-pipelines/rnadeseq) pipeline. Check the previous section.
