# 16S amplicon sequencing

This is how to perform 16S amplicon analyses.

## Ampliseq pipeline

To perform 16S amplicon sequencing analyses we employ the [nf-core/ampliseq](https://github.com/nf-core/ampliseq) pipeline.

### Quick start

* Latest stable release `-r 1.1.0`

A typical command would look like this

```bash
nextflow run nf-core/ampliseq -profile binac -r 1.1.0 \
--reads “data” \
--FW_primer "GTGYCAGCMGCCGCGGTAA" \
--RV_primer "GGACTACNVGGGTWTCTAAT" \
--metadata "metadata.tsv" \
--trunc_qmin 35 \
--classifier_removeHash
```

See [here](https://github.com/nf-core/ampliseq/blob/master/docs/usage.md#--multiplesequencingruns) the info on how to create the `metadata.tsv` file.

If data are distributed on multiple sequencing runs, please use `--multipleSequencingRuns` and note the different requirements for metadata file and folder structure in the [pipeline documentation](https://github.com/nf-core/ampliseq/blob/master/docs/usage.md#--multiplesequencingruns)

### Known bugs

* `1.0.0` & `1.1.0` have a known bug that is why the `--classifier_removeHash` param should be used for these versions.

## Reporting

There are no details about reporting yet.
