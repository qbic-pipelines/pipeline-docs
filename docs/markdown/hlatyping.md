# HLA typing

We currently have available the HLA typing pipeline for type I typing. For type II typing, we don't have a good solution. Please contact Stefan before writing it in the offer.

## HLAtying nf-core pipeline

## HLA typing for type II

It is less than ideal, but we can do type II typing with [HLA-LA](https://github.com/DiltheyLab/HLA-LA).

### Installing HLA-LA

Create a fresh conda environment and install `HLA-LA`:

```bash
conda install hla-la
```

After intslling this, you still need to install the data packates and prepare the inference graph. The following commands shoud do the job, where `$PREFIX` is your installation path (e.g. inside conda/miniconda ):

```bash
mkdir $PREFIX/opt/hla-la/graphs
cd $PREFIX/opt/hla-la/graphs
wget http://www.well.ox.ac.uk/downloads/PRG_MHC_GRCh38_withIMGT.tar.gz
tar -xvzf PRG_MHC_GRCh38_withIMGT.tar.gz

cd $PREFIX/opt/hla-la/src
wget https://www.dropbox.com/s/mnkig0fhaym43m0/reference_HLA_ASM.tar.gz
tar -xvzf reference_HLA_ASM.tar.gz
```

Then run the command to build the graphs:

```bash
../bin/HLA-LA --action prepareGraph --PRG_graph_dir ../graphs/PRG_MHC_GRCh38_withIMGT
```

Here is an example bash script to submit the job to the cluster scheduler. Please substitute your email address and installation path / path to BAM files:

```bash
#!/bin/bash
# Reserve 8 threads on 1 node for this job
#PBS -l nodes=1:ppn=8:cfc
#PBS -q cfc
# Request 60GB of RAM
#PBS -l vmem=60G
#PBS -N HLAII-typing
#PBS -o HLAII-typing-out

#  Send an e-mail when the job begins(b)/ends(e)/aborts(a)/suspends(s)
#PBS -m bea
# Set your mailing address for job
#PBS -M <your.email-address>
#  If the walltime is reached and the job has not ended yet,
#  you will have to start over.
#PBS -l walltime=08:00:00

# Load necessary modules or other stuff..
echo   "**************** Starting analysis ****************\n
        ****************$(date)****************\n\n"

# ADD your installation path here
~/bin/miniconda/envs/QRABS/bin/HLA-LA.pl --workingDir /lustre_qbic/ws_qstor/ws/qeaga01-QRABS-0 --graph PRG_MHC_GRCh38_withIMGT --BAM T2_normal.recal.bam --sampleID T2_normal --maxThreads 8

echo   "**************** Analysis ended ****************\n
        ****************$(date)****************\n\n"
```

To submit the script just run:

```bash
sbatch <your_script.sh>
```

The typing results can be found in the `../working/$mySampleID/hla/R1_bestguess_G.txt` file. More info on how to interpret the results is [here](https://github.com/DiltheyLab/HLA-LA#interpreting-the-output-from-hlaprgla).
