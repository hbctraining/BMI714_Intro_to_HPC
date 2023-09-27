---
title: Creating R libraries on O2
authors: Will Gammerdinger, Meeta Mistry
date: "Tuesday, September 26, 2023"
---
Approximate time: 40 minutes

## Learning Objectives

* Describe the advantages of using R on HPC
* Installing packages on O2
* Create personal R libraries on O2
  
## Scaling up R Computation with HPC Resources

In the last session we demonstrated the use of R in a high performance compute environment using a very basic R script. In this lesson we would like to expand on that by demonstrating how best to set yourself if you plan on using R on O2.


<p align="center">
<img src="../img/R-hpc.png" width="400">
</p>


### Why use R on the cluster?
If you are working with small(er) data files, using R on your personal computer will suffice. However, once the data become large there are a few reasons why it might be to your advantage to switch to using R on a high performance compute cluster.

* **Increased** access to **compututational resources**
    * Large datasets have increased memory requirements for processing
    * R can be built and linked to libraries which utilizes multi-core technology for automatic parallel execution
* Access to **different R versions**
    * Especially helpful for reproducing older analyses
    * R modules have been installed and are ready to use
* Run **R in a non-interactive session**
    * Can submit multiple R scripts as separate jobs running in the background
command Line parameters 
* **Running an R script with parameters**. As we showed previously, we can create R scripts which take user arguments as input. This can be helpful for:
    *  Repeated analyses on different datasets
    *  Independent analysis tasks for a larger dataset
 

## Using R on O2

Let's begin by logging in, if you haven't already done so.

```bash
ssh eCommonsID@o2.hms.harvard.edu
```

