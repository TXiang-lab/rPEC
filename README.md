# rPEC <img src="https://img.shields.io/badge/Issues-%2B-brightgreen.svg" /><img src="https://img.shields.io/badge/license-GPL3.0-blue.svg" />    
### R package for pedigree correction in animal breeding
## Contents

-   [Overview](#overview)
-   [Installation](#installation)
-   [Data preparation](#Data-preparation)
-   [Usage](#usage)
-   [Output](#Output)

------------------------------------------------------------------------
### <u>Overview</u>

`rPEC` is a useful and user-friendly tool for pedigree correction in animal breeding, Especially when users doubt the accuracy of the pedigree records.  

 The core functions of this package are coded by c++ (`Rcpp` and `RcppArmadillo `), and the package supports multi-threaded tasks, which improves running speed.

Users only need to provide the **haplotype file** in **VCF** or **hap and sample** format, **target individuals' id** and **candidate individuals' id**, which are included the haplotype file.

### <u>Installation</u>

`rPEC` links to R packages `Rcpp`, `RcppArmadillo`. These dependencies should be installed before installing `rPEC`. 

```R
install.packages(c("Rcpp", "RcppArmadillo"))
```

#### Install rPEC on Linux 

```R
packageurl <- "https://github.com/TXiang-lab/rPEC/raw/main/0.1.0/rPEC_0.1.0_R_x86_64-pc-linux-gnu.tar.gz"
install.packages(packageurl,repos=NULL,method="libcurl")
```

#### Install rPEC on Linux for domestic user

```R
packageurl <- "https://gitee.com/qsmei/blupADC/attach_files/828967/download/rPEC_0.1.0_R_x86_64-pc-linux-gnu.tar.gz"
install.packages(packageurl,repos=NULL,method="libcurl")
```

#### Install rPEC on Windows

```R
packageurl <- "https://github.com/TXiang-lab/rPEC/raw/main/0.1.0/rPEC_0.1.0.zip"
install.packages(packageurl,repos=NULL)
```

#### Install rPEC on Windows for domestic user

```R
packageurl <- "https://gitee.com/qsmei/blupADC/attach_files/828968/download/rPEC_0.1.0.zip"
install.packages(packageurl,repos=NULL)
```

After installed successfully, the `rPEC` package can be loaded by typing

``` {.r}
library(rPEC)
```

### <u>Data preparation</u>

Users need to provide haplotype data in **VCF** or **hap and sample** format.

#### VCF

Genotype data in VCF format is recommended to be phased by software **Beagle5.2** or **Eagle2.4**.

``` R
##fileformat=VCFv4.2
##filedate=20210627
##source="beagle.29May21.d6d.jar"
##INFO=<ID=AF,Number=A,Type=Float,Description="Estimated ALT Allele Frequencies">
##INFO=<ID=DR2,Number=A,Type=Float,Description="Dosage R-Squared: estimated squared correlation between estimated REF dose [P(RA) + 2*P(RR)] and true REF dose">
##INFO=<ID=IMP,Number=0,Type=Flag,Description="Imputed marker">
##FORMAT=<ID=GT,Number=1,Type=String,Description="Genotype">
##FORMAT=<ID=DS,Number=A,Type=Float,Description="estimated ALT dose [P(RA) + 2*P(AA)]">
#CHROM  POS     ID      REF     ALT     QUAL    FILTER  INFO    FORMAT  40122   40123   40126   40127  
1       6260    M2      T       A       .       PASS    .       GT      1|0     0|1     1|0     0|1   
1       152890  M17     A       T       .       PASS    .       GT      0|0     0|0     0|0     0|0
1       217560  M24     A       T       .       PASS    .       GT      1|0     0|1     1|0     0|1  
1       282580  M30     A       T       .       PASS    .       GT      0|1     1|0     0|1     1|0   
1       298030  M33     T       A       .       PASS    .       GT      0|1     1|0     0|1     1|0 
1       312770  M36     T       A       .       PASS    .       GT      0|0     1|0     0|0     0|0  
1       322090  M37     T       A       .       PASS    .       GT      0|1     1|0     0|1     1|0  
1       431960  M46     T       A       .       PASS    .       GT      0|1     1|0     0|1     1|0 
1       566310  M53     T       A       .       PASS    .       GT      1|0     0|1     1|0     0|1 
```

#### hap and sample

This format can be transformed from haplotype data in **VCF** format using software **BCFtools**, which is running faster in **rPEC** than **VCF** format. The command in Linux is

``` {.r}
bcftools convert --hapsample --vcf-ids test_phase.vcf -o output
```

**hap**

``` {.r}
1 1:6260_T_A 6260 T A 1 0 0 1 1 0 0 1 1 0 0 0 0 1 1 0 0 0 0 0 0 1 1 0 0 1 0 0 0 1 1 0 0 1 1 
1 1:152890_A_T 152890 A T 0 0 0 0 0 0 0 0 0 0 0 1 0 0 0 0 0 1 1 0 0 0 0 0 0 0 0 1 0 0 0 0 0 
1 1:217560_A_T 217560 A T 1 0 0 1 1 0 0 1 1 0 0 1 0 1 1 0 0 1 1 0 0 1 1 0 0 1 0 1 0 1 1 1 1 
1 1:282580_A_T 282580 A T 0 1 1 0 0 1 1 0 0 1 1 0 1 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 1 0 0 0 0
1 1:298030_T_A 298030 T A 0 1 1 0 0 1 1 0 0 1 1 0 1 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 
1 1:312770_T_A 312770 T A 0 0 1 0 0 0 0 0 0 0 0 0 1 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
1 1:322090_T_A 322090 T A 0 1 1 0 0 1 1 0 0 1 1 0 1 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 
1 1:431960_T_A 431960 T A 0 1 1 0 0 1 1 0 0 1 1 0 1 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 
1 1:566310_T_A 566310 T A 1 0 0 1 1 0 0 1 1 0 0 0 0 1 1 0 0 0 0 0 0 1 1 0 0 1 0 0 0 1 1 0 0
```

**sample**

``` {.r}
ID_1 ID_2 missing
0 0 0
40122 40122 0
40123 40123 0
40126 40126 0
40127 40127 0
40128 40128 0
40130 40130 0
40133 40133 0
```

## <u>Usage</u>

#### Feature 1.  input file as VCF format

``` R
library(rPEC)
valid_id_file=c("47487","47637", "47687", "47897", "47941" ,"47948" ) ###vector of target individuals' id
train_id_file=c("40896" ,"40914", "44376","46860" ,"46943" ,"46943")  ###vector of candidate individuals' id
   result=PEC(vcf_file_name="example.vcf",               ###VCF file name
              valid_id=valid_id_file,                       ###target individuals' id name         
			  train_id=train_id_file,                       ###candidate individuals' id name
			  return_result=TRUE,                           ###return result
			  output_file_name="match_parent",              ###output file name
			  win_size=10,                                  ###window size
			  threads=30)                                   ###threads
```

#### Feature 2.  input file as hap and sample format

``` R
library(rPEC)
   result=PEC(hap_file_name="example.hap",           ##hap file name
			  sample_file_name="example.sample",     ##sample file name
              valid_id=valid_id_file,
			  train_id=train_id_file,
			  return_result=TRUE,
			  output_file_name="match_parent",
			  win_size=10,
			  threads=30)                     
```

## <u>Output</u>

result$window_number returns the number of window that candidate individuals matched target individuals.

result$parent_order returns the order  in which candidate individuals matched target individuals.

The candidate individual which matches the target individual to the most  number of window is regarded as the parent.

``` R
str(result)
List of 2
 $ window_number: num [1:6, 1:6] 3283 2346 2170 2467 2009 ...
  ..- attr(*, "dimnames")=List of 2
  .. ..$ : chr [1:6] "47487" "47637" "47687" "47897" ...
  .. ..$ : chr [1:6] "40896" "40914" "44376" "46860" ...
 $ parent_order : chr [1:6, 1:6] "40896" "40914" "44376" "46860" ...
  ..- attr(*, "dimnames")=List of 2
  .. ..$ : chr [1:6] "47487" "47637" "47687" "47897" ...
  .. ..$ : chr [1:6] "1" "2" "3" "4" ...
```

match_parent.txt

``` R
x
47487 40896
47637 40914
47687 44376
47897 46860
47941 46943
47948 46943
```

The first column is target individuals' id, the second is the parents' id. 
