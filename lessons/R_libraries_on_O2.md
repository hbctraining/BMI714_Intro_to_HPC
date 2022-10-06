
## R Personal Libraries on O2

Let's begin by logging in, if you haven't already done so.

```bash
ssh eCommonsID@o2.hms.harvard.edu
```

The first command we will type on the command prompt will be to **start an "interactive session" on O2**. This will take us off of the login node and on to a compute node. *We talked about this in-class during the lecture on HPC and O2. For more detail, and to refresh your memory we have the [slides linked here]()* 

```bash
$ srun -p interactive --pty --mem 8G -t 0-12:00 /bin/bash 
```

Press enter after you type in that command. You will get a couple of messages, but in a few seconds you should get back the command prompt `$`; the string of characters before the command prompt, however, have changed. It should say something like `[eCommonsID@compute-a-16-73 ~]`. 
Make sure that your command prompt is now preceded by a character string that contains the word "compute". We want to do all of our work on the compute nodes, and not on the head/login node.

### Loading the appropriate R module

Several versions of R are available on O2 as modules.

```bash
$ module spider R
```

Next, let's check if we can directly load the `R/4.1.1` module or if we need to do anything special.

```bash
$ module spider R/4.1.1
```

Now that we have a better idea of what we need to do, let's load the R module and get started.

```bash
$ module load gcc/6.2.0 R/4.1.1

$ R
```

The terminal window should now turn into the R console with the R prompt `>`. Try running a function like sessionInfo():

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


***

*This lesson has been developed by members of the teaching team at the [Harvard Chan Bioinformatics Core (HBC)](http://bioinformatics.sph.harvard.edu/). These are open access materials distributed under the terms of the [Creative Commons Attribution license](https://creativecommons.org/licenses/by/4.0/) (CC BY 4.0), which permits unrestricted use, distribution, and reproduction in any medium, provided the original author and source are credited.*
