# BCRseq / TCRseq analysis

To perform bulk amplicon BCRseq / TCRseq data analysis you can use the [nf-core/airrflow](https://nf-co.re/airrflow) pipeline.
To perform single-cell BCRseq / TCRseq data analysis with data generated with the 10x genomics chromium (e.g. immune profiling libraries orr similar), you will have to run the [nf-core/scrnaseq](https://nf-co.re/scrnaseq) pipeline first running the `cellranger-multi` subworkflow. Afterwards, you can use the [nf-core/airrflow](nf-co.re/airrflow) pipeline departing from the AIRR format tables provided as output by `cellranger multi` to perform downstream analysis.

## nf-core/airrflow pipeline

The latest stable release is `-r 3.0.0`.

A typical command to run the pipeline is as follows:

```
nextflow run nf-core/airrflow -r 3.0.0 \
-profile cfc \
--input samplesheet.tsv \
--library_generation_method specific_pcr_umi \
--cprimers CPrimers.fasta \
--vprimers VPrimers.fasta \
--umi_length 12 \
--outdir ./results
```

Please familiarize yourself with the [nf-core/airrflow](https://nf-co.re/airrflow) documentation on the nf-core website before running the pipeline. It also contains documentation on how to create the [samplesheets](https://nf-co.re/airrflow/2.4.0/usage#fastq-input-samplesheet).
