
### Exercise 1: "Quality trimming on 6 Fastq files, *in serial* with multithreading"
* Log into O2 using Terminal (on Macs) or Git BASH (on Windows)
* Start an interactive session with a single core
* Change directories into the `~/unix-intro/`, and copy over files using the following command:

```
$ cp /n/groups/hbctraining/unix-intro/other/*-slurm* . 
```

* Open the `trimmomatic-serial-slurm.sbatch` script with `nano` 
* Modify the SLURM (`SBATCH`) options to use only 4 cores
* Add the `SBATCH` options to make sure that you get an email when the job completes/ends
* Submit the script to the SLURM queue using `sbatch`
* Once submitted, immediately check the status of your job. How many jobs do you see running? Is there a difference in the "partition" on which they are running?
* When the job is completed it will create a new directory with new files: What is the name of the new directory? How many new files and directories were created within it?
* List only those files that end in `.zip`, 


### Exercise 2: "Quality trimming on 6 Fastq files, *in parallel* with multithreading"
* Check and make sure you have an interactive session going and also that you are in the `~/unix-intro/` directory.
* Use `nano` to open the `trimmomatic-multithreaded-slurm.sh` file and make note of the `sbatch` submission command in it. Name some of the SLURM/SBATCH options that we are requesting for each job in the loop.
* Run `trimmomatic-multithreaded-slurm.sh` using `sh` instead of `sbatch`.
* How many job submission notifications did you get?
* Once submitted, immediately check the status of your jobs. How many are running and how many are pending?
* Once again, when the job is complete a new directory with new files will be created. Use `ls -l` to determine if the same output was generated for both.
* What do you think the advantage is of running the job(s) this way as compared to Exercise 1?




