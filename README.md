
<!-- README.md is generated from README.Rmd. Please edit that file -->

# IOBR: Immuno-Oncology Bioinformatics Research

IOBR is an R package to perform comprehensive analysis of tumor
microenvironment and signatures for immuno-oncology.

## 1 Introduction and Installation

### 1.1 Introduction

  - 1.IOBR integrates 8 published methodologies decoding tumor
    microenvironment (TME) contexture: `CIBERSORT`, `TIMER`, `xCell`,
    `MCPcounter`, `ESITMATE`, `EPIC`, `IPS`, `quanTIseq`;
  - 2.IOBR collects 255 published signature gene sets, involving tumor
    microenvironment, tumor metabolism, m6A, exosomes, microsatellite
    instability, and tertiary lymphoid structure. Running the function
    `signature_collection_citation` to attain the source papers. The
    function `signature_collection` returns the detail signature genes
    of all given signatures.
  - 3.IOBR adopts three computational methods to calculate the signature
    score, comprising `PCA`,`z-score`, and `ssGSEA`;
  - 4.IOBR integrates multiple approaches for variable transition,
    visualization, batch survival analysis, feature selection, and
    statistical analysis.
  - 5.IOBR also integrates methods for batch visualization of subgroup
    characteristics.

![IOBR logo](./man/figures/IOBR-Workflow.png)

### 1.2 Installation

Before installing IOBR, please install all dependencies by executing the
following command in R console:

The dependencies includs `tibble`, `survival`, `survminer`, `limma`,
`limSolve`, `GSVA`, `e1071`, `preprocessCore`, `ggplot2` and `ggpubr`.

``` r
options("repos"= c(CRAN="https://mirrors.tuna.tsinghua.edu.cn/CRAN/"))
options(BioC_mirror="http://mirrors.tuna.tsinghua.edu.cn/bioconductor/")
if (!requireNamespace("BiocManager", quietly = TRUE)) install("BiocManager")
depens<-c('tibble', 'survival', 'survminer', 'sva', 'limma', "DESeq2","devtools",
          'limSolve', 'GSVA', 'e1071', 'preprocessCore', 'ggplot2', "biomaRt",
          'ggpubr', "devtools", "tidyHeatmap", "caret", "glmnet", "ppcor","timeROC","pracma")
for(i in 1:length(depens)){
  depen<-depens[i]
  if (!requireNamespace(depen, quietly = TRUE))
    BiocManager::install(depen)
}

if (!requireNamespace("EPIC", quietly = TRUE))
  devtools::install_github("GfellerLab/EPIC", build_vignettes=TRUE)
if (!requireNamespace("MCPcounter", quietly = TRUE))
  devtools::install_github("ebecht/MCPcounter",ref="master", subdir="Source")
if (!requireNamespace("estimate", quietly = TRUE)){
  rforge <- "http://r-forge.r-project.org"
  install.packages("estimate", repos=rforge, dependencies=TRUE)
}
```

The package is not yet on CRAN. You can install it from Github:

``` r
if (!requireNamespace("IOBR", quietly = TRUE))
  devtools::install_github("DongqiangZeng0808/IOBR",ref="master")
```

## 1.3 Library R packages

``` r
library(IOBR) 
library(EPIC)
library(estimate) 
library(MCPcounter)
Sys.setenv(LANGUAGE = "en") # The error message would be present in English.
options(stringsAsFactors = FALSE) #To avoid transiting character into factor.
```

![IOBR logo](./man/figures/IOBR-Package.png)

## 1.4 Availabie methods to decode TME contexture

``` r
tme_deconvolution_methods
#>         MCPcounter               EPIC              xCell          CIBERSORT 
#>       "mcpcounter"             "epic"            "xcell"        "cibersort" 
#> CIBERSORT Absolute                IPS           ESTIMATE                SVM 
#>    "cibersort_abs"              "ips"         "estimate"          "svm_ref" 
#>               lsei              TIMER          quanTIseq 
#>         "lsei_ref"            "timer"        "quantiseq"
# Return available parameter options of deconvolution methods
```

If you use this package in your work, please cite both our package and
the method(s) you are using.

#### Licenses of the deconvolution methods

