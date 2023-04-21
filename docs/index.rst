.. QBiC analysis documentation master file, created by
   sphinx-quickstart on Sun Jul 19 19:28:57 2020.
   You can adapt this file completely to your liking, but it should at least
   contain the root `toctree` directive.

Welcome to the QBiC analysis documentation!
===========================================

This documentation contains the guidelines for running Bioinformatics analyses at QBiC.
How to run nf-core pipelines, which are the latest tested releases and how to report the results.

You can contribute or add changes to the documentation, by making a pull request to the pipelines-docs_ github repository.

.. _pipelines-docs: https://github.com/qbic-pipelines/pipeline-docs

Additionally, this repository will collect background information for the different analysis types.

Analysis types
--------------

:doc:`markdown/pipelines/pipeline_releases`
   Pipeline releases that are currently used at QBiC.

:doc:`markdown/pipelines/exomeseq`
   WGS and WES sequencing

:doc:`markdown/pipelines/rnaseq`
   RNA sequencing

:doc:`markdown/pipelines/ampliseq`
   16S amplicon sequencing

:doc:`markdown/pipelines/hlatyping`
   HLA typing

:doc:`markdown/pipelines/scrnaseq`
   Single-cell RNA sequencing

:doc:`markdown/pipelines/airrflow`
   BCR / TCR sequencing

:doc:`markdown/pipelines/guideseq`
   Guideseq analysis


Clusters and remotes
--------------------

:doc:`markdown/running_jobs`
   Submitting jobs to the SLURM clusters

:doc:`markdown/denbi_cloud`
   Setting up instances on deNBI denbi_cloud

:doc:`markdown/igenomes`
   Updating igenomes resource on the cluster

.. Hidden TOCs

.. toctree::
   :maxdepth: 3
   :caption: Analysis pipelines
   :hidden:

   markdown/pipelines/pipeline_releases
   markdown/pipelines/exomeseq
   markdown/pipelines/rnaseq
   markdown/pipelines/ampliseq
   markdown/pipelines/hlatyping
   markdown/pipelines/scrnaseq
   markdown/pipelines/airrflow
   markdown/pipelines/guideseq

.. toctree::
   :maxdepth: 3
   :caption: Clusters and remotes
   :hidden:

   markdown/clusters/running_jobs
   markdown/clusters/denbi_cloud
   markdown/clusters/tower
   markdown/clusters/igenomes
