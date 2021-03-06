# ProkSeq
ProkSeq is an automated RNA-seq data analysis package for Prokaryotic, where users can perform all the necessary steps of RNA-seq data analysis from quality control to pathway enrichment analysis. It has a wide variety of options for differential expression, normalized expression, visualization, and
quality control, and publication-quality figures. It is also less time consuming as the user does not need to observe and control the analysis process. The user needs to specify the descriptions of the samples and define the parameter file accordingly. ProkSeq also automatically do the quality filtering of the bad reads and run the analysis on good quality reads.

DOWNLOAD:
=========
The pipeline can be obtained from the following repositories.

DOCKER:
-------
	We strongly recommend using docker to run the pipeline. The external dependencies and R dependencies are all 
	bundled in the container.
	The container prokseq-v2.1:v1 is available in https://hub.docker.com/repository/docker/snandids/prokseq-v2.1

	Step 1: To pull the image from the Docker Hub registry:
	   docker pull snandids/prokseq-v2.1:v1

	Step 2: To Run:
	   docker run -it snandids/prokseq-v2.1:v1
	   sh-5.0# cd prokseq
	   
	Step 3: Activate the environment
	   sh-5.0# source /etc/profile.d/conda.sh
	   sh-5.0# conda activate py36
           (py36) sh-5.0# <YOU WILL GET THIS PROMPT>

	Step 4: Run the example
           Here run the pipeline with the example files:
              (py36) sh-5.0# python scripts/pipeline-v2.8.py -s samples.bowtie.PEsample -p param.input.bowtie -n 4
              The script is running with PE (paired-end) samples described in 
	      samples.bowtie.PEsample, and with the parameters defined in 
	      param.input.bowtie. The program is submitted with four processors.

	Step 5: Work with real data. To copy the files (sample[fq/fastq], GTF, BED, fasta, etc) to the container:
	   Find out the containerID from another terminal. For example:
	   Run the command 
	      docker ps -a
	   Output (somewhat similar):
	   CONTAINER ID        IMAGE                      COMMAND             CREATED             STATUS                    PORTS               NAMES
	   8f780c0a9969        snandids/prokseq-v2.1:v1   "sh"                5 minutes ago       Up 5 minutes                                  fervent_feynman

	Then run the command:
	   docker cp test.fasta 8f780c0a9969:/root/prokseq/test.fasta
	In this above example, local test.fasta is copied to the container (/root/prokseq/).

CONDA:
------
        Step 1: Fetch the package.
           conda install -p <PATH_TO_DOWNLOAD> -c <CHANNEL> prokseq
           Example:
              mkdir testPrseq
              conda install -p /home/path/testPrseq -c snandids prokseq

        Step 2: Once the package is obtained, run the following commands.
           tar -xvzf exampleFiles.tar.gz

        Step 3: Install dependencies.
           tar -xvzf depend.tar.gz
           A depend folder will be created with all the required dependencies. Most of these packages were
           compiled on architecture x86_64 Fedora 31. Users may have to recompile some of them.

           Install the R and the R bioconductor packages.
	   
           Though the pipeline is written in Python3.6, but some packages included in the pipeline require
           Python2.7. Therefore, it is advised to install Python2. The program should find python2 and
           python (python3) in the env PATH. To make life easier, we recommend create environment (Step 4).

        Step 4: Create virtual environment
           conda create -n yourenvname python=3.6
           conda activate yourenvname
           conda install pandas
           pip2.7 install numpy
           pip2 install qc bitsets RSeQC
           pip2 install --upgrade cython bx-python pysam RSeQC numpy

        Step 5:
           Once all the dependencies and R packages are installed, and the example files are untared,
           change the PATH in parameter file (Eg. param.input.bowtie). The PATH should point to the packages.

           For example:
              If you are using the above-mentioned path [/home/path/testPrseq] from Step 1, then specify
              the path as below for all the packages in the parameter file.
              #       Specify the path to pypy required for running afterqc
              PATH PYPY /home/path/testPrseq/depend/pypy2.7-v7.2.0-linux64/bin

           Then run the following command to test run the pipeline.
              python scripts/pipeline-v2.8.py -s samples.bowtie.PEsample -p param.input.bowtie -n 4
              Description: The script is running with PE (paired-end) samples described in samples.bowtie.PEsample,
              and with the parameters defined in param.input.bowtie. The program is submitted with four processors.

        To remove:
           conda remove -p /home/path/testPrseq prokseq


