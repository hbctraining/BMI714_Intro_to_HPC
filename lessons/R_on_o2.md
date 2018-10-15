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

1. O2 does not support RStudio use from the cluster, instead R can be run directly from an interactive session or as an R script (recommended). *Other clusters may have RStudio servers talk to the system administrators for additional information.*
1. To enable most type of plotting or image generation, you have to set up your account/computer to use X11 forwarding.
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
sessionInfo()
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
library("dplyr")
```

We get an error that there is no such package.
```
Error in library("dplyr") : there is no package called ‘dplyr’
```

If, we try to install it using `install.pacakges("dplyr")` we get the following error and a suggestion to create a personal library. 

```
Warning in install.packages("dplyr") :
  'lib = "/n/app/R/3.5.1/lib64/R/library"' is not writable
Would you like to use a personal library instead? (yes/No/cancel)
```

Based on this message it looks like the main library folder can only be modified by the sys admins. The cluster has been set up to allow every user to create their own libraries for the distinct versions of R, this is to account for every user having different needs.

Say `cancel` or do Ctrl + C to escape back to the R command prompt and quit out of R without saving the workspace image.

```r
q()
```

#### Setting up the folder and local environment to allow package installations

```bash
## create a folder for pacakge installations
mkdir -p ~/R/3.5.1/library

## modify the environment to redirect installations to above folder
R_LIBS_USER="~/R/3.5.1/library"

## check the contents of the environment variable R_LIBS_USER
echo $R_LIBS_USER
```

Now if you were to start R and try `install.pacakges("dplyr")`, it should not give you a warning anymore, but you will be prompted to choose a CRAN mirror or server to download from - try to pick a relatively close location.


Create a folder for every R version you are working with, e.g. `~/R/3.5.1/library`, `~/R/3.4.1/library` and so on. **Keep R installations separate for different verions of R** and save the library folders for old R versions. This will make your work more reproducible and working in R more efficient.

> **Note 1:**
>
> You can also add the command to modify the `$R_LIBS_USER` variable to a hidden file called `~/.Renviron`, that way it will be available to you the next time you log on.
> 
> ```bash
> nano ~/.Renviron
> ```
> 
> Modify the first line in `~/.Renviron` to be `R_LIBS_USER="~/R/3.5.1/library"` instead of the default value, and save the file.

> **Note 2:**
>
> An alternative method would be not tinker with the `R_LIBS_USER` environment variable, but to get into the habit of specifying the install location when installing, e.g. `install.packages("dplyr", lib="~/R/3.5.1/library")`



 
> **NOTE:** If you were using X11 forwarding to view images, you could include the `--x11` flag in the interactive command:
`srun --pty -p interactive -t 0-12:00 --x11 --mem 36G /bin/bash`. Details regarding X11 forwarding are available on the [HMS RC wiki](https://wiki.rc.hms.harvard.edu/display/O2/Using+X11+Applications+Remotely).

## Running R scripts on O2

You can use any of the above ways to run an Rscript on Orchestra. But, you will need a different shebang line:

```bash
#!/usr/bin/env Rscript
```
And, you can also submit it as a job to the LSF queue as follows:

```bash
$ bsub -q short -W 12:00 -R "rusage[mem=16000]" "Rscript mean.R" 
# note the high memory usage above
```

Talk to the folks at HMS RC to find out which packages are already installed, and also about the best way to install R packages locally. They have a [how-to guide available online](https://wiki.med.harvard.edu/Orchestra/PersonalRPackages) for installing packages locally, if you feel comfortable trying it on your own.

***


## R scripts

In addition to running R interactively on the cluster, you can also run R scripts from the command prompt in Unix. These scripts are just like shell scripts, but with R code in them; we created a few in the past sessions. For running a script from the Unix command prompt, it will have to take into account the absolute or relative location of the files and folders that will be used. Also, your local environment (library specified in the `.Renviron` file) will need to have all the packages installed and available. 

Let's explore R scripts in a bit more detail by using part of the script we created for the **single-cell RNA-seq marker identification**. Let's make a new folder in our home directory called `Rscripts` and run code from our `marker_id.R` script using the `pbmcs_seurat_tsne.rds` .RData object.

```bash
# Create directory for lesson
$ mkdir ~/Rscripts

# Change directories into Rscripts
$ cd ~/Rscripts

# Copy over the TSNE data
$ cp /n/groups/hbctraining/ngs-data-analysis-longcourse/sessionVI/pbmcs_seurat_tsne.rds .
```

Now let's create the R script with vim. We have added `print()` statements to the script to help with trouble-shooting, if necessary:

```bash
$ vim marker_id.R
```

```r
# Single-cell RNA-seq - Marker identification

# Load libraries
library(dplyr)
library(Seurat)
library(AnnotationHub)
library(ensembldb)

# Load Seurat clustered data
seurat <- readRDS("pbmcs_seurat_tsne.rds")

print("Identifying markers")
# Identify gene markers
all_markers <-FindAllMarkers(seurat,
                             min.pct =  0.25,
                             min.diff.pct = 0.25)

