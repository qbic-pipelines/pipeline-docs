# WES & WGS analysis

To perform DNA seq and Variant calling analyses for short-read normal, tumor-only, and paired samples, we use the [nf-core/sarek](https://github.com/nf-core/sarek) pipeline.
Initially designed for Human, and Mouse, it can work on any species with a reference genome. Sarek can also handle tumour / normal pairs and could include additional relapses.

For an in-detail overview, please visit [https://nf-co.re/sarek](https://nf-co.re/sarek).

The pipeline can be utilized for Whole genome sequencing (WGS) and Whole Exome/Panel sequencing (WES).
There are some differences in running the pipeline for the two modalities, so the next sections provide examples and tips for the different types of analyses.

> **_Note_** The pipeline was recently rewritten to DSL2, which brought a significant amount of changes. This document details usage for nf-core/sarek >3.0. If you need to rerun older versions, please take a look [here](https://github.com/qbic-pipelines/pipeline-docs/blob/7f79a88458e05686216179ee5c791bb7a1f70f9b/docs/markdown/pipelines/exomeseq.md) and visit the official [nf-core documentation](https://nf-co.re/sarek/2.7.2). For older versions, select the respective one in the drop-down menu on the right hand side.

> :warning: In older versions parameters are not validated! It is easy to get command line flags wrong, please double check that the respective parameter exists in the version you are using!

## Input csv file

### Starting from Fastq

In order to use sarek, you will need to create your own input file. You can find more information on how to create your input file in the [sarek input documentation](https://nf-co.re/sarek/3.1.2/usage#input-sample-sheet-configurations).

We usually start our analyses at QBiC from paired-end fastq files. For these files, so you can take this `input.csv` as example:

```csv
patient,sex,status,sample,lane,fastq_1,fastq_2
patient1,XX,0,normal_sample,lane_1,/path/to/Data/test_L001_1.fastq.gz,/path/to/Data/test_L001_2.fastq.gz
patient1,XX,0,normal_sample,lane_2,/path/to/Data/test_L002_1.fastq.gz,/path/to/Data/test_L002_2.fastq.gz
patient1,XX,0,normal_sample,lane_3,/path/to/Data/test_L003_1.fastq.gz,/path/to/Data/test_L003_2.fastq.gz
patient1,XX,1,tumor_sample,lane_1,/path/to/Data/test2_L001_1.fastq.gz,/path/to/Data/test2_L001_2.fastq.gz
patient1,XX,1,tumor_sample,lane_2,/path/to/Data/test2_L002_1.fastq.gz,/path/to/Data/test2_L002_2.fastq.gz
patient1,XX,1,relapse_sample,lane_1,/path/to/Data/test3_L001_1.fastq.gz,/path/to/Data/test3_L001_2.fastq.gz
```

Columns:

- _patient_: the first column contains the subject ID. Samples belonging to the same patient / animal, should have the same subject ID.
- _sex_ (optional, default: NA): the subject sex. XX for women, XY for men. This column is required for copy-number calling, for all other analysis types it is _optional_ and has no effect.
- _status_ (optional, default: 0): 0 for normal samples, 1 for tumor samples. This is important for somatic variant calling.
- _sample_: sample ID must be unique across all samples. However, samples that are spread over several lanes must have the same ID to be merged correctly. Depending on the type of experiment, the `Q_TEST_SAMPLE` is a good candidate to be used here.
- _lane_: if a sample is spread over multiple lanes, just enter the files individually in different rows, with the same sample ID but different lane IDs. In this example, the normal sample is spread over 3 lanes, and the first tumor sample over 2 lanes. The relapse sample is not spread over several lanes.
- _Fastq1_: First Fastq pair (R1).
- _Fastq2_: Second Fastq pair (R2).

### Starting from BAM/CRAM

The pipeline now natively contains qbic-pipelines/bamtofastq. This means that for analyses that include re-aligning, the pipeline can be started directly from BAM files.

Furthermore, the pipeline supports starting fom different steps to allow easy re-analysis or complementary analysis. For details on the input sample sheet and required parameters, start checking [here](https://nf-co.re/sarek/3.1.2/usage#start-with-duplicate-marking---step-markduplicates).

## Variant calling tools

You can check which tools are available for germline variant calling and somatic variant calling. Not all tools are available for both steps. See overview [here](https://nf-co.re/sarek/3.1.2/usage#which-variant-calling-tool-is-implemented-for-which-data-type)

Please familiarize yourself with the [sarek docs](https://nf-co.re/sarek).

## Whole Genome Sequencing

Running WGS analysis with Sarek does not require any additional files.

### Quick start

- Latest stable release `-r 3.1.2`.

Please use these parameters to run your WGS analysis with sarek:

```bash
#!/bin/bash
nextflow run nf-core/sarek -r 3.1.2 \
-profile cfc \
--genome 'GRCh38' \
--input 'input.csv' \
--aligner 'bwa-mem2' \
--tools 'haplotypecaller,strelka,mutect2,manta,ascat,controlfreec,cnvkit,vep,snpeff' \
```

> **_Note_**: We parallelize across genomic regions. This speeds up computation at the expense of storage. Since often storage is a bottleneck, it is useful to reduce the amount of parallelization. For this, you can increase the parameter `--nucleotides_per_second`. Recommended setting: `100000`.

### Somatic Variant Calling

#### Paired

If your analysis requires _only_ somatic variant calls and no germline variant calling, set `--only_paired_variant_calling` to reduce the amount of computation done.

#### Tumor-only

If you run tumor-only samples without matched normals, it is _highly_ recommended to generate a panel-of-normals for Mutect2 and CNVKit. For details, see here for [mutect2](https://nf-co.re/sarek/3.1.2/usage#how-to-create-a-panel-of-normals-for-mutect2) and [cnvkit](https://cnvkit.readthedocs.io/en/stable/tumor.html) for details.

#### Visualization

Depending on your project, `vep`-annotated vcf makes creation of oncoplots with [maftools](https://bioconductor.org/packages/release/bioc/vignettes/maftools/inst/doc/maftools.html) easier.

## Whole Exome and Targeted Sequencing

Running WES analysis and targeted sequencing with Sarek requires the BED file that specifies the Exomic regions or the Targeted regions that were used in the exome capture kit or Targeted design.

### Quick start

- Latest stable release `-r 3.1.2`.

Please use these parameters to run your WES analysis with sarek:

```bash
#!/bin/bash
nextflow run nf-core/sarek -r 3.1.2 \
-profile cfc \
--genome 'GRCh38' \
--input 'input.tsv' \
--aligner 'bwa-mem2' \
--tools 'haplotypecaller,strelka,mutect2,manta,ascat,controlfreec,cnvkit,vep,snpeff' \
--intervals `/path/to/targetBED` \
--wes
```

### Important remarks for WES analysis

#### Target bed files

The target BED files are provided by the Exome capture kit companies. For common Human genomes and capture kits, the BED files are available in the `cfc` and `Binac` clusters. This target bed file is crucial for the analysis and needs to be requested with the sequencing facility. If your BED file is not there, contact your team leader.

- `GRCh37` & `Twist capture kit`

  ```txt
  /nfsmounts/igenomes/Homo_sapiens/Ensembl/GRCh37/Exome_BED/Twist_Exome_Target_GRCh37_noUn_gl000228_2020_05_05.bed
  ```

- `hg38` & `Agilent Technologies capture kit`

  ```txt
  /nfsmounts/igenomes/Homo_sapiens/UCSC/hg38/Exome_BED/S31285117_Regions_Agilent_Technologies_exome_hc38.bed
  ```

- `hg19` & `Twist capture kit`

  ```txt
  /nfsmounts/igenomes/Homo_sapiens/UCSC/hg19/Exome_BED/Twist_Exome_Target_hg19_noUn_gl000228_2020_05_05.bed
  ```

- Mouse `mm10` & `Agilent SureSelect All Exon`

  ```txt
  /nfsmounts/igenomes/Mus_musculus/UCSC/mm10/Exome_BED/mm10_Agilent_SureSelect_Mouse_All_Exon_Kit_2016_01_21_sorted.bed
  ```

- Mouse `GRCm38` & `Agilent SureSelect Mouse`

  ```txt
  /nfsmounts/igenomes/Mus_musculus/Ensembl/GRCm38/Exome_BED/GRCm38_Agilent_SureSelect_Mouse_All_Exon_Kit_2016_01_21_sorted.bed
  ```

In general for WES data analysis people advise to use target regions that are padded (e.g. +- 150 bp). But to be precise there are multiple aspects:

- The mapping should be done on the whole reference to get information about multi reads mapping also outside of the exome (to discard). This is done by default by the pipeline, even when the target bed file is provided.
- Then reads mapping within the target exome regions should be kept, for this a padding should be added to prevent loosing reads at the boarders. This is done by default by the pipeline for base quality score recalibration and variant calling.
- for QC to get an unbiased view about the overall quality of the experiment its best to use the exact target regions without padding. The pipeline can currently not be run with various bed files. If this is necessary, it might be useful to rerun certain steps with unpadded target files.
- and for the final variants it depends, if one want to be sure not to increase the number of FPs it would also make sense to use unpadded target regions. If the goal would be to get as many variants as possible, then of course one could use padded regions (which made quite a difference in the final numbers in my case)
- Sarek actually maps reads against the whole reference, and then uses the target bed file for the QC, BQSR, and to filter variants. So _simply hand over the unpadded target region_. This is usually provided by the Exon capture kit company.
- BED file compatibility: if you have to use your own BED file, make sure the chromosome names are compatible between the reference and the targetBED file (e.g. UCSC “chr1” <-> Ensemble “1”). For human, if they differ and your analysis is done only on main chromosomes, then you could simply convert the names by adding “chr” to the chromosome names.

## Known issues

- There is currently a bug regarding the reference names: if you specify `--genome GRCh38`, sarek actually uses `hg38` (UCSC naming convention, and not Ensembl). See the [issue](https://github.com/nf-core/sarek/issues/86).

- Our cluster has problems with MarkDuplicates Spark, which is why is should be avoided. As of `2.7` the non-spark variant is run by default. Please be aware to set `--no_gatk_spark` if you run `2.6.1` or earlier.

## Running the pipeline with a non iGenomes genome

> **_Note_**: This part is currently under heavy development in the pipeline. For most recent docs, please see [here](https://nf-co.re/sarek/3.1.2/usage#how-to-customise-snpeff-and-vep-annotation).

If you wish to run Sarek with a genome not present in iGenomes, the command line will look similar to the following :

```bash
nextflow run nf-core/sarek -r 3.1.2  \
-resume \
-profile cfc \
--fasta '/sfs/7/workspace/ws/qealk01-QVBAP-0/reference/genome.fa' \
--input 'samples.csv' \
--save_reference \
--aligner 'bwa-mem2' \
--bwa-mem2 false \
--igenomes_ignore \
--genome null \
--tools 'haplotypecaller,strelka,mutect2,manta,ascat,controlfreec,cnvkit,vep,snpeff' \
--snpeff_cache '/sfs/7/workspace/ws/qealk01-QVBAP-0/reference/cache/' \
--annotation_cache true \
--snpeff_db Solanum_lycopersicum
```

We here take _Solanum lycopersicum_ (tomato) as an example.
The following parameters have to be added :

- `--fasta path/to/genome.fa`. The assembly file should come from standardized reference genomes (i.e NCBI, ENSEMBL).
- `--igenomes_ignore`,
- `--genome custom`,
- `--snpeff_cache`, with the path to the SnpEff cache file (see below for more explanations),
- `--annotation_cache true`,
- `--snpeff_db` with the name of the genome you downloaded through SnpEff (see below).

### SnpEff cache

To download the SnpEff cache on the cfc, you can install the snpEff version, that is used in the sarek version you are running, with conda. Currently, for `sarek 3.1.2`, snpEff version `snpeff=5.1` is used. If you run an older sarek release, you can check the `environment.yml` (DSL1) or the respective module file on GitHub for the used snpeff version.
It is important to use the snpEff version that the Sarek release you want to use is using:

```bash
conda create -n snpeff snpeff=5.1
#or when channels are not set-up to contain e.g. bioconda
conda create -n snpeff -c bioconda snpeff=5.1
#activate the environment
conda activate snpeff
```

Then search for your species / bacterial strain in the available databases:

```bash
snpEff databases | less
#or
snpEff databases | grep "Solanum"
```

And download the required cache with `-v name_of_your_genome`. This will create the cache in your directory, which you will then specify in the sarek pipeline.

```bash
snpEff download -v Solanum_lycopersicum -dataDir /absolute/path/to/datadir
```

In case your species or bacterial strain is not listed here, please refer to the snpEff [documentation](http://pcingola.github.io/SnpEff/se_buildingdb/) on how to build the cache.

In a similar fashion, VEP can be used with custom genomes.

## Reporting

Reporting hasn't been established yet for WGS & WES. For individual scripts, you can ask around in the qbic #sarek channel. We are currently working on a reporting pipeline to streamline this step.
