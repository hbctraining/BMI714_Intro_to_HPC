---
title: Creating R libraries on O2
authors: Will Gammerdinger, Meeta Mistry
date: "Tuesday, September 26, 2023"
---
Approximate time: 40 minutes

## Learning Objectives

* Describe the advantages of using R on HPC
* Create personal R libraries on O2
* Utilize installed packages on O2
  
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
$ srun -p interactive --pty --mem 8G -t 0-12:00 /bin/bash 
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

At the command prompt you can provide lines of code to execute tasks. Type in a simple function that you are familiar with, for example `sessionInfo()`:

```r
> sessionInfo()
```


### Installing packages on O2

Now, let's try loading the `dplyr` library.

```r
> library(dplyr)
```

We get an error that there is no such package.

```
Error in library("dplyr") : there is no package called ‘dplyr’
```

If we try to install it using `install.packages("dplyr")`, we get the following error and a suggestion to create a personal library. 

```
Warning in install.packages("dplyr") :
  'lib = "/n/app/R/4.1.1/lib64/R/library"' is not writable
Would you like to use a personal library instead? (yes/No/cancel)
```

Based on this message it looks like the main library folder cannot be modified by us. Instead, the cluster has been set up to allow every user to create their own personal libraries for the distinct versions of R, this is to account for every user having different needs.

Say `cancel` or do Ctrl + C to escape back to the R command prompt and **quit out of R without saving the workspace image**.

```r
> q()
```

### Setting up the folder and local environment to allow package installations

The following commands can be run in the shell at the command prompt.
```bash
## create a folder for pacakge installations
$ mkdir -p ~/R/4.1.1/library

## modify the environment to redirect installations to above folder
$ export R_LIBS_USER="~/R/4.1.1/library"

## check the contents of the environment variable R_LIBS_USER
$ echo $R_LIBS_USER
```

Now if you were to start R and try `install.pacakges("dplyr")`, it should not give you a warning anymore, but you will be prompted to choose a CRAN mirror or server to download from - try to pick a relatively close location.

**Keep R installations separate for different verions of R**. You can do this by creating a folder for every R version you are working with, e.g. `~/R/4.0.1/library`, `~/R/4.1.1/library` and so on. This will make your work more reproducible and working in R more efficient.

> **Note 1:**
>
> You can also add the command to modify the `$R_LIBS_USER` variable to a hidden file called `~/.Renviron`, that way it will be available to you the next time you log on.
> 
> ```bash
> echo 'R_LIBS_USER="~/R/4.1.1/library"' >  ~/.Renviron
> ```

> **Note 2:**
>
> An alternative method would be to not tinker with the `R_LIBS_USER` environment variable, but instead to get into the habit of specifying the install location when installing, e.g. `install.packages("dplyr", lib="~/R/4.1.1/library")`

> **Note 3:**
>
> Talk to the folks at HMS RC to find out which packages are already installed. For additional information please take a look at their [online "how-to" guide](https://wiki.rc.hms.harvard.edu/display/O2/Personal+R+Packages).

> ### X11 forwarding
>  
> If you want to have images pop up interactively when you are working with R on O2 or even make/save plots during your job, you will need to install and run additional software. 
> 
> * Windows users will need [Xming](http://sourceforge.net/projects/xming/)
> * MacOSX users will need [Xquartz](http://xquartz.macosforge.org/landing/)
> 
> **Note, this section is not hands-on, please try it out on your own time.**
> 
> Once you have the correct software installed, make sure it is running before you log on to O2 with the additional `-XY` argument.
> ```bash
> $ ssh -XY ecommonsID@o2.hms.harvard.edu
> ```
> 
> Once on O2, you can start an interactive session with the additional `--x11` argument.
> ```bash
> $ srun --pty -p interactive -t 0-12:00 --x11 /bin/bash
> ```
> 
> You can also start a batch job with the additional `--x11=batch` argument.
> 
> Additional instructions and a troubleshooting guide is available on the [HMS-RC's O2 wiki](https://wiki.rc.hms.harvard.edu/display/O2/Using+X11+Applications+Remotely). 

***

*This lesson has been developed by members of the teaching team at the [Harvard Chan Bioinformatics Core (HBC)](http://bioinformatics.sph.harvard.edu/). These are open access materials distributed under the terms of the [Creative Commons Attribution license](https://creativecommons.org/licenses/by/4.0/) (CC BY 4.0), which permits unrestricted use, distribution, and reproduction in any medium, provided the original author and source are credited.*
