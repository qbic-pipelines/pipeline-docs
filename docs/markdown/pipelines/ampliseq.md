# 16S amplicon sequencing

This is how to perform 16S amplicon analyses. A video explanation of the biology, the bioinformatics problem and the analysis pipeline can be found for version 2.1.0 in the [nf-core bytesize talk 25](https://nf-co.re/events/2021/bytesize-25-nf-core-ampliseq).

## Ampliseq pipeline

To perform 16S amplicon sequencing analyses we employ the [nf-core/ampliseq](https://github.com/nf-core/ampliseq) pipeline.

### Quick start

- Latest stable release `-r 2.4.1`

A typical command would look like this

```bash
nextflow run nf-core/ampliseq -profile cfc -r 2.4.1 \
--input “data” \
--FW_primer "GTGYCAGCMGCCGCGGTAA" \
--RV_primer "GGACTACNVGGGTWTCTAAT" \
--metadata "metadata.tsv" \
--trunc_qmin 35
```

Sequencing data can be analysed with the pipeline using a folder containing `.fastq.gz` files with [direct fastq input](https://nf-co.re/ampliseq/2.4.1/usage#direct-fastq-input) or [samplesheet input](https://nf-co.re/ampliseq/2.4.1/usage#samplesheet-input), also see [here](https://nf-co.re/ampliseq/2.4.1/parameters#input).

See [here](https://nf-co.re/ampliseq/2.4.1/parameters#metadata) the info on how to create the `metadata.tsv` file.

If data are distributed on multiple sequencing runs, please use `--multipleSequencingRuns` and note the different requirements for metadata file and folder structure in the [pipeline documentation](https://nf-co.re/ampliseq/2.4.1/parameters#multiple_sequencing_runs)

## Reporting

There are no details about reporting yet. Please refer to the [output documentation](https://nf-co.re/ampliseq/2.4.1/output).
