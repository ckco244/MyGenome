# My Genome
## Table of Contents

- [Overview](#overview)
- [Prerequisites](#prerequisites)
- [Signing in to VM](#signing-in-to-vm)
- [Installation of Software](#installation-of-software)
- [Usage](#usage)
  - [Downloading Sequence Data](#downloading-sequence-data)
  - [Quality Analysis](#quality-analysis)
  - [Trimming](#trimming)
  - [Genome Assembly](#genome-assembly)
  - [Genome Completeness Using BUSCO](#busco)
  - [Gene Prediction](#Gene-Prediction-and-Annotation-Pipeline-using-SNAP,-AUGUSTUS,-and-MAKER)
  - [Uploading Data](#uploading-data)
- [Contributing](#contributing)
- [License](#license)

## Overview
The primary objective of this Applied Biotechnology project is to assemble and analyze fungal genomes using computational tools and command-line interfaces. By obtaining raw genome sequence data from various fungal species, we aim to assess the quality of sequencing reads and perform genome assembly using bioinformatics pipelines.

Our workflow involves FastQC for quality control, Linux-based command-line tools for data processing, and Velvet for de novo genome assembly. Additionally, we employ BLAST for sequence alignment and annotation, enabling us to compare assembled genomes against existing databases. Through this process, we gain hands-on experience with genomic data analysis, computational biology techniques, and the critical evaluation of genome assembly results.

This GitHub details the steps taken to complete this as well as the commands used to complete the required tasks.

## Prerequisites

- A VM instance set up on UKY CS department server
- Windows device with power shell

## Signing in to VM

Open power shell on your laptop and enter the following command

```bash
#Command to log into VM server
ssh abc123@abc123.cs.uky.edu
```

where abc123 is your linkblue id. You will then be prompted to enter in your password. Enter it in (your password will not be shown as you type it out).

## Installation of Software

*Work done moving forward should be completed in screen to avoid interruptions in the installation process.*

1. Download a compressed archive containing the files we will need for class:
```bash
wget https://www.cs.uky.edu/~acta225/CS485/workshop-materials.tar.xz
```

2. Extract the files in the archive:
```bash
tar xJvf workshop-materials.tar.xz
```

3. When this command finishes, you can save some space by deleting the archive
```bash
rm workshop-materials.tar.xz
```

*Make sure you are still in your home directory before continuing.*

4. Run the following command to download an install script to install all the appropriate programs and data we will need moving forward.
```bash
wget https://www.cs.uky.edu/~acta225/CS485/vm_soft_setup.sh
```

5. We can now run the install script.
```bash
sudo bash vm_soft_setup.sh
```

## Usage
### Downloading Sequence Data
Sequence data is provided in fastq file formats from various sources. Sequence data used in this project was sourced from Dr. Mark Farman. We will used the scp protocol to download sequence data to our VM instance.
```
# Copies Po4 folder from Farman VM to personal VM containing my assigned genome sequences
scp -r ngs@10.163.183.71:Desktop/Po4 MyGenome
```

The imported files had difficult names to read so they were renamed as Pd8838_1.fq.gz and Pd8838_2.fq.gz respectively using the mv command
```
mv 'difficult to read filename.fq.gz' Pd8838_1.fq.gz
mv 'difficult to read filename 2.fq.gz' Pd8838_2.fq.gz
```
The files were then unzipped using gunzip

```
#Unzipping files
gunzip Pd8838_1.fq.gz
gunzip Pd8838_1.fq.gz

#Deleting zipped files
rm Pd8838_1.fq.gz
rm Pd8838_2.fq.gz
```
the mygenome directory now contains two uncompressed fastq files of the sequencing reads.

Pd8838_1.fq and Pd8838_2.fq

These will be the two files used for quality analysis and assembly.

### Quality Analysis
Before genome assembly can take place it is important to understand the quality of data we are working with. There are many different programs to do this but we will be using fastqc for this project.

```
#Running Fastqc with two input files
fastqc Pd8838_1.fq Pd8838_2.fq
```

This command runs and returns .html files with the results for each file.
These .html files were transferred to a local machine for viewing using the scp protocol. 

```
#Transferring .html files for viewing
scp -r ckco244@ckco244.cs.uky.edu:/MyGenome/Po4/Pd8838_1_fastqc.html "C:/downloads"
scp -r ckco244@ckco244.cs.uky.edu:/MyGenome/Po4/Pd8838_2_fastqc.html "C:/downloads"
```
A preview of the forward read Pd8838_1.html file is shown below. The full PDF can be found [here](https://github.com/user-attachments/files/19211406/Pd8838_1.pdf).
![Image](https://github.com/user-attachments/assets/48a06687-5de0-4f14-8c81-5f4943a015a6)

As we can see from the fasqc analysis, there are a couple issues present with our genome. The per tile sequenc quality fails but is unfortunately an artifact of measurement and can not be remedied with trimming. One thing we can attempt to improve is our adaptor contamination of the genome. This will be illustrated in the next section trimming.

### Trimming
Trimming is a critical preprocessing step in genome assembly that enhances the quality of sequencing data. It involves the removal of low-quality sequences, adapter contamination, and other artifacts from raw sequencing reads. This step is essential to ensure the accuracy of downstream analyses, including genome assembly, sequence alignment, and annotation.

**Trimmomatic** is a powerful tool designed for trimming sequencing reads in FASTQ format. It applies several trimming operations to optimize data quality:

**ILLUMINACLIP**: Removes adapter sequences from the reads.  
**SLIDINGWINDOW**: Trims reads based on a sliding window approach, cutting when the average quality score falls below a specified threshold.  
**LEADING & TRAILING**: Removes low-quality bases from the start (**LEADING**) or the end (**TRAILING**) of each read.  
**MINLEN**: Discards any reads that fall below a specified length after trimming.  

The following command demonstrates how to use Trimmomatic for trimming paired-end reads:

```
# Trimmomatic command for genome assembly
java -jar Trimmomatic-0.38/trimmomatic-0.38.jar PE -threads 2 -phred33 -trimlog myLogfile.txt \
Po4/Pd8838_1.fq Po4/Pd8838_2.fq \
Pd8838_1_p.fq Po4/Pd8838_1_up.fq \
Po4/Pd8838_2_p.fq Po4/Pd8838_2_up.fq \
ILLUMINACLIP:Po4/adaptors.fasta:2:30:10 SLIDINGWINDOW:20:20 MINLEN:120
This code block takes in both read files, and outputs paired and unpaired files for both reads. Two files in, four files out. For genome assembly, we will be using only the paired files produced by trimmomatic 'Pd8838_1_p.fq' and 'Pd8838_2_p.fq'
```
A preview of the trimmed forward read Pd8838_1_p.html file is shown below. The full PDF can be found [here](https://github.com/user-attachments/files/19211548/Pd8838_1_p.pdf).
![Image](https://github.com/user-attachments/assets/0baa38a4-a6ec-44f5-a80d-a5d45cc70581)

As we can see from using Trimmomatic, we have fixed the issue with adaptor contamination at the cost of sequence length distribution. But this dataset should be sufficient for genome assembly.

### **Genome Assembly**

After preprocessing the reads with Trimmomatic, the next step is to perform de novo genome assembly using Velvet. Velvet is a widely used tool for short read genome assembly, especially for next-generation sequencing (NGS) data. It uses a de Bruijn graph approach to assemble the reads into longer contigs.

### **Velvet Assembly Process**

The process of genome assembly involves three primary tools:

**Velvet**: The core assembler, which takes the preprocessed, high-quality paired-end reads and assembles them into contigs.  
**VelvetG**: A graphical interface and additional utility for managing the assembly process, such as adjusting assembly parameters.  
**VelvetOptimizer**: A tool that helps find the best assembly parameters by optimizing values like k-mer length.  

#### 1. **Run Velvet (Initial Assembly)**

This step runs the Velvet assembler, specifying the input reads and the desired k-mer length (`k`). The k-mer length is an important parameter, and it is recommended to try different values to find the best assembly result.
```
#Command to run velvet with a inital kmer value of 120
velveth 120 -shortPaired -fastq -separate Pd8838_1_paired.fq Pd8838_2_paired.fq
```
**Explanation:**  
assembly_dir: Directory where Velvet will output the assembled contigs. (ours was blank so it reates a default directory)  
120: The k-mer length (you can experiment with different k-mer values).  
-fastq -shortPaired: Specifies paired-end reads in FASTQ format.  
Pd8838_1_p.fq and Pd8838_2_p.fq: These are the paired-end, trimmed files from Trimmomatic.  

### 2. Run VelvetOptimizer (Optimize k-mer Length)

**VelvetOptimizer** is a tool designed to help you find the optimal **k-mer length** for genome assembly. By testing multiple k-mer values, VelvetOptimizer selects the best one based on the resulting assembly quality. This is a critical step in genome assembly, as the choice of k-mer length directly impacts the accuracy and contiguity of the final assembled genome.

#### **Step 1: Initial Run to Find an Optimal k-mer Range**

The following command initiates VelvetOptimizer to test a range of k-mer values, with a step size of **10**. This initial run helps to identify a suitable k-mer range where the optimal value may lie.

```bash
# Run VelvetOptimizer to find the best k-mer length in the range 61 to 131 with steps of 10
sbatch velvetoptimiser_noclean.sh Pd8838 61 131 10
```

The velvetoptimiser_noclean.sh script is provided with the following arguments:

61: The starting k-mer value.  
131: The ending k-mer value.  
10: The step size for testing k-mer values in increments of 10.  

velvetoptimizer_noclean.sh is detailed below
```bash
Copy
Edit
#!/bin/bash

#SBATCH --time 48:00:00
#SBATCH --job-name=VelvetOptimiser
#SBATCH --nodes=1
#SBATCH --ntasks=8
#SBATCH --cpus-per-task=16
#SBATCH --partition=normal
#SBATCH --mem=180GB
#SBATCH --mail-type=ALL
#SBATCH --mail-user=user@domain.com

echo "SLURM_NODELIST: "$SLURM_NODELIST

# Parameters
strainID=$1
lowK=$2
highK=$3
step=$4

# Create a directory for the strain
mkdir $strainID

# Copy paired-end FASTQ files into the strain's directory
cp $strainID*1_paired*f*q* $strainID/
cp $strainID*2_paired*f*q* $strainID/

cd $strainID

# Create hard-coded read names for VelvetOptimizer
cp $strainID*1_paired*f*q* forward.fq
cp $strainID*2_paired*f*q* reverse.fq

# Load the Singularity container to run VelvetOptimizer
module load ccs/singularity

# Run VelvetOptimizer inside the Singularity container
singularity run --app perlvelvetoptimiser226 /share/singularity/images/ccs/conda/amd-conda2-centos8.sinf VelvetOptimiser.pl \
  -s $lowK -e $highK -x $step -d velvet_${strainID}_${lowK}_${highK}_${step}_noclean -o ' -clean no' -f ' -shortPaired -fastq -separate forward.fq reverse.fq'

# Clean up by removing the hard-coded forward and reverse read files
rm forward.fq
rm reverse.fq

# Rename the optimal assembly from contigs.fa to <strainID>.fasta
mv velvet_${strainID}_${lowK}_${highK}_${step}/contigs.fa velvet_${strainID}_${lowK}_${highK}_${step}/${strainID}_${lowK}_${highK}_${step}".fasta"

# Copy the final assembly to the central directory for assemblies
cp velvet_${lowK}_${highK}_${step}/${strainID}".fasta" /project/farman_s24cs485g/ASSEMBLIES/${strainID}_${lowK}_${highK}_${step}".fasta"

# Copy the log file to the central directory and rename it with the strain identifier
logfile=`ls velvet_${lowK}_${highK}_${step}/*Logfile.txt`
cp $logfile /project/farman_s23cs485g/ASSEMBLIES/${strainID}_${lowK}_${highK}_${step}_${logfile/*\//}

# Optionally: Backup the assemblies and output files to cloud storage (uncomment to enable)
# rclone copy ASSEMBLIES/${f}".fasta" GoogleDrive:LCC/ASSEMBLIES
# rclone copy velvet_assembly GoogleDrive:LCC/${f}_velvet_assembly

# Return to the starting directory
cd ..
```
**Explanation:**  
SLURM Job Options: The script starts by specifying the SLURM job parameters, such as time limit, number of nodes, CPUs per task, memory, and email notifications.  
Input Parameters: The script accepts four arguments:  
strainID: The identifier for the strain being processed.  
lowK: The starting k-mer value.  
highK: The ending k-mer value.  
step: The step size for testing k-mer values.


**File Management:**  
It creates a new directory for the strain.  
Copies the paired-end FASTQ files into that directory.  
Renames the files for consistency in VelvetOptimizer.  
VelvetOptimizer Execution: The script loads the Singularity container and runs the VelvetOptimizer.pl script with the specified k-mer range and parameters.


**Output Handling:**  
After running VelvetOptimizer, the script removes temporary files.  
Renames the final assembly file to a specific format and copies it to the ASSEMBLIES directory.  
The log file is also copied for reference.  
The results of this run will help VelvetOptimizer determine the optimal k-mer length by evaluating assembly quality across different k-mer values in this range.  

**Step 2: Refining the k-mer Range Around the Optimal Value**  
After running the first VelvetOptimizer command, an optimal k-mer value of 100 was identified. To fine-tune the k-mer value, the following command is used to narrow the search to a range between 95 and 105, with a step size of 2.

```
sbatch velvetoptimiser_noclean.sh Pd8838 95 105 2
```
This narrowed search focuses on k-mer values around 100, which was found to be optimal in the previous run. The result of this second run returned the final optimal k-mer value of 99. This is the kmer value that will be used for final assembly.

## BUSCO

**BUSCO** (*Benchmarking Using Single-Copy Orthologs*) is a tool for assessing genome completeness. It works by searching for a set of highly conserved, single-copy orthologous genes using `tblastn`. These genes are expected to be present in all genomes of a given lineage and provide a metric for genome assembly quality.

BUSCO includes databases for a wide range of lineages:

- Animals
- Plants
- Fungi
- Others

These databases can be further refined to more specific groups (e.g., phylum, class).  
For this analysis, we will use the **odb10_ascomycota** database, specific to the fungal phylum *Ascomycota*.

### Instructions for Running BUSCO

### 1. Log into the MCC Supercomputer

Use your preferred SSH client to connect to the MCC supercomputer.

### 2. Navigate to Your Working Directory

```
bash
cd /project/farman_s25abt480/yourUserName
```

copy the BuscoSingularity.sh script into this working directory and edit using nano to include your email address.

Run the script as follows

```
sbatch /project/farman_s24cs485g/SLURM_SCRIPTS/BuscoSingularity.sh path/to/MyGenome.fasta
```
**note: this run will take quite a while to complete**

Once BUSCO finished there will be a logfile output that will show the results from your run.

# Gene Prediction and Annotation Pipeline using SNAP, AUGUSTUS, and MAKER

## Overview

This guide walks through the process of gene prediction using SNAP and AUGUSTUS, followed by integration with MAKER. The workflow includes:

1. Preparing a training set  
2. Training SNAP  
3. Running SNAP on a target genome  
4. Running AUGUSTUS with pre-trained parameters  
5. Combining results with MAKER  

---

## 1. Preparing Environment

Start a `screen` session for persistent environment:

```bash
screen -S genes bash -l
```

Check where SNAP is installed:

```bash
echo $ZOE
ls $ZOE
ls $ZOE/HMM
less $ZOE/HMM/C.elegans.hmm
```

---

## 2. Training SNAP

### 2.1 Preparing Input Data

Create a working directory:

```bash
mkdir -p ~/genes/snap
cd ~/genes/snap
```

Download the genome and annotation files (from local desktop):

```bash
cp /path/to/B71Ref2.fasta .
cp /path/to/B71Ref2_a0.3.gff3 .
```

Preview the annotation:

```bash
head B71Ref2_a0.3.gff3
```

Combine GFF3 and FASTA:

```bash
echo '##FASTA' | cat B71Ref2_a0.3.gff3 - B71Ref2.fasta > B71Ref2.gff3
```

Check the merged file:

```bash
grep '##FASTA' -B 5 -A 5 B71Ref2.gff3
```

### 2.2 Convert to ZFF format

```bash
maker2zff B71Ref2.gff3
```

### 2.3 Analyze and Extract Training Data

```bash
fathom genome.ann genome.dna -gene-stats
fathom genome.ann genome.dna -categorize 1000
fathom uni.ann uni.dna -gene-stats
fathom uni.ann uni.dna -export 1000 -plus
fathom export.ann export.dna -gene-stats
```

### 2.4 Train SNAP HMM

```bash
forge export.ann export.dna
hmm-assembler.pl Moryzae . > Moryzae.hmm
less Moryzae.hmm
```

---

## 3. Running SNAP on MyGenome

Copy your genome to working directory:

```bash
cp /path/to/MyGenome.fasta .
```

Run SNAP:

```bash
snap-hmm Moryzae.hmm MyGenome.fasta > MyGenome-snap.zff
fathom MyGenome-snap.zff MyGenome.fasta -gene-stats
snap-hmm Moryzae.hmm MyGenome.fasta -gff > MyGenome-snap.gff2
```

---

## 4. Running AUGUSTUS

### 4.1 Setup

```bash
cd ~/genes/augustus
augustus --help 2>&1 | less
augustus --species=help 2>&1 | less
```

Use `screen` if running remotely.

### 4.2 Run AUGUSTUS

```bash
augustus --species=magnaporthe_grisea --gff3=on --singlestrand=true --progress=true ../snap/MyGenome.fasta > MyGenome-augustus.gff3
```

Examine results:

```bash
less MyGenome-augustus.gff3
```

Compare GFF2 (SNAP) vs GFF3 (AUGUSTUS) outputs.

---

## 5. Running MAKER

### 5.1 Inputs

Ensure you have the following:

- `MyGenome.fasta`
- SNAP HMM: `genes/snap/Moryzae.hmm`
- Transcript evidence: `genes/maker/genbank/ncbi-cds-Magnaporthe_organism.fasta`
- Protein evidence: `genes/maker/genbank/ncbi-protein-Magnaporthe_organism.fasta`

### 5.2 Setup

Navigate to the MAKER directory:

```bash
cd ~/genes/maker
```

Generate default configuration files:

```bash
maker -CTL
```

Edit `maker_opts.ctl` to set the following:

```text
genome=MyGenome.fasta
est=genbank/ncbi-cds-Magnaporthe_organism.fasta
protein=genbank/ncbi-protein-Magnaporthe_organism.fasta
snaphmm=../snap/Moryzae.hmm
```

Leave `augustus_species=` blank if using external GFF3 from AUGUSTUS.

Run MAKER:

```bash
maker
```

**As MAKER executes, you can see the various programs that it runs: Exonerate, SNAP, and so on. This execution will take approximately 4 hours!**

MAKER output will include:

- `MyGenome-annotations.gff`
- `*.maker.output/*` directories with individual scaffold results

---

## Resources

- [SNAP GitHub](https://github.com/KorfLab/SNAP)
- [AUGUSTUS Website](http://bioinf.uni-greifswald.de/augustus/)
- [MAKER Documentation](http://www.yandell-lab.org/software/maker.html)

---
