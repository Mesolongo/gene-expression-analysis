# Gene Expression in Cancer — RNA-Seq Analysis

## Overview

This repository contains three complementary analyses of RNA-seq gene expression data
from The Cancer Genome Atlas (TCGA), exploring whether gene expression patterns can
distinguish between different cancer types and identify candidate biomarker genes.

- **Dataset:** Gene Expression Cancer RNA-Seq — UCI ML Repository / TCGA
- **Samples:** 801 patients across 5 cancer types (BRCA, KIRC, LUAD, PRAD, COAD)
- **Features:** 20,531 gene expression values per sample
- **Goal:** Identify genes whose expression levels best separate cancer subtypes and
  build a machine learning model capable of classifying cancer types from gene expression profiles

---

## Project Structure

    gene-expression-analysis/
    │
    ├── data/
    │   ├── data.csv
    │   └── labels.csv
    ├── images/
    │   ├── class_distribution.png
    │   ├── feature_importances.png
    │   ├── top20_gene_distributions.png
    │   ├── top20_gene_boxplots.png
    │   └── top20_gene_correlation_matrix.png
    ├── notebooks/
    │   ├── notebook.ipynb
    │   ├── statistical_analysis.ipynb
    │   └── model.ipynb
    ├── stats_images/
    │   ├── distributions_gene1_gene3.png
    │   ├── dunn_posthoc_heatmap.png
    │   └── confidence_intervals_gene1.png
    ├── requirements.txt
    └── README.md

---

## Part 1 — Exploratory Data Analysis

### Methodology

1. Loaded and merged gene expression features with cancer type labels
2. Audited for missing values — none found
3. Explored class distribution across 5 cancer subtypes
4. Applied **variance filtering** to select the top 20 most variable genes
5. Analyzed univariate distributions of selected genes
6. Compared gene expression across cancer types using boxplots
7. Computed a correlation matrix among the top 20 genes

### Key Findings

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
- Genes **4773**, **8891**, and **15301** form a tightly correlated cluster (*r* = 0.99),
  suggesting shared regulatory mechanisms or pathway membership
- **Gene 19661** shows a strong negative correlation with this cluster (*r* = −0.93),
  potentially reflecting competing regulatory mechanisms — a phenomenon commonly observed
  in cancer biology

### Visualizations

#### Cancer Type Distribution
![Class Distribution](images/class_distribution.png)

#### Feature importances
![Feature importances](images/feature_importances.png)

#### Top 20 Gene Expression Distributions
![Gene Distributions](images/top20_gene_distributions.png)

#### Gene Expression by Cancer Type (Boxplots)
![Boxplots](images/top20_gene_boxplots.png)

#### Correlation Matrix — Top 20 Genes
![Correlation Matrix](images/top20_gene_correlation_matrix.png)

---

## Part 2 — Statistical Analysis

### Methodology

1. Computed descriptive statistics (mean, median, std, CV) for gene_1, gene_2, gene_3 per cancer type
2. Tested normality using Shapiro-Wilk test and visualised distributions with histograms and KDE curves
3. Applied Kruskal-Wallis test to assess whether gene_1 expression differs across cancer types
4. Performed Dunn's post-hoc test with Bonferroni correction to identify which pairs differ
5. Computed and visualised 95% confidence intervals per cancer type for gene_1

### Key Findings

- **gene_3** is remarkably stable across all cancer types (CV < 9%), suggesting it is not
  a strong differentiator between subtypes
- **gene_1** shows the highest variability (CV up to 47% in KIRC), making it the strongest
  candidate for group comparison
- Both gene_1 and gene_3 are **non-normally distributed** (Shapiro-Wilk, p < 0.05),
  justifying the use of non-parametric tests
- Kruskal-Wallis confirmed **significant differences in gene_1 expression across cancer
  types** (H=89.89, p=1.39e-18)
- Dunn's post-hoc test revealed that **KIRC differs significantly from every other cancer
  type** (all p < 0.05), while COAD, LUAD, and PRAD show no significant differences among
  themselves
- The 95% CI plot shows **zero overlap** between KIRC and all other groups — KIRC mean
  expression (≈2.40) sits approximately 1 full unit below COAD, LUAD, and PRAD

