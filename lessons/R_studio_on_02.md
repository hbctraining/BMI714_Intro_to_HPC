## Creating an RStudio session

### View our current jobs

Before we get started with creating an RStudio session on O2, we can first look at the first jobs we have running on O2 usng `squeue`. This will show our currently running jobs:

```
squeue -u $USER
```

We can see that we currently have 1 interactive job currently running. We will continue to monitor our jobs using the `watch` function.

```
watch squeue -u $USER
```

## Selecting the Resources for RStudio

Now we can request the resources that we need for RStudio. In order to do this, we will need to open up our web browser and go to:

https://o2portal.rc.hms.harvard.edu

At the top ribbon of the webpage we can select the `HMS-RC Applications` tab and a dropdown menu will appear. From this dropdown menu, select `RStudio Environment`. At this point, you may be asked to provide you Harvard Key credentials. Now we are on a page where we can request resources for our RStudio environment.

We need to request the SLURM account, partition, number of cores, Wall time  and memory. 

### loading modules?

Once we have requested those resources, we can click "Launch" at the bottom. 

We can now see that our job is queued in our `sbatch` command and the Job ID matches the number in the parantheses in our browser. Initially our job will be slated as queued. 

Once the resources for our job have been allocated, we can click "Connect to RStudio Server" to open up RStudio in our browser.

### Open up the script in RStudio; make some changes

Now let's make some edits to our R script in the browser. We can save these changes and now let's look at our Rscript in the terminal:

```
less example_script.R
```

Now we can see that our edits are reflected in the R Scripot.

## RStudio Environmnet is persistant

If we choose to close out RStudio environment, we will notice that when we reopen it, it is the same environment that we left.


## Show how to run the script in the terminal tab of RStudio

However, we can also run our script directly in the terminal with R using `Rscript`.

`Rscript example_script.R`

## export PATH to personal libraries?

## Advantages of the portal

Some advantages to using the portal are:
- Allows for the use of high memory nodes
- Allows for multithreading jobs (this can be done in RStudio with some packages right?

Advantages to using RScript
- Allows for long computations to be carried without be concerned about closing R session
