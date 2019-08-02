---
title: "RNAseq Cártamo"
author: "Diego G. Teixeira"
date: "Gerado em 2019-08-02"
output: 
  html_document:
    keep_md: true
---



## Introdução

Esse é o protocolo de análises em bioinformática de dados de RNA seq provenientes de cártamo. O dado é composto por 4 bilbiotecas (2 de amostra controle e 2 de amostra tratada com NaCl) sequencinados em ION TORRENT.

As bibliotecas são:

* IonXpressRNA_005.Controle1.2014-02-10T04_44_50Z.318C.fastq -> C1
* IonXpressRNA_006.Controle2.2014-02-10T04_44_50Z.318C.fastq -> C2
* IonXpressRNA_007.N1.2014-02-10T23_29_46Z.318C.fastq -> N1
* IonXpressRNA_008.N2.2014-02-10T23_29_46Z.318C.fastq -> N2


#Pré-análises

###Controle de qualidade das reads
Para avaliar a qualidade das reads sequenciadas, foi utilizado o software [Fastqc](https://www.bioinformatics.babraham.ac.uk/projects/fastqc/).


Table: Características das reads

 Biblioteca    # sequências    Comprimento    %GC 
------------  --------------  -------------  -----
     C1          1365359         25-342       52  
     C2          1218666         25-347       52  
     N1          1430210         25-362       52  
     N2          1877348         25-350       53  

Observando a qualidade de acordo com a posição da base dentro das reads, é identificado que o quaril inferior, do BoxWhisker plot, alcança a região de baixa qualidade a partir da posição 270: 

* A linha vermelha central como o valor da mediana
* A caixa amarela representa a faixa interquartil (25-75%)
* As linhas inferiores e superiores indicam os 10% e 90% dos pontos
* A linha azul representa a qualidade média

<div class="figure" style="text-align: center">
<img src="Figures/quality/raw/C1_per_base_quality.png" alt="C1" width="90%" />
<p class="caption">C1</p>
</div>
<div class="figure" style="text-align: center">
<img src="Figures/quality/raw/C2_per_base_quality.png" alt="C2" width="90%" />
<p class="caption">C2</p>
</div>
<div class="figure" style="text-align: center">
<img src="Figures/quality/raw/N1_per_base_quality.png" alt="N1" width="90%" />
<p class="caption">N1</p>
</div>
<div class="figure" style="text-align: center">
<img src="Figures/quality/raw/N2_per_base_quality.png" alt="N2" width="90%" />
<p class="caption">N2</p>
</div>


Para melhorar a qualidade das reads, foi realizado um corte nas bases das reads com comprimento superior a 260pb utilizando a ferramenta [seqtk](https://github.com/lh3/seqtk).

**_comando:_**  ~/Programs/seqtk/seqtk trimfq -e 260 C1 > C1.trim.fastq.

<div class="figure" style="text-align: center">
<img src="Figures/quality/trim/C1_per_base_quality.png" alt="C1 trim" width="90%" />
<p class="caption">C1 trim</p>
</div>
<div class="figure" style="text-align: center">
<img src="Figures/quality/trim/C2_per_base_quality.png" alt="C2 trim" width="90%" />
<p class="caption">C2 trim</p>
</div>
<div class="figure" style="text-align: center">
<img src="Figures/quality/trim/N1_per_base_quality.png" alt="N1 trim" width="90%" />
<p class="caption">N1 trim</p>
</div>
<div class="figure" style="text-align: center">
<img src="Figures/quality/trim/N2_per_base_quality.png" alt="N2 trim" width="90%" />
<p class="caption">N2 trim</p>
</div>

# Mapeamento

###Abordagem 1: Mapeamento contra o genoma de _Heliantus annuus_
o arquivos fasta e a anotação em gff3 de _Heliantus annuus_ foram obtidos através do [Ensembl plants](http://plants.ensembl.org/info/website/ftp/index.html). Em seguida o arquivo fasta foi indexado pelo programa [Hisat2](https://ccb.jhu.edu/software/hisat2/index.shtml). Ainda utilizando o Hisat2, as reads foram mapeadas ao genoma. 

*_Comando:_* hisat2 -p 14  -x H_annuus/Helianthus_annuus -U trimed/N2.trim.fastq -S sam/N2.sam


Table: Mapeamento contra o genoma de H. annuus

 Biblioteca    # reads    Não mapeada    Alinhada 1x    Alinhada >1x    % de mapeamento 
------------  ---------  -------------  -------------  --------------  -----------------
     C1        1365359      1213232         73009          79118             11.14      
     C2        1218666      1073805         67421          77440             11.89      
     N1        1430210      1190019        119473          120718            16.79      
     N2        1877348      1636663        113928          126757            12.82      
