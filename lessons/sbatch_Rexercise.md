## Exercise

Create an R script and add the appropriate slurm directives to run it as a batch script using the `sbatch` command from Slurm.

### Create the R script
* **Type `nano sqrt_input.R` at the command prompt**. This will open up a new script file where you can add the contents of your R script.

> **NOTE:** `nano` is a text editor that you can use when working on the command line. There are other editors you can choose from. For more information on command-line text editors, [check out this lesson](https://hbctraining.github.io/Intro-to-shell-flipped/lessons/03_working_with_files.html#writing-files).

* The script will take in a number and return the square root of that number rounded to two decimal places. You can **copy and paste the code below** into the text editor.

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

* Now to **save this file** press <kbd>CTRL + O</kbd>, followed by the return key. You have now written the changes to file. You  can press <kbd>CTRL + X</kbd> to **exit**.
 
* Once you have created the script, **test that it runs well interactively**. *Note, you will first need the R module loaded in your current O2 session, if it's not already there!*

	<details>
	<summary><b><i>How to run the R script</i></b></summary>
	<br>
	<p> The script can be run from the command line using the `Rscript` command. Don't forget to provide a numeric value as input.
	            
	<pre>
	Rscript sqrt_input.R 60

### Create a job submission script

1. Using nano, create a new file called `sqrt_R.sbatch`.

2. Open up the new file and add a shebang line `#!/bin/bash`.

3. Next, add SLURM/`sbatch` directives at the top of the script requesting the following resources:
   * Use partition `priority` (`-p`)
   * Request 5 minutes (`-t`)
   * Request 100MB of memory (`--mem`)
   * Request a single core (`-c`)
   * Give the job a name (`--job-name`)
   * Specify the output file name (`-o`)
   * Specify the error file name (`-e`)
   
4. Add the line of code to load the required modules.
5. Add the line of code to run the R script. 
6. Save the file and exit the nano text editor.


### Run the script to start a new job on O2
1. Run the new script using the `sbatch` command

### Check the job/run 
1. Use `sacct` to check the status of your job submission
1. Check the contents of your current directory -
    * Are there any new files with names ending in `.out` and `.err`?
    * What are the contents of these two files?

<details>
<summary><b><i>Couldn't get the script to work? Click here for the answer key.</i></b></summary>
<br>
<p> Your sbatch script should look something like this: 
<pre>
#!/bin/bash

#SBATCH -p priority 		# partition name
#SBATCH -t 0-2:00 		# hours:minutes runlimit after which job will be killed
#SBATCH --mem 8G 		# amount of memory requested
#SBATCH --job-name sqrt_R_script 		# Job name
#SBATCH -o sqrt.out		# File to which standard out will be written
#SBATCH -e sqrt.err 		# File to which standard err will be written

# Load required modules
module load gcc/6.2.0 R/4.1.1

# Point to personal library, if required
# export R_LIBS_USER="~/R/4.1.1/library"

# Run the R script
Rscript sqrt_input.R 60

</pre>
</p>
</details>

***

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

> ### Rstudio on O2
> Another alternative is to working with R on O2 is to use the [O2Portal RStudio App](https://harvardmed.atlassian.net/wiki/spaces/O2/pages/2233335809/HMS+-+RStudio+on+O2). This app will start the RStudio application on one of the O2 cluster compute nodes. This application allows you to write R code, run it, and create graphs interactively using the RStudio interface. Fore more information [check out the O2 wiki page](https://harvardmed.atlassian.net/wiki/spaces/O2/pages/2233335809/HMS+-+RStudio+on+O2).
---

*This lesson has been developed by members of the teaching team at the [Harvard Chan Bioinformatics Core (HBC)](http://bioinformatics.sph.harvard.edu/). These are open access materials distributed under the terms of the [Creative Commons Attribution license](https://creativecommons.org/licenses/by/4.0/) (CC BY 4.0), which permits unrestricted use, distribution, and reproduction in any medium, provided the original author and source are credited.*
