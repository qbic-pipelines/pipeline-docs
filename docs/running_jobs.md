
# Submitting a job to a cluster

These are general statements on how to submit jobs to our clusters.

## Submitting nf-core pipelines

These applies to all nf-core pipelines and all Nextflow pipelines outside of nf-core that were created using the nf-core template.

Please start all your nextflow jobs from the head node.
Nextflow interacts directly with the Slurm scheduler and will take care of submitting individual jobs to the nodes.
If you submit via an interactive job, weird errors can occur, e.g. the cache directory for the containers is mounted read only on the compute nodes, so you can't pull new containers from a node.

### On the CFC cluster

Please use the cfc profile `-profile cfc`. 