Default directory layout should look like below:
------------------------------------------------
	.
	./README
	./depend/afterqc
	./depend/bowtie2
	./depend/FastQC
	./depend/pypy2.7-v7.2.0-linux64
	./depend/readFasta
	./depend/RSeQC-2.6.2
	./depend/salmon-latest_linux_x86_64
	./depend/samtools
	./depend/samtools-1.10
	./depend/subread-1.4.6-p5-Linux-i386
	./depend/wigToBigWig
	./scripts/pipeline-v2.8.py
	./scripts/samtools.sh
	./scripts/libmod
	./scripts/libmod/checkEnv.py
	./scripts/libmod/errMsgFn.py
	./scripts/libmod/execCmd.py
	./scripts/libmod/__init__.py
	./scripts/libmod/pipeFunc.py
	./scripts/libmod/__pycache__
Example files layout:
---------------------
	./sampleCtrl_1.R1.fq
	./sampleCtrl_1.R2.fq
	./sampleCtrl_2.R1.fq
	./sampleCtrl_2.R2.fq
	./sampleCtrl_3.R1.fq
	./sampleCtrl_3.R2.fq
	./sampleTreat_1.R1.fq
	./sampleTreat_1.R2.fq
	./sampleTreat_2.R1.fq
	./sampleTreat_2.R2.fq
	./sampleTreat_3.R1.fq
	./sampleTreat_3.R2.fq
	./samples.bowtie.PEsample
	./samples.bowtie.SEsample
	./samples.salmon.PEsample
	./samples.salmon.SEsample
	./param.input.bowtie
	./param.input.salmon
	./oldAnnotationGFF.bed
	./oldAnnotationGFF.gtf
	./orf_coding_all.fasta
	./SequenceChromosome.fasta




REQUIRMENTS:
============
Users can run the ProkSeq program to see which depending packages are missing. By default, ProkSeq will search for required programs and print the availability of the program.

ProkSeq requires the following packages:

EXTERNAL TOOLS:
---------------
	Package/program	:	Purpose
	---------------		-------
	FastQC 		:	Quality check
	Bowtie 		: 	Aligning the reads
	Pypy 		: 	For speed and memory usage we sometimes
				uses pypy an alternative implementation 
				of python 3.6
	featureCounts
	from subread	: 	Counting reads to genomic features such as
				genes, exons, promoters, and genomic bins.
	AfterQc		: 	Automatic filtering trimming of the fastq
				sequences.
	Samtools	:	For post-processing of the SAM and BAM 
				reads alignment files.
	Salmon		:	A tool for wicked-fast transcript 
				quantification from RNA-seq data.
	

	R packages	:	Purpose
	--------		-------
	ggplot2		:	A system for declaratively creating graphics,
				based on The Grammar of Graphics.

	Bioconductor Packages:	Purpose
	---------------------	-------
	DESeq2		:	Differential gene expression analysis based
				on the negative binomial distribution.	
	edgeR		:	Package for examining differential expressi-
				on of replicated count data.
	NOISeq		:	A non-parametric approach for the different-
				ial expression analysis of RNseq-data. 
	limma		:	A package for the analysis of gene expressi-
				on data arising from microarray or RNA-seq
				technologies.
	clusterProfiler	:	To analyze functional profiles of genomic
				coordinates (supported by ChIPseeker), gene
				 and gene clusters.
	apeglm		:	The adaptive t prior shrinkage estimator
				used to Shrink log2 fold changes.



PYTHON LIBRARIES:
----------------
This program is written in python 3.6, and uses the following python libraries. os, subprocess, re, pandas, optparse, math, threading. These libraries may be installed using pip.


SYNTAX:
=======
	Usage: pipeline-v2.8.py [options] arg

	Options:
  	-h, --help            show this help message
  	-s SAMPLE_FILE_NAME, --sample=SAMPLE_FILE_NAME
	                        provide the sample description file
  	-p PARAMETER_FILE_NAME, --param=PARAMETER_FILE_NAME
        	                provide the parameters defined file
  	-n NUMBER OF PROCESSORS, --numproc=NUMBER OF PROCESSORS
	                        provide the number of processors

