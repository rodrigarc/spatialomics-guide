# Spatial Transcriptomics Analysis: Human Lymph Node (Visium HD)

## Project Overview  
This repository contains a complete analytical pipeline for **Visium HD spatial transcriptomics data** from a human FFPE lymph node sample. The workflow includes:

1. **Quality Control**  
2. **Normalization & Clustering**  
3. **Cell Type Deconvolution**  
4. **Cell-Cell Communication Analysis**  

Key biological insights include immune cell spatial organization, proliferative niches, and ligand-receptor interactions.

---

## Repository Structure  

```
├── data/  
│   ├── output_space_ranger_4-0-1/       # Raw Space Ranger outputs  
│   └── reference_dataset/                # scRNA-seq reference data  
├── results/  
│   ├── 01_qc_outputs/                    # QC plots and filtered data  
│   ├── 02_normalization_and_clustering/  # Clustering results  
│   ├── 03_cell_type_deconvolution/       # RCTD deconvolution outputs  
│   └── 04_cell_communication/            # CellChat interaction networks  
└── src/  
    ├── 01_spatial_QC.qmd                 # Quality control  
    ├── 02_normalization_and_clustering.qmd  
    ├── 03_cell_type_deconvolution.qmd    # RCTD deconvolution  
    └── 04_cell_communication.qmd         # CellChat analysis  

```

---

## Key Workflow Steps  

### 1. Quality Control (`01_spatial_QC.qmd`)  
- **Input**: `raw_feature_bc_matrix.h5` (10x Genomics Visium HD)  
- **Steps**:  
  - Filter low-quality spots (UMI < 30, genes < 40, MT% < 12.5)  
  - Visualize QC metrics (UMI/gene distributions, spatial QC plots)  
  - Cell cycle scoring and immune marker visualization (CD3E, BCL6, IGHG1)  
- **Output**: Filtered Seurat object (`spatial_qc_results.rds`)  

### 2. Normalization & Clustering (`02_normalization_and_clustering.qmd`)  
- **Methods**:  
  - BANKSY clustering with Leiden algorithm  
  - Manual annotation using marker genes (e.g., `CD3E` for T cells)  
- **Output**: Annotated clusters (`Banksy_manually_annotated_object.rds`)  

### 3. Cell Type Deconvolution (`03_cell_type_deconvolution.qmd`)  
- **Reference Data**: scRNA-seq from [He et al. (2020)](https://doi.org/10.1038/s41586-020-2922-4) (lymph node + blood)  
- **Tools**:  
  - `spacexr` RCTD for spot deconvolution  
  - Integration with BANKSY clusters  
- **Key Findings**:  
  - Myeloid/T cell spatial segregation  
  - Rejected spots (~50%) enriched for non-immune cells (fibroblasts, ECs)  

### 4. Cell-Cell Communication (`04_cell_communication.qmd`)  
- **Tool**: `CellChat` with spatial constraints  
- **Analyses**:  
  - Global interaction networks (strength/count)  
  - Pathway-specific visualization (e.g., MIF signaling)  
- **Output**: Interaction probability matrices (`cellchat_object.rds`)  

---

## Dependencies  
```r
# Core packages
library(Seurat)       # v5.0.1
library(spacexr)      # RCTD deconvolution
library(CellChat)     # v1.6.1
library(BANKSY)       # Spatial clustering
library(scRNAseq)     # Reference datasets

# Supporting packages
library(tidyverse)    # Data wrangling
library(arrow)        # Efficient data storage
```

## Usage

1. Run scripts sequentially:

```
Rscript src/01_spatial_QC.qmd  
Rscript src/02_normalization_and_clustering.qmd  
...

```
2. Outputs: Automatically saved to dated subfolders in results/.

## Data availability

Data Availability
Primary Data: [10x Genomics Visium HD (Human Lymph Node)](https://www.10xgenomics.com/datasets/visium-hd-cytassist-gene-expression-libraries-human-lymph-node-v4)

Reference scRNA-seq: scRNAseq::fetchDataset("he-organs-2020")

