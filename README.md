# rPEC <img src="https://img.shields.io/badge/Issues-%2B-brightgreen.svg" /><img src="https://img.shields.io/badge/license-GPL3.0-blue.svg" />    
### R package for pedigree correction in animal breeding
## Contents

-   [Overview](#overview)
-   [Installation](#installation)
-   [Update](#Update)
-   [Data preparation](#Data-preparation)
-   [Usage](#usage)
-   [Output](#Output)

------------------------------------------------------------------------
### <u>Overview</u>

`rPEC` is a useful and user-friendly tool for pedigree correction in animal breeding, Especially when users doubt the accuracy of the pedigree records.  

The core functions of this package are coded by c++ (`Rcpp` and `RcppArmadillo `), and the package supports multi-threaded tasks, which improves running speed.

Users are supported to provide two kinds of genotype files. the first one is the **haplotype file** in **VCF** format, which has been  phased by the phasing software users chose by themselves. The second one is the **genotype file without phasing**, which can straightly be phased in `rPEC`. 

**Target individuals' id** and **candidate individuals' id**, which should also be provided by users, need be included in the genotype file.

### <u>Update</u>

#### 0.2.0

- add the function of  phasing genotype file, which is related to  R package `blupADC` and the phasing function of it is supported by  software **Beagle5.2** (2021.11.22.)

### <u>Installation</u>

`rPEC` links to R packages `Rcpp`, `RcppArmadillo`. These dependencies should be installed before installing `rPEC`. 

```R
install.packages(c("Rcpp", "RcppArmadillo"))
```

The function of phasing genotype file of `rPEC` is related to R package `blupADC` , which can be installed in the linkage below:

https://github.com/TXiang-lab/blupADC#features

(If users provide the **haplotype file** in **VCF** format and don't  use the function of phasing genotype file of `rPEC`, then don't need to install R package `blupADC`)

Note that the R package `rPEC` can only be installed on R version 4.0 

#### Install rPEC on Linux 

```R
packageurl <- "https://github.com/TXiang-lab/rPEC/blob/main/binary/0.2.0/linux-R-4.0/rPEC_0.2.0_R_x86_64-pc-linux-gnu.tar.gz"
install.packages(packageurl,repos=NULL,method="libcurl")
```

#### Install rPEC on Linux for domestic user

```R
packageurl <- "https://gitee.com/chuankefu/r-pec/attach_files/887621/download/rPEC_0.2.0_R_x86_64-pc-linux-gnu.tar.gz"
install.packages(packageurl,repos=NULL,method="libcurl")
```

#### Install rPEC on Windows

```R
packageurl <- "https://github.com/TXiang-lab/rPEC/blob/main/binary/0.2.0/win-R-4.0/rPEC_0.2.0.zip"
install.packages(packageurl,repos=NULL)
```

#### Install rPEC on Windows for domestic user

```R
packageurl <- "https://gitee.com/chuankefu/r-pec/attach_files/887622/download/rPEC_0.2.0.zip"
install.packages(packageurl,repos=NULL)
```

After installed successfully, the `rPEC` package can be loaded by typing

``` {.r}
library(rPEC)
```

### <u>Data preparation</u>

#### 🌈The first kind of genotype file is haplotype file in VCF format

#### ① VCF

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

PLUS: 

Download linkage of **Beagle5.2** 

https://faculty.washington.edu/browning/beagle/beagle.html#download

command of phasing by software **Beagle5.2** 

``` {.r}
java -Xmx50g -jar beagle.5.2.jar gt=genotype_file.vcf out=out_put_file impute=true nthreads=10
```

Download linkage of **Eagle2.4**

https://alkesgroup.broadinstitute.org/Eagle/downloads/

command of phasing by software **Eagle2.4**

``` {.r}
eagle \
 --vcf=genotype_file.vcf \
 --chrom 1 \   ##which chrom to phase, here is chrom 1
 --outPrefix=out_put_file \
 --geneticMapFile=Eagle_v2.4.1/tables/genetic_map_1cMperMb.txt \   ##genetic map file provided by Eagle2.4
 --numThreads=30 \
 --vcfOutFormat=v \
 2>&1 | tee chrom18.log
```

#### 🌈The second kind of genotype file is genotype file without phasing

#### ① VCF

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
1       6260    M2      T       A       .       PASS    .       GT      1/0     0/1     1/0     0/1   
1       152890  M17     A       T       .       PASS    .       GT      0/0     0/0     0/0     0/0
1       217560  M24     A       T       .       PASS    .       GT      1/0     0/1     1/0     0/1  
1       282580  M30     A       T       .       PASS    .       GT      0/1     1/0     0/1     1/0   
1       298030  M33     T       A       .       PASS    .       GT      0/1     1/0     0/1     1/0 
1       312770  M36     T       A       .       PASS    .       GT      0/0     1/0     0/0     0/0  
1       322090  M37     T       A       .       PASS    .       GT      0/1     1/0     0/1     1/0  
1       431960  M46     T       A       .       PASS    .       GT      0/1     1/0     0/1     1/0 
1       566310  M53     T       A       .       PASS    .       GT      1/0     0/1     1/0     0/1 
```

#### ② Plink (map&ped)

**ped**

The first column stands for family name, the second column stands for the individual name, the seventh column and the after columns stand for the genotype data

``` R
47487	47487	0	0	0	0	T	T	A	A	A	A	T	T	A	A	T	T	A	A
47637	47637	0	0	0	0	T	T	A	A	A	A	A	T	T	A	T	T	T	A
47687	47687	0	0	0	0	T	A	A	A	A	T	T	A	A	T	T	T	A	T
47897	47897	0	0	0	0	T	T	A	A	T	A	A	T	T	A	T	T	T	A
47941	47941	0	0	0	0	T	T	A	A	A	A	T	A	A	T	T	T	A	T
47948	47948	0	0	0	0	T	T	A	A	A	A	T	A	A	T	T	T	A	T
40896	40896	0	0	0	0	T	T	A	A	A	A	T	A	A	T	T	T	A	T
```

**map**

The first column stands for chromosome, the second column stands for the name of SNP, the third column stands for the genetic position (CM), and the fourth column stands for the physical position

``` R
1	M2	0.00626	6260
1	M17	0.15289	152890
1	M24	0.21756	217560
1	M30	0.28258	282580
1	M33	0.29803	298030
1	M36	0.31277	312770
1	M37	0.32209	322090
```

#### ③ Hapmap

The first column stands for the name of SNP, the thrid column stands for chromosome, the fourth column stands for the physical postion, and the twelth column and the after columns stand for the genotype data

``` R
rs#	alleles	chrom	pos	strand	assembly	center	protLSID	assayLSID	panelLSID	QCcode	47487	
M2	NA	1	6260	NA	NA	NA	NA	NA	NA	NA	TT	
M17	NA	1	152890	NA	NA	NA	NA	NA	NA	NA	AA	
M24	NA	1	217560	NA	NA	NA	NA	NA	NA	NA	AA	
M30	NA	1	282580	NA	NA	NA	NA	NA	NA	NA	TT	
M33	NA	1	298030	NA	NA	NA	NA	NA	NA	NA	AA	
M36	NA	1	312770	NA	NA	NA	NA	NA	NA	NA	TT	
M37	NA	1	322090	NA	NA	NA	NA	NA	NA	NA	AA	
M46	NA	1	431960	NA	NA	NA	NA	NA	NA	NA	AA	
M53	NA	1	566310	NA	NA	NA	NA	NA	NA	NA	TT	
M63	NA	1	627770	NA	NA	NA	NA	NA	NA	NA	TT	
M79	NA	1	769890	NA	NA	NA	NA	NA	NA	NA	TT	
M82	NA	1	778900	NA	NA	NA	NA	NA	NA	NA	TT	
M89	NA	1	866390	NA	NA	NA	NA	NA	NA	NA	AA	
```

#### ④ BLUPF90

The first column stands for individual name, the second column stands for the genotype data (numeric)

``` R
47487	0002202200020
47637	0001101110011
47687	1011101110011
47897	0011101100010
47941	0001101110011
47948	0001101110011
40896	0001101110010
40914	0000000010000
```

**map**（if input genotype file is in **BLUPF90** format, users need to provide a map file)

The first column stands for chromosome, the second column stands for the name of SNP, the third column stands for the physical position, and the fourth column and the fifth column stand for the reference allele and alternative allele respectively

``` R
1 M2 6260 T A
1 M17 152890 A T
1 M24 217560 A T
1 M30 282580 A T
1 M33 298030 T A
1 M36 312770 T A
1 M37 322090 T A
1 M46 431960 T A
1 M53 566310 T A
1 M63 627770 T A
1 M79 769890 T A
1 M82 778900 A T
1 M89 866390 A T
```

#### ⑤ Numeric

``` R
47487	0	0	0	2	2	0	2	2	0	0	0	2	0	
47637	0	0	0	1	1	0	1	1	1	0	0	1	1
47687	1	0	1	1	1	0	1	1	1	0	0	1	1
47897	0	0	1	1	1	0	1	1	0	0	0	1	0
47941	0	0	0	1	1	0	1	1	1	0	0	1	1
47948	0	0	0	1	1	0	1	1	1	0	0	1	1
40896	0	0	0	1	1	0	1	1	1	0	0	1	0
40914	0	0	0	0	0	0	0	0	1	0	0	0	0
```

**map**（if input genotype file is in **Numeric** format, users need to provide a map file)

``` R
1 M2 6260 T A
1 M17 152890 A T
1 M24 217560 A T
1 M30 282580 A T
1 M33 298030 T A
1 M36 312770 T A
1 M37 322090 T A
1 M46 431960 T A
1 M53 566310 T A
1 M63 627770 T A
1 M79 769890 T A
1 M82 778900 A T
1 M89 866390 A T
```

## <u>Usage</u>

#### 🌈Input file as haplotype file in VCF format

``` R
library(rPEC)
setwd(system.file("exampledata", package = "rPEC"))         ###path of example
getwd()                                           ###the path of work directory
target_id_file=c("47487","47637", "47687", "47897", "47941" ,"47948" ) 
candidate_id_file=c("40896" ,"40914", "44376","46860" ,"46943" ,"46943")  
result=run_PEC(
        input_file_name="example",                 ###input file name, no suffix
        input_file_path=getwd(),                   ###input file path
        input_file_type="VCF",                     ###input file type
        phasing=FALSE,                             ###whether input file need to phase        
        target_id=target_id_file,                  ###id of target individuals
        candidate_id=candidate_id_file,            ###id of candidate individuals        
        return_result=TRUE,                        ###return result in R or not 
        output_file_name="match_parent",           ###output file name
        output_file_path=getwd(),    	           ###output file path
        win_size=10,                               ###window size
        threads=16)                                ###threads
```



#### 🌈Input file as genotype file without phasing

``` R
library(rPEC)
setwd(system.file("exampledata", package = "rPEC"))         ###path of example
getwd()                                           ###the path of work directory
target_id_file=c("47487","47637", "47687", "47897", "47941" ,"47948" ) 
candidate_id_file=c("40896" ,"40914", "44376","46860" ,"46943" ,"46943")  
result=run_PEC(
        input_file_name="example.hmp.txt",         ###input file name, no suffix
        input_file_path=getwd(),                   ###input file path
        input_file_type="Hapmap",                  ###input file type
        phasing=TRUE,                              ###whether input file need to phase        
        target_id=target_id_file,                  ###id of target individuals
        candidate_id=candidate_id_file,            ###id of candidate individuals        
        return_result=TRUE,                        ###return result in R or not 
        output_file_name="match_parent",           ###output file name
        output_file_path=getwd(),    	           ###output file path
        win_size=10,                               ###window size
        threads=16)                                ###threads
```

**input_file_type and suffix of input_file_name**

| input_file_type（character） | input_file_name（character） | suffix |
| ---------------------------- | ---------------------------- | ------ |
| Hapmap                       | example.hmp.txt              | yes    |
| Plink                        | example                      | no     |
| BLUPF90                      | example.blupf90              | yes    |
| Numeric                      | example.numeric              | yes    |
| VCF                          | example                      | no     |

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
target_id true_parent
47487 40896
47637 40914
47687 44376
47897 46860
47941 46943
47948 46943
```

The first column is target individuals' id, the second is the true parents' id. 
