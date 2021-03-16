# Genome Assembly Pipeline

Ermin Zhao 2021

## Overview

Genome assembly (or more generally sequence assembly) refers to the act of “assembling” raw DNA sequence data into larger contiguous sequences. If sequencing a genome, the ideal assembly would consist of a one sequence per chromosome.The following is an bacterial genome assembly pipeline. Data can be donwloaded from the NCBI Sequence Read Archive (**SRA**).

[**NCBI SRA**](https://www.ncbi.nlm.nih.gov/sra)

### Identify issues in Sequence Data

The NCBI SRA contains a massive amount of sequence data from Next Generation Sequencers. Obtaining this data is fairly straightforward and can be accomplished through the cluster. To download the “**.sra**” files, like a file called SRR495762, use the following command:

`module load sratoolkit/2.8.0`

`prefetch SRR495762`

For space reasons, the SRA data is compressed beyond normal “zipping”, use software **sratoolkit** to convert from SRA format to FASTQ format.

`fastq-dump --split-3 --gzip -O /your/output/directory/ file.sra`

Run FastQC to check problems like duplicate reads, adapter contamination and low average per site quality scores. 

`module load fastqc/0.10.1`

`fastqc /your/input/directory//*.fq.gz -o /your/output/directory/`


### Improve the overall Qaulity of Sequence Data

Now that you’ve identified potential issues with your sequence data, your goal is to improve the data using bioinformatic tools. We will run two programs (tally and trim_galore) which will improve the level of duplicate reads, reduce adapter contamination, and remove low quality bases.

Ran Tally to remove duplicate paired-end reads.  

`tally -i input_1.fastq.gz -j input_2.fastq.gz --pair-by-offset --with-quality -o output_1.fastq.gz -p output_2.fastq.gz`

Remove adapter contamination and low-quality bases using [Trim Galore v0.4.3](https://www.bioinformatics.babraham.ac.uk/projects/trim_galore/) 

`trim_galore -q 30 --stringency 1 --gzip --length 50 –o /output/directory/ --paired input_1.fastq.gz input_2.fastq.gz`

### Genome Assembly

K-mers are used in modern genome assembly algorithms. Simplistically, we break the sequence reads into smaller k-mers and then look for overlap between k-mers in the assembly of larger contiguous sequences. The size of the k-mer chosen in your assembly is one of the most important parameters. A small k-mer size may produce more, but smaller contigs, while larger k-mer size may produce fewer, but larger contigs. The art is finding the sweet spot.

Velvet, abyss and spades are software used to do assembly, here we use **SPAdes** as an example:

`module load SPAdes/3.9.1` 

`spades.py --careful -1 /home/your/input_file_1.fq.gz -2 /your/input_file_2.fq.gz -k 29,39,49,59,69,79,89,99 -o /your output directory`


### Annotation

Gene prediction and annotation software are Prodigal, Prokka, DDBJ Fast Annotation and Submission Tool (DFAST) and Rapid Annotations using Subsystems Technology (RAST). 





