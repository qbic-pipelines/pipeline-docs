# guideseq analysis

The GUIDE-Seq Analysis Package

This is a detailed guide to running Lukas Heumos' singularity container/wrapper around <i>aryeelab</i>'s guideseq pipeline:
https://github.com/aryeelab/guideseq
The <i>aryeelab</i> pipeline is based on python2.

> Note 1:
> There is an updated version with python3 from <i>tsailab</i> > https://github.com/tsailabSJ/guideseq .
> However, there is an issue/bug with the last 2 steps of the pipeline not producing any output files. So we revert to the python2 version above.

> Note 2:
> The singularity container can also be found at QBiC backup at `/mnt/nfs/qbic/project_management/qeajl01/`
> Related files e.g manifest files, more detailed guides etc can be found at `github.com/qbic-projects/guideseq`

## A. Prepare singularity container

1. As we are using an older python2 version, checkout the older docker commit from Lukas' github.

<details>
<summary>Click to expand code !</summary>

```
git clone https://github.com/Zethson/guide-seq-container
git checkout commit 97255c96a7066d1cbe81f18c0da0f5de903bdca1

# check first line of ubuntu version in docker file, it should say:
FROM ubuntu:16.04  ## if just ubuntu, it will build latest by default

# Build the old container locally
docker build -t guide_seq:latest .

# Now create the Singularity image from it
mkdir -p /tmp/singularity_output
docker run -v /var/run/docker.sock:/var/run/docker.sock \
-v /tmp/singularity_output:/output \
--privileged -t --rm \
singularityware/docker2singularity \
guide_seq:latest

```

</details>

## B. Copy the singularity image to CFC and run programme

```
singularity shell guide_seq_latest-2021-03-17-aee25278c977.simg
```

There is no executable for the pipeline, so clone the repo from aryeelab

```
# remember to clone recursively!
git clone --recursive https://github.com/aryeelab/guideseq.git

# set locale and install requirements
export LC_ALL=C
cd guideseq
pip install -r requirements.txt

# do test run
python guideseq/guideseq.py all -m test/test_manifest.yaml

```

## C. Setting up input files

The following files are required to run a guidseq analysis

1. reference genome
2. accompanying index files to the undemultiplexed raw sequence files

If the raw sequence files are already demultiplexed, there are 2 possible solutions to geenerate the index files:

1. Fast, easy way.
   Download a custom python script `parse_bc_reads_labels` from http://seqanswers.com/forums/showthread.php?t=27428
   The script has been deleted from the original link, but it is saved here
   The script creates index files directly from the demultiplexed sequence files

```
# create conda python 2 environment to install cogent

# run the downloaded script
python ~/src/others/parse_bc_reads_labels.py Undetermined_S0_L001_R1_001.fastq Undetermined.I1.fastq

```

2. using `bcl2fastq` on the base called files to generate the undemultiplexed sequence files and index files.
   Follow the instructions in `bcl2fastq2-v2-20-software-guide-15051736-03.pdf`.
   Most important step:

```
bcl2fastq -R data/bcl/ --create-fastq-for-index-read
```

## D. Prepare yaml manifest file

Example of manifest file e.g. `manifest_4.yaml`

<details>
<summary>Click to expand manifest file!</summary>

```
reference_genome: ../../data/ref_genome/grch37/Homo_sapiens.GRCh37.75.dna.primary_assembly.fa
output_folder: output

bwa: /bwa/bwa
bedtools: /bedtools2/bin/bedtools

demultiplex_min_reads: 100000

undemultiplexed:
    forward: ../../data/raw_data/Undetermined_S0_L001_R1_001.fastq.gz
    reverse: ../../data/raw_data/Undetermined_S0_L001_R2_001.fastq.gz
    index1: ../../data/raw_data/Undetermined_S0_L001_I1_001.fastq.gz
    index2: ../../data/raw_data/Undetermined_S0_L001_I2_001.fastq.gz

samples:
    control:
        target:
        barcode1: TCGCCTTA
        barcode2: TAGATCGC
        description: control

    Cas9:
        target: ATGTCCATGGGGGCACCGCGGGG
        barcode1: CTAGTACG
        barcode2: CTCTCTAT
        description: Cas9

    Hifi:
        target: ATGTCCATGGGGGCACCGCGGGG
        barcode1: TTCTGCCT
        barcode2: TATCCTCT
        description: Hifi
```

</details>

## E. Execute guideseq.py

Run on cfc

```
## try with 14 cores for 28 hours
srun --pty -n 1 -c 14 --time=1680 bash

# load singularity shell
singularity shell -B /sfs/7/workspace/ws/qeajl01-QPJRV_JunHoe-0/ guide_seq_latest-2021-03-17-aee25278c977.simg

# run guideseq on singularity shell
python guideseq/guideseq.py all -m manifest_4.yaml

```

## F. Troubleshooting

You might encounter an error with `/scratch`, as below.

```
# frequent runs or large dataset can fill up /scratch and cause an error like this:
sort: cannot create temporary file in '/scratch/835717': No such file or directory

# possible solution: set temp directory
export TMPDIR=/tmp/

```

