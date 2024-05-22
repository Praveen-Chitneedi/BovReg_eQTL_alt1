[![Nextflow version](https://img.shields.io/badge/Nextflow-v20.01.0-brightgreen)](https://www.nextflow.io/) [![Docker version](https://img.shields.io/badge/Docker-v20.10.8-blue)](https://www.docker.com/) [![Singularity-ce version](https://img.shields.io/badge/Singularity-v3.11.4-green.svg)](https://www.sylabs.io/) [![Podman version](https://img.shields.io/badge/Podman-v3.4.4-violet.svg)](https://podman.io/)


# eQTL-Detect: Nextflow based pipeline for eQTL detection

eQTL-Detect is a [Nextflow](https://www.nextflow.io/) based bioinformatics workflow to detect the cis, trans and splicing eQTLs (expresssion quantitative trait loci) by perfoming associations with genotype and expression data.
This repository provides the required Nextflow-DSL2 scripts to run the pipeline and demo data for trial run. User can run the whole analysis either with a single standalone script or using three separate script modules and all the required tools for running the pipeline can be installed using either docker or singularity or podman container tecnology. We also provided some basic profiles for running the pipeline on different HPC environments.
This pipeline was primarily developed to detect eQTLs in cattle (Bos taurus), but users can adopt this pipeline for other species by providing the reference genome assembly and transcriptome annotation gtf files of the species of interest.

## Software required
Users need to install [Nextflow](https://www.nextflow.io/) and a container tool, which is either [Docker](https://www.docker.com/) or [Singularity](https://www.sylabs.io/) or [Podman](https://podman.io/).


##  Input file formats and demo data
Users can download the demo data and can perform a trail run of the pipeline and the links for downloading the test data are given below.

- The reference genome and annotation file for the trial run can be downloaded here [reference genome:](https://ftp.ensembl.org/pub/release-109/fasta/bos_taurus/dna/Bos_taurus.ARS-UCD1.2.dna.toplevel.fa.gz) fasta format and [reference annotation:](https://ftp.ensembl.org/pub/release-109/gtf/bos_taurus/Bos_taurus.ARS-UCD1.2.109.gtf.gz) gtf format. The path of the reference genome and the corresponding annotation file should be declared in [nextflow.config](https://github.com/BovReg/BovReg_eQTL/blob/main/nextflow.config).
  

- The genotype should be provided in [vcf](https://samtools.github.io/hts-specs/VCFv4.3.pdf) format, a demo data for test run can be downloaded here: Demo_genotype_data [download](https://zenodo.org/records/10997393/files/Demo_genotype_BovReg.tar.gz?download=1) and a [Demodata/Geno_input.tsv](https://github.com/BovReg/BovReg_eQTL/blob/main/Demodata/Geno_input.tsv) file was provided with the file paths of different chromosomes present in the demo data. ( Note: For the trial run the downloaded folder containing all the vcf.gz files "Demo_genotype_BovReg.tar.gz" should be saved and uncompressed in the folder [Demodata](https://github.com/BovReg/BovReg_eQTL/blob/main/Demodata)) . 

- The Phenotype data can be provided in any of the following formats and all the input files paths should be provides in a tsv file. An example tsv files with demodata is available in the folder [Demodata](https://github.com/BovReg/BovReg_eQTL/tree/main/Demodata).

   1. Raw data (RNAseq expression data in fastq format): Demo_data [download](https://zenodo.org/records/7949616/files/Demo_RNAseqData_BovReg.tar.gz?download=1) and a [Demodata/fastq_paired_input.tsv](https://github.com/BovReg/BovReg_eQTL/blob/main/Demodata/fasta_paired_input.tsv) file was provided with the file paths for different fastq samples present in the demo data. ( Note: For the trial run the downloaded folder containing the corresponding fastq files "Demo_RNAseqData_BovReg.tar.gz" should be saved and uncompressed in the folder [Demodata](https://github.com/BovReg/BovReg_eQTL/blob/main/Demodata)) . 

   2. Aligned reads (RNAseq expression data in bam format): Demo_data [download](https://zenodo.org/records/7950181/files/Demo_RNAseqBam_BovReg.tar.gz?download=1) and a [Demodata/Bam_input.tsv](https://github.com/BovReg/BovReg_eQTL/blob/main/Demodata/Bam_input.tsv) file was provided with the file paths for different bam samples present in the demo data. ( Note: For the trial run the downloaded folder containing the corresponding bam files "Demo_RNAseqBam_BovReg.tar.gz" should be saved and uncompressed in the folder [Demodata](https://github.com/BovReg/BovReg_eQTL/blob/main/Demodata)) .

   4. Expression counts across samples for genelevel, transcript level and spilcing counts (expression count matrices as text file): Demo_data [Demodata/Count_matrices.tsv](https://github.com/BovReg/BovReg_eQTL/blob/main/Demodata/Count_matrices.tsv). 
   

- The genotype-phenotype corresponding samples information should be provided as text file: [Demo_data/RNA_WGS_CorresID_BovReg.txt](https://github.com/BovReg/BovReg_eQTL/blob/main/Demodata/RNA_WGS_CorresID_BovReg.txt).


## Commands to run the pipeline:

This analysis can run with a single script or by using modular scripts based on user preferences.

-  Users should provide the read type and read strandedness for the RNAseq data with boolean logic true or false in the [nextflow.config](https://github.com/BovReg/BovReg_eQTL/blob/main/nextflow.config) file:

  - **Parameters** 
    - Read type: --pairedEnd_reads, --singleEnd_reads 
    - Strandedness: --firstStranded, --secondStranded and --unStranded
    - Additionally all the other parameters required to run the pipeline are defined in [nextflow.config](https://github.com/BovReg/BovReg_eQTL/blob/main/nextflow.config) file, users can change the default values if required.

- **Single command approach:** To run the whole pipeline with single command.

      nextflow run main.nf
    
   - The alignment step can be skipped if the user has aligned bam files as input, which can be mentioned as boolean logic 'true' in [nextflow.config](https://github.com/BovReg/BovReg_eQTL/blob/main/nextflow.config).

   - This script can also run only by providing the expression count matrices, which can be mentioned as boolean logic 'true' in [nextflow.config](https://github.com/BovReg/BovReg_eQTL/blob/main/nextflow.config).



- **Modular approach:** For modular analysis users can opt for the following scripts.
     Note: Users can skip Module 1, if they have aligned and sorted bam files and if users have count matrices the modules 1 and 2 can be ignored and only Module 3 can be used for eQTL detection.

  - Module 1: Indexing the reference genome and aligning the RNAseq reads

           nextflow run module_1_eQTLDetect.nf

  - Module 2: Extract genotypes from samples having corresponding RNAseq data, Quantification and merging RNAseq samples counts.

        nextflow run module_2_eQTLDetect.nf

  - Module 3: Perform cis, trans and sQTL mapping
    
         nextflow run module_3_eQTLDetect.nf  

