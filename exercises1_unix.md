### Exercise 1: "Starting with the shell"

1. Login to O2 and start an interactive session.
2. Change directories into `unix-intro`.
3. List the contents of the `other` directory. How many files are in the folder?
4. Make a copy of the `sequences.fa` file and put it in the `other` folder. Name this file `sequences-copy.fa`.


### Exercise 2: "Paths and shortcuts"

5. Change directories into `other`. List all the `Mov10` FASTQ files in `raw_fastq` from your current directory without changing directories.
6. Do each of the following using a single `ls` command without navigating to a different directory. _HINT: You will want to use a wildcard here._
	* List all of the files in /bin that start with the letter 'c'
	* List all of the files in /bin that contain the letter 'a'
	* List all of the files in /bin that end with the letter 'o'
	* BONUS: List all of the files in /bin that contain the letter 'a' or 'c'. (This was not covered in the lesson)

### Exercise 3: "Examining files"

7. Print the contents of `sequences-copy.fq` to the screen. 
8. Use the `head` command to keep only the frst two sequences of this file. 

### BONUS: "Searching and redirection" 

> **NOTE:** Before you tackle these questions, it will be helpful to look over the [Advanced Shell topics](https://hbctraining.github.io/Intro-to-Unix-QMB/lessons/Shell_basics#advanced-shell-topics)

9. The last two lines of the file `sequences-copy.fq` represent a protein sequence. Use the `tail` command to take those two lines and redirect them into a new file called `protein.fa`.
10. Use `grep` to search for the patterm `CAGCT` in the `sequences-copy.fq` file. Use your shell knowledge to count how many times that pattern appears in the file. Now use the `man` pages to find out how you can count using the `grep` command. 












