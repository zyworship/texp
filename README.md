# TeXP
TeXP is a pipeline to evaluate the transcription level of transposable elements in short read RNA-seq data

#About
TeXP is a pipeline for quantifying abundances of Transposable Elements transcripts from RNA-Seq data. TeXP is based on the assumption that RNA-seq reads overlapping Transposable Elements is a composition of pervasive transcription signal and autonomous transcription of Transposable Elements.

https://www.biorxiv.org/content/10.1101/648667v1.full

# How to quickly run TeXP

```
docker run -it fnavarro/texp:latest /bin/bash
```

Download a fastq file from a RNA-seq experiment, for example, MCF-7 from the ENCODE project

```  
  wget -c -t0 "https://www.encodeproject.org/files/ENCFF000HFF/@@download/ENCFF000HFF.fastq.gz" -O file.fastq.gz
```
  
Run TeXP
```
  ./TeXP.sh -f file.fastq.gz -t 1 -o process/example/ -n quick_texp_run
```
The output files will be generated at:
```
  ls process/example/quick_texp_run
	*.L1HS_hg38.count (Naive counts) 
	*.L1HS_hg38.count.corrected (Corrected counts)
	*.L1HS_hg38.count.rpkm (Naive RPKM)
	*.L1HS_hg38.count.rpkm.corrected (Corrected RPKM)
	*.L1HS_hg38.count.signal_proportions 
```

TIPS:
If fastq files are stored locally you can use
```
docker run -it -v ~/Desktop/:/texp fnavarro/texp:latest /bin/bash
```
To mount "~/Desktop" at your docker container

# Requirements
 - Bowtie2 (2.3+)
 - Bedtools (2.26+)
 - Fastx-toolkit (0.0.14+)
 - perl (5.24+)
 - python (2.7)
 - R (3.3+)
  - Penalized package (0.49+)
 - samtools (1.3+)
 - wgsim (a12da33 on Oct 17, 2011)
---
 - Bowtie2 hg38 reference index (http://files2.gersteinlab.org/public-docs/2019/08.14/bowtie2.hg38.tar.bz2)
 - Hg38 repetitive element annotation (http://files2.gersteinlab.org/public-docs/2019/08.14/rep_annotation.hg38.tar.bz2)
 
# Download TeXP
 $> git clone https://github.com/gersteinlab/texp.git

 Edit TeXP.sh and Update INSTALL_DIR variable to the path where TeXP was cloned 

 # Installing TeXP dependencies
apt-get update

- Install binaries dependencies

apt-get install -y \
	bedtools=2.26.0+dfsg-3 \
	bowtie2=2.3.0-2 \
	fastx-toolkit=0.0.14-3 \
	gawk=1:4.1.4+dfsg-1 \
	git \
	perl=5.24.1-3+deb9u1 \
	python=2.7.13-2 \
	r-base=3.3.3-1 \
	r-base-dev=3.3.3-1 \
	samtools=1.3.1-3 \
	wget 


- Install Wgsim

mkdir -p /src; \ 
	cd /src ; \
	git clone https://github.com/lh3/wgsim.git; \
	cd wgsim; \
	gcc -g -O2 -Wall -o wgsim wgsim.c -lz -lm; \
	mv wgsim /usr/bin/; \
	cd /;


- Download Libraries

Fix path (/data/library) to the a proper location at your computation enviroment

mkdir -p /data/library/rep_annotation; \
	cd /data/library/rep_annotation; \
	wget -c -t0 "http://files2.gersteinlab.org/public-docs/2019/08.14/rep_annotation.hg38.tar.bz2" -O rep_annotation.hg38.tar.bz2; \
	tar xjvf rep_annotation.hg38.tar.bz2; \
	rm -Rf rep_annotation.hg38.tar.bz2
	
mkdir -p /data/library/bowtie2; \
	cd /data/library/bowtie2; \
	wget -c -t0 "http://files2.gersteinlab.org/public-docs/2019/08.14/bowtie2.hg38.tar.bz2" -O bowtie2.hg38.tar.bz2; \
	tar xjvf bowtie2.hg38.tar.bz2; \
	rm -Rf bowtie2.hg38.tar.bz2



- Install R packages dependencies

echo 'install.packages(c("penalized"), repos="http://cloud.r-project.org", dependencies=TRUE)' > /tmp/packages.R \
    && Rscript /tmp/packages.R


# TeXP config
 A few paramaters must be setup so TeXP can properly work outside a docker enviroment; Parameters are set on opts.mk and the user MUST properly set it up.

 - LIBRARY_PATH: Absolute path pointing to TeXP library, general this is the path you downloaded TeXP
 - EXT_LIBRARY_PATH: Absolute path containing the bowtie2 reference index and Transposable element annotation bed file, downloaded as instructed above
 - EXE_DIR: If binaries are found in a single path, EXE_DIR can be used to generalize binary location. For example, if bowtie2, bedtools, etc are located at /usr/bin/, you should set EXE_DIR := /usr
 - Dependencies installed in different paths should be defined manually, for example, if wgsim is installed at the home folder, the user must set:
    - WGSIM_BIN := ~/wgsim/bin/wgsim
  - Finally the user must set to CONFIGURED := TRUE

---


# Docker image
Alternatively, docker images containing all dependencies and libraries can be used. The TeXP docker image also is pre-configured to work outside the box.
Check https://hub.docker.com/r/fnavarro/texp/ for futher instructions:
docker pull fnavarro/texp


---
# Running TeXP
 $> ./TeXP.sh -f [FILE_NAME] -t [INT] -o [OUTPUT_PATH] n [SAMPLE_ID]

 -f: Input file (fastq,fastq.gz,sra)

 -t: Number of threads

 -o: Output path (i.e. ./ or ./processed)

 -n: Sample name (i.e. SAMPLE01)
 
 ---
