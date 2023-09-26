## BMI 713: Using R on the Command Line

## Exercises

### Setting up R environment on O2

1. You have decided that you would like to use an older version of R available on O2 (R 3.6.1). Create a directory to store libraries and set the `R_LIBS_USER` environment variable to store that path.
2. Load the R 3.6.1 module and open up R.
3. Install the `dplyr` package.


### Submit an R job script to SLURM

1. Create and R script called `dividing_numbers.R`, using nano or another text editor.
2. The script will take in two numeric arguments: the first number will be used as the numerator and the second will used as the denominator. Your script will divide the two numbers provided as arguments, round the result to two significant digits and return that value.
3. Once you have created the script, test that it runs well interactively.
4. Create a job submission script with the folowing resources:
      - use the `short` partition
      - one hour of time
      - 1G of memory
      - *BONUS: add a SLURM directive so that you get an email when your job is started and completed.*
