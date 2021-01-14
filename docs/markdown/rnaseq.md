# RNAseq analysis

## RNAseq pipeline

Start by running the [nf-core/rnaseq](https://github.com/nf-core/rnaseq) pipeline.

### Quick start

* Latest stable release `-r 1.4.2`.

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

* Latest stable release `-r 1.1.0`.

Example command (change `cfc` for `binac` on binac cluster):

```bash
#!/usr/bin/bash
module purge
module load devel/singularity/3.4.2
nextflow run qbicsoftware/rnadeseq -r 1.1.0 -profile docker \
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

## Differential Exon Usage analysis

This guides through the execution of a DEU analysis, pointing out the necessary scripts and files.

### Prerequisites

* BAM files to be analysed
* A conda environment with HTSeq installed (conda install -c bioconda htseq)
* The script dexseq_prepare_annotation2.py from [https://github.com/vivekbhr/Subread_to_DEXSeq](https://github.com/vivekbhr/Subread_to_DEXSeq)
* Python
* The annotation .gtf file you have obtained the BAM files with
* The file /DESeq2/raw_counts/merged_gene_counts.txt copied in its location by the DESeq2 script

### Definitions

* DEU: Differential Exon Usage.

### Process

Run nf-core/rnaseq pipeline on rnaseq data **->** Transform your annotation .gtf **->** execute featureCounts on the BAM files in results/MarkDuplicates/ **->** Analyse this output in R

### Procedure

#### Step 1: Run nf-core/rnaseq pipeline on your data

1. Create an appropriate workspace on the cluster
2. Download the data
3. Check that you have annotation and fasta file at hand for the organism you’re working with
4. Start the workflow in a screen session

#### Step 2: Transform your annotation .gtf file

1. Create a conda environment, or use one that you already have, and activate it
2. Install HTSeq:
```bash
conda install -c bioconda htseq
3. Download the script dexseq_prepare_annotation2.py from [https://github.com/vivekbhr/Subread_to_DEXSeq](https://github.com/vivekbhr/Subread_to_DEXSeq)
4. Execute: python dexseq_prepare_annotation2.py -f out.gtf in.gtf out.gff (from your originary in.gtf you get and out.gtf (for featureCounts) and an out.gff.

#### Step 3: execute featureCounts

1. On cfc: module load qbic/subread/1.6.0
2. Run featureCounts on the BAM files produced by the pipeline: 
```bash
featureCounts -p -F GTF -a out.gtf -o counts.out Cont_1.bam Cont_2.bam
3. As output you have now a count table with a column for every input file; copy it locally on your machine
4. Open Rstudio and the script

#### Step 4: execute R analysis

1. Open Rstudio and the script demo_dexseq.R.
2. In the same folder as the script you need to place:
    * load_SubreadOutput.R
    * Counts.out (output from featureCounts)
    * out.gtf
3. NOTE: in the script, the number of workers is set to 1 to adapt the condition to my local machine (setting it to higher numbers was causing R not to execute the command successfully), but this parameter is typically increased on other machines.