The first command we will type on the command prompt will be to **start an "interactive session" on O2**. This will take us off of the login node and put us on to a compute node. *We talked about this in-class during the lecture on HPC and O2. For more detail, and to refresh your memory we have the [slides linked here](https://github.com/mistrm82/Intro-to-Unix-QMB/blob/master/slides/HPC_intro_O2_Oct2023_BMI713.pdf)* 

```bash
$ srun -p interactive --pty --mem 1G -t 0-2:00 /bin/bash 
```

Press enter after you type in that command. You will get a couple of messages, but in a few seconds you should get back the command prompt `$`; the string of characters before the command prompt, however, have changed. It should say something like `[eCommonsID@compute-a-16-73 ~]`. 
Make sure that your command prompt is now preceded by a character string that contains the word "compute". We want to do all of our work on the compute nodes, and not on the head/login node.

### Loading the appropriate R module

In order to use R on O2, we need to first load the module. There are several versions of R are available as modules.

```bash
$ module spider R
```

```bash
---------------------------------------------------------------------------------------------------------------
  R:
---------------------------------------------------------------------------------------------------------------
    Description:
      R is a free software environment for statistical computing and graphics, includes extra libraries

     Versions:
        R/3.2.5
        R/3.3.3
        R/3.4.1-extra
        R/3.4.1
        R/3.5.1-extra
        R/3.5.1
        R/3.6.1
        R/4.0.1
        R/4.1.1
        R/4.1.2
        R/4.2.1
        R/4.3.1
```

We will want to use R 4.1.1 for the exercises in this lesson. Before we load it let's check to see if we need to do anything special before.

```bash
$ module spider R/4.1.1
```

**Turns out that we first need to load the gcc 6.2.0 compiler before loading R.** 

```bash
$ module load gcc/6.2.0 R/4.1.1
```

Now that we have R loaded, to use it we simply type in R to the terminal window and press the return key. You will see that you have successfully moved away from the shell command prompt and into **the R console with the R prompt `>`**.

```bash
$ R

R version 4.1.1 (2021-08-10) -- "Kick Things"
Copyright (C) 2021 The R Foundation for Statistical Computing
Platform: x86_64-pc-linux-gnu (64-bit)

R is free software and comes with ABSOLUTELY NO WARRANTY.
You are welcome to redistribute it under certain conditions.
Type 'license()' or 'licence()' for distribution details.

  Natural language support but running in an English locale

R is a collaborative project with many contributors.
Type 'contributors()' for more information and
'citation()' on how to cite R or R packages in publications.

Type 'demo()' for some demos, 'help()' for on-line help, or
'help.start()' for an HTML browser interface to help.
Type 'q()' to quit R.

> 
```


### Installing Packages on O2

Packages are collections of R functions, data, and compiled code in a well-defined format, created to add specific functionality. There are 10,000+ user contributed packages and growing.

There are a set of **standard (or base) packages which are considered part of the R source code and automatically available as part of your R installation**. Base packages contain the basic functions that allow R to work, and enable standard statistical and graphical functions on datasets.

You can check what libraries are loaded in your current R session by typing into the console:

```bash
sessionInfo() #Print version information about R, the OS and attached or loaded packages

# OR

search() #Gives a list of attached packages
```

The more you work with R, you will come to realize that there is a cornucopia of R packages that offer a wide variety of functionality. To use **additional packages will require installation**. Many packages can be installed from the [CRAN](http://cran.r-project.org/) or [Bioconductor](https://www.bioconductor.org/) repositories.


Let's try loading the `dplyr` library.

```r
> library(dplyr)
```

We get **an error** that there is no such package.

```
Error in library("dplyr") : there is no package called ‘dplyr’
```

If were working in R on our personal computer, the next logical step would be to try and install the package. However, if we try that same code her on O2, we get an error and a suggestion to create a personal library. 

```
install.packages("dplyr")


Warning in install.packages("dplyr") :
  'lib = "/n/app/R/4.1.1/lib64/R/library"' is not writable
Would you like to use a personal library instead? (yes/No/cancel)
```

Based on this message it looks like the main library folder cannot be modified by us. Instead, the cluster has been set up to allow every **user to create their own personal libraries for the distinct versions of R**. This is to account for every user having different needs.

**Type `cancel`** or do Ctrl + C to escape back to the R command prompt and **quit out of R without saving the workspace image**. Before we can install any packages let's get setup with personal libraries.

```r
> q()
```

### Creating Personal R Libraries 

> **NOTE:** The details we provide in this section is a condensed version of what is provided on the [HMSRC O2 Wiki](https://harvardmed.atlassian.net/wiki/spaces/O2/pages/1588662168/Personal+R+Packages). We encourage you to peruse the docs to get more detailed information for working with R on O2 and/or talk to the folks at HMS RC when troubleshooting.

A personal R library is basically a space in your home folder which is dedicated for source code related to any R packages you choose to install. Once you have a package installed for a specific version of R, you simply point to that directory when loading your libraries. There are two main things you need to do:

1. Create a  folder for your libraries
2. Define that space (path to the directory) as an environment variable, so R knows to look there when loading libraries

#### Create a folder for R libraries
Typically, your personal libraries are placed in your `$HOME` folder. There is no specific naming convention, but one is suggested in the command below (the idea is to include the R version in the path). **Keep R installations separate for different verions of R**. You can do this by creating a folder for every R version you are working with, e.g. `~/R/4.0.1/library`, `~/R/4.2.1/library` and so on. This will make your work more reproducible and working in R more efficient.

The following command can be run in the shell at the command prompt:

```bash
## create a folder for pacakge installations
$ mkdir -p ~/R/4.1.1/library
```

####  Define the folder as a designated space for R libraries
**Environment variables** are, in short, variables that describe the environment in which programs run, and they are predefined for a given computer or cluster that you are on. You can reset them to customize the environment.

Let's see the full list of environment variables on O2:

```bash
$ env
```

We are going to **set the `R_LIBS_USER` environment variable. We can see in the list above that it currently does not exist. To create it, we will use `export` to assign it such that it contains the path to folder that we just created: 

```bash
# Modify the environment to redirect installations to above folder
$ export R_LIBS_USER="~/R/4.1.1/library"

## Check the contents of the environment variable R_LIBS_USER
$ echo $R_LIBS_USER
```

Now if you were to start R and try `install.packages("dplyr")`, it should not give you a warning anymore, but you will be prompted to choose a CRAN mirror or server to download from. 

**Do I need to export `R_LIBS_USER` for every R session on O2?** 

In order for R to know how to find your personal libraries, it has to be explicitly specified. It can be accomplished as described above, but _it can also be set in a hidden file_ that is sourced every time R is opened. This hidden file is called `.Renviron`.

To **set up your `.Renviron` file** with the export command in it, you can run the code below:

```bash
echo 'R_LIBS_USER="~/R/4.1.1/library"' >  ~/.Renviron
```
Just be sure to change this if you decide to use another version of R!

> **NOTE:**: An alternative method would be to not tinker with the `R_LIBS_USER` environment variable, but instead to get into the habit of specifying the install location when installing, e.g. `install.packages("dplyr", lib="~/R/4.1.1/library")`.

***

*This lesson has been developed by members of the teaching team at the [Harvard Chan Bioinformatics Core (HBC)](http://bioinformatics.sph.harvard.edu/). These are open access materials distributed under the terms of the [Creative Commons Attribution license](https://creativecommons.org/licenses/by/4.0/) (CC BY 4.0), which permits unrestricted use, distribution, and reproduction in any medium, provided the original author and source are credited.*
