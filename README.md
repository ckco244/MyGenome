# Project Title

Brief description of your project, its purpose, and what it aims to achieve.

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
  - [Uploading Data](#uploading-data)
- [Contributing](#contributing)
- [License](#license)

## Overview

The main goal of our ABT480 class is to gain knowledge of CLIs to be able to parse and manipulate sequencing data to construct high-quality genome datasets. This GitHub details the steps taken to complete this as well as the commands used to complete the required tasks.

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
The resulting .html files are shown below.


### Trimming
### Genome Assembly
### Uploading Data

## Contributing

## License