EXAMPLE:
--------
	python scripts/pipeline-v2.8.py -s samples -p param.input -n 4

	The program will run with sample file "sample", and parameter file "param.input". The program will also utilize 4 processors.

To run the program, the dependencies mentioned above are essential. However, the executable binaries are bundled in the folder "depend". The pipeline needs two more files, the parameter, and the sample files. The details of the parameter and sample files are as below. An example parameter and sample file are bundled together with the package.

PARAMETER FILE:
===============
There should be one parameter file. The entries of the file should be as follows.

	####################################################################
	#	File "param.input" - defination file
	#	Define the paths and parameters to run the external packages.
	####################################################################
	#	Describe the Bowtie options below as:
	#	These are the default values.
	BOWTIE -I 0
	BOWTIE -X 500
	BOWTIE -k 1
	BOWTIE -p 1
	#	In case the package salmon is to be run, uncomment the options.
	#	These are the default values.
	#SALMONINDEX -k 29
	#SALMONQUANT -l A
	#SALMONQUANT -p 2
	#SALMONQUANT --validateMappings TRUE
	#	Define the Featurecounts options as below:
	FEATURECOUNTS a oldAnnotationGFF.gtf
	FEATURECOUNTS o test
	#	Specify a count file name
	COUNTFILE testN.csv
	#	geneBody coverage.r require a bed file. Specify the name of bed file as below:
	geneBody_coverage r oldAnnotationGFF.bed
	#	Specify if batch effect removal is required. FALSE if not required.
	BATCH_EFFECT_REMOVE TRUE
	#	For pathway analysis, define the log fold change cutoff as below:
	PATHWAY cutoffPositive 2.0
	PATHWAY cutoffNegative -2.0
	#	For pathway analysis, define the organism in three alphabets as below.
	#	ypy = Yersinia pseudotuberculosis
	#	User need to change the kegg abbreviation of their genome which can be
	#	found in https://www.genome.jp/kegg/catalog/org_list.html. Here ypy is
	#	the Yersinia pseudotuberculosis YPIII
	PATHWAY Organism ypy
	#	For Gene Ontology of the pathway analysis, define GO term and gene name file.
	PATHWAY TERM2GENE data/TERM2GENE.csv
	PATHWAY TERM2NAME data/TERM2NAME.csv
	#	Specify the path to samtools
	PATH SAMTOOLS /home/snandi/samtools/bin 
	#	Specify the root path. That means where the ProkSeq bundle is unpacked.
	#	The location should have the following folders
	#	1. depend - contains all the binaries of the external packages
	#	2. scripts - contains all the modules required for ProkSeq, and pipeline-vx.x.sh
	#	3. data - Contains Gene ontology files for pathway analysis.
	PATH ROOT /home/snandi/firojPipeline/version_2.6
	#	If the above environment (depend, scripts, data) is true, the following
	#	line maye uncommented.
	#PATH DEFAULT
	#	Specify the path to geneBody_coverage
	PATH geneBody_coverage /home/snandi/firojPipeline/version_2.6/depend/RSeQC-2.6.2/scripts/
	#	Specify the path to FEATURECOUNTS
	PATH FEATURECOUNTS /home/snandi/firojPipeline/version_2.6/depend/subread-1.4.6-p5-Linux-i386/bin/
	#	Specify the path to fastqc
	PATH FASTQC /home/snandi/firojPipeline/version_2.6/depend/FastQC
	#	Specify the path to bowtie
	PATH BOWTIE /home/snandi/firojPipeline/version_2.6/depend/bowtie2/bowtie2-2.3.5.1-linux-x86_64
	#	Specify the path to pypy required for running afterqc
	PATH PYPY /home/snandi/firojPipeline/version_2.6/depend/pypy2.7-v7.2.0-linux64/bin
	#	Specify the path to readfasta
	PATH READFASTA /home/snandi/firojPipeline/version_2.6/depend
	#
	#	End of file "param.input"
	#

In general, the entries starting with BOWTIE instructs the program to run the tool with the additional parameter. Similarly FEATURECOUNTS. Entries beginning with PATH indicates the path to the executables of the external tools.  However, if one uses the bundled packages in the depend folder, PATH DEFAULT should be mentioned.

