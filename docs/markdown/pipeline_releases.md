# Pipeline releases

Below is a list of the pipeline releases that should be used at QBiC for the analysis.
Unless a release has a bug that makes it impossible to work with your data, please use this fixed releases.
These versions will be revised periodically and tested in our infrastructure before update.

## Pipeline releases to be used at QBiC

| Analysis type         | Pipeline                                                              | Use version |
|-----------------------|-----------------------------------------------------------------------|:-----------:|
| RNAseq                | [nf-core/rnaseq](https://nf-co.re/rnaseq/1.4.2)                               |    1.4.2    |
| RNAseq DE analysis    | [qbic-pipelines/rnadeseq](https://github.com/qbic-pipelines/rnadeseq) |    1.3.2    |
| WGS / WES             | [nf-core/sarek](https://nf-co.re/sarek/2.7)                                 |    2.7    |
| HLAtyping             | [nf-core/hlatyping](https://nf-co.re/hlatyping/1.2.0)                         |    1.2.0    |
| 16S amplicon          | [nf-core/ampliseq](https://nf-co.re/ampliseq/1.1.3)                           |    1.1.3    |
| Shotgun metagenomics  | [nf-core/mag](https://nf-co.re/mag/1.1.2)                                     |    1.1.2    |
| BCR / TCR repseq      | [nf-core/bcellmagic](https://nf-co.re/bcellmagic/1.2.0)                       |    1.2.0    |
| CAGEseq               | [nf-core/cageseq](https://nf-co.re/cageseq/1.0.2)                             |    1.0.2    |
| MHC immunopeptidomics | [nf-core/mhcquant](https://nf-co.re/mhcquant/1.6.0)                           |    1.6.0    |
| ATACseq               | [nf-core/atacseq](https://nf-co.re/atacseq/1.2.1)                             |    1.2.1    |
| Chipseq               | [nf-core/chipseq](https://nf-co.re/chipseq/1.2.1)                             |    1.2.1    |
| Epitope prediction    | [nf-core/epitopeprediction](https://nf-co.re/epitopeprediction/1.1.0)         |    1.1.0    |
| Bacterial assembly    | [nf-core/bacass](https://nf-co.re/bacass/1.1.1)                               |    1.1.1    |
| RNA fusion            | [nf-core/rnafusion](https://nf-co.re/rnafusion/1.2.0)                         |    1.2.0    |

## Routine params to trigger most common pipelines

For our Slurm cluster, we use the profile `cfc` that is stored under [nf-core/configs](https://github.com/nf-core/configs/blob/master/conf/cfc.config).

If you want to test the pipelines in your system, you can use `-profile test,docker` or `-profile test,singularity` if you have docker or singularity installed. Note that the params files will need to be edited to your input data.

### Sarek variant calling

To run pipeline tests use the following command:

```bash
nextflow run nf-core/sarek -r 2.7 -profile test,cfc
```

To launch sarek for germline WGS using the [example params](https://github.com/qbic-pipelines/pipeline-docs/docs/params/sarek_WGS_germline.json) use the following command:

```bash
nextflow run nf-core/sarek -r 2.7 -profile cfc -params-file sarek_WGS_germline.json
```

To launch sarek for somatic WES using the [example params](https://github.com/qbic-pipelines/pipeline-docs/docs/params/sarek_WXS_somatic.json) use the following command:

```bash
nextflow run nf-core/sarek -r 2.7 -profile cfc -params-file sarek_WXS_somatic.json
```

### RNAseq analysis

To run pipeline tests we use the following command:

```bash
nextflow run nf-core/rnaseq -r 1.4.2 -profile test,cfc
```

To launch rnaseq for RNAseq using our [example params](https://github.com/qbic-pipelines/pipeline-docs/docs/params/rnaseq.json) use the following command:

```bash
nextflow run nf-core/rnaseq -r 1.4.2 -profile cfc -params-file rnaseq.json
```
