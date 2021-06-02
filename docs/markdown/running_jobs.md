
# Submitting jobs to the cluster

These are general statements on how to submit jobs to our clusters.

* [Submitting Nextflow pipelines](#submitting-nextflow-pipelines)
  * [Dependencies](#dependencies)
    * [Install Nextflow](#install-nextflow)
    * [Nextflow version](#nextflow-version)
  * [Loading singularity modules](#loading-singularity-modules)
  * [Pipeline profiles](#pipeline-profiles)
  * [Example bash file](#example-bash-file)
* [Screen sessions](#screen-sessions)
* [Useful scheduler commands](#useful-scheduler-commands)
* [Submitting custom jobs](#submitting-custom-jobs)
  * [Starting an interactive session](#starting-an-interactive-session)
  * [Submitting a bash script with `sbatch`](#submitting-a-bash-script-with-sbatch)

## Submitting Nextflow pipelines

These applies to all nf-core pipelines and all Nextflow pipelines outside of nf-core that were created using the nf-core template.
For Nextflow pipelines not created with the nf-core template, some
of the functionality might not be available, for example the `cfc` and `binac` profiles.

Please start all your nextflow jobs from the head node.
Nextflow interacts directly with the Slurm scheduler and will take care of submitting individual jobs to the nodes.
If you submit via an interactive job, weird errors can occur, e.g. the cache directory for the containers is mounted read only on the compute nodes, so you can't pull new containers from a node.

### Dependencies

To run Nextflow pipelines in our clusters, you will need Nextflow, java and singularity installed.
Luckily, our sysadmin made a module for singularity and java in our clusters already, so you will just need to load these modules.

You will still have to install Nextflow for your user, that's very simple and described in the next section.

#### Install Nextflow

* Download the executable package by copying and pasting the following command in your terminal window:

    ```bash
    wget -qO- get.nextflow.io | bash
    ```

* Optionally, move the nextflow file in a directory accessible by your `$PATH` variable (this is only required to avoid to remember and type the Nextflow full path each time you need to run it).

For more information, visit the [Nextflow documentation](https://www.nextflow.io/docs/latest/en/latest/getstarted.html).

#### Nextflow version

It is advisable to keep the Nextflow version at `>= 19.10.0` which can be achieved by updating Nextflow using:

```bash
nextflow -self-update
```

You can also check that the environment variable `NXF_VER` is set to the version of Nextflow that you want to use:

```bash
echo $NXF_VER
```

If not, set it by running:

```bash
NXF_VER=19.10.0
```

### Loading singularity modules

We currently load Singularity as a module on both BinAC and CFC to make sure that all paths are set accordingly and load required configuration parameters tailored for the respective system.
Please do use *only* these Singularity versions and *NOT* any other (custom) singularity versions out there.
These singularity modules will already load the required java module
so you don't need to take care of that.

```bash
module load devel/singularity/3.4.2
```

### Pipeline profiles

#### On CFC

Please use the [cfc profile](https://github.com/nf-core/configs/blob/master/conf/cfc.config) to run nf-core pipelines on the CFC cluster, by adding `-profile cfc` to your command. For example:

```bash
nextflow run nf-core/rnaseq -r 1.4.2 -profile cfc
```

If you need the development version of a pipeline, please use the [cfc_dev profile](https://github.com/nf-core/configs/blob/master/conf/cfc_dev.config) `-profile cfc_dev` so that the containers are not cached and you pull the latest version of the container.

```bash
nextflow run nf-core/rnaseq -r 1.4.2 -profile cfc_dev
```

For Nextflow pipelines not part of nf-core and not created with the nf-core create command, these profiles will not be available.
If you need to run one of these pipelines, you can get the profile
from the [nf-core configs](https://github.com/nf-core/configs) repository and provide it to the pipeline as `-c custom.config`.

```bash
nextflow run custom/pipeline -c custom.config
```

#### On BinAC

Please use the [binac profile](https://github.com/nf-core/configs/blob/master/conf/binac.config) by adding `-profile binac` to run your analyses. For example:

```bash
nextflow run nf-core/rnaseq -r 1.4.2 -profile cfc_dev
```

For Nextflow pipelines not part of nf-core and not created with the nf-core create command, these profiles will not be available.
If you need to run one of these pipelines, you can get the profile
from the [nf-core configs](https://github.com/nf-core/configs) repository and provide it to the pipeline as `-c custom.config`.

### Example bash file

It is a good practice to keep the commands used to run a pipeline in a bash file.
Keep also the modules loaded in this bash file so that you can know which singularity version was
used for the analysis.
Here is an example bash file:

```bash
#!/bin/bash
module purge
module load devel/singularity/3.4.2
nextflow run nf-core/sarek -r 2.6.2 -profile cfc,test
```

## Screen sessions

So your jobs can continue running once you log out of the cluster, you should use some kind of tool to allow the jobs to continue running on the background.
One of these tools is [screen](https://www.linode.com/docs/networking/ssh/using-gnu-screen-to-manage-persistent-terminal-sessions/).
Some basic screen commands are:

* starting a screen session:

    ```bash
    screen -S <session_name>
    ```

* To detach from a screen session, use `ctrl + alt + d`

* List existing screen sessions:

    ```bash
    screen -ls
    ```

* attaching to an existing screen session:

    ```bash
    screen -x <session_name or ID>
    ```

* exiting a screen session:

    ```bash
    exit
    ```

## Useful scheduler commands

Here are some useful commands for the Slurm scheduler.

* Checking the job queue:

    ```bash
    squeue
    ```

* Checking the node status:

    ```bash
    sinfo
    ```

## Submitting custom jobs

*Important note*: running scripts without containerizing them is never 100% reproducible, even when using conda environments.
It is ok for testing, but talk to your group leader about the possibilities of containerizing the analysis or adding your scripts to a pipeline.

To run custom scripts (R or python or whatever you need) in the cluster, it is mandatory to use a dependency management system. This ensures at leaset some reproducibility for the resutls. You have two possibilities: use a clean conda environment and eexport it as an `environment.yml` file, or working on Rstudio and then using Rmaggedon.

* *Using conda*: create a conda environment and install there all the necessary dependencies. Once you have them all, export the dependencies to a yml file containing the project code:

```bash
conda env export > QXXXX_environment.yml
```

* *Using Rmageddon*: work on your project on Rstudio and then use the r-lint / Rmageddon tool. Check out the [Rmageddon SOP](https://docs.google.com/document/d/1tGMNpABCICa40573x9lrpdN8zs2gYq5iTkPvYJKbq-8/edit?usp=sharing) on how to do this.

If you just run jobs directly on the cluster, they will run on the head node. This is not desired, as all users use the head node
and it will make the general usage of the cluster slow.
To run custom scripts you can either start an interactive session or submit a bash script with `sbatch`.

### Starting an interactive session

Start first a screen session:

```bash
screen -S <session_name>
```

Then request an interactive job to the Slurm scheduler with the desired resources. For example:

```bash
srun -N 1 --ntasks-per-node=8 --mem=16G --time=12000 --pty bash
```

Change the resources as needed:

* N are the number of nodes
* `--ntasks-per-node` are the number of cpus
* `--mem` is the memory required
* `--time` is the time required in seconds

The scheduler will then prompt:

```bash
srun: job XXXXXX queued and waiting for resources
srun: job XXXXXX has been allocated resources
```

Then you can run your scripts as usual. If the computation lasts longer than the specified time, it will be killed.
To kill the job just exit the interactive console with `exit`.
You should see your job listed when running `squeue`.

### Submitting a bash script with `sbatch`

If you have a batch script, you can submit it to the cluster with the `sbatch` command.

```bash
sbatch <your_script.sh>
```

In order to specify the allocated resources, your bash script needs to have the resources specified in the header before the job (all the lines starting with `#SBATCH`).
You can edit the script with your required resources.
Take this bash script as example, which merges `*.fastq.gz` files starting with the codes specified in the `codes.tsv` file (handling separately the R1 and R2 reads).

```bash
#!/bin/bash
# set the number of nodes and processes
#SBATCH --nodes=1

# set the number of tasks per node
#SBATCH --cpus-per-task=20

# set memory per cpu
#SBATCH --mem=60GB

# set max wallclock time HH:MM:SS
#SBATCH --time=100:00:00

# set name of job
#SBATCH --job-name=merge_lane

# set queue (partition)
#SBATCH --partition=compute

# mail alert at start, end and abortion of execution
#SBATCH --mail-type=ALL
#SBATCH --mail-user=<your.email@example.com>

# your commands start here:
set -eou pipefail

file="codes.tsv"
while IFS= read -r f1
do
  # move files into folders
  mkdir $f1
  printf 'Making folder %s \n' "$f1"
  mv $f1*.fastq.gz $f1/
  mv $f1*.metadata $f1/
  find $f1 -type f -name '*_R1_*.fastq.gz' -print0 | sort -z | xargs -0 cat > "$f1"/"$f1"_R1.fastq.gz
  find $f1 -type f -name '*_R2_*.fastq.gz' -print0 | sort -z | xargs -0 cat > "$f1"/"$f1"_R2.fastq.gz
done <"$file"
```
