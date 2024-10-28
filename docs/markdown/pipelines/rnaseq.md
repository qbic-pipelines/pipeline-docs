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
nextflow run qbic-pipelines/rnadeseq -r 2.4.1 -profile cfc \
--gene_counts 'merged_gene_counts.txt' \
--input 'QXXXX_sample_preparations.tsv' \
--model 'linear_model.txt' \
--contrast_matrix 'contrasts.tsv' \
--project_summary 'QXXXX_summary.tsv' \
--multiqc 'MultiQC.zip' \
--software_versions 'software_versions.csv' \
--outdir 'results'
```

### Docker containers

After the release of rnadeseq 2.1, the docker containers were moved from Docker Hub to the GitHub Container Registry (ghcr) as Docker announced a change of free subscriptions. For all rnadeseq versions >2.1, this is reflected in the pipeline, so you don't need to do anything.

For version 2.1 or later, should you have trouble executing the pipeline because the container was removed from Docker Hub (this could for example lead to an error like "docker: Error response from daemon: manifest for qbicpipelines/rnadeseq:2.1 not found: manifest unknown: manifest unknown."), please save the following code to a `container.config` (change 2.1 to the version you want to use):

```bash
process {
    withName: REPORT {
        container = 'ghcr.io/qbic-pipelines/rnadeseq:2.1'
    }
}
```

If you want to run a version <2.1, the `container.config` has to look like this:

```bash
process.container = 'ghcr.io/qbic-pipelines/rnadeseq:1.1.0'
```

The container link has to be adjusted to the version you want to run. You should just have to change the version number, but you can double-check on https://github.com/qbic-pipelines/rnadeseq/pkgs/container/rnadeseq if you are not sure if the link is correct.

Then, call this `container.config` when running the pipeline, like so:

```bash
qbic-pipelines/rnadeseq -r 2.4.1 -profile cfc \
--many-many "params" \
-c path/to/container.config
```

### Known issues

- No known issues

## Switch to nf-core/differentialabundance

In the future, we want to switch to the [nf-core/differentialabundance](https://github.com/nf-core/differentialabundance) pipeline. It was developed as a pipeline that is capable of a differential abundance analysis of different types of input data, including proteomics experiments if the upstream analysis was done with MaxQuant.

### Quick start

Example command on the CFC cluster:

```bash
#!/usr/bin/bash
nextflow run nf-core/differentialabundance -r 1.5.0 -profile cfc,rnaseq \
-params-file /sfs/9/ws/shared_files/qbic_differentialabundance_rnaseq.yml \
--matrix 'salmon.merged.gene_counts_length_scaled.tsv' \
--input 'sample_preparations.tsv' \
--contrast_matrix 'contrasts.tsv' \
--report_contributors 'Jane Doe\nDirector of Institute of Microbiology\nUniversity of Smallville;John Smith\nPhD student\nInstitute of Microbiology\nUniversity of Smallville'
```

The file `/sfs/9/ws/shared_files/qbic_differentialabundance_rnaseq.yml` contains several settings that will probably be necessary for any such analysis at QBiC and is saved in a folder on CFC which is accessible by every qbic-staff member. This includes a custom CSS and PNG file which set the style of the HTML report that is generated at the end of pipeline runs (i.e. this changes the color of highlighted text to QBiC blue and adds a combination of the QBiC rectangle and the pipeline logo to the very top of the report). If you want to run such an analysis on another machine, simply copy the relevant files from the `shared_files` folder over, then modify the paths in the YML file accordingly.

For more information about how to run the pipeline, have a look at the [usage docs](https://nf-co.re/differentialabundance/docs/usage/).

## Reporting

The reporting is taken care of as part of the [qbic-pipelines/rnadeseq](https://github.com/qbic-pipelines/rnadeseq) and [nf-core/differentialabundance](https://github.com/nf-core/differentialabundance) pipelines. Check the previous sections.