### Biological Interpretation

The consistent suppression of gene_1 expression in KIRC (Kidney Renal Clear Cell Carcinoma)
may reflect epigenetic silencing, metabolic reprogramming characteristic of renal cell
carcinoma, or tissue-of-origin transcriptional differences. These findings are statistically
robust but would require biological annotation and independent replication before clinical
conclusions can be drawn.

### Visualizations

#### Gene Expression Distributions (gene_1 and gene_3)
![Distributions](stats_images/distributions_gene1_gene3.png)

#### Dunn's Post-hoc Test — Bonferroni-corrected p-values
![Dunn Heatmap](stats_images/dunn_posthoc_heatmap.png)

#### 95% Confidence Intervals — gene_1 by Cancer Type
![Confidence Intervals](stats_images/confidence_intervals_gene1.png)

---

## Part 3 — Machine Learning Classification

### Methodology

1. Applied variance filtering to reduce features from 20,531 to 10,265 genes
2. Split data into 80% training and 20% test sets using stratified sampling
3. Scaled features using StandardScaler (fit on training data only)
4. Trained a Logistic Regression baseline model with `class_weight='balanced'`
5. Trained a Random Forest classifier with 100 estimators and `class_weight='balanced'`
6. Evaluated both models using accuracy, confusion matrix, and classification report
7. Extracted and visualised the top 10 most important genes from the Random Forest model
8. Compared both models using 5-fold cross-validation with macro-averaged F1-score

### Results

| Model | Test Accuracy | CV F1-Macro (Mean ± Std) |
|---|---|---|
| Logistic Regression | 0.9814 | 0.9950 ± 0.0044 |
| Random Forest | 0.9876 | 0.9938 ± 0.0051 |

### Key Findings

- **Logistic Regression was selected as the preferred model** — it achieved a higher
  cross-validation F1-macro mean (0.9950) and lower standard deviation (0.0044),
  indicating stronger generalisation and consistency across unseen data
- **Both models achieved above 98% test accuracy**, demonstrating that cancer type
  is highly predictable from gene expression profiles alone
- **Misclassification errors were biologically meaningful** — Logistic Regression
  confused COAD and LUAD (both digestive/lung origin), while Random Forest confused
  LUAD and BRCA (both adenocarcinomas sharing glandular epithelial tissue origin)
- **`gene_1` did not appear in the top 10 feature importances** despite being
  statistically significant in Part 2 — highlighting that statistical significance
  and classification utility are distinct concepts. A gene highly differentiated in
  one subtype (KIRC) contributes only one split in the forest, whereas the top-ranked
  genes help separate multiple cancer types simultaneously
- **`class_weight='balanced'`** successfully prevented bias toward the majority class
  (BRCA at 37.5%), with COAD — the smallest class at 9.7% — achieving an F1-score
  of 0.91 or above in both models

### Visualizations

#### Top 10 Feature Importances — Random Forest
![Feature Importances](images/feature_importances.png)

---

## How to Run

1. Clone the repository:

       git clone https://github.com/Mesolongo/gene-expression-analysis.git
       cd gene-expression-analysis

2. Install dependencies:

       pip install -r requirements.txt

3. Open the EDA notebook:

       jupyter notebook notebook/notebook.ipynb

4. Open the statistical analysis notebook:

       jupyter notebook notebook/statistical_analysis.ipynb

5. Open the machine learning notebook:

       jupyter notebook notebook/model.ipynb

> **Note:** Data files are not included due to size. Download them directly from
> [UCI ML Repository](https://archive.ics.uci.edu/dataset/401/gene+expression+cancer+rna+seq)

---

## Tools Used

- **Python 3**
- **Pandas** — data loading, merging, and manipulation
- **NumPy** — numerical computation
- **Matplotlib** — plot layout and customization
- **Seaborn** — statistical visualizations
- **SciPy** — statistical tests (Shapiro-Wilk, Kruskal-Wallis, confidence intervals)
- **scikit-posthocs** — Dunn's post-hoc test with Bonferroni correction
- **scikit-learn** — machine learning models, preprocessing, and evaluation
- **Jupyter Notebook** — interactive analysis environment