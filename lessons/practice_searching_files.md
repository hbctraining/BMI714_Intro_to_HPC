---
title: "Practice: Searching and Redirection"
author: "Sheldon  McKay, Bob Freeman, Mary Piper"
date: "Thursday, May 5, 2016"
---


## Practice with searching and redirection

Finally, let's use the new tools in our kit and a few new ones to examine our gene annotation file, **chr1-hg19_genes.gtf**, which we will be using later to find the genomic coordinates of all known exons on chromosome 1.

`$ cd ~/unix-intro/reference_data/`

Let's explore our `chr1-hg19_genes.gtf` file a bit. What information does it contain?

`$ less chr1-hg19_genes.gtf`

	chr1    unknown exon    14362   14829   .       -       .       gene_id "WASH7P"; gene_name "WASH7P"; transcript_id "NR_024540"; tss_id "TSS7245";
	chr1    unknown exon    14970   15038   .       -       .       gene_id "WASH7P"; gene_name "WASH7P"; transcript_id "NR_024540"; tss_id "TSS7245";
	chr1    unknown exon    15796   15947   .       -       .       gene_id "WASH7P"; gene_name "WASH7P"; transcript_id "NR_024540"; tss_id "TSS7245";
	chr1    unknown exon    16607   16765   .       -       .       gene_id "WASH7P"; gene_name "WASH7P"; transcript_id "NR_024540"; tss_id "TSS7245";
	chr1    unknown exon    16858   17055   .       -       .       gene_id "WASH7P"; gene_name "WASH7P"; transcript_id "NR_024540"; tss_id "TSS7245";

> The GTF file is a tab-delimited gene annotation file often used in NGS analyses. For more information on this file format, check out the [Ensembl site](http://useast.ensembl.org/info/website/upload/gff.html). 

The columns in the **GTF file contain the genomic coordinates of gene features (exon, start_codon, stop_codon, CDS) and the gene_names, transcript_ids and protein_ids (p_id) associated with these features**. Note that sometimes an exon can be associated with multiple different genes and/or transcripts. For example, 

`$ grep FAM138* chr1-hg19_genes.gtf | head -n 5`

This search returns two different genes, FAM138A and FAM138F, that contain the same exon.

`$ grep PLEKHN1 chr1-hg19_genes.gtf | head -n 5`

This search returns two different transcripts of the same gene, NM_001160184 and NM_032129, that contain the same exon.

Now that we know what type of information is inside of our gtf file, let's explore our commands to answer a simple question about our data: **how many total exons are present on chromosome 1 using `chr1-hg19_genes.gtf`?**

To determine the number of total exons on chromosome 1, we are going to perform a series of steps:
	
	1. Subset the dataset to only include the feature type and genomic location information
	2. Extract only the genomic coordinates of exon features
	3. Remove duplicate exons
	4. Count the total number of exons
	
####Subsetting dataset
We will define an exon by it's genomic coordinates. Therefore, we only need the feature type and the genomic location (chr, start, stop, and strand) information to find the total number of exons. The columns corresponding to this information are 1, 3, 4, 5, and 7. 

'cut' is a program that will extract columns from files.  It is a very good command to know.  Let's first try out the 'cut' command on a small dataset (just the first 5 lines of chr1-hg19_genes.gtf) to make sure we have the command correct:

`$ cut -f1,3,4,5,7 chr1-hg19_genes.gtf | head -n 5`
   
'-f1,3,4,5,7' means to cut these fields (columns) from the dataset.  

	chr1	exon	14362	14829	-
	chr1	exon	14970	15038	-
	chr1	exon	15796	15947	-
	chr1	exon	16607	16765	-
	chr1	exon	16858	17055	-

The `cut` command assumes our data columns are separated by tabs (i.e. tab-delimited). The `chr1-hg19_genes.gtf` is a tab-delimited file, so the default `cut` command works for us. However, data can be separated by other types of delimiters. Another common delimiter is the comma, which separates data in comma-separated value (csv) files. If your data is not tab delimited, there is a `cut` command argument (-d) to specify the delimiter.

Our output looks good, so let's cut these columns from the whole dataset (not just the first 5 lines) and save it as a file, **`chr1-hg19genes_cut`**:

`$ cut -f1,3,4,5,7 chr1-hg19_genes.gtf > chr1-hg19genes_cut`

Check the cut file to make sure that it looks good using `less`. 

####Extracting genomic coordinates of exon features
We only want the exons (not CDS or start_codon features), so let's use `grep` to only keep the exon lines and save to file, **`chr1_exons`**:

`$ grep exon chr1-hg19genes_cut > chr1_exons`

#### Removing duplicate exons
Now, we need to remove those exons that show up multiple times for different genes or transcripts.    

We can use a new tool, `sort`, to remove exons that show up more than once.  We can use the `sort` command with the `-u` option to return only unique lines.

`$ sort -u chr1_exons`

####Counting the total number of exons
Now, to count how many unique exons are on chromosome 1, we need to pipe the output to `wc -l`:

`$ sort -u chr1_exons | wc -l`
    

****
**Exercise**

How could have you have determined the number of total exons by combining all of the previous commands (starting with the original chr1-hg19_genes.gtf), into a single command (no intermediate files) using pipes?

****

**Commands, options, and keystrokes covered in this lesson**

```bash
grep
> (output redirection, write to file)
>> (output redirection, append to file)
| (output redirection, take output from previous command as input to the next command)
wc
cut
sort
```

---
*To share or reuse these materials, please find the attribution and license details at [license.md](https://github.com/hbc/Intro-to-Unix/blob/master/license.md).*

