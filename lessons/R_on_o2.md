---
title: "Using R on O2"
authors: "Mary Piper, Meeta Mistry, Radhika Khetani"
---

Approximate time: 20 minutes

## Learning Objectives

* Illustrate how to set-up personal R libraries on O2 to enable package installation
* Demonstrate how to run an R script using `interactive` and `sbatch` jobs
* Employ positional parameters in an R script to make scripts more flexible

## Using R on a Unix system

You can work with R on O2, but there are few differences from how you work with it on your laptop. 

1. O2 does not support RStudio use from the cluster, instead R can be run directly from an interactive session or as an R script (recommended). *Other clusters may have RStudio servers available, please talk to the respective system administrators for additional information.*
1. To enable most types of plotting or image generation, you have to set up your account/computer to use X11 forwarding.
1. Modules for R are available, and these have some commonly used pacakges installed. However, the local environment would need to be modified and a user-specific folder(s) has to be created for installing additional pacakges.

Let's walk through some of these in more detail, but make sure you are still logged on to O2 and in an interactive session.

## Load the appropriate R module

Several versions of R are available on O2 as modules.

```bash
$ module spider R
```

Next, let's check if we can directly load the `R/3.5.1` module or if we need to do anything special.

```bash
$ module spider R/3.5.1
```

Now that we have a better idea of what we need to do, let's load the R module and get started.

```bash
$ module load gcc/6.2.0 R/3.5.1

$ R
```

The terminal window should now turn into the R console with the R prompt `>`. You can run all of the analyses performed on your laptops on the cluster, but there is no RStudio-like GUI.

```r
> sessionInfo()
```

You should see something like what we have below. *How is the output of `sessionInfo()` different from on your computer?*

```
R version 3.5.1 (2018-07-02)
Platform: x86_64-pc-linux-gnu (64-bit)
Running under: CentOS Linux 7 (Core)

Matrix products: default
BLAS/LAPACK: /n/app/openblas/0.2.19/lib/libopenblas_core2p-r0.2.19.so

locale:
 [1] LC_CTYPE=en_US.UTF-8       LC_NUMERIC=C              
 [3] LC_TIME=en_US.UTF-8        LC_COLLATE=en_US.UTF-8    
 [5] LC_MONETARY=en_US.UTF-8    LC_MESSAGES=en_US.UTF-8   
 [7] LC_PAPER=en_US.UTF-8       LC_NAME=C                 
 [9] LC_ADDRESS=C               LC_TELEPHONE=C            
[11] LC_MEASUREMENT=en_US.UTF-8 LC_IDENTIFICATION=C       

attached base packages:
[1] stats     graphics  grDevices utils     datasets  methods   base     

loaded via a namespace (and not attached):
[1] compiler_3.5.1
```

## Installing packages on O2

Let's try loading the `dplyr` library.

```r
> library("dplyr")
```

We get an error that there is no such package.
```
Error in library("dplyr") : there is no package called ‘dplyr’
```

If we try to install it using `install.pacakges("dplyr")`, we get the following error and a suggestion to create a personal library. 

```
Warning in install.packages("dplyr") :
  'lib = "/n/app/R/3.5.1/lib64/R/library"' is not writable
Would you like to use a personal library instead? (yes/No/cancel)
```

Based on this message it looks like the main library folder cannot be modified by us. Instead, the cluster has been set up to allow every user to create their own libraries for the distinct versions of R, this is to account for every user having different needs.

Say `cancel` or do Ctrl + C to escape back to the R command prompt and quit out of R without saving the workspace image.

```r
> q()
```

#### Setting up the folder and local environment to allow package installations

```bash
## create a folder for pacakge installations
$ mkdir -p ~/R/3.5.1/library

## modify the environment to redirect installations to above folder
$ export R_LIBS_USER="~/R/3.5.1/library"

## check the contents of the environment variable R_LIBS_USER
$ echo $R_LIBS_USER
```

Now if you were to start R and try `install.pacakges("dplyr")`, it should not give you a warning anymore, but you will be prompted to choose a CRAN mirror or server to download from - try to pick a relatively close location.