print("Acquiring annotations")
# Connect to AnnotationHub
ah <- AnnotationHub()

# Access the Ensembl database for organism
ahDb <- query(ah, 
              pattern = c("Homo sapiens", "EnsDb"), 
              ignore.case = TRUE)
	      
# Acquire the latest annotation files
id <- ahDb %>%
  mcols() %>%
  rownames() %>%
  tail(n = 1)
  
# Download the appropriate Ensembldb database
edb <- ah[[id]]

# Extract gene-level information from database
annotations <- genes(edb, 
                     return.type = "data.frame")
		     
# Merge annotations with all markers		     
all_markers <- dplyr::left_join(all_markers, annotations[ , c(1:3, 5)],
                         by = c("gene" = "gene_id"))

# Rearrange order of columns to make clearer
all_markers <- all_markers[, c(6:8, 1:5, 9:10)]

print("Writing to file")
# Write results to file
write.csv(all_markers, "all_markers.csv", quote = F, row.names = FALSE)
```
	
Before we can run the script we don't want to forget the shebang line, which is slightly different since we are running an R script:

```bash
#!/usr/bin/env Rscript
```

You can run an R script from the shell command prompt in several ways, such as:
	
```bash
# DO NOT RUN!
$ R < marker_id.R
	
$ R CMD BATCH marker_id.R
```

But we are going to run our `marker_id.R` script using the following method:

```r
$ Rscript marker_id.R
```

Instead of running interactively, you could also submit it as a job to a Slurm queue as follows:

```bash
# DO NOT RUN!
$ sbatch -p priority -t 0-12:00 --job-name sc_marker_id --mem 36G -o %j.out -e %j.err "Rscript marker_id.R" 
# note the high memory usage above
```

> **NOTE:** It is not uncommon to require additional memory, especially for single-cell RNA-seq (some projects have required up to 128G memory). Since requesting so much memory can take a while to receive, submitting the job to the `priority` queue can be helpful.

### R positional parameters

Finally, it is helpful to know that these R scripts can take positional parameters as well. Therefore, we could use the same script to process different clustering analyses. We can add command line arguments to the script using the `commandArgs()` function:

```bash
$ vim marker_id.R
```

Go to top of file using `gg` in command mode and **delete the entire file** with `dG`.

Now paste in the following script with command line arguments and save.

```r
#!/usr/bin/env Rscript

# Usage: this Rscript is using Seurat to identify cell cluster markers. The input is an RDS file containing a Seurat object with clustering information contained within, and the output is a csv file containing the cluster markers. The script expects as a command line argument the path to the Seurat object and prefix to output file. To run:  Rscript marker_id.R "path/to/seurat.rds" "prefix_to_output_file"

# Single-cell RNA-seq - Marker identification

# Load libraries
library(dplyr)
library(Seurat)
library(AnnotationHub)
library(ensembldb)

#options(echo=TRUE)
args <- commandArgs(trailingOnly = TRUE)

# Load Seurat clustered data
seurat <- readRDS(args[1])


print("Identifying markers")
# Identify gene markers
all_markers <-FindAllMarkers(seurat,
                             min.pct =  0.25,
                             min.diff.pct = 0.25)

print("Acquiring annotations")
# Connect to AnnotationHub
ah <- AnnotationHub()

# Access the Ensembl database for organism
ahDb <- query(ah, 
              pattern = c("Homo sapiens", "EnsDb"), 
              ignore.case = TRUE)
	      
# Acquire the latest annotation files
id <- ahDb %>%
  mcols() %>%
  rownames() %>%
  tail(n = 1)
  
# Download the appropriate Ensembldb database
edb <- ah[[id]]

# Extract gene-level information from database
annotations <- genes(edb, 
                     return.type = "data.frame")
		     
# Merge annotations with all markers		     
all_markers <- dplyr::left_join(all_markers, annotations[ , c(1:3, 5)],
                         by = c("gene" = "gene_id"))

# Rearrange order of columns to make clearer
all_markers <- all_markers[, c(6:8, 1:5, 9:10)]

print("Writing to file")
# Write results to file
write.csv(all_markers,  paste0(args[2], "_all_markers.csv"), quote = F, row.names = FALSE)
```

Now to run we can provide the parameters:

```bash
Rscript marker_id.R "pbmcs_seurat_tsne.rds" "seurat_res0.8_all_clusters"
```

The print statements will output as the script proceeds. When completed take a quick peek at the markers file generated:

```bash
less seurat_res0.8_all_clusters_all_markers.csv
```

***

*This lesson has been developed by members of the teaching team at the [Harvard Chan Bioinformatics Core (HBC)](http://bioinformatics.sph.harvard.edu/). These are open access materials distributed under the terms of the [Creative Commons Attribution license](https://creativecommons.org/licenses/by/4.0/) (CC BY 4.0), which permits unrestricted use, distribution, and reproduction in any medium, provided the original author and source are credited.*
