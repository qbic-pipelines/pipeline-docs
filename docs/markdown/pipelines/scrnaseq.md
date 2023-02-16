# scRNAseq analysis

## Intro and docs

For a comprehensive understanding of scRNAseq data processing, check out these courses:

- [Broad institute course](https://broadinstitute.github.io/2020_scWorkshop/)
- [Biocellgen group](https://biocellgen-public.svi.edu.au/mig_2019_scrnaseq-workshop/public/index.html)

## Pipeline

For single-cell RNAseq analysis please use the [nf-core/scrnaseq](nf-co.re/scrnaseq) pipeline. We currently recommend running the cellranger tool as it's the most requested one and currently compatible with our downstream analysis scripts.

For secondary analysis, we are currently just running custom R and Rmarkdown files using the Seurat library. Please ask your team leader for example scRNAseq projects.
