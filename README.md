```markdown
# ICE

The ICE package is designed for performing Iterative-imputation-based Cell Enrichment on normalized gene expression data. It includes functions to compute enrichment scores and identify marker genes associated with specific gene sets.

## Installation

The development version from GitHub:

```r
# install.packages("devtools")
devtools::install_github("ICE")
```

## Usage Example

Below is a quick demonstration of how to use the `ICE` package:

```r
library(ICE)
library(reticulate) # Use reticulate for MAGIC imputation
use_python("/usr/bin/python3", required = TRUE) # Set MAGIC environment


# Load normalized gene expression data
normalized_data <- readRDS("ICE/data/pancreas_normalized.RDS")

# Read gene set information
gene_set <- read.delim("ICE/data/input_gene_set.tsv")

# Prepare gene sets for analysis
gs <- list()
for (i in unique(gene_set$marker_group)) {
  target_gs <- list(gene_set[gene_set$marker_group == i, "gene"])
  names(target_gs) <- i
  gs <- c(gs, target_gs)
}

# Perform GSEA
ICE_result <- ICE(normalized_data, gs)

ICE_es <- ICE_result[[1]] # Enrichment score
ICE_cutoff <- ICE_result[[2]] # Enrichment score cutoff
ICE_markers <- ICE_result[[3]] # Marker gene information for GSEA input
```

(Optional) Run ICE with imputed expression matrix, without iteration.

```r
ICE_result = ICE(normalized_data,gs,imputed_data = imputed_data,iteration = FALSE)
```


## Contributing

Contributions are welcome! If you find bugs or want to contribute new features, please submit an issue or pull request on GitHub.


## License

This project is licensed under the [MIT License](LICENSE).
```
