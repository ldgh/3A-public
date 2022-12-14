# 3A-public


[![en](https://img.shields.io/badge/lang-en-red.svg)](https://github.com/ldgh/3A-public/blob/4eb4585f508d3462c38a908aadce26fc1bc855d1/README.EN.md)



- [Introdução](#introdução)
- [Bibliotecas](#bibliotecas)
- [Flags e Arquivos de Input](#flags)
- [Outros inputs](#outros)

## Introdução

As inferências de ancestralidade genética em uma população ou indivíduos miscigenados pode se dar em três níveis : populacional, individual e local cromossômica, sendo que todas essas medidas estão correlacionadas. A ancestralidade populacional é a porcentagem do conjunto dos genomas de uma população que deriva de cada uma das populações parentais. As ancestralidades individuais são as porcentagens do genoma de um indivíduo miscigenado originárias de cada uma das populações parentais.  A inferência de ancestralidade cromossômica é a inferência da ancestralidade de cada ponto de cada cromossomo de cada indivíduo. 


O LDGH integra diferentes metodologias para realizar as inferências de ancestralidades com dados genômicos. O fluxograma da Figura 2 ilustra nosso pipeline bioinformático para realizar diferentes análises genético populacionais relacionados com as inferências da ancestralidade, com loci independentes (REAP, NaTORA, PCA, Admixture) e a partir de haplótipos (RFMix, IBDrefine, Chromopainter).


[Clique para ir para o Fluxograma Público de Ancestralidade do LDGH (atualizado)](https://github.com/ldgh/3A-public/blob/e278da1f3de28d0863295d88151ec1f25be28b26/ancestry_sptember2022_carol_publico.pdf)

<p align="center">
    <img width=500 src="https://user-images.githubusercontent.com/73356412/193077671-df457bd4-243a-4a83-aa08-2ee320939986.png">	
</p>

<p align="center">
    Figura 2
</p>

As Figuras A e B, extraídas de [Kehdy et al. (2015)](https://www.pnas.org/doi/full/10.1073/pnas.1504447112) ilustram a ancestralidade de populações brasileiras do projeto EPIGEN-Brasil. 

<p align="center">   
<img src="https://user-images.githubusercontent.com/73356412/193340754-a5770f80-e91d-4cb0-83e3-34d8be7eb820.png">
</p>

<p align="center">
    Figuras A e B
</p>

O cromosaicos da Figura 3 ilustram a ancestralidade local cromossomica de alguns membros do nosso grupo de pesquisa.


<p align="center">
    <img width=300 src="https://user-images.githubusercontent.com/73356412/192803093-224c2710-d960-47c5-ad2d-fc196d931d94.png">
    <img width=300 src="https://user-images.githubusercontent.com/73356412/192804759-be8ba054-3566-4826-9787-7d622d015af6.png">
	<img width=300 src="https://user-images.githubusercontent.com/73356412/192842209-9cae5b3d-0f31-462a-a837-343a74788ae6.png">
</p>

<p align="center">
    Figura 3
</p>


[LEAL, T. P.]( http://lattes.cnpq.br/1908814778674963) do nosso grupo tem desenvolvido a ferramenta MosA3ic para automatizar a integração de parte dessas metodologias (PCA, Admixture e a inferência da ancestralidade local com RFMix2). O programa atualmente (31/12/2020) realiza todos os passos necessários para as seguintes análises:


- [x] PCA
- [x] ADMIXTURE
- [x] Local Ancestry com RFMix2
- [ ] IBD
- [ ] Chromopainter

O script funciona no padrão de arquivos de descrição, isso é, você configura os arquivos de entrada de forma que todos os parâmetros estejam descritos nos files.
O programa tem o seguinte getopt:

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
## Bibliotecas 
 
No script implementado em Python 3 utilizamos as seguintes bibliotecas:

- os
- sys
- argparse
- numpy
- subprocess
- matplotlib
- math
- pandas
- seaborn

Também utilizamos um script implementado em Python2 modificado chamado [plot_karyogram](https://github.com/armartin/ancestry_pipeline.git) 
que utiliza as seguintes bibliotecas:




- os
- argparse
- numpy
- pylab
- matplotlib
- brewer2mpl

## Flags

#### Analises

``
-a analises.txt
``

Esse arquivo tem como objetivo descrever quais análises e parâmetros referentes aos plots. Note que cada linha é independente entre si, isso é, você pode usar somente uma linha 
caso todos os pré-requisitos para rodar aquela linha sejam saisfeitos.



```
PCA	8	/home/thiagop/3A_JC/PCAColor.txt
ADMIXTURE	2	5	2	2	AFR	/home/thiagop/3A_JC/Cores.txt
rfmix
rfmix individual	200627820015_R09C01	/home/thiagop/3A_JC/admix/RFMix	LocalAncestry	0.95	EUR,NAT,AFR,ASIA,UNK	red,forestgreen,blue,pink,black	1	22
rfmix populational	/home/thiagop/3A_JC/admix/RFMix	LocalAncestry	0.95	EUR,NAT,AFR,ASIA,UNK	red,forestgreen,blue,pink,black	1	22
```

#### Programs

``
-p programs.txt
``

Esse arquivo tem como objetivo de facilitar a portabilidade do script entre diferentes máquinas. O arquivo é composto por duas colunas: nome do programa e caminho do programa separados por tab.
Os programas necessários para rodar todo o script são:

- [plink](https://www.cog-genomics.org/plink/)
- [smartcleaning (MosaiQC)](https://github.com/ldgh/Smart-cleaning-public)
- [convertf (EIGENSTRAT)](https://github.com/DReichLab/EIG)
- [smartpca (EIGENSTRAT)](https://github.com/DReichLab/EIG)
- [admixture](http://dalexander.github.io/admixture/download.html)
- [bcftools](https://samtools.github.io/bcftools/)
- [vcftools](http://vcftools.sourceforge.net/)
- [rfmix](https://github.com/slowkoni/rfmix)
- [karyogram](https://github.com/ldgh/3A/blob/master/plot_karyogram.py)
- [mergecleandata](https://github.com/ldgh/MergedCleanData)


```
plink	/home/thiago/Programs/plink
smartcleaning	/home/thiagop/3A/Programs/MosaiQC
convertf	/home/thiago/Programs/convertf
smartpca	/home/thiago/Programs/smartpca
admixture	/home/thiago/Programs/admixture
bcftools	bcftools
shapeit4	/home/thiago/Programs/shapeit4-master/bin/shapeit4
vcftools	vcftools
rfmix	/home/thiago/Programs/rfmix-master/rfmix
karyogram	/home/thiago/Programs/karyogram/plot_karyogram.py
mergecleandata	/home/thiago/Programs/mergeCleanData.py
```


#### Reference

``
-r reference.txt
``

Esse arquivo tem como objetivo descrever o caminho dos arquivos de referencia. Apesar do reference.txt ter 4 arquivos, na versão atual o programa usa somente dois:


- **Genetic maps (MAPS)**
- **Descrição dos centromeros(centromer) para o [plot_karyogram.py](https://github.com/armartin/ancestry_pipeline/blob/master/centromeres_hg19.bed)**
- **Os outros dois files(DBSNP e KGP) são para rodar o SDQC, coisa que recomendamos que seja feita antes usando o [SDQC](https://github.com/ldgh/SDQC) mais novo**



```
DBSNP	/media/thiago/Data/Unificado/Ref/dbSNP_HG38_HG37_last_version_chr*_corrected.txt.gz
KGP	/media/thiago/Data/Unificado/Ref/HumanOmni5Exome-4-v1-1-B-auxilliary-file.txt
MAPS	/media/thiago/Data/Unificado/Ref/chr*.b37.gmap.gz
centromer	/home/thiago/Programs/karyogram/centrometer
```



#### Database

``
-d database.txt
``

Esse arquivo tem como objetivo descrever as bases de dados. Cada banco de dados será composto por 6 colunas:

- **Identificador**, isso é, a forma com que esse banco de dados será chamado pelo resto da execução
- **Grupo** (em geral usamos EUR, AFR, NAT e Admixed). O único nome que, se mudado, pode trazer problemas a execução é o Admixed, uma vez que na execução do RFMix ele colocará toda população não Admixed como parental
- **Caminho absoluto** para a base de dados sem o sufixo (bed/bim/fam)
- **Primeiro cromossomo**
- **Ultimo cromossomo**
- **Versão do genoma**



```
#pop, grupo, local, chr inicial, chr final, genoma de referencia
MENCK	Admixed	/home/thiagop/3A_JC/MENCK	1	22	37
NAT	NAT	/home/thiagop/3A_JC/NAT	1	22	37
AFR	AFR	/home/thiagop/3A_JC/AFR	1	22	37
EUR	EUR	/home/thiagop/3A_JC/EUR	1	22	37
ASIA	ASIA	/home/thiagop/3A_JC/ASIAN	1	22	37
```

## Outros

### PCAColor (utilizado dentro do arquivo analises.txt)
>ADMIXTURE	2	12	2	2	MEGA1	/home/thiago/PycharmProjects/3A/Cores


- **PCA**: Faz todas as análises necessárias (merge datasets, LD prunning, convertf e smartpca), gera a quantidade de PCs na segunda colunas (no exemplo, 8) e plota baseado no arquivo PCAColor.txt. O PCA color tem o ID quye deve estar incluido no arquivo do **Database**, o [formato](https://matplotlib.org/3.3.3/api/markers_api.html) e a [cor](https://matplotlib.org/3.1.0/gallery/color/named_colors.html).

<p align="center">
    <img src="https://user-images.githubusercontent.com/73356412/192793154-8d7c6b07-0d6b-4c24-9faf-6582e6427151.png">
</p>

> Kehdy et al. Figure 3 (B e D) 
Published online 2015 Jun 29. [doi: 10.1073/pnas.1504447112](https://www.pnas.org/doi/full/10.1073/pnas.1504447112)


- **ADMIXTURE**: Faz todas as análises necessárias (merge datasets, LD prunning, admixture), gera o ADMIXTURE do K da segunda coluna até o K da terceira coluna (2 e 5 no exemplo), repetindo cada K a quantidade de vezes da coluna 5 (2) e o plot deve ser guiar pela ancestralidade referência da coluna 6 (AFR) e com as cores no barplot seguindo a lista de cores do arquivo definido (Cores.txt).  **A quarta coluna foi descontinuada uma vez que o número de threads foi passado para o getopt**. Note que você pode mudar o plot do ADMIXTURE usando a flag -A, que só rodará o plot do ADMIXTURE.


<p align="center">
    <img src="https://user-images.githubusercontent.com/73356412/192796172-3a303bbe-dae6-47ee-91b7-70db9163cafe.png">
</p>

> Kehdy et al. Figure 3 (A e C) 
Published online 2015 Jun 29. [doi: 10.1073/pnas.1504447112](https://www.pnas.org/doi/full/10.1073/pnas.1504447112)

- **RFMIX**: Faz todas as análises necessárias (merge datasets, haplotipagem, separar referência dos miscigenados e inferência via rfmix2)
- **RFMIX individual**: Faz o plot dos cromossomos miscigenados de um indivíduo específico (coluna 2) que está presente na pasta presentes na coluna 3 (/home/thiagop/3A_JC/admix/RFMix) com o nome presente na coluna 4 (LocalAncestry), usando somente as janelas com o valor maior ou igual com o valor rpesente na coluna 5 (0.95), cujas parentais estão presente na coluna 6 (EUR,NAT,AFR,ASIA,UNK) que tem as cores na coluna 7 (red,forestgreen,blue,pink,black) e que tem como primeiro cromossomo na coluna 8 e o último na coluna 9 (1 e 22 respectivamente) 
 
![Mosaic002](https://user-images.githubusercontent.com/73356412/192803093-224c2710-d960-47c5-ad2d-fc196d931d94.png)![Mosaic003](https://user-images.githubusercontent.com/73356412/192804759-be8ba054-3566-4826-9787-7d622d015af6.png)
![Mosaic011](https://user-images.githubusercontent.com/73356412/192842209-9cae5b3d-0f31-462a-a837-343a74788ae6.png)



- **RFMIX populational**: Faz o plot das ancestralidades pro cromossomo e distribuição dos tamanhos de tracts do banco de dados que está presente na pasta presentes na coluna 2 (/home/thiagop/3A_JC/admix/RFMix) com o nome presente na coluna 3 (LocalAncestry), usando somente as janelas com o valor maior ou igual com o valor rpesente na coluna 4 (0.95), cujas parentais estão presente na coluna 5 (EUR,NAT,AFR,ASIA,UNK) que tem as cores na coluna 6 (red,forestgreen,blue,pink,black) e que tem como primeiro cromossomo na coluna 7 e o último na coluna 8 (1 e 22 respectivamente) 


<p align="center">
    <img width=600 src="https://user-images.githubusercontent.com/73356412/192812095-d9f275e6-bbf0-44e6-a8c9-ff681b0a972a.jpg">
</p>


<p align="center">
    <img width=600 src="https://user-images.githubusercontent.com/73356412/192812171-3c62633a-6e02-4379-a3c4-64c1cdde199f.jpg">
</p>


### Cores (utilizado dentro do arquivo analises.txt)

Assim como no PCAColor, um arquivo referente as cores a serem utilizadas no resultados dos Admixture:


> PCA	12	/home/thiago/PycharmProjects/3A/PCAColor


