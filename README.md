# ICE: Imputation-based Cell Enrichment

**ICE** is an R package designed for performing *Imputation-based Cell Enrichment* on normalized gene expression data. It provides functions to compute enrichment scores and identify cells enriched for specific gene sets, leveraging **MAGIC imputation** via **reticulate**.

## Installation

Installation is a two-step process. First, set up a dedicated Conda environment for the Python dependencies. Second, install the required R packages.

### 1. Set Up the Conda Environment

This project requires a Python environment to run the MAGIC imputation algorithm.

```bash
# Create and activate a new conda environment named "ice"
conda create -n ice python=3.10
conda activate ice

# Install the magic-impute package
conda install -c conda-forge -c bioconda magic-impute
```

### 2. Install R Packages

With the Conda environment active, launch R and install ICE and its dependencies:

```r
# Install core dependencies from CRAN and Bioconductor
install.packages(c("dplyr", "pracma", "Seurat", "BiocManager", "devtools", "reticulate"))
BiocManager::install("GSVA")

# Install Rmagic and ICE from GitHub
devtools::install_github("KrishnaswamyLab/MAGIC/Rmagic")
devtools::install_github("pxulab/ICE")
```

## Usage

### A Note for Linux Users

On some Linux systems, R may fail to load libraries from the Conda environment correctly. If you encounter errors related to **libstdc++.so.6**, run the following before launching R:

```bash
conda activate ice
export LD_PRELOAD=$CONDA_PREFIX/lib/libstdc++.so.6
```

When finished, it is good practice to unset this variable:

```bash
unset LD_PRELOAD
```

### Example Workflow

```r
# Load the necessary libraries
library(ICE)
library(reticulate)

# Point reticulate to the correct Python environment
# This is essential for the MAGIC imputation step
# Skip it if MAGIC is installed by pip in default python environment.
reticulate::use_condaenv("ice", required = TRUE)

# 1. Load normalized gene expression data
# The matrix should have genes as rows and cells as columns
normalized_data <- readRDS(system.file("data", "pancreas_normalized.RDS", package = "ICE"))

# 2. Load gene set definitions
gene_set <- read.delim(system.file("data", "input_gene_set.tsv", package = "ICE"))

# 3. Format the gene sets into a named list for analysis
gs <- list()
for (i in unique(gene_set$marker_group)) {
  target_gs <- list(gene_set[gene_set$marker_group == i, "gene"])
  names(target_gs) <- i
  gs <- c(gs, target_gs)
}

# 4. Run the main ICE function to perform Gene Set Enrichment Analysis
ICE_result <- ICE(normalized_data, gs, iteration = TRUE)

# 5. Access the results
ICE_es <- ICE_result[[1]]      # Enrichment scores
ICE_cutoff <- ICE_result[[2]]  # Enrichment score cutoffs
ICE_markers <- ICE_result[[3]] # Marker genes used for GSEA input
```

### Alternative: Using a Pre-imputed Matrix

For large-scale datasets or when using a custom imputation method, ICE can run on a pre-imputed expression matrix by disabling iteration:

```r
# Assuming 'imputed_data' is your pre-computed imputed matrix
ICE_result <- ICE(
  normalized_data = NA,
  gs = gs,
  imputed_data = imputed_data,
  iteration = FALSE
)
```

## Contributing

Contributions are welcome! If you find a bug or would like to suggest a new feature, please submit an issue or pull request on GitHub.

## License

This project is licensed under the [MIT License](LICENSE).

