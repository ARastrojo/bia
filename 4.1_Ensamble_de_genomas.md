# _De Novo_ Assembly tutorial

[Introduction presentation](https://docs.google.com/presentation/d/1n6DpzoNQbPP01gbJ0mKnNw-A2Nse6IGVRUZ6tvemjrY/edit?usp=sharing)

[Gráficos de Bruijn](https://www.youtube.com/watch?v=p4XKrJ2zzQo)

**Conda environments**

Before starting to work with the data we are going to create a new _conda environment_ where we are going to install the necessary programs. Conda environments are virtual spaces where we can install whatever we need for a project without modifying the base installation. This is very usefull when some of the programs we want to use require different libraries versions that cannot be install at the same time. Additionally, if something goes wrong, we can remove the environment an start over. To create a conda environment we use the following command:

```bash
conda create -n ngs python=3.11 -y
# We can use -y option to let conda install all required libraries without asking for confirmation.
```

> We use python 3.11 because is the version were all the programs seem to work well (if something works, do not touch!!!). We can also create an environment without any python version, and let conda install the best python version when we install the first program that require python. 

[*Gdown*](https://pypi.org/project/gdown/)

To easily download files/folders from Google Drive we are going to use *gdown* package:

```bash
conda activate ngs
pip install gdown
conda deactivate

# It can also be install using conda
# conda install -c bioconda gdown -y
```

* * *

## *De Novo* Assembly

To learn the basis of *de novo* assembly we will use a small dataset to reduce computational requirements and to speed up the process. Specifically, we are going to use a subset (50k paired reads) from the reads obtained in *Ectromelia virus* genome sequencing. Therefore, in our example dataset we should expect a single genome, and hopefully a single contig/scaffold.

## 1. Pre-processing

### 1.1. Download dataset

> Activate *ngs* environment (conda activate ngs)

Download ECTV reads:
```bash
cd /home/${USER}/Documents/
mkdir unit_3
cd unit_3
gdown https://drive.google.com/uc?id=1gtnWLZWdZxn6j-oDvro2sDw3YHPZI2LM
unzip ECTV_reads.zip
```

> If _gdown_ fails, which is common due to many concurrent connections, you can download the reads manually [here](https://drive.google.com/file/d/1gtnWLZWdZxn6j-oDvro2sDw3YHPZI2LM/view?usp=sharing).

### 1.2 Checking file integrity

It is very recomended to check the integrity of the files we have just downloaded. [MD5sum](https://en.wikipedia.org/wiki/Md5sum) and other more recent programs ([SHA1sum](https://en.wikipedia.org/wiki/Sha1sum)) are algorithms that ["transform"](https://en.wikipedia.org/wiki/Cryptographic_hash_function) the content of file to a short chain of characters (hashes). Hashes do not change unless the content of the files were modified (the name of the file is not relevant). Therefore, it is very common that databases or sequencing facilities provide MD5 hashes to the users to allow for integrity checking.

In the table below you can find the MD5 hashes of ECTV reads files:

| File | MD5 |
| --- | --- |
| ECTV_R1.fastq | fa3e37e336213d01d927df2a4f0aea12 |
| ECTV_R2.fastq | 8a569dc04acc87067d33d3d58b26dd6d |

Execute the following commands to calculate MD5 hashes of the local files:

```bash
md5sum ECTV_R1.fastq 
md5sum ECTV_R1.fastq 
```

or just

```bash
md5sum *.fastq
```

> In Mac system *md5sum* command is call simple *md5*

Finally, just inspect the hashes by eye to check for any change. Usually, if files are broken for whatever reason, the md5 hash is completely different and just looking at the last 5-6 digits is going to show us if something goes wrong.

### 1.3 Counting the number of reads

Both files should have the same number of reads (Illumina paried-end reads). Is a good practice to check the number of reads in both files. Despite having checked the MD5 hashes, sometimes, database uploaded files are wrong (submitter may have uploaded truncated files rather than the original files). Additionally, knowing the number of reads would be useful for basis quality metrics (assembled/mapped reads or quality pass reads).

Most sequencing reads are in [FASTQ format](https://en.wikipedia.org/wiki/FASTQ_format). Each sequence in FASTQ format in represented in four consecutive lines:

```
@HWUSI-EAS1752R:23:FC62KHPAAXX:6:3:3542:1008 1:N:0:GCCAAT
GATTTGGGGTTCAAAGCAGTATCGATCAAATAGTAAATCCATTTGTTCAACTCACAGTTT
+
!''*((((***+))%%%++)(%%%%).1***-+*''))**55CCF>>>>>>CCCCCCC65
```

- First line, is the **sequence name** beginning with "@". The name of the sequence usually contains information of the instrument, the number of run, the lane, etc.  
- Second line is the **sequence** itself.  
- Third line is for **comments** (usually empty).  
- Last line is the **quality** of the sequence code using [Phred scale](https://en.wikipedia.org/wiki/Phred_quality_score) (currently most sequencing platforms used Phred+33 scale).  

So, the easy way of counting the number of reads in a file is using *wc* linux command (*word count*):

```bash
wc -l ECTV_R1.fastq
wc -l ECTV_R2.fastq
```

*-l* option is applied to count the number of lines. However, we have to divide by 4 to get the number of reads. To avoid this, we can apply some piping:

```bash
wc -l ECTV_R1.fastq | awk '{print $1/4}'
wc -l ECTV_R2.fastq | awk '{print $1/4}'
```

Or using a *for* loop:

```bash
for file in *.fastq;
do
reads=`wc -l ${file} | awk '{print $1/4}'`
echo $file $reads
done
```

> Why don't we use grep? ...

### 1.4. Check sequence quality with FastQC

This tool is already installed in your virtual machines, but not in the local machines. So the first step is to install [FastQC](https://www.bioinformatics.babraham.ac.uk/projects/fastqc/) program:

```bash
conda install -c bioconda fastqc -y
```

Now, run the program to check our reads:

```bash
mkdir ECTV_Quality
fastqc ECTV_R1.fastq -o ECTV_Quality/
fastqc ECTV_R2.fastq -o ECTV_Quality/
```

We can use *-t* options to increase the number of threads the program will use (not needed in this small dataset).

Open the html files to get information about the number of sequences, the length distribution, the %GCs content, the average quality, etc.

**R1 quality plot**

![r1_quality](https://user-images.githubusercontent.com/13121779/162767027-92e2adeb-bec8-4571-8257-3196cd7de944.png)

**R2 quality plot**

![r2_quality](https://user-images.githubusercontent.com/13121779/162767078-d14e19d6-40a9-498a-828d-f3b65ebad31e.png)

*Illumina tiles*

Tiles are small imaging areas on the flow cell defined as the field of view by the camera (see more details [here](https://support-docs.illumina.com/IN/NextSeq_550-500/Content/IN/NextSeq/FlowCell_Tiles_fNS.htm)). 

![tiles](https://github.com/ARastrojo/Metagenomics/blob/a55b02eba1b8c472341e62c3b62e66faa655b5ee/images/illumina_tile.png)


### 1.5. Quality filtering of raw reads

There are many popular tools, such as [CutAdapt](https://cutadapt.readthedocs.io/en/stable/), [FastX](http://hannonlab.cshl.edu/fastx_toolkit/), [Trimmomatic](http://www.usadellab.org/cms/?page=trimmomatic), etc. They are all very similar, but the last is very fast and easy to use.

Trimmomatic installation:

```bash
conda install -c bioconda trimmomatic -y
```

Running Trimmomatic:

```bash
trimmomatic PE -phred33 ECTV_R1.fastq ECTV_R2.fastq ECTV_R1_qf_paired.fastq ECTV_R1_qf_unpaired.fastq ECTV_R2_qf_paired.fastq ECTV_R2_qf_unpaired.fastq SLIDINGWINDOW:4:20 MINLEN:70
```

**Trimmomatic defaults parameters:** LEADING:3 TRAILING:3 SLIDINGWINDOW:4:15 MINLEN:36

> Trimmomatic output:
> 
> Multiple cores found: Using 2 threads
> 
> Input Read Pairs: 50000 Both Surviving: 41996 (83.99%) Forward Only Surviving: 4107 (8.21%) Reverse Only Surviving: 1791 (3.58%) Dropped: 2106 (4.21%)
> 
> TrimmomaticPE: Completed successfully

Let's take a look to some parameters:

- **SLIDINGWINDOW:** Performs a sliding window trimming approach. It starts scanning at the 5‟ end and clips the read once the average quality within the window falls below a threshold.
    
- **MINLEN**: Drop the read if it is below a specified length.
    

> What do you think about the SLIDINGWINDOW clipping strategy?
> Why do you think we have use 70 as MINLEN?
> How long are ECTV raw reads?

Now check the number of high-quality paired-end reads selected in this pre-processing step and compare their quality profile with that of the input:

```bash
mkdir ECTV_QF_Quality
fastqc ECTV_R1_qf_paired.fastq -o ECTV_QF_Quality
fastqc ECTV_R2_qf_paired.fastq -o ECTV_QF_Quality
```

**ECTV\_R1\_qf_paired quality plot**

![per_base_quality](https://user-images.githubusercontent.com/13121779/162787145-78f469bd-04fc-43c0-8ca6-2e4bc341462c.png)

**ECTV\_R1\_qf_paired quality plot**

![per_base_quality](https://user-images.githubusercontent.com/13121779/162787227-5bc4471d-100e-4737-b020-396f646be7b1.png)

General quality has improve specially at the end of the reads where quality was lower in raw sequences.

> **NOTE:** We can count quality filtered reads by using *wc* but Trimmomatic's output already have this information. However, it is important to write down this data to evaluate the general quality as the percentage of quality filtered reads.

### 1.6 Removal of reads aligning to the human and phiX174 genomes (*Bowtie2*)

phiX174 was the first genome to be sequence ([Sanger, F., Air, G., Barrell, B. et al. Nucleotide sequence of bacteriophage φX174 DNA. Nature 265, 687–695 (1977)](https://doi.org/10.1038/265687a0)). Since that, the viral genome is included as an spike by Illumina kits to control quality of the sequencing process and some contaminating reads could be left.

In the other hand, human contamination is also very frequent, as humans prepare and manipulate the samples before being sequenced. In some cases, the genome of other organisms, such as mouse or monkey, should be used instead of human, depending on the experimental set. Imagine, for instance, that we are sequencing the genome of a virus grown in VERO cells (derived from monkey kidney fibroblast). In that case we should remove all reads mapping to the monkey genome that could be present in our raw data as a contamination.

To perform short read alignment we are going to use [Bowtie2](http://bowtie-bio.sourceforge.net/bowtie2/index.shtml). There are other popular aligners such as [BWA](http://bio-bwa.sourceforge.net/), but Bowtie2 parameters are easy to understand and modify. Specially, as we are not really interested in aligned reads, those that mapped to the human or phiX174 genomes, by using Bowtie2 we have an option to extract not aligned reads easily. We can also extract unaligned reads from BWA alignment, but is not an straight way.

Before preforming the alignment, Bowtie2 requires the preparation of an index containing the reference sequences to align against, in our case the human and phiX174 genomes. We can perform this decontamination process in two steps, by first removing human reads and then phiX174 reads. Pre-built human genome indexes can be downloaded from [Bowtie2 webpage](https://benlangmead.github.io/aws-indexes/bowtie). However, as human genome is too big to run the alignment in a short time, we are going to use a smaller human reference sequence containing all coding sequence instead of the whole genome sequence (for a correct decontamination the whole human genome, or the appropriate organism, should be used).

- **Installing Bowtie2**

Let's try:

```bash
conda install -c bioconda -c default -c conda-forge bowtie2 -y
```

The installation fails in the virtual machine:

```bash
Channels:
 - bioconda
 - conda-forge
 - defaults
Platform: linux-64
Collecting package metadata (repodata.json): done
Solving environment: - warning  libmamba Added empty dependency for problem type SOLVER_RULE_UPDATE
failed

LibMambaUnsatisfiableError: Encountered problems while solving:
  - package bowtie2-2.2.1-py27h2bce143_4 requires python >=2.7,<2.8.0a0, but none of the providers can be installed

Could not solve for environment specs
The following packages are incompatible
\u251c\u2500 bowtie2 is installable with the potential options
\u2502  \u251c\u2500 bowtie2 [2.2.1|2.2.4|...|2.3.5.1] would require
\u2502  \u2502  \u2514\u2500 python [2.7* |>=2.7,<2.8.0a0 ], which can be installed;
\u2502  \u251c\u2500 bowtie2 [2.2.1|2.2.4|...|2.3.4.3] would require
\u2502  \u2502  \u2514\u2500 python [3.5* |>=3.5,<3.6.0a0 ], which can be installed;
\u2502  \u251c\u2500 bowtie2 [2.2.1|2.2.4|...|2.5.1] would require
\u2502  \u2502  \u2514\u2500 python >=3.6,<3.7.0a0 , which can be installed;
\u2502  \u251c\u2500 bowtie2 [2.2.1|2.2.4|...|2.5.1] would require
\u2502  \u2502  \u2514\u2500 python >=3.7,<3.8.0a0 , which can be installed;
\u2502  \u251c\u2500 bowtie2 [2.2.1|2.2.4|...|2.5.2] would require
\u2502  \u2502  \u2514\u2500 python >=3.8,<3.9.0a0 , which can be installed;
\u2502  \u251c\u2500 bowtie2 [2.2.1|2.2.4|...|2.5.2] would require
\u2502  \u2502  \u2514\u2500 python >=3.9,<3.10.0a0 , which can be installed;
\u2502  \u251c\u2500 bowtie2 [2.2.4|2.2.5|...|2.3.0] would require
\u2502  \u2502  \u2514\u2500 python 3.4* , which can be installed;
\u2502  \u251c\u2500 bowtie2 [2.2.4|2.2.5|...|2.3.4.1] would require
\u2502  \u2502  \u2514\u2500 python 3.6* , which can be installed;
\u2502  \u2514\u2500 bowtie2 [2.4.5|2.5.0|2.5.1|2.5.2] would require
\u2502     \u2514\u2500 python >=3.10,<3.11.0a0 , which can be installed;
\u2514\u2500 pin-1 is not installable because it requires
   \u2514\u2500 python 3.11.* , which conflicts with any installable versions previously reported.

Pins seem to be involved in the conflict. Currently pinned specs:
 - python 3.11.* (labeled as 'pin-1')

```

As mentioned before, I am not a computer expert, and I do not want to spend my time fighting against computing problems... So what can we do? Googling the error and try by copy/paste the possible solutions, which sometimes is the better solution. But here, we are going to install _Bowtie2_ ourselves:

```bash
# Create a folder to contain software and download
mkdir -p /home/metag/software/
cd /home/metag/software/
wget https://sourceforge.net/projects/bowtie-bio/files/bowtie2/2.5.2/bowtie2-2.5.2-linux-x86_64.zip
unzip bowtie2-2.5.2-linux-x86_64.zip

# Create a bin folder to make symbolic links of the required binaries
mkdir -p /home/metag/bin
cd /home/metag/bin
ln -s /home/metag/software/bowtie2-2.5.2-linux-x86_64/bowtie2 .
ln -s /home/metag/software/bowtie2-2.5.2-linux-x86_64/bowtie2-build .

# Now we have to tell the shell were to find the binaries by editing /home/metag/.bashrc using _vim_ or _nano_ and adding the next line:
export PATH=/home/metag/bin/:$PATH

# Finally, we need to initialize the profile
source /home/metag/.bashrc
```


Another options that also works is to create a new conda environment without indicating any python version to allow conda to install the most convenient:
```bash
conda create -n bowtie2 -c bioconda bowtie2 -y
````

However, this way we have to move between environments 

- **Decontamination human reads**

The file containing all human coding sequences can be download from [NCBI database](https://ftp.ncbi.nlm.nih.gov/genomes/refseq/vertebrate_mammalian/Homo_sapiens/latest_assembly_versions/GCF_000001405.40_GRCh38.p14/GCF_000001405.40_GRCh38.p14_cds_from_genomic.fna.gz). Then, we need to build the index as follows:

```bash
wget https://ftp.ncbi.nlm.nih.gov/genomes/refseq/vertebrate_mammalian/Homo_sapiens/latest_assembly_versions/GCF_000001405.40_GRCh38.p14/GCF_000001405.40_GRCh38.p14_cds_from_genomic.fna.gz
gunzip GCF_000001405.40_GRCh38.p14_cds_from_genomic.fna.gz
bowtie2-build GCF_000001405.40_GRCh38.p14_cds_from_genomic.fna.gz human_cds
```

> **Creating human_cds index will take ~1 hour...**

So, to reduce the time I have created the index for you which is located in /home/metag/Documents/data/human_cds_index/. The index is formed by several files, all of then ended with _.bt2_ extension. To avoid moving or copying files we will create symbolic links in our folder. To do so, we use a loop:

```bash
cd /home/metag/Documents/unit_3
for f in /home/metag/Documents/data/human_cds_index/*.bt2;
do
ln -s $f .
done
```

Link to download human cds index if you are using your our machine:
```bash
# Downloading index
gdown --folder https://drive.google.com/drive/folders/1ames4k0NYqKlkxObuGbjJLDh2-UwHVdH
# create symbolic links
for f in human_cds_index/*; do ln -s $f .; done
```

| File                | md5 hash                         |
|---------------------|----------------------------------|
| human_cds.1.bt2     | 895e2c36196b6e23f6b0b6860c6afd6d |
| human_cds.2.bt2     | 5d9d04a58dd4508468a242ced3de84de |
| human_cds.3.bt2     | 1a80d116d89bc49d8bb242dbca52c075 |
| human_cds.4.bt2     | eefdf85401a6553e6f6babc87b91af86 |
| human_cds.rev.1.bt2 | 6ae49bf96e7977db0442a00a0193dde6 |
| human_cds.rev.2.bt2 | 56a21b29ea45e4c121b9ac0fd3991bc9 |

- **Aligning reads against human cds**

```bash
bowtie2 -x human_cds -1 ECTV_R1_qf_paired.fastq -2 ECTV_R2_qf_paired.fastq --un-conc ECTV_qf_paired_nohuman_R%.fastq -S tmp.sam
```

> *tmp.sam* contains the aligned reads that we are not interested in, so we can remove this file.
> ECTV_qf_paired_nohuman_R%.fq.gz contains unaligned reads (the % is replaced by 1 and 2 accordingly to the read pair)

Now we can count the clean reads:

```bash
wc -l *_qf_paired_nohuman* | awk '{print $1/4}'
```

> **NOTE:** Write down the remaining number of reads after decontamination.

- **PhiX174 decontamination**

In this case, PhiX174 is small enough to run the whole process:

```bash
# Download from NCBI and decompress
wget https://ftp.ncbi.nlm.nih.gov/genomes/refseq/viral/Sinsheimervirus_phiX174/latest_assembly_versions/GCF_000819615.1_ViralProj14015/GCF_000819615.1_ViralProj14015_genomic.fna.gz
gunzip GCF_000819615.1_ViralProj14015_genomic.fna.gz

# Building Bowtie2 index
bowtie2-build GCF_000819615.1_ViralProj14015_genomic.fna phix

# Aligning human decontaminated reads against PhiX174 index
bowtie2 -x phix -1 ECTV_qf_paired_nohuman_R1.fastq -2 ECTV_qf_paired_nohuman_R2.fastq --un-conc ECTV_qf_paired_nohuman_noPhiX_R%.fastq -S tmp.sam

# Count decontaminated reads
wc -l ECTV_qf_paired_nohuman_noPhiX_R* | awk '{print $1/4}'
```

## 2. De novo assembly with Spades

We are going to use [SPAdes](https://github.com/ablab/spades), one of the most popular *de Bruijn* graph *de novo* assemblers. Take a look to the [paper](https://www.ncbi.nlm.nih.gov/pmc/articles/PMC3342519/) describing the program.

This assembler has specific assembly protocols for different experimental set (*--sc*, single cell data; *--meta*, metagenomics data; --isolate, a single genome sequencing or *--rna*, for RNA-seq) and sequencing platforms (Illumina, IonTorrent, Nanopore or PacBio). The program does not only make de novo assembly but also run an error correction tool for Illumina sequences. If we run the program with the flag --careful, SPAdes will also run MismatchCorrector, a post processing tool which uses BWA aligner. However, this flag is only recommended for assembly of small genomes and is not compatible with some other options such as single cell (*--sc*) or metagenomics (*--meta*).

Another important option is *-k* flag with which we can define the kmer length to use in the assembly. If we do not provide a list of *kmers*, the program will select automatically three of them based on average reads length and perfoms a combined assembly. Nowadays, with this modern assembler it is better to allow the program to choose the best *kmers*, but at the beginning of the genomic era researches had to choose them by making multiple assemblies. As a general rule, *kmers* have to be smaller than average read length and must be odd and less than 128 (althougth other assemblers can use longer *kmer* sizes).

### 2.1. Runing SPAdes

We can easily install SPAdes:

```bash
conda install -c bioconda spades -y
#############################################################################################
#                                                                                           #
#   Note: SPAdes installed through bioconda on MacOS may be somewhat slower than the SPAdes #
#   binaries distributed by the authors at                                                  #
#                                                                                           #
#   http://cab.spbu.ru/files/release3.15.2/SPAdes-3.15.2-Darwin.tar.gz
#                                                                                           #
#   due to unavailability of parallel libstdc++ for the Clang compiler used by bioconda on  #
#   MacOS; see https://github.com/ablab/spades/issues/194#issuecomment-523175204            #
#                                                                                           #
#############################################################################################
````

As our dataset come from a single viral genome, the most adequate protocol should be *--isolate*, however in my experience, default assembly protocol with *--careful* flag give us better results (based mainly on contig length). To check this, we are going to test both procotols:

```bash
spades.py --careful -t 2 -1 ECTV_qf_paired_nohuman_noPhiX_R1.fastq -2 ECTV_qf_paired_nohuman_noPhiX_R2.fastq -o ECTV_careful
````


```bash
spades.py --isolate -t 2 -1 ECTV_qf_paired_nohuman_noPhiX_R1.fastq -2 ECTV_qf_paired_nohuman_noPhiX_R2.fastq -o ECTV_isolate
```

### 2.2. Check the number of contigs and scaffolds:

We can take a look to the number of contigs/scaffold we have obtained from both assemblies:

```bash
grep -c '>' ./ECTV_careful/*.fasta
```

> ./ECTV\_careful/before\_rr.fasta:20
> ./ECTV_careful/contigs.fasta:8
> ./ECTV_careful/scaffolds.fasta:7

```bash
grep -c '>' ./ECTV_isolate/*.fasta
```

> ./ECTV_isolate/before_rr.fasta:22
> ./ECTV_isolate/contigs.fasta:11
> ./ECTV_isolate/scaffolds.fasta:9

*before_rr.fasta* is just a intermediate file from SPAdes, so we do not care about it.

Looking just at the number of contigs/scaffolds it seems that *--careful* option is better, but the number of contigs/scaffolds is not everything. Let's look at the lengths and coverage of the contigs (fasta headers):

```bash
grep '>' -m 5 ./ECTV_careful/*.fasta
grep '>' -m 5 ./ECTV_isolate/*.fasta
```

| Isolate | Careful |
| --- | --- |
| >NODE\_1\_length\_61911\_cov_7.828683 | >NODE\_1\_length\_61911\_cov_7.996460 |
| >NODE\_2\_length\_36053\_cov_7.188705 |     |
|     | >NODE\_2\_length\_61252\_cov_7.070559 |
| >NODE\_3\_length\_30550\_cov_10.405148 | >NODE\_3\_length\_30550\_cov_10.598754 |
| >NODE\_4\_length\_29611\_cov_8.192245 | >NODE\_4\_length\_29611\_cov_8.358201 |
| >NODE\_5\_length\_25623\_cov_6.437265 |     |
| >NODE\_6\_length\_13405\_cov_7.176180 | >NODE\_5\_length\_13405\_cov_7.334082 |
| >NODE\_7\_length\_261\_cov_1.072816 |     |
| >NODE\_8\_length\_228\_cov_1.942197 |     |
| >NODE\_9\_length\_227\_cov_0.610465 | >NODE\_6\_length\_227\_cov_0.610465 |
| >NODE\_10\_length\_102\_cov_4.340426 | >NODE\_7\_length\_102\_cov_4.595745 |
| >NODE\_11\_length\_90\_cov_11.514286 | >NODE\_8\_length\_90\_cov_11.685714 |

We can do the same for the scaffolds:

```bash
grep '>' ./ECTV_careful/scaffolds.fasta
grep '>' ./ECTV_isolate/scaffolds.fasta
```

| Isolate | Careful |
| --- | --- |
| >NODE\_1\_length\_92471\_cov_8.673347 | >NODE\_1\_length\_92471\_cov_8.849528 |
| >NODE\_2\_length\_36053\_cov_7.188705 |     |
|     | >NODE\_2\_length\_61252\_cov_7.070559 |
| >NODE\_3\_length\_29611\_cov_8.192245 | >NODE\_3\_length\_29611\_cov_8.358201 |
| >NODE\_4\_length\_25623\_cov_6.437265 |     |
| >NODE\_5\_length\_13643\_cov_7.075213 | >NODE\_4\_length\_13405\_cov_7.334082 |
| >NODE\_6\_length\_261\_cov_1.072816 |     |
| >NODE\_7\_length\_227\_cov_0.610465 | >NODE\_5\_length\_227\_cov_0.610465 |
| >NODE\_8\_length\_102\_cov_4.340426 | >NODE\_6\_length\_102\_cov_4.595745 |
| >NODE\_9\_length\_90\_cov_11.514286 | >NODE\_7\_length\_90\_cov_11.685714 |

OPTIONAL: I have writen an small Python script ([contigstats.py](https://github.com/ARastrojo/Metagenomics/blob/main/contigstats.py)) that can be use to obtain a quick view of the assembly stats. It calculates the number of sequences in a fasta file, the minimun/mean/maximun sequence length and [N50 parameter](https://en.wikipedia.org/wiki/N50,_L50,_and_related_statistics) (*Wikipedia: "Given a set of contigs, the N50 is defined as the sequence length of the shortest contig at 50% of the total genome length."*).

```bash
python contigstats.py ECTV_*/*.fasta
```

| sample | contigs | min | max | mean | n50 | bases | non\_standard\_bases |
| --- | --- | --- | --- | --- | --- | --- | --- |
| ECTV_careful/contigs.fasta | 8   | 90  | 61911 | 24644 | 61252 | 197148 | 0   |
| ECTV_careful/scaffolds.fasta | 7   | 90  | 92471 | 28165 | 61252 | 197158 | 10  |
| ECTV_isolate/contigs.fasta | 11  | 90  | 61911 | 18006 | 30550 | 198061 | 0   |
| ECTV_isolate/scaffolds.fasta | 9   | 90  | 92471 | 22009 | 36053 | 198081 | 20  |

> NOTE: I have remove *before_rr.fasta* data from the table for simplicity.

## 3. Quast comparison of assembly strategies.

A better and more complete way of making assemblies comparison is by using dedicated tools such [QUAST] (http://quast.sourceforge.net/quast.html). This tool provides different metrics of comparison among assemblies in pdf or html files and it can also be used [online] (http://cab.cc.spbu.ru/quast/).

> **Advice** (This can be complete ignored): It is always better to use existing programs and/or tools, supported by publications, than writing your own (there is no need to reinvent the wheel). However, if your project goal is to develop a new algorithm/program, of course it is worth, but if your are working in the sequencing of a new species genome, why losing the time building a new assembler? This could be applied to every program/script... Moreover, reviewers are very picky with the use of *in house* scripts/programs.

- **Installing Quast**

If we try to install Quast  in our _ngs_ environment it will fail because there are some incompatible libraries with python 3.11. Therefore we must create a new environmet for quast.
```bash
# conda deactivate # To exist from ngs environment
conda create -n quast -y # we do no specify any python version
conda activate quast
conda install -c bioconda quast -y

# or 
# conda create -n quast -c bioconda quast -y
```

Now that we have QUAST, we must have all contigs/scaffolds from the different assemblies in the same folder, but instead of copying the data, to reduce redundancy and save a little bit of disk space, we are going to make use of symbolic links.

> NOTE: saving a disk space is irrelevant in this case, but when you are working in a real metagenomic or other sequencing projects it could be an important issue.

```bash
cd /home/metag/Documents/unit_3/
mkdir quast
ln -rs ./ECTV_careful/contigs.fasta ./quast/contigs_careful.fasta 
ln -rs ./ECTV_careful/scaffolds.fasta ./quast/scaffolds_careful.fasta 
ln -rs ./ECTV_isolate/contigs.fasta ./quast/contigs_isolate.fasta 
ln -rs ./ECTV_isolate/scaffolds.fasta ./quast/scaffolds_isolate.fasta 
```

> _-r_ option does not exit in mac, take care creating symbolic link in mac with relative path, although it is possible to create them, it is better to use absolute paths in mac. 

```bash
# links in mac
cd quast
ln -s ../ECTV_careful/contigs.fasta contigs_careful.fasta
ln -s ../ECTV_careful/scaffolds.fasta scaffolds_careful.fasta
ln -s ../ECTV_isolate/contigs.fasta contigs_isolate.fasta
ln -s ../ECTV_isolate/scaffolds.fasta scaffolds_isolate.fasta
```

Additionally, in this single genome sequencing example we have a reference genome to compare our assemblies with (this is not possible for metagenomes), so we are going to supply this reference genome to quast: 

```bash
cd quast
wget ftp.ncbi.nlm.nih.gov/genomes/all/GCF/000/841/905/GCF_000841905.1_ViralProj14211/GCF_000841905.1_ViralProj14211_genomic.fna.gz
gunzip GCF_000841905.1_ViralProj14211_genomic.fna.gz 
mv GCF_000841905.1_ViralProj14211_genomic.fna ECTV_reference_genome.fasta
conda activate quast
quast.py contigs* scaffolds* -R ECTV_reference_genome.fasta
conda deactivate
```

Take a look to the report.pdf/report.html (also report.txt):

![Cummulative length](https://user-images.githubusercontent.com/13121779/163284268-3b69abd3-dfe4-4ffc-9350-beb36f96f415.png)

**Assembly stats**

![Genome stats Screenshot](https://user-images.githubusercontent.com/13121779/163284470-d08f2ff8-4055-4dfe-9bad-09c036259e5e.png)

## 4. _De novo_ Assembly of metagenome (virome)

In order to have a more realistic example of the whole process we are going to use simulated metagenomic reads. I have used [InSilicoSeq](https://github.com/HadrienG/InSilicoSeq). The advantage of using simulated reads instead of real metagenomic data resides in the fact that with simulated reads we can have the original genomes used for the simulation and the proportion of each one in the data. 

To speed up the process, and to show you how to _automate_ it, in this case we are going to use a _bash_ script. Fisrt, we need to create a folder 

```bash
cd /home/metag/Documents/
mkdir unit_3b
cd unit_3b
```

Then, create a file with _vim_, _nano_ or other plain text editor and save it as virome_script.sh (in the folder we want to execute the script):

```bash
#!/bin/bash

# We need to add this command to avoid a conda initiation problem when running from a script -> https://stackoverflow.com/questions/34534513/calling-conda-source-activate-from-bash-script
eval "$(conda shell.bash hook)"

# Activation ngs environment
conda activate ngs

# Download reads
gdown 1QModYfordyNU0LjnE27-plr-QEftbSi5

# If you are using virtual machine the file is already downloaded 
# ln -s /home/metag/Documents/data/viromas/virome_1.tar.gz .
tar -xzf virome_1.tar.gz

# Raw reads quality  assessment
mkdir quality
fastqc virome_1_R1.fastq.gz -o quality
fastqc virome_1_R2.fastq.gz -o quality

# Quality filtering
trimmomatic PE -phred33 virome_1_R1.fastq.gz virome_1_R2.fastq.gz \
    virome_1_R1_qf_paired.fq.gz virome_1_R1_qf_unpaired.fq.gz \
    virome_1_R2_qf_paired.fq.gz virome_1_R2_qf_unpaired.fq.gz \
    SLIDINGWINDOW:4:20 MINLEN:150 LEADING:20 TRAILING:20 AVGQUAL:20

# QF reads quality assessment
fastqc virome_1_R1_qf_paired.fq.gz -o quality
fastqc virome_1_R2_qf_paired.fq.gz -o quality

# Decontaminating human reads
bowtie2 -x ../unit_3/human_cds -1 virome_1_R1_qf_paired.fq.gz -2 virome_1_R2_qf_paired.fq.gz --un-conc-gz virome_1_qf_paired_nonHuman_R%.fq.gz -S tmp.sam

# Decontaminating PhiX174 reads
bowtie2 -x ../unit_3/phix -1 virome_1_qf_paired_nonHuman_R1.fq.gz -2 virome_1_qf_paired_nonHuman_R2.fq.gz --un-conc-gz virome_1_qf_paired_nonHuman_nonPhix_R%.fq.gz -S tmp.sam

# Assembly
spades.py -t 4 --careful -1 virome_1_qf_paired_nonHuman_nonPhix_R1.fq.gz -2 virome_1_qf_paired_nonHuman_nonPhix_R2.fq.gz -o virome_1_careful
spades.py -t 4 --meta    -1 virome_1_qf_paired_nonHuman_nonPhix_R1.fq.gz -2 virome_1_qf_paired_nonHuman_nonPhix_R2.fq.gz -o virome_1_meta
spades.py -t 4 --sc      -1 virome_1_qf_paired_nonHuman_nonPhix_R1.fq.gz -2 virome_1_qf_paired_nonHuman_nonPhix_R2.fq.gz -o virome_1_sc
conda deactivate

# Assembly analysis

# Activation quast environment
conda activate quast
mkdir quast
cd quast
ln -s ../virome_1_careful/contigs.fasta   virome_1_contigs_careful.fasta
ln -s ../virome_1_careful/scaffolds.fasta virome_1_scaffolds_careful.fasta
ln -s ../virome_1_meta/contigs.fasta      virome_1_contigs_meta.fasta
ln -s ../virome_1_meta/scaffolds.fasta    virome_1_scaffolds_meta.fasta
ln -s ../virome_1_sc/contigs.fasta        virome_1_contigs_sc.fasta
ln -s ../virome_1_sc/scaffolds.fasta      virome_1_scaffolds_sc.fasta
ln -s ../virome_1_genomes.fasta           virome_1_genomes.fasta
quast.py virome_1_contigs_careful.fasta virome_1_contigs_meta.fasta virome_1_contigs_sc.fasta virome_1_scaffolds_careful.fasta virome_1_scaffolds_meta.fasta virome_1_scaffolds_sc.fasta -R virome_1_genomes.fasta
conda deactivate
```

We have to give execution permision to the file:

```bash

# Create virome_script.sh
chmod 755 virome_script.sh 
# or 
chmod +x virome_script.sh
````

Now, run the script:
```bash
./virome_script.sh
```

> How can we improve the script to used with a different input file?

> Why using _single_cell_ option for a metagenome assembly? Take a look to this [paper](https://www.ncbi.nlm.nih.gov/pmc/articles/PMC6511391/).

## 5. Homework

Repeat all the steps with a viral metagenome from /home/metag/Documents/data/viromes/ of your choice (or from the [Google drive folder](https://drive.google.com/drive/folders/1lzKVp_bkAkLcS5b2Sk5eeAYzzZU5s8R7?usp=sharing)).   

Compare different *de novo assemblies* options (try --meta) or different *kmer* values or different quality filtering parameters.  

You must perform at least 3 different assemblies. 

Write a brief summary describing the bioinformatic pipeline you have followed (trimming, decontamination, improve in quality, number of reads remove in each step, etc.). Compare different *de novo assemblies* with QUAST and choose the best based on the obtained metrics (smaller number of contigs, higher N50, smaller L50, longest total assembly length, etc.).

> NOTE: In the quality filtering step, modify the MINLEN argument considering the original read length. Consider that reads with a minimum of 50% of the average original size are ok for subsequent analyses.

Submit this document as a task to Moodle/Unit3 before 15th of May.
