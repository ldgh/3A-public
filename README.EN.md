# 3A-public


- [Introduction](#Introduction)
- [Libraries](#Libraries)
- [Flags and Input Files](#flags)
- [Other inputs](#others)

## Introduction

Script implemented by [LEAL, T. P.]( http://lattes.cnpq.br/1908814778674963) aims to automate and standardize genetic-population analyzes performed in LDGH. The program currently (12/31/2020) performs all the necessary steps for the following analyses:

- [x] PCA
- [x] ADMIXTURE
- [x] Local Ancestry with RFMix2
- [ ] IBD
- [ ] Chromopaint

[Click to go to LDGH Ancestry Public Flowchart (updated)](https://github.com/ldgh/3A-public/blob/e278da1f3de28d0863295d88151ec1f25be28b26/ancestry_sptember2022_carol_publico.pdf)

The script works in the description files pattern, that is, you configure the input files so that all parameters are described in the files.
The program has the following getopt:

```
usage: 3A.py [-h] -a ANALYSIS -p PROGRAMS -r REFERENCE -d DATABASE -f FOLDER
             [-A] [-n] [-T THREAD]

3A script: Automated Ancestry Analysis Script

optional arguments:
  -h, --help            show this help message and exit

Required arguments:
  -a ANALYSIS, --analysis ANALYSIS
                        File with the description of analysis to be performed
  -p PROGRAMS, --programs PROGRAMS
                        File with the path to the programs used
  -r REFERENCE, --reference REFERENCE
                        File with the path of reference files
  -d DATABASE, --database DATABASE
                        File with the description of the databases
  -f FOLDER, --folder FOLDER
                        Folder to store the intermediary files and output
                        files

Optional arguments:
  -A, --changeAdmixture
                        Change the colors in ADMIXTURE plot
  -n, --notClean        Use this flag if the data is not clean by
                        SmartCleanning
  -T THREAD, --thread THREAD
                        Number of processors to be used in haplotyping and
                        local ancestry inference
```

## Libraries
 
In the script implemented in Python 3 we use the following libraries:

- you
- sys
- argparse
- numpy
- subprocess
- matplotlib
- math
- pandas
- seaborn

We also use a modified Python2 implemented script called [plot_karyogram](https://github.com/armartin/ancestry_pipeline.git)
which uses the following libraries:




- you
- argparse
- numpy
- pylab
- matplotlib
- brewer2mpl

## Flags

#### Analyzes

``
-a analyses.txt
``

This file aims to describe which analyzes and parameters refer to the plots. Note that each line is independent of each other, that is, you can use only one line
if all prerequisites for running that line are met.



```
PCA 8 /home/thiagop/3A_JC/PCAColor.txt
ADMIXTURE 2 5 2 2 AFR /home/thiagop/3A_JC/Cores.txt
rfmix
rfmix single 200627820015_R09C01 /home/thiagop/3A_JC/admix/RFMix LocalAncestry 0.95 EUR,NAT,AFR,ASIA,UNK red,forestgreen,blue,pink,black 1 22
rfmix populational /home/thiagop/3A_JC/admix/RFMix LocalAncestry 0.95 EUR,NAT,AFR,ASIA,UNK red,forestgreen,blue,pink,black 1 22
```

#### Programs

``
-p programs.txt
``

This file is intended to facilitate the portability of the script between different machines. The file consists of two columns: program name and program path separated by tab.
The programs needed to run the entire script are:

- [plink](https://www.cog-genomics.org/plink/)
- [smartcleaning (MosaiQC)](https://github.com/ldgh/Smart-cleaning-public)
- [convertf(EIGENSTRAT)](https://github.com/DReichLab/EIG)
- [smartpca (EIGENSTRAT)](https://github.com/DReichLab/EIG)
- [admixture](http://dalexander.github.io/admixture/download.html)
- [bcftools](https://samtools.github.io/bcftools/)
- [vctools](http://vctools.sourceforge.net/)
- [rfmix](https://github.com/slowkoni/rfmix)
- [karyogram](https://github.com/ldgh/3A/blob/master/plot_karyogram.py)
- [mergecleandata](https://github.com/ldgh/MergedCleanData)


```
plink /home/thiago/Programs/plink
smartcleaning /home/thiagop/3A/Programs/MosaiQC
convertf /home/thiago/Programs/convertf
smartpca /home/thiago/Programs/smartpca
admixture /home/thiago/Programs/admixture
bctools bctools
shapeit4 /home/thiago/Programs/shapeit4-master/bin/shapeit4
vcftools vcftools
rfmix /home/thiago/Programs/rfmix-master/rfmix
karyogram /home/thiago/Programs/karyogram/plot_karyogram.py
mergecleandata /home/thiago/Programs/mergeCleanData.py
```

#### Reference

``
-r reference.txt
``

This file is intended to describe the path of the reference files. Although reference.txt has 4 files, in the current version the program only uses two:


- **Genetic maps (MAPS)**
- **Description of centromeres(centromer) for [plot_karyogram.py](https://github.com/armartin/ancestry_pipeline/blob/master/centromeres_hg19.bed)**
- **The other two files (DBSNP and KGP) are for running SDQC, which we recommend you do before using the newer [SDQC](https://github.com/ldgh/SDQC)**



```
DBSNP /media/thiago/Data/Unificado/Ref/dbSNP_HG38_HG37_last_version_chr*_corrected.txt.gz
KGP /media/thiago/Data/Unified/Ref/HumanOmni5Exome-4-v1-1-B-auxilliary-file.txt
MAPS /media/thiago/Data/Unified/Ref/chr*.b37.gmap.gz
centromer /home/thiago/Programs/karyogram/centrometer
```

#### Data base

``
-d database.txt
``

This file aims to describe the databases. Each database will consist of 6 columns:

- **Identifier**, that is, the way this database will be called for the rest of the run
- **Group** (in general we use EUR, AFR, NAT and Admixed). The only name that, if changed, can bring problems to the execution is Admixed, since in the execution of RFMix it will put the entire non-Admixed population as parental
- **Absolute path** to the database without the suffix (bed/bim/fam)
- **First chromosome**
- **Last chromosome**
- **Genome version**



```
#pop, group, location, start chr, end chr, reference genome
MENCK Admixed /home/thiagop/3A_JC/MENCK 1 22 37
NAT NAT /home/thiagop/3A_JC/NAT 1 22 37
AFR AFR /home/thiagop/3A_JC/AFR 1 22 37
EUR EUR /home/thiagop/3A_JC/EUR 1 22 37
ASIA ASIA /home/thiagop/3A_JC/ASIAN 1 22 37
```

## Others

### PCAColor (used within the analyses.txt file)
>ADMIXTURE 2 12 2 2 MEGA1 /home/thiago/PycharmProjects/3A/Cores


- **PCA**: Performs all necessary analysis (merge datasets, LD prunning, convertf and smartpca), generates the number of PCs in the second column (in the example, 8) and plots based on the PCAColor.txt file. The PCA color has the ID that must be included in the **Database** file, the [format](https://matplotlib.org/3.3.3/api/markers_api.html) and the [color](https: //matplotlib.org/3.1.0/gallery/color/named_colors.html).
- <p align="center">
    <img src="https://user-images.githubusercontent.com/73356412/192793154-8d7c6b07-0d6b-4c24-9faf-6582e6427151.png">
</p>

> Kehdy et al. Figure 3 (B e D) 
Published online 2015 Jun 29. [doi: 10.1073/pnas.1504447112](https://www.pnas.org/doi/full/10.1073/pnas.1504447112)

- **ADMIXTURE**: Performs all the necessary analyzes (merge datasets, LD prunning, admixture), generates the ADMIXTURE from the K of the second column to the K of the third column (2 and 5 in the example), repeating each K the amount of times of column 5 (2) and the plot must be guided by the ancestry reference of column 6 (AFR) and with the colors in the barplot following the list of colors of the defined file (Cores.txt). **The fourth column has been deprecated as the number of threads has been passed to getopt**. Note that you can change the ADMIXTURE plot using the -A flag, which will only run the ADMIXTURE plot.

<p align="center">
    <img src="https://user-images.githubusercontent.com/73356412/192796172-3a303bbe-dae6-47ee-91b7-70db9163cafe.png">
</p>


> Kehdy et al. Figure 3 (A e C) 
Published online 2015 Jun 29. [doi: 10.1073/pnas.1504447112](https://www.pnas.org/doi/full/10.1073/pnas.1504447112)


- **RFMIX**: Performs all the necessary analyzes (merge datasets, haplotyping, separate reference from mixed races and inference via rfmix2)
- **Individual RFMIX**: Plots the mixed chromosomes of a specific individual (column 2) that is present in the folder present in column 3 (/home/thiagop/3A_JC/admix/RFMix) with the name present in column 4 (LocalAncestry), using only windows with a value greater than or equal to the rpesent value in column 5 (0.95), whose parents are present in column 6 (EUR,NAT,AFR,UNK) which have the colors in column 7 (red,forestgreen,blue,pink,black) and which has the first chromosome in column 8 and the last one in column 9 (1 and 22 respectively)
 
![Mosaic002](https://user-images.githubusercontent.com/73356412/192803093-224c2710-d960-47c5-ad2d-fc196d931d94.png)![Mosaic003](https://user-images.githubusercontent.com/ 73356412/192804759-be8ba054-3566-4826-9787-7d622d015af6.png)
![Mosaic011](https://user-images.githubusercontent.com/73356412/192842209-9cae5b3d-0f31-462a-a837-343a74788ae6.png)



- **RFMIX populational**: Plots the ancestries for the chromosome and the distribution of tract sizes from the database that is present in the folder present in column 2 (/home/thiagop/3A_JC/admix/RFMix) with the present name in column 3 (LocalAncestry), using only the windows with a value greater than or equal to the value represented in column 4 (0.95), whose parents are present in column 5 (EUR,NAT,AFR,ASIA,UNK) which has the colors in column 6 (red,forestgreen,blue,pink,black) and which has the first chromosome in column 7 and the last one in column 8 (1 and 22 respectively)

<p align="center">
    <img width=600 src="https://user-images.githubusercontent.com/73356412/192812095-d9f275e6-bbf0-44e6-a8c9-ff681b0a972a.jpg">
</p>


<p align="center">
    <img width=600 src="https://user-images.githubusercontent.com/73356412/192812171-3c62633a-6e02-4379-a3c4-64c1cdde199f.jpg">
</p>

### Colors (used inside the analyses.txt file)

As in PCAColor, a file referring to the colors to be used in the Admixture results:


> PCA 12 /home/thiago/PycharmProjects/3A/PCAColor