On first run, it is also very likely you will encounter an error after the demultiplexing step like below:

<details>
<summary>Click to expand error code !</summary>

```
[03/05 11:47:11AM][ERROR][guideseq] Error umitagging
[03/05 11:47:11AM][ERROR][guideseq] Traceback (most recent call last):
  File "/opt/conda/envs/guide_seq/bin/guideseq.py", line 167, in umitag
    os.path.join(self.output_folder, 'umitagged'))
  File "/opt/conda/envs/guide_seq/lib/python3.7/site-packages/umi/umitag.py", line 56, in umitag
    for r1,r2,i1,i2 in zip(fq(read1), fq(read2), fq(index1), fq(index2)):
  File "/opt/conda/envs/guide_seq/lib/python3.7/site-packages/umi/umitag.py", line 26, in fq
    fastq = open(file, 'r')
FileNotFoundError: [Errno 2] No such file or directory: 'output/demultiplexed/control.r1.fastq'
```

</details>

That is due to a bug in the programme where it fails to create `control.r1` and `control.r2` files with the correct names due to the way it reads the primer/barcode sequences.
Instead thoes files are named from combining the barcode1 + barcode2 sequences.

<details>
<summary>Click to expand example of wrongly named files !</summary>

```
(guideSeq) bash-4.2$ tree output/demultiplexed/
output/demultiplexed/
├── AAGGCGAAGATCGC.i1.fastq
├── AAGGCGAAGATCGC.i2.fastq
├── AAGGCGAAGATCGC.r1.fastq
├── AAGGCGAAGATCGC.r2.fastq
├── GGCAGAAATCCTCT.i1.fastq
├── GGCAGAAATCCTCT.i2.fastq
├── GGCAGAAATCCTCT.r1.fastq
├── GGCAGAAATCCTCT.r2.fastq
├── GTACTAGTCTCTAT.i1.fastq
├── GTACTAGTCTCTAT.i2.fastq
├── GTACTAGTCTCTAT.r1.fastq
├── GTACTAGTCTCTAT.r2.fastq
├── undetermined.i1.fastq
├── undetermined.i2.fastq
├── undetermined.r1.fastq
└── undetermined.r2.fastq
```

</details>

The solution is to manually rename those files
Lukas' solution: (please refer to the manifest file above to understand)

> E.g. one of the files in demultiplex is AAGGCGAAGATCGC.i1.fastq
> This comes from the control barcodes: i7 TCGCCTTA. Do a reverse complement, and drop end A, so
> AAGGCGA + i5 (dropped first T) AGATCGC

<details>
<summary>Click to expand solution !</summary>

```
# before
guideSeq) bash-4.2$ tree output/demultiplexed/
output/demultiplexed/
├── AAGGCGAAGATCGC.i1.fastq
├── AAGGCGAAGATCGC.i2.fastq
├── AAGGCGAAGATCGC.r1.fastq
├── AAGGCGAAGATCGC.r2.fastq
├── GGCAGAAATCCTCT.i1.fastq
├── GGCAGAAATCCTCT.i2.fastq
├── GGCAGAAATCCTCT.r1.fastq
├── GGCAGAAATCCTCT.r2.fastq
├── GTACTAGTCTCTAT.i1.fastq
├── GTACTAGTCTCTAT.i2.fastq
├── GTACTAGTCTCTAT.r1.fastq
├── GTACTAGTCTCTAT.r2.fastq
├── undetermined.i1.fastq
├── undetermined.i2.fastq
├── undetermined.r1.fastq
└── undetermined.r2.fastq

# after
(guideSeq) bash-4.2$ tree output/demultiplexed/
output/demultiplexed/
├── control.i1.fastq
├── control.i2.fastq
├── control.r1.fastq
├── control.r2.fastq
├── Hifi1.i1.fastq
├── Hifi1.i2.fastq
├── Hifi1.r1.fastq
├── Hifi1.r2.fastq
├── Cas9.i1.fastq
├── Cas9.i2.fastq
├── Cas9.r1.fastq
├── Cas9.r2.fastq
├── undetermined.i1.fastq
├── undetermined.i2.fastq
├── undetermined.r1.fastq
└── undetermined.r2.fastq
```

</details>

After renaming the files, restart the pipeline and it should complete without any errors,

# G. Output

The main output is the `visualization` folder, will contain files e.g. `Cas9_offtargets.svg`.
These svg files shows sites of the primer sequence that could be potentially off-target.

The numbers at the right side is explained below.

<details>
<summary>Click to expand explanation !</summary>
> There are two tabs in the Excel one for identified off targets for Cas9 and Hifi each. In there is each a blue row indicating the off target that is visualized in your results/visualization folder. From this you can see that the number at the end derives from column "bi.sum.mi" which is according to the manual here: https://github.com/tsailabSJ/guideseq#visualize , the sum of the forward and reverse reads (with distinct molecular indices), i.e. the barcodes from the manifest file , I assume.
</details>

Good luck! :)
