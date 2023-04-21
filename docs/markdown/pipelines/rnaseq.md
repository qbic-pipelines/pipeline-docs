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
nextflow run qbic-pipelines/rnadeseq -r 2.1 -profile docker \
--gene_counts 'merged_gene_counts.txt' \
--input 'QXXXX_sample_preparations.tsv' \
--model 'linear_model.txt' \
--contrast_matrix 'contrasts.tsv' \
--project_summary 'QXXXX_summary.tsv' \
--multiqc 'MultiQC.zip' \
--software_versions 'software_versions.csv' \
--genome 'GRCh37'
```

### Docker containers

After the release of rnadeseq 2.1, the docker containers were moved from Docker Hub to the GitHub Container Registry (ghcr) as Docker announced a change of free subscriptions. For all rnadeseq versions >2.1, this is reflected in the pipeline, so you don't need to do anything.

For version 2.1, should you have trouble executing the pipeline because the container was removed from Docker Hub (this could for example lead to an error like "docker: Error response from daemon: manifest for qbicpipelines/rnadeseq:2.1 not found: manifest unknown: manifest unknown."), please save the following code to a `container.config`:

```bash
process {
    withName: REPORT {
        container = 'ghcr.io/qbic-pipelines/rnadeseq:2.1'
    }
}
```

If you want to run a version <2.1, the container.config has to look like this:

```bash
process.container = 'ghcr.io/qbic-pipelines/rnadeseq:1.1.0'
```

The container link has to be adjusted to the version you want to run. You should just have to change the version number, but you can double-check on https://github.com/qbic-pipelines/rnadeseq/pkgs/container/rnadeseq if you are not sure if the link is correct.

Then, call this container.config when running the pipeline, like so:

```bash
qbic-pipelines/rnadeseq -r 2.1 -profile docker \
--many-many "params" \
-c path/to/container.config
```

### Known issues

- No known issues

## Reporting

The reporting is taken care of as part of the [qbic-pipelines/rnadeseq](https://github.com/qbic-pipelines/rnadeseq) pipeline. Check the previous section.
