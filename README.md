
# **djvdj** <img src="man/figures/logo.png" align="right" alt="" width="120" />

<!-- badges: start -->

[![R-CMD-check](https://github.com/rnabioco/djvdj/actions/workflows/R-CMD-check.yaml/badge.svg)](https://github.com/rnabioco/djvdj/actions/workflows/R-CMD-check.yaml)
[![codecov](https://codecov.io/gh/rnabioco/djvdj/branch/master/graph/badge.svg?token=ZI26OJII4P)](https://codecov.io/gh/rnabioco/djvdj)
<!-- badges: end -->

The djvdj package provides a range of tools to analyze and manipulate
single cell V(D)J sequencing data. These tools are straightforward and
easily integrate into a standard [Seurat](https://satijalab.org/seurat/)
workflow. This is a work in progress, please report any bugs by opening
a new issue.

## Installation

You can install the development version of djvdj from
[GitHub](https://github.com/rnabioco/djvdj) with:

``` r
devtools::install_github("rnabioco/djvdj")
```

## Import

With djvdj you can import V(D)J sequencing results from [Cell
Ranger](https://support.10xgenomics.com/single-cell-vdj/software/pipelines/latest/using/vdj#header)
and add these data to a [Seurat](https://satijalab.org/seurat/) or
[SingleCellExperiment](https://www.bioconductor.org/packages/release/bioc/html/SingleCellExperiment.html)
object using `import_vdj()`. Additional functions are provided to filter
and modify (`filter_vdj()`, `mutate_vdj()`, `summarize_vdj()`) the
object based on V(D)J metrics including chains, clonotypes, and CDR3
sequences.

``` r
# Import VDJ data
# A vector of paths can be provided to load multiple datasets
# If prefixes were added to the cell barcodes when the object was generated,
# include these as the vector names
paths <- c(
  BL6 = "splenocytes/BL6_BCR",
  MD4 = "splenocytes/MD4_BCR"
)

# Add V(D)J data to object
so <- import_vdj(
  input         = so,      # Seurat or SingleCellExperiment object
  vdj_dir       = paths,   # Cellranger output directories
  filter_paired = FALSE    # Only include clonotypes with paired chains
)
```

## Calculate

djvdj allows you to calculate a range of population diversity and
similarity metrics implemented with the
[abdiv](https://github.com/kylebittinger/abdiv) package. The function
`calc_diversity()` can be used to measure diversity on a per-cluster or
per-sample basis to allow for comparison across conditions.
`calc_similarity()` will measure repertoire overlap between clusters or
samples to allow for direct comparisons between cells of interest.
Additional functions are also available to calculate clonotype
abundances and V(D)J gene usage (`calc_frequency()`,
`calc_gene_usage()`).

``` r
so <- calc_diversity(
  input       = so,              # Seurat object
  data_col    = "clonotype_id",  # Column containing clonotypes
  cluster_col = "orig.ident",    # Column containing cell clusters to compare
  method      = abdiv::simpson   # Diversity metric to use
)
```

## Plot

For each ‘calc’ function, djvdj also provides a corresponding ‘plot’
function to summarize the results.

``` r
# Compare the usage of different V and J genes
ggs <- plot_gene_usage(
  input       = so,                     # Seurat object
  data_cols   = c("v_gene", "j_gene"),  # Column(s) containing V(D)J genes to plot
  cluster_col = "orig.ident",           # Column containing cell clusters to compare
  chain       = "IGK",                  # Chain to plot
  plot_colors = "#6A51A3"
)

Reduce(`+`, ggs)
```

![](man/figures/README-usage-1.png)<!-- -->
