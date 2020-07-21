# Debugging

Here is described how to report your errors so that we can solve them, and some common errors withe their solutions.

* [General error reporting](#general-error-reporting)
* [Common errors](#common-errors")

## General error reporting

Whenever you experience an error in a pipeline that you cannot solve by yourself, please ask for help in Slack. There are dedicate channels for the most common analysis pipelines used, and otherwise jsut refr to the `#data-analysis` channel.

We need a full error report so we can easily check what is wrong. Please report:

* The command used for executing the workflow
* If you used a custom config with the `-c` parameter, report what is written in that config
* The work directory for the process where this error occurs (you might need to change the group to `qbicgrp` to give access to it)
* The error message that you got.
* If there is no error message, please check the `.command.err`, `.command.log`, `.command.out` files in the work directory of this process for some error messages and report them too.

For example:

"Hi all, I was running the pipeline `<pipeline-name>`and I got an error. The command I was using is:

```bash
nextflow run nf-core/sarek -r 2.6.1 -profile cfc -c 'custom.config' --input 'input.tsv' --genome 'GRCh38' --tools Strelka -resume
```

The `custom.config` was:

```bash
process {
  withName:MapReads {
  memory = {500.GB as nextflow.util.MemoryUnit}
  }
  withName:MarkDuplicates {
  scratch = '/sfs/7/workspace/ws/qeamo01-QDUVU-0/tmp'
  }
}
```

And the error was:

```bash
Error executing process > 'ConcatVCF (FreeBayes-QDUVU017A7_DX196409)'
Caused by:
  Process `ConcatVCF (FreeBayes-QDUVU017A7_DX196409)` terminated with an error exit status (141)
Command executed:
  concatenateVCFs.sh -i Homo_sapiens_assembly38.fasta.fai -c 8 -o FreeBayes_QXXXX017A7_DX196409.vcf
Command exit status:
  141
Command output:
  (empty)
Command wrapper:
  nxf-scratch-dir cfc021:/scratch/537530/nxf.wnMVv37YvK
Work dir:
  /sfs/7/workspace/ws/QXXXX-XXXX-0/work/bc/25331c08fe11bdd7cffe23226b242c
Tip: you can try to figure out what's wrong by changing to the process work dir and showing the script file named `.command.sh
```

Thanks for your help!"

## Common errors

### Error: "cannot find file in folder"

This should not happen anymore, but was previously unfortunately quite often the case. Please check the module file whether the path you intend to load data from is existing inside the Singularity container. To do that:

Enter a Shell inside your container (requires Singularity to be loaded prior to doing anything).

```bash
singularity shell work/singularity/<your_image>.img
```

Navigate to your error path that Nextflow told you about in the error message, and check whether your files are accessible:

```bash
cd work/<hash>/<hash>/
ls -l, less -S <file>
```

If not, the path your are staging the files from might actually be missing from the respective singularity configuration file. Open an issue in Zendesk telling Matthias Seybold about it.

### Error: "Your process was likely terminated by the external system"

This is typically just a cluster message, that your job / process was killed because it consumed:

* Too much memory
* Too much time
* Too many CPUs

You can give more resources to the failing process with a custom config. For example, for the `MapReads` and the `MarkDuplicates` process:

```bash
process {
  withName:MapReads {
  memory = {500.GB as nextflow.util.MemoryUnit}
  }
  withName:MarkDuplicates {
  cpus = 8
  }
}
```

Then run your pipeline providing this config file wiht the `-c` parameter:

```bash
nextflow run nf-core/sarek -r 2.6.1 -profile cfc ... -c <your-custom.config>
```

### Error: "Unable to create TMPDIR [xxxx]: No space left on device"

An example of this error as shown in the `.command.err`:

```bash
slurmstepd: error: Unable to create TMPDIR [/scratch/521635]: No space left on device
slurmstepd: error: Setting TMPDIR to /tmp
nxf-scratch-dir cfc011:/tmp/nxf.cY1BzV3jqA
Using GATK jar /opt/conda/envs/nf-core-sarek-2.6.1/share/gatk4-4.1.7.0-0/gatk-package-4.1.7.0-local.jar
```

And somewhere hidden in the `.command.log`:

```bash
Suppressed: java.io.IOException: No space left on device
```

This error occurs because the `scratch` space on the nodes for staging files there is not sufficent. In this case your workspace can be used instead as `tmp`dir. To do that:

* Create a directory named `tmp` in your workspace
* Specify this directory as `scratch` for the failing process. This can be provided as a `custom.config` file. Example:

  ```bash
  process {
  withName:MarkDuplicates {
  scratch = '/sfs/7/workspace/ws/my-ws-name/tmp'
  }
  }
  ```

* Resume the pipeline specifying this config with the `-c <your-custom.config>