NOTE: The program will override the Bowtie options, and the package salmon will be run if both bowtie and salmon's options are provided. Therefore, if salmon is required, please comment on the Bowtie option lines, and vice versa.


SAMPLE FILE:
============
Another file is required for the program. This file is called the samples. This file should 
have the following format. Please don't change the format of the file. Simply replace the
fastq, sam and the conditions of the sample.

	###################################################################################
	#	File "sample" - sample description file
	#	Specify the names of the sample files and tag them as "treat" and "control".
	###################################################################################
	#	Specify the genome file, and specify the path where the 
	#	indexed file will be stored, and the prifex of the indexed genome.
	#	Default is 'bowtie2_genome'.
	GENOME SequenceChromosome.fasta bowtie2_genome/sequenceChr
	#	Specify the fastq files
	#	Specify the output name of the sam files.
	#	Followed by the tag/class/condition of the sample (treated or control)
	#	List all the fastq files as below.
	FASTQ sampleTreat_1.R1.fq sampleTreat_1.R2.fq sampleTreat_1.sam treat
	FASTQ sampleTreat_2.R1.fq sampleTreat_2.R2.fq sampleTreat_2.sam treat
	FASTQ sampleTreat_3.R1.fq sampleTreat_3.R2.fq sampleTreat_3.sam treat
	FASTQ sampleCtrl_1.R1.fq sampleCtrl_1.R2.fq sampleCtrl_1.sam control
	FASTQ sampleCtrl_2.R1.fq sampleCtrl_2.R2.fq sampleCtrl_2.sam control
	FASTQ sampleCtrl_3.R1.fq sampleCtrl_3.R2.fq sampleCtrl_3.sam control
	#
	#	End of file "sample"
	#

In general, this file starts with GENOME entry. As in the example, the genome file to be used in the analysis is SequenceChromosome.fasta. The next argument indicates what would be the prefix of the indexed genome, and where to store.
The following lines are FASTQ. The first argument in the entry is forward fastq file, and second is the reverse fastq file. However, if one has only single-end reads, only one entry may be there. The subsequent argument is the SAM file name. After running the bowtie what should be the output name of the sam files. In this example, sampleTreat_1.sam, sampleTreat_2.sam, etc are the sam files for sampleTreat_1.R1/R2.fq, sampleTreat_2.R1/R2.fq, etc. The last argument is the condition/class of the sample file (example: "treat" and "control").

In case of SALMON:

	###################################################################################
	#       File "sample" - sample description file
	#       Specify the names of the sample files and tag them as "treat" and "control".
	###################################################################################
	#       Specify the genome file and specify the path where the 
	#       indexed file will be stored, and the prifex of the indexed genome.
	#       Default is 'bowtie2_genome'.
	GENOME orf_coding_all.fasta transcripts_index
	#       Specify the fastq files
	#       Specify the output name of the quant files.   
	#       Followed by the tag/class/condition of the sample (treated or control)
	#       List all the fastq files as bellow.
	FASTQ sampleTreat_1.R1.fq sampleTreat_1.R2.fq sal_quant1 treat
	FASTQ sampleTreat_2.R1.fq sampleTreat_2.R2.fq sal_quant2 treat
	FASTQ sampleTreat_3.R1.fq sampleTreat_3.R2.fq sal_quant3 treat
	FASTQ sampleCtrl_1.R1.fq sampleCtrl_1.R2.fq sal_quant4 control
	FASTQ sampleCtrl_2.R1.fq sampleCtrl_2.R2.fq sal_quant5 control
	FASTQ sampleCtrl_3.R1.fq sampleCtrl_3.R2.fq sal_quant6 control
	#
	#       End of file "sample"
	#

In general, this file starts with GENOME entry. As in the example, the transcript file to be used in the analysis is transcripts.fasta. The next argument indicates what would be the prefix of the indexed file, and where to store.
The following lines are FASTQ. The first argument in the entry is forward fastq file and second is the reverse fastq file. However, if one has only single-end reads, only one entry may be there. The subsequent argument is the directory where the alignment file has to be stored. The last argument is the condition/class of the sample file (example: "treat" and "control").

