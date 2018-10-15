---
title: "Intermediate Shell"
authors: "Sheldon  McKay, Mary Piper, Radhika Khetani"
---

## Searching files

We showed a little how to search within a file using `less`. We can also
search within files without even opening them, using `grep`. Grep is a command-line
utility for searching plain-text data sets for lines matching a string or regular expression.
Let's give it a try!

Suppose we want to see how many reads in our file are really bad, with 10 consecutive Ns  
Let's search for the string `NNNNNNNNNN` in file 

`$ cd ~/unix-intro/raw_fastq`

`$ grep NNNNNNNNNN Mov10_oe_1.subset.fq`

We get back a lot of lines.  What if we want to see the whole fastq record for each of these reads?
We can use the `-B` and `-A` arguments for grep to return the matched line plus one before (`-B 1`) and two
lines after (`-A 2`). Since each record is four lines and the second line is the sequence, this should
give the whole record.

`$ grep -B1 -A2 NNNNNNNNNN Mov10_oe_1.subset.fq`

for example:

	@HWI-ST330:304:H045HADXX:1:1101:1111:61397
	CACTTGTAAGGGCAGGCCCCCTTCACCCTCCCGCTCCTGGGGGANNNNNNNNNNANNNCGAGGCCCTGGGGTAGAGGGNNNNNNNNNNNNNNGATCTTGG
	+
	@?@DDDDDDHHH?GH:?FCBGGB@C?DBEGIIIIAEF;FCGGI#########################################################
	--
	@HWI-ST330:304:H045HADXX:1:1101:1106:89824
	CACAAATCGGCTCAGGAGGCTTGTAGAAAAGCTCAGCTTGACANNNNNNNNNNNNNNNNNGNGNACGAAACNNNNGNNNNNNNNNNNNNNNNNNNGTTGG
	+
	?@@DDDDDB1@?:E?;3A:1?9?E9?<?DGCDGBBDBF@;8DF#########################################################


## Redirection

This is great, now we know how to pull out lines that match a pattern of interest. But all those
sequences just went whizzing by with grep. **How can we capture them?**

We can do that with something called "redirection". The idea is that
we're redirecting the output from the terminal (all the stuff that went
whizzing by) to something else. In this case, we want to print it
to a file, so that we can look at it later.

The redirection command for putting something in a file is `>`

Let's try it out and put all the sequences that contain 'NNNNNNNNNN'
from all the files in to another file called `bad_reads.txt`.

`$ grep -B1 -A2 NNNNNNNNNN Mov10_oe_1.subset.fq > bad_reads.txt`

The prompt should sit there a little bit, and then it should look like nothing
happened. But type `ls`. You should have a new file called `bad_reads.txt`. Take
a look at it and see if it has what you think it should.

If we use '>>', it will append to rather than overwrite a file.  This can be useful for saving more than one search, for example:
    
`$ grep -B1 -A2 NNNNNNNNNN Mov10_oe_2.subset.fq >> bad_reads.txt`

Since our `bad_reads.txt` file isn't a raw_fastq file, we should move it to a different location within our directory. We decide to move it to the `other` folder using the command `mv`. 

`$ mv bad_reads.txt ../other/`

There's one more useful redirection method that we're going to show, and that's
called the pipe, written as `|`. It's probably not a key on
your keyboard you use very much. What `|` does is take the output of one command that went
scrolling by on the terminal and runs it through another command.
When it was all whizzing by before, we wished we could just slow it down and
look at it, like we can with `less`. Well it turns out that we can! We pipe
the `grep` command to `less`

`$ grep -B1 -A2 NNNNNNNNNN Mov10_oe_1.subset.fq | less`

Now we can use the arrows to scroll up and down and use `q` to get out.

We can also do something tricky and use the command `wc`. `wc` stands for
*word count*. It counts the number of lines or characters. So, we can use
it to count the number of lines we're getting back from our `grep` command.
And that will magically tell us how many sequences we're finding.

`$ grep NNNNNNNNNN Mov10_oe_1.subset.fq | wc`

This command tells us the number of lines, words and characters in the file. If we
just want the number of lines, we can use the `-l` flag for `lines`.

`$ grep NNNNNNNNNN Mov10_oe_1.subset.fq | wc -l`

Redirecting is not super intuitive, but it's really powerful for stringing
together these different commands, so you can do whatever you need to do.

The philosophy behind these commands is that none of them
really do anything all that impressive. BUT when you start chaining
them together, you can do some really powerful things really
efficiently. If you want to be proficient at using the shell, you must
learn to become proficient with the pipe and redirection operators:
`|`, `>`, `>>`.


---
*To share or reuse these materials, please find the attribution and license details at [license.md](https://github.com/hbc/Intro-to-Unix/blob/master/license.md).*

