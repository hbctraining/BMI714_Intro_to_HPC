## BMI 713: Using R on the Command Line

## Class Exercises

### Setting up R environment on O2

1. You have decided that you would like to use the most recent version of R available on O2 (R 3.6.1). Create a directory to store libraries and set the `R_LIBS_USER` environment variable to store that path.
2. Load the R 3.6.1 module and open up R.
3. Install the `dplyr` package.


### Submit an R job script to SLURM

4. Create and R script that takes in two arguments. The two arguments are the numerator and denominator. Your script will divide the two numbers provided as arguments, round the result to two significant digits and return that value.
5. Once you have tested that this script runs well interactively, create a job submission script. Change your resources so that you run on the short partition, for one hour and only require 1G of memory. *BONUS: add a SLURM directive so that you get an email when your job is started and completed.*
