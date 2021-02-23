# WES & WGS analysis

To perform Exome seq and Variant calling analyses, we use the [nf-core/sarek](https://github.com/nf-core/sarek) pipeline.
The pipeline can be utilized for Whole genome sequencing (WGS) and Whole exome sequencing (WES) analyses.
There are some differences in running the pipeline for the two modalities, so the next sections provide examples and tips for the different types of analyses.

## Input tsv file

In order to use sarek, you will need to create your own input file. You can find more information on how to create your input file in the [sarek input documentation](https://nf-co.re/sarek/2.7/usage#tsv-file).

We usually start our analyses at QBiC from paired-end Fastq files. For these files, so you can take this `input.tsv` as example:

```txt
Patient1    XY  0   Sample1 Lane1   /path/to/Data/Sample1_L001_R1.fastq.gz  /path/to/Data/Sample1_L001_R2.fastq.gz
Patient1    XY  0   Sample1 Lane2   /path/to/Data/Sample1_L002_R1.fastq.gz  /path/to/Data/Sample1_L002_R2.fastq.gz
Patient1    XY  1   Sample2 Lane1   /path/to/Data/Sample2_L001_R1.fastq.gz  /path/to/Data/Sample2_L001_R2.fastq.gz
Patient1    XY  1   Sample2 Lane2   /path/to/Data/Sample2_L002_R1.fastq.gz  /path/to/Data/Sample2_L002_R2.fastq.gz
Patient1    XY  1   Sample2 Lane3   /path/to/Data/Sample2_L003_R1.fastq.gz  /path/to/Data/Sample2_L003_R2.fastq.gz
Patient2    XY  0   Sample3 Lane1   /path/to/Data/Sample3_L001_R1.fastq.gz  /path/to/Data/Sample3_L001_R2.fastq.gz
Patient2    XY  1   Sample4 Lane1   /path/to/Data/Sample4_L001_R1.fastq.gz  /path/to/Data/Sample4_L001_R2.fastq.gz
```

Columns:

* *Subject*: the first column contains the subject ID. Samples belonging to the same patient / animal, should have the same subject ID.
* *Sex*: the subject sex. XX for women, XY for men.
* Status: 0 for normal samples, 1 for tumor samples. This is important for somatic variant calling.
* *Sample ID*: sample ID should be unique across all samples. However, samples that are spread over several lanes should have the same ID.
* *Lane ID*: if a sample is spread over multiple lanes, just enter the files individually in different rows, with the same sample ID but different lane IDs. In this example, Sample 1 is spread over 2 lanes, and Sample 2 over 3 lanes. Sample 3 and 4 are not spread over several lanes.
* *Fastq1*: First Fastq pair (R1).
* *Fastq2*: Second Fastq pair (R2).

## Variant calling tools

You can check which tools are available for Germline variant calling and Somatic variant calling. Not all tools are available for both steps.

Please familiarize yourself with the [sarek docs](https://nf-co.re/sarek).

## Whole Genome Sequencing

Running WGS analysis with Sarek does not require any additional files.

### Quick start

* Latest stable release `-r 2.7`.

Please use these parameters to run your WGS analysis with sarek:

```bash
#!/bin/bash
module purge
module load devel/singularity/3.4.2
nextflow run nf-core/sarek -r 2.7 \
-profile cfc \
--genome 'GRCh38' \
--input 'input.tsv' \
--tools 'HaplotypeCaller,Strelka,mutect2,manta,ascat,controlFreec,VEP,snpEff' \
```

### Known issues

* There is currently a bug regarding the reference names: if you specify `--genome GRCh38`, sarek actually uses `hg38` (UCSC naming convention, and not Ensembl). See the [issue](https://github.com/nf-core/sarek/issues/86).

### Fixed issues since 2.6.1

* The `2.6.1` release had a bug with `mutect2`, this is now addressed and can be used.
* Our cluster has problems with MarkDuplicates Spark, which is why is should be avoided. As of `2.7` the non-spark variant is run by default. Please be aware to set `--no_gatk_spark` if you run `2.6.1` or earlier.

## Whole Exome and Targeted Sequencing

Running WES analysis and targeted sequencing with Sarek requires the BED file that specifies the Exomic regions or the Targeted regions that were used in the exome capture kit or Targeted design.

### Quick start

* Latest stable release `-r 2.7`.

Please use these parameters to run your WES analysis with sarek:

```bash
#!/bin/bash
module purge
module load devel/singularity/3.4.2
nextflow run nf-core/sarek -r 2.7 \
-profile cfc \
--genome 'GRCh38' \
--input 'input.tsv' \
--tools 'HaplotypeCaller,Strelka,mutect2,manta,ascat,controlFreec,VEP,snpEff' \
--targetBED `/path/to/targetBED`
```

### Important remarks for WES analysis

* *TargetBED* files: the target BED files are provided by the Exome capture kit companies. For common Human genomes and capture kits, the BED files are available in the `cfc` and `Binac` clusters. If your BED file is not there, contact your team leader.

  * `GRCh37` & `Twist capture kit`

    ```txt
    /nfsmounts/igenomes/Homo_sapiens/Ensembl/GRCh37/Exome_BED/Twist_Exome_Target_GRCh37_noUn_gl000228_2020_05_05.bed
    ```

  * `hg38` & `Agilent Technologies capture kit`

    ```txt
    /nfsmounts/igenomes/Homo_sapiens/UCSC/hg38/Exome_BED/S31285117_Regions_Agilent_Technologies_exome_hc38.bed
    ```

  * `hg19` & `Twist capture kit`

    ```txt
    /nfsmounts/igenomes/Homo_sapiens/UCSC/hg19/Exome_BED/Twist_Exome_Target_hg19_noUn_gl000228_2020_05_05.bed
    ```

  * Mouse `mm10` & `Agilent SureSelect All Exon`

    ```txt
    /nfsmounts/igenomes/Mus_musculus/UCSC/mm10/Exome_BED/mm10_Agilent_SureSelect_Mouse_All_Exon_Kit_2016_01_21_sorted.bed
    ```
    
  * Mouse `GRCm38` & `Agilent SureSelect Mouse`

    ```txt
    /nfsmounts/igenomes/Mus_musculus/Ensembl/GRCm38/Exome_BED/GRCm38_Agilent_SureSelect_Mouse_All_Exon_Kit_2016_01_21_sorted.bed
    ```
    
* Notes on Target BEDs: In general for WES data analysis people advise to use target regions that are padded (e.g. +- 150 bp), also the Sarek docs said that. But to be precise there are multiple aspects:
  * The mapping should be done on the whole reference to get information about multi reads mapping also outside of the exome (to discard)
  * Then reads mapping within the target exome regions should be kept, for this a padding should be added to prevent loosing reads at the boarders
  * for QC to get an unbiased view about the overall quality of the experiment its best to use the exact target regions without padding
  * and for the final variants it depends, if one want to be sure not to increase the number of FPs it would also make sense to use unpadded target regions. If the goal would be to get as many variants as possible, then of course one could use padded regions (which made quite a difference in the final numbers in my case)
  * Sarek actually maps reads against the whole reference, and then uses the targetBED file for the QC and to filter variants. So *simply hand over the unpadded target region*. This is usually provided by the Exon capture kit company.
* BED file compatibility: if you have to use your own BED file, make sure the chromosome names are compatible between the reference and the targetBED file (e.g. UCSC “chr1” <-> Ensemble “1”). For human, if they differ and your analysis is done only on main chromosomes, then you could simply convert the names by adding “chr” to the chromosome names.

## Running the pipeline with a non iGenomes genome

If you wish to run Sarek with a genome not present in iGenomes, the command line will look similar to the following :

```bash
module purge
module load devel/singularity/3.4.2
nextflow run nf-core/sarek -r 2.7  \
-resume \
-profile cfc \
--fasta '/sfs/7/workspace/ws/qealk01-QVBAP-0/reference/genome.fa' \
--input 'samples.tsv' \
--save_reference \
--bwa false \
--igenomes_ignore \
--genome custom \
--no_gatk_spark \
--tools 'strelka,snpEff' \
--snpeff_cache '/sfs/7/workspace/ws/qealk01-QVBAP-0/reference/cache/' \
--annotation_cache true \
--snpeff_db Solanum_lycopersicum
```

We here take _Solanum lycopersicum_ (tomato) as an example.
The following parameters have to be added :

* `--fasta path/to/genome.fa`. The assembly file should come from standardized reference genomes (i.e NCBI, ENSEMBL).
* `--igenomes_ignore`,
* `--genome custom`,
* `--snpeff_cache`, with the path to the SnpEff cache file (see below for more explanations),
* `--annotation_cache true`,
* `--snpeff_db` with the name of the genome you downloaded through SnpEff (see below).

### SnpEff cache

To download the SnpEff cache on the cfc, you can run the following command :

```bash
java -jar /lustre_cfc/software/qbic/snpEff/snpEff.jar -download -v Solanum_lycopersicum -dataDir .
```

with `-v name_of_your_genome`. This will create the cache in your directory, which you will then specify in the sarek pipeline.

### Known issues

* There is currently a bug regarding the reference names: if you specify `--genome GRCh38`, sarek actually uses `hg38` (UCSC naming convention, and not Ensembl). See the [issue](https://github.com/nf-core/sarek/issues/86).

### Fixed issues since 2.6.1

* The `2.6.1` release had a bug with `mutect2`, this is now addressed and can be used.
* Our cluster has problems with MarkDuplicates Spark, which is why is should be avoided. As of `2.7` the non-spark variant is run by default. Please be aware to set `--no_gatk_spark` if you run `2.6.1` or earlier.

## Reporting

Reporting hasn't been established yet for WGS & WES.