| method                                                                      | license                                                                                                       | citation                                                                                                                                                                                                                                                                                                 |
| --------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [CIBERSORT](https://cibersort.stanford.edu/)                                | free for non-commerical use only                                                                              | Newman, A. M., Liu, C. L., Green, M. R., Gentles, A. J., Feng, W., Xu, Y., … Alizadeh, A. A. (2015). Robust enumeration of cell subsets from tissue expression profiles. Nature Methods, 12(5), 453–457. <https://doi.org/10.1038/nmeth.3337>                                                            |
| [ESTIMATE](https://bioinformatics.mdanderson.org/public-software/estimate/) | free ([GPL2.0](https://bioinformatics.mdanderson.org/estimate/))                                              | Vegesna R, Kim H, Torres-Garcia W, …, Verhaak R. (2013). Inferring tumour purity and stromal and immune cell admixture from expression data. Nature Communications 4, 2612. <http://doi.org/10.1038/ncomms3612>                                                                                          |
| [quanTIseq](http://icbi.at/software/quantiseq/doc/index.html)               | free ([BSD](https://github.com/icbi-lab/immunedeconv/blob/master/LICENSE.md))                                 | Finotello, F., Mayer, C., Plattner, C., Laschober, G., Rieder, D., Hackl, H., …, Sopper, S. (2019). Molecular and pharmacological modulators of the tumor immune contexture revealed by deconvolution of RNA-seq data. Genome medicine, 11(1), 34. <https://doi.org/10.1186/s13073-019-0638-6>           |
| [TIMER](http://cistrome.org/TIMER/)                                         | free ([GPL 2.0](http://cistrome.org/TIMER/download.html))                                                     | Li, B., Severson, E., Pignon, J.-C., Zhao, H., Li, T., Novak, J., … Liu, X. S. (2016). Comprehensive analyses of tumor immunity: implications for cancer immunotherapy. Genome Biology, 17(1), 174. <https://doi.org/10.1186/s13059-016-1028-7>                                                          |
| [IPS](https://github.com/icbi-lab/Immunophenogram)                          | free ([BSD](https://github.com/icbi-lab/Immunophenogram/blob/master/LICENSE))                                 | P. Charoentong et al., Pan-cancer Immunogenomic Analyses Reveal Genotype-Immunophenotype Relationships and Predictors of Response to Checkpoint Blockade. Cell Reports 18, 248-262 (2017). <https://doi.org/10.1016/j.celrep.2016.12.019>                                                                |
| [MCPCounter](https://github.com/ebecht/MCPcounter)                          | free ([GPL 3.0](https://github.com/ebecht/MCPcounter/blob/master/Source/License))                             | Becht, E., Giraldo, N. A., Lacroix, L., Buttard, B., Elarouci, N., Petitprez, F., … de Reyniès, A. (2016). Estimating the population abundance of tissue-infiltrating immune and stromal cell populations using gene expression. Genome Biology, 17(1), 218. <https://doi.org/10.1186/s13059-016-1070-5> |
| [xCell](http://xcell.ucsf.edu/)                                             | free ([GPL 3.0](https://github.com/dviraran/xCell/blob/master/DESCRIPTION))                                   | Aran, D., Hu, Z., & Butte, A. J. (2017). xCell: digitally portraying the tissue cellular heterogeneity landscape. Genome Biology, 18(1), 220. <https://doi.org/10.1186/s13059-017-1349-1>                                                                                                                |
| [EPIC](https://gfellerlab.shinyapps.io/EPIC_1-1/)                           | free for non-commercial use only ([Academic License](https://github.com/GfellerLab/EPIC/blob/master/LICENSE)) | Racle, J., de Jonge, K., Baumgaertner, P., Speiser, D. E., & Gfeller, D. (2017). Simultaneous enumeration of cancer and immune cell types from bulk tumor gene expression data. ELife, 6, e26476. <https://doi.org/10.7554/eLife.26476>                                                                  |

#### Licenses of the signature-esitmation method

| method                                                                   | license                                                | citation                                                                                                                                                                                                              |
| ------------------------------------------------------------------------ | ------------------------------------------------------ | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [GSVA](http://www.bioconductor.org/packages/release/bioc/html/GSVA.html) | free ([GPL (\>= 2)](https://github.com/rcastelo/GSVA)) | Hänzelmann S, Castelo R, Guinney J (2013). “GSVA: gene set variation analysis for microarray and RNA-Seq data.” BMC Bioinformatics, 14, 7. doi: 10.1186/1471-2105-14-7, <http://www.biomedcentral.com/1471-2105/14/7> |

## References

DQ Zeng, ZL Ye, RF Shen, Y Xiong, JN Wu, WJ Qiu WJ Liao.

IOBR: Multi-omics Immuno-Oncology Biological Research to decode tumor
microenvironment and signatures.

## Contact

E-mail any questions to <dongqiangzeng0808@gmail.com>
