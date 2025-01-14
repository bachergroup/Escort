### Introduction

Welcome to shinyEscort, a user-friendly Shiny application designed to help researchers in constructing more accurate trajectories on their single-cell RNA-seq data. With shinyEscort, you input your original data and normalized data, and the application walks you through our evaluation framework.

### Getting started
In R, assuming you have successfully downloaded Escort, then you can start the Shiny app locally by running the following:

```r
library(Escort)
library(shiny)

shinyEscort()
```

If you encounter any issues, please report these to our Github: [Report Issues](https://github.com/xiaorudong/Escort/issues)

### About

An overview of shinyEscort and the Escort workflow is provided in the "About" tab positioned at the top of the left column sidebar.

![Screenshot of the entry page](shiny_about.png)


### Step 1
#### Input:
To initiate the analysis assessing a dataset's evidence of a trajectory signal, click on the "Step 1" button in the left column sidebar. Input must include raw and normalized single-cell RNA-seq datasets in .csv format. Then click the “Import” button. Data should have undergone basic quality control removing low quality cells, doublets, and empty droplets. 

For hands-on practice, example datasets are stored in the data directory of the R package featuring simulated scenarios such as homogeneous data, datasets with distinct cluster types, and linear topology datasets. In this demonstration, we utilize "rawcount_linear_splatter.csv" and "normcount_linear_splatter.csv" which showcases a simulated scRNA-seq data with a linear structure.

#### Output:

**The number of cells and genes** <br/>
Internally, Escort checks the compatibility of normalized and raw dataset to ensure they originate from the same source. If they are consistent, the number of genes and cells will be displayed. In cases where the datasets do not align or if there is any missing input data, an "NA" will be displayed in the number of genes and cells. Users should re-examine whether they input the correct datasets.<br/>

**UMAP and t-SNE representations** <br/>
On the right side, UMAP and t-SNE representations provide a visual representation of the datasets. <br/>

**Existence of trajectory** <br/>
Two scenarios considered inappropriate for trajectory inference include cells from biologically distinct clusters and sets of cells with insufficient heterogeneity. These scenarios are evaluated in the result boxes labeled "Distinct cell types" and "Homogeneous cells." The determination of trajectory existence is displayed above the UMAP and t-SNE representations.

***Diverse cell types***
In instances where the dataset fails this evaluation, detecting distinct clusters triggers a subsequent differential expression analysis utilizing the edgeR R package indicating cluster-specific genes. We display the top 30 differentially expressed genes between pairwise clusters. Users should examine whether fitting a trajectory that connects these cell types is biologically reasonable. If so, then users should re-examine whether intermediate cell types exist, presence of batch effects, and choice of normalization method.

***Homogeneous cells***
For datasets demonstrating too little heterogeneity, a list of highly variable genes is presented in the "HVGs" subtab, followed by a Gene Ontology (GO) enrichment analysis, and the results are presented within the "GO" subtab. Again, the appropriateness of an underlying trajectory should be considered. To proceed, users should investigate whether other processes could be overriding the biological signal of interest (e.g. cell cycle) or if excessive signal from ribosomal or mitochondrial gene is present. Any upstream pre-processing may also need to be re-examined or eliminated. 

![Screenshot of Step 1](shiny_step1.png)


### Generate embeddings

#### Input:

Custom embeddings for evaluation can be generated in this tab. For now, we offer the selection of a dimension reduction technique (UMAP, MDS, PCA, or TSNE) and the number of highly variable genes. By default, Escort utilizes Slingshot for the preliminary trajectory fitting that takes place in Step 3. Users may also generate their own embedding options and skip this tab. How do to so is described in the [GGenerating embeddings in R to use in the Shiny app.](https://rbacher.rc.ufl.edu/escort/docs-escort/vignettes.html)

#### Output:

**Visualization** <br/>
Visualize the embedding and trajectory on the right.<br/>

**Download .rds** <br/>
Click "Download" to save the embedding as an .rds file that can be uploaded in the next step.

![Screenshot of generating any embedding](shiny_dr.png)

### Step 2:

#### Input:
Next, in Step 2, users upload any .rds files generated in the prior step (or generated on their own). In this demonstration, we upload six .rds files that were generated, as shown in the accompanying screenshot.

#### Output:
Escort evaluates embeddings based on inter-cellular relationships, preservation of similarity relationships, and cell density.
Results are presented in three tables. <br/>

**Inter-cellular relationships** <br/>
Evaluates cell connectivity on the embedding. A result of "TRUE" followed by a checkmark "✓" indicates the absence of disconnected clusters in the embedding. <br/>

**Preservation of similarity relationships** <br/>
Determines the preservation score for similarity relationships, reflecting the percentage of cells exhibiting a high level of similarity. A higher value indicates a greater number of cells performing well in preserving similarity relationships. <br/>

**Cell density** <br/>
Examines the distribution of cells in the embedding. A higher value indicates a more uniform distribution in the two-dimensional embedding space. This uniformity poses challenges for trajectory inference methods in identifying a robust trajectory.

![Screenshot of Step 2](shiny_step2.png)

### Step 3

#### Input:
No manual input required; calculations are performed automatically once Step 2 begins.

#### Output:
Utilizing the embedding-specific preliminary trajectory, Escort estimates the proportion of cells with ambiguous projections along the trajectory, displaying the results in a table. A higher value indicates more ambiguous cells in the embeddings.
![Screenshot of Step 3](shiny_step3.png)


### Summary

#### Input:
No manual input required; calculations are performed automatically once Step 2 begins.

#### Output:
**Evaluation Table** <br/>
Escort uses a standardized benchmark scoring system ranging from negative infinity to two. Higher scores indicate better performance. Embeddings with a score greater than zero are recommended, while those with a score less than or equal to zero are considered non-recommended. Users can easily identify optimal embedding choices for constructing a trajectory through this scoring system. The conclusive results, featuring embeddings and trajectories, are presented in a table sorted by score. <br/>

**Representations** <br/>
A visual representation of the top 6 highest-scoring embeddings and trajectories is shown below the table.
![screen shot of conclusion](shiny_conclusion.png)
