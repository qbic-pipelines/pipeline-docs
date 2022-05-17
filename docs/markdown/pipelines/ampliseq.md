# 16S amplicon sequencing

This is how to perform 16S amplicon analyses.

## Ampliseq pipeline

To perform 16S amplicon sequencing analyses we employ the [nf-core/ampliseq](https://github.com/nf-core/ampliseq) pipeline.

### Quick start

- Latest stable release `-r 2.1.1`

A typical command would look like this

```bash
nextflow run nf-core/ampliseq -profile cfc -r 2.1.1 \
--input “data” \
--FW_primer "GTGYCAGCMGCCGCGGTAA" \
--RV_primer "GGACTACNVGGGTWTCTAAT" \
--metadata "metadata.tsv" \
--trunc_qmin 35 \
--classifier_removeHash
```

See [here](https://nf-co.re/ampliseq/1.2.0/parameters#manifest) the info on how to create the `metadata.tsv` file.

If data are distributed on multiple sequencing runs, please use `--multipleSequencingRuns` and note the different requirements for metadata file and folder structure in the [pipeline documentation](https://nf-co.re/ampliseq/1.2.0/parameters#multiplesequencingruns)

### Known bugs

- All versions include a known bug that is why the `--classifier_removeHash` param should be used.

## Reporting

There are no details about reporting yet.
