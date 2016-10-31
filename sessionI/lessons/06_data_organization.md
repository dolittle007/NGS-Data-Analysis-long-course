---
title: "Data Management and Project Organization"
author: "Mary Piper, Meeta Mistry"
date: "Wednesday October 26, 2016"
---

Approximate time: 45 minutes

## Learning Objectives:

* Recognizing the need for Data Management
* Planning a good genomics experiment and getting started with project organization
* Have a general idea of the experiment and its objectives
* Understand how and why we chose this dataset


## Data Management

Project organization is one of the most important parts of a sequencing project, but is **often overlooked in the excitement to get a first look at new data**. While it's best to get yourself organized before you begin analysis, it's never too late to start.

In the most important ways, the methods and approaches we need in bioinformatics are the same ones we need at the bench or in the field - **planning, documenting, and organizing** will be the key to good reproducible science. Before we begin organizing our project we will first talk a little bit about how to manage your data and some considerations for when you are working with datasets of large sizes in general.

* Download [Data management slides](https://github.com/hbc/NGS-Data-Analysis-long-course/raw/Fall_2016/sessionI/slides/data_management.pdf)


### Planning 

You should approach your sequencing project in a very similar way to how you do a biological experiment, and ideally, begins with **experimental design**. We're going to assume that you've already designed a beautiful sequencing experiment to address your biological question, collected appropriate samples, and that you have enough statistical power. 

### Organizing

Every computational analysis you do is going to spawn many files, and inevitability, you'll want to run some of those analyses again. Genomics projects can quickly accumulate hundreds of files across tens of folders. Before you start any analysis it is best to first get organized and **create a planned storage space for your workflow**.

We will start by creating a directory that we can use for the rest of the RNA-seq session:

First, make sure that you are in your home directory,

```
$ pwd
```
this should give the result: `/home/user_name`

Now, make a directory for the RNA-seq analysis within the `ngs_course` folder using the `mkdir` command

```
$ mkdir ngs_course/rnaseq
```

Next you want to set up the following structure within your project directory to keep files organized:

```
rnaseq/
  ├── data/
  ├── meta/
  ├── results/
  ├── scripts/
  └── logs/

```

*This is a generic structure and can be tweaked based on personal preferences.* A brief description of what might be contained within the different sub-directories is provided below:

* **`data/`**: This folder is usually reserved for any raw data files that you start with. 

* **`meta/`**: This folder contains any information that describes the samples you are using, which we often refer to as metadata. 

* **`results/`**: This folder will contain the output from the different tools you implement in your workflow. To stay organized, you should create sub-folders specific to each tool/step of the workflow. 

* **`scripts/`**: This folder will contain the scripts that you use to run analyses at different points in the workflow.

* **`logs/`**: It is important to keep track of the commands you run and the specific pararmeters you used, but also to have a record of any standard output that is generated while running the command. 


Let's create a directory for our project by changing into `rnaseq` and then using `mkdir` to create the five directories.

```
$ cd ngs_course/rnaseq
$ mkdir data meta results scripts logs
``` 

Verify that you have created the directories:

```
$ ls -lF
```
 
If you have created these directories, you should get the following output from that command:

```
drwxrwsr-x 2 rsk27 rsk27   0 Jun 17 11:21 data/
drwxrwsr-x 2 rsk27 rsk27   0 Jun 17 11:21 logs/
drwxrwsr-x 2 rsk27 rsk27   0 Jun 17 11:21 meta/
drwxrwsr-x 2 rsk27 rsk27   0 Jun 17 11:21 results/
drwxrwsr-x 2 rsk27 rsk27   0 Jun 17 11:21 scripts/
```
Now we will create the subdirectories to setup for our RNA-Seq analysis, and populate them with data where we can. The first step will be checking the quality of our data, and trimming the files if necessary. We need to create two directories within the `data` directory, one folder for untrimmed reads and another for our trimmed reads: 

```
$ cd ~/ngs_course/rnaseq/data
$ mkdir untrimmed_fastq
$ mkdir trimmed_fastq
```
    
The raw_fastq data we will be working with is currently in the `unix_lesson/raw_fastq` directory. We need to copy the raw fastq files to our `untrimmed_fastq` directory:

`$ cp ~/ngs_course/unix_lesson/raw_fastq/*fq untrimmed_fastq`

Later in the workflow when we perform alignment, we will require reference files to map against. These files are also in the `unix_lesson` directory, you can copy the entire folder over into `data`:

`$ cp -r ~/ngs_course/unix_lesson/reference_data .`

### Documenting

For all of those steps, collecting specimens, extracting DNA, prepping your samples, you've likely kept a lab notebook that details how and why you did each step, but **documentation doesn't stop at the sequencer**! 

#### Log files

In your lab notebook, you likely keep track of the different reagents and kits used for a specific protocol. Similarly, recording information about the tools and parameters is important for documenting your computational experiments. 

* Keep track of software versions used
* Record information on parameters used and summary statistics at every step (e.g., how many adapters were removed, how many reads did not align)

> Different tools have different ways of reporting log messages and you might have to experiment a bit to figure out what output to capture. You can redirect standard output with the `>` symbol which is equivalent to `1> (standard out)`; other tools might require you to use `2>` to re-direct the `standard error` instead. 
 
#### README

Keeping notes on what happened in what order, and what was done, is essential for reproducible research. If you don’t keep good notes, then you will forget what you did pretty quickly, and if you don’t know what you did, no one else has a chance. After setting up the filesystem and running a workflow it is useful to have a **README file within your project** directory. This file will usually contain a quick one line summary about the project and any other lines that follow will describe the files/directories found within it. An example README is shown below. Within each sub-directory you can also include README files to describe the analysis and the files that were generated. 

```
## README ##
## This directory contains data generated from the NGS Data Analysis Course
## Date: November 1st, 2016

There are five directories in this directory:

data: contains raw data
meta:  contains...
results:
logs:
```
 
***

**Exercise**

Take a moment to create a README for the `rnaseq` folder (hint: use `vim` to create the file). Give a short description of the project and brief descriptions of the types of files you will be storing within each of the sub-directories. 

***


## Understanding the dataset
The dataset we are using is part of a larger study described in [Kenny PJ et al, Cell Rep 2014](http://www.ncbi.nlm.nih.gov/pubmed/25464849). The authors are investigating interactions between various genes involved in Fragile X syndrome, a disease of aberrant protein production, which results in cognitive impairment and autistic-like features. **The authors sought to show that RNA helicase MOV10 regulates the translation of RNAs involved in Fragile X syndrome.**

From this study we are using the [RNA-Seq](http://www.ncbi.nlm.nih.gov/geo/query/acc.cgi?acc=GSE50499) data which is publicly available in the [SRA](http://www.ncbi.nlm.nih.gov/sra). In addition to the raw sequence data we also need to collect **information about the data**, also known as **metadata**.  We are usually quick to want to begin analysis of the sequence data (FASTQ files), but how useful is it if we know nothing about the samples that this sequence data originated from? Some relevant metadata for our dataset is provided below:

* The RNA was extracted from **HEK293F cells** that were transfected with a MOV10 transgene or an irrelevant siRNA.  
* The libraries for this dataset are **stranded** and were generated using the **dUTP method**. 
* Sequencing was carried out on the **Illumina HiSeq-2500 for 100bp single end** reads. 
* The full dataset was sequenced to **~40 million reads** per sample, but for this workshop we will be looking at a small subset on chr1 (~300,000 reads/sample).
* For each group we have three replicates as described in the figure below.


![Automation](../img/exp_design.png)


## Understanding the workflow

For any bioinformatics experiment you will have to go through a series of steps in order to obtain your final desired output. The execution of these steps in a sequential manner is what we often refer to as a **workflow or pipeline**. A simplified version of the workflow we will be using in this course is provided below. We have some of the steps briefly outlined here, but plan to cover each in more detail over the duration of this course.

<img src=../img/rnaseq_workflow.png width=500>

1. Library preparation of biological samples (pre-sequencing)
2. Quality control - Assessing quality of sequence reads using FastQC
3. Quality control - Trimming and/or filtering sequencing reads (if necessary)
4. Align reads to reference genome using STAR (splice-aware aligner)
5. Quantifying expression/Counting the number of reads mapping to each gene
6. Statistical analysis to identify differentially expressed genes (count normalization, linear modeling using R-based tools)





----

*This lesson has been developed by members of the teaching team at the [Harvard Chan Bioinformatics Core (HBC)](http://bioinformatics.sph.harvard.edu/). These are open access materials distributed under the terms of the [Creative Commons Attribution license](https://creativecommons.org/licenses/by/4.0/) (CC BY 4.0), which permits unrestricted use, distribution, and reproduction in any medium, provided the original author and source are credited.*
