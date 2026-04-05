# Gene Expression in Cancer — RNA-Seq EDA

## Overview

This project performs an exploratory data analysis (EDA) on RNA-seq gene expression data
from The Cancer Genome Atlas (TCGA) to explore whether gene expression patterns can 
distinguish between different cancer types and identify candidate biomarker genes.

- **Dataset:** Gene Expression Cancer RNA-Seq — UCI ML Repository / TCGA
- **Samples:** 801 patients across 5 cancer types (BRCA, KIRC, LUAD, PRAD, COAD)
- **Features:** 20,531 gene expression values per sample
- **Goal:** Identify genes whose expression levels best separate cancer subtypes

## Project Structure

gene-expression-analysis/
│
├── data/
│   ├── data.csv
│   └── labels.csv
├── images/
│   ├── class_distribution.png
│   ├── top20_gene_distributions.png
│   ├── top20_gene_boxplots.png
│   └── top20_gene_correlation_matrix.png
├── notebook/
│   └── notebook.ipynb
├── requirements.txt
└── README.md

## Methodology

1. Loaded and merged gene expression features with cancer type labels
2. Audited for missing values — none found
3. Explored class distribution across 5 cancer subtypes
4. Applied **variance filtering** to select the top 20 most variable genes
5. Analyzed univariate distributions of selected genes
6. Compared gene expression across cancer types using boxplots
7. Computed a correlation matrix among the top 20 genes

## Key Findings

- The dataset is **class imbalanced** — BRCA (Breast Cancer) accounts for 37.5% of samples 
(300/801), reflecting historical prioritization in TCGA data collection
- Gene expression distributions are predominantly **right-skewed**, consistent with typical 
RNA-seq data where most genes are lowly expressed
- Several genes show **bimodal distributions**, suggesting distinct expression subgroups 
across cancer types
- Genes **3541**, **3540**, and **15897** show the strongest separation across cancer types 
in group comparison analysis — strong biomarker candidates
- **BRCA** consistently displays the most distinct expression profile compared to other 
cancer types
- Genes **4773**, **8891**, and **15301** form a tightly correlated cluster (*r = 0.99*), 
suggesting shared regulatory mechanisms or pathway membership
- **Gene 19661** shows a strong negative correlation with this cluster (*r* = −0.93), 
potentially reflecting competing regulatory mechanisms — a phenomenon commonly observed 
in cancer biology

### Open Research Question
> What are genes 4773, 8891, 15301, and 19661, and which biological pathways do they 
belong to? Could their expression levels serve as diagnostic biomarkers?

## Visualizations

### Cancer Type Distribution
![Class Distribution](images/class_distribution.png)

### Top 20 Gene Expression Distributions
![Gene Distributions](images/top20_gene_distributions.png)

### Gene Expression by Cancer Type (Boxplots)
![Boxplots](images/top20_gene_boxplots.png)

### Correlation Matrix — Top 20 Genes
![Correlation Matrix](images/top20_gene_correlation_matrix.png)

## How to Run

1. Clone the repository:
```bash
   git clone https://github.com/Mesolongo/gene-expression-eda.git
   cd gene-expression-eda
```
2. Install dependencies:
```bash
   pip install -r requirements.txt
```
3. Open the notebook:
```bash
   jupyter notebook notebook/notebook.ipynb
```

## Tools Used

- **Python 3**
- **Pandas** — data loading, merging, and manipulation
- **Matplotlib** — plot layout and customization
- **Seaborn** — statistical visualizations
- **ucimlrepo** — dataset retrieval from UCI ML Repository
- **Jupyter Notebook** — interactive analysis environment

**Note:** Data files are not included due to size. Download them directly from 
[UCI ML Repository](https://archive.ics.uci.edu/dataset/401/gene+expression+cancer+rna+seq)
or load via `ucimlrepo` as shown in the notebook.