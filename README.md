# tagseq-qiime2-snakemake
Pipeline to run qiime2 with snakemake



# Set up

* make working directory of raw_data (sra explorer tool), intro dataset as test
* Bioproject: PRJNA393172 - test data from Hu et al. 2018
* Go to [SRA explorer](https://ewels.github.io/sra-explorer/) to search with BioProject, select bash script for downloading FastQ files.
* Make sure database is set up  - link to other repo
* Generate manifest file

## Set up working directory & conda environment
```
git clone https://github.com/shu251/tagseq-qiime2-snakemake.git
cd tagseq-qiime2-snakemake

# Create conda environment
conda env create -f /envs/snake-18S-env.yaml --name snake-18S 

# Enter environment
source activate snake-18S

# Check versions and correct environment set up
snakemake --version
qiime info
```

## Download tag-sequencing data from SRA

```
cd tagseq-qiime2-snakemake/raw_data/
# Copy output from SRA explorer into a new text file 'download.sh'
nano download.sh

# Download all sequences
bash download.sh
```

## Create required files for input into QIIME2 and snakemake
The ```write-manifest.R``` script will input the list of fastq files it finds in ```raw_data/``` to generate a QIIME2 specific manifest file (manifest.txt) and a text file with the SRR ID list for input into snakemake and a corresponding list of sample names.
```
# Run R script
Rscript write-manifest.R

# outputs:
## manifest.txt
## SampleList.txt
```


## Run quality control and trimming
Modify config.yaml file to include path to raw sequences and where output trimming and QC files will be written to.

Snakemake pipeline performs fastqc on all raw reads. Then uses trimmomatic to remove barcodes and repeats the fastqc post-trimming. Finally, uses data from fastqc runs to generate a multiqc report.


```
snakemake --use-conda
# Error message related to how snakemake is reading in conda (finding?) conda environment
```


### Now we have trimmed reads


### Run write-manifest.R script


### DADA2 specs

```
qiime dada2 denoise-paired --help # for help menu
```
# Modify the config.yml file to add your preferences for each flag:

#Truncate sequences at the 3' end when sequence quality may decrease
--p-trunc-len-f #forward read truncation
--p-trunc-len-r #reverse read truncation

#Trim forward and reverse reads at 5' end based on quality
--p-trim-left-f #forward read trim
--p-trim-left-r #reverse read trim

#Quality threshold for above trimming
--p-trunc-q

#Any reads with expected error higher than this value will be removed
--p-max-ee

#Choice of chimera removal - Choices('pooled', 'none', 'consensus')
--p-chimera-method

#Number of reads to consider in training set for error model
--p-n-reads-learn # defaul is 1 million

# Feature table - or 'OTU/ASV table'
--o-table

# Representative sequences for the feature table
--o-representative-sequences ARTIFACT FeatureData[Sequence]

# Statistics output
--o-denoising-stats