Create a folder for every R version you are working with, e.g. `~/R/3.5.1/library`, `~/R/3.4.1/library` and so on. **Keep R installations separate for different verions of R** and save the library folders for old R versions. This will make your work more reproducible and working in R more efficient.

> **Note 1:**
>
> You can also add the command to modify the `$R_LIBS_USER` variable to a hidden file called `~/.Renviron`, that way it will be available to you the next time you log on.
> 
> ```bash
> echo 'R_LIBS_USER="~/R/3.5.1/library"' >  ~/.Renviron
> ```

> **Note 2:**
>
> An alternative method would be to not tinker with the `R_LIBS_USER` environment variable, but instead to get into the habit of specifying the install location when installing, e.g. `install.packages("dplyr", lib="~/R/3.5.1/library")`

> **Note 3:**
>
> Talk to the folks at HMS RC to find out which packages are already installed. For additional information please take a look at their [online "how-to" guide](https://wiki.med.harvard.edu/Orchestra/PersonalRPackages).

## Running R scripts on O2

You can run an R script from the shell command prompt in several ways, each of the following should work.
	
```bash
$ R < my_script.R
```

```bash
$ R CMD BATCH my_script.R
```

```bash
$ Rscript my_script.R
```

To make sure that your script works properly on O2 with any of the above commands, it needs the the following specification at the top of the script.

```bash
#!/usr/bin/env Rscript
```

And, you can also submit it as a job to the SLURM queue as follows:

```bash
$ sbatch -p priority -t 0-12:00 --mem 36G -o %j.out -e %j.err --wrap="Rscript my_script.R" 
## note the high memory usage
```

#### Giving an input to Rscripts

Finally, it is helpful to know that these R scripts can take files or arguments as input. This concept is called "positional parameters". If an R script is able to accept specific input, and the files or parameters are not *hard-coded* into the script, the script is a lot more flexible and efficient.

We can add command line arguments to the script can be accessed internally within the script using the `commandArgs()` function. Let's create an example script that takes in a number and gives us the square root of that number rounded to two decimal places.

```bash
$ nano sqrt_input.R
```

Now copy and paste the following script and save the file.

```r
#!/usr/bin/env Rscript

# Usage: this Rscript will accept a number and provide the square root of that number rounded to two decimal places.
# Rscript sqrt_input.R <number>

print("reading in arguments from command line")
args <- commandArgs(trailingOnly = TRUE)

## commandArgs reads in the arguments as a character vector
print("converting input to numeric")
num <- as.numeric(args[1])

print("running the sqrt() and round() functions on the input")
round(sqrt(num), digit=2)
```

Now we can run the script with a parameter/argument, i.e. the number we want to get the square root of:

```bash
$ Rscript sqrt_input.R 60
```

Note how the print statements output as the script proceeds.

## X11 forwarding
 
If you want to have images pop up interactively when you are working with R on O2 or even make/save plots without viewing them, you will need to install and run additional software. 

* Windows users will need [Xming](http://sourceforge.net/projects/xming/)
* MacOSX users will need [Xquartz](http://xquartz.macosforge.org/landing/)

**Note, this section is not hands-on, please try it out on your own time.**

Once you have the correct software installed, make sure it is running before you log on to O2 with the additional `-XY` argument.
```bash
$ ssh -XY ecommonsID@o2.hms.harvard.edu
```

Once on O2, you can start an interactive session with the additional `--x11` argument.
```bash
$ srun --pty -p interactive -t 0-12:00 --x11 /bin/bash
```

You can also start a batch job with the additional `--x11=batch` argument.
```bash
$ sbatch -p short -t 0-12:00 --x11=batch --wrap="Rscript my_script.R"
```

Additional instructions and a troubleshooting guide is available on the [HMS-RC's O2 wiki](https://wiki.rc.hms.harvard.edu/display/O2/Using+X11+Applications+Remotely). 

***

*This lesson has been developed by members of the teaching team at the [Harvard Chan Bioinformatics Core (HBC)](http://bioinformatics.sph.harvard.edu/). These are open access materials distributed under the terms of the [Creative Commons Attribution license](https://creativecommons.org/licenses/by/4.0/) (CC BY 4.0), which permits unrestricted use, distribution, and reproduction in any medium, provided the original author and source are credited.*
