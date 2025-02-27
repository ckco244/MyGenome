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
### Quality Analysis
### Trimming
### Genome Assembly
### Uploading Data

## Contributing

## License