DATA FILES:
===========
	1. Samples files in fastq format.
	2. Pathway analysis:
		1. TERN2GENE.csv
		   Gene Ontology to terms mapping csv file (Eg: GO:0000001,mitochondrion inheritance). This is
		   genome specific Gene ontology file.  TERM2GENE.csv is a comma delimited 2 column file.
		   First column is the GO term and second column is the gene name. User can download the GO file
		   or GFF annotation file from Genome2D webserver
		   (http://genome2d.molgenrug.nl/g2d_core_select_genbank.php)
		2. TERM2NAME.csv
		   Gene Ontology to gene mapping csv file (Eg: GO:0003688,YPK_0001). This is the GO term 
		   classification which is common for all organisms.

	3. For Bowtie implementation:
	      Genome file in fasta format
	   For Salmon implementation:
	      Transcript file in fasta format
	4. GTF file
	5. Bed file


INSTALLATION INSTRUCTION FOR THE DEPENDENCIES:
=============================================
If one or any of the above dependencies are missing user can install it  by following the instructions below.

Pyhton3:
--------
	#Ubuntu
	Ubuntu 17.10, Ubuntu 18.04 (and above) come with Python 3.6 by default. 
	Ubuntu 16.10 and 17.04 do not come with Python 3.6 by default, but it is in the Universe repository.
	You should be able to install it with the following commands:

	- sudo apt-get update
	- sudo apt-get install python3.6

	For Ubuntu 14.04 or 16.04, Python 3.6 is not in the Universe repository, and user do not need to get
	it from a Personal Package Archive (PPA). For example, to install Python from the “deadsnakes” PPA,
	do the following:
	- sudo apt-get update
	- sudo apt-get install python3.6

	#CentOS
	User should first update the system with the yum package manager:
	- sudo yum update
	- sudo yum install yum-utils
	Then install the CentOS IUS package 
	- sudo yum install https://centos7.iuscommunity.org/ius-release.rpm
	Then install Python and Pip:
	- sudo yum install python36u
	- sudo yum install python36u-pip

Installation of R:
------------------
	#Installing R on Ubuntu 19.04/18.04/16.04
	Before installing R, user need to update the system package index and upgrade all  installed packages
	using the following two commands:
	-sudo apt update
	-sudo apt -y upgrade
	After that, run the following in the command line to install base R.
	-sudo apt -y install r-base
	# Install R on CentOS 7 
	R packages are available in the EPEL repositories. It can be installed by typing:
	-sudo yum install epel-release

	Once the repository is added, install R by typing:
	-sudo yum install R

Installation of R Bioconductor packages:
----------------------------------------
	if (!requireNamespace("BiocManager", quietly = TRUE))
    		install.packages("BiocManager")

	edgeR:
	BiocManager::install("edgeR")
	DESeq2:
	BiocManager::install("DESeq2")
	NOISeq:
	BiocManager::install("NOISeq")
	limma:
	BiocManager::install("limma")
	clusterProfiler:
	BiocManager::install("clusterProfiler")
	apeglm:
	BiocManager::install("apeglm")


Samtools:
---------
	#Ubuntu 18.04 or higher
	Install samtools by entering the following commands in the terminal:
	-sudo apt update
	-sudo apt install samtools
	For the other version of Ubuntu or centose use the samtools.sh script in the package folder. User can
	go to the PorkSeq folder and open a terminal and write sh samtools.sh. The program will install samtools
	in the samtools directory. 

EXTERNAL TOOLS:
---------------
	This program uses the following tools.
	1. FastQC : This package runs the quality check
	2. Bowtie : Needed for aligning the reads
	3. Pypy : For speed and memory usage we sometime uses pypy an alternative implementation of python 3.6
	4. featureCounts from subread: a software program developed for counting reads to genomic features such
	as genes, exons, promoters and genomic bins.
	5. AfterQc: Tools for automatic filtering trimming of the fastq sequences.

To run the program the above mentions dependencies are essential. However, the executable binaries are bundled in the folder depend.
For ubuntu 18.04 or higher version use sudo apt-get update | apt-get install python3-pandas to install pandas 
User can download the depend folder along with all the dependencies from the following link
https://umeauniversity-my.sharepoint.com/:u:/g/personal/aakk0004_ad_umu_se/EZ6UF28lCcJGiuPOWQ8oVr0BtQAK1caGUEdVHuP29_I01g?e=o1K0mh  
