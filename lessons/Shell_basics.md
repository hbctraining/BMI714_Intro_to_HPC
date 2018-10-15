---
title: "Shell basics"
authors: "Sheldon  McKay, Mary Piper, Radhika Khetani"
---

## Setting up

We will spend most of our time learning about the basics of the shell by manipulating some experimental data.

Since we are going to be working with this data on a remote server, **Orchestra2 (O2)**, we first need to log onto the server. After we're logged on, we will each make our own copy of the example data folder.

### Logging in

**With Macs**

Macs have a utility application called "**Terminal**" for performing tasks on the command line (shell), both locally and on remote machines. We will be using it to log into O2.

**With Windows**

By default, there is no terminal for the bash shell available in the Windows OS, so you have to use a downloaded program, "**Git BASH**". Git BASH is part of the [Git for Windows](https://git-for-windows.github.io/) download, and is a shell (bash) emulator.

> You can also use [Putty](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html) to log in to remote machines from Windows computers, but it is a little more involved and has different capabilities.

#### Let's log in! 

Type in the following command with your username to login:

```bash
ssh username@o2.hms.harvard.edu
```

You will receive a prompt for your password, and you should type in your associated password; note that the cursor will *not move* as you type in your password.

A warning might pop up the first time you try to connect to a remote machine, type "Yes" or "Y". 

### Copying example data folder

Once logged in, you should see the O2 icon, some news, and the command prompt: 

```bash
[rc_training10@login01 ~]$ 
```

Just like with `>` in R, when you see the `$`, it means that bash is ready to start accepting commands from you.

The command prompt will have some characters before it, something like `[rc_training01@login01 ~]`, this is telling you what the name of the computer you are working on is.

The first command we will type on the command prompt will be to start a so-called "interactive session" on O2. This will take us off of the login node and on to a compute node. *We talked about this last week during the lecture on HPC and O2. For more detail, and to refresh your memory we have the [slides linked here](https://www.dropbox.com/s/9hesf747u6mmjcd/HPC_intro_O2.pdf?dl=1)* 


```bash
$ srun -p interactive --pty --mem 8000 -t 0-12:00 /bin/bash 
```

Press enter after you type in that command. You will get a couple of messages, but in a few seconds you should get back the command prompt `$`; the string of characters before the command prompt, however, have changed. They should say something like `[rc_training01@compute-a-16-73 ~]`. 
Make sure that your command prompt is now preceded by a character string that contains the word "compute". We want to do all of our work on the worker nodes, and not on the head/login node.

Copy the example data folder to your home directory using the following command:

```
$ cp -r /n/groups/hbctraining/unix-intro/ .
# Don't miss the dot/period "." at the end of this command!
```   
`cp` is the command for copy. It requires you to specify the location of the item(s) you want to copy (`/n/groups/hbctraining/unix-intro/`) and the destination (`.`); please note the space between the 2 in the command. The `.` specifies that we want to copy it to "here", the current directory we are in. The "-r" is an option/argument that modifies the copy command to do something slightly different than usual.

## Starting with the shell

We have each created our own copy of the example data folder into our home directory, **unix-intro**. Let's go into the data folder and explore the data using the shell.

```$ cd unix-intro```

> 'cd' stands for 'change directory'

Let's see what is in here. Type:

```$ ls```

You will see:

	genomics_data  other  raw_fastq  README.txt  reference_data

> ls stands for 'list' and it lists the contents of a directory.

There are five items listed.  What are they? We can use a "modifier" with `ls` to get more information; this modifier is called an argument.

```$ ls -l```

`ls -l` gives a lot more information than just `ls`.

	total 124
	drwxrwsr-x 2 mp298 mp298  78 Sep 30 10:47 genomics_data
	drwxrwsr-x 6 mp298 mp298 107 Sep 30 10:47 other
	drwxrwsr-x 2 mp298 mp298 228 Sep 30 10:47 raw_fastq
	-rw-rw-r-- 1 mp298 mp298 377 Sep 30 10:47 README.txt
	drwxrwsr-x 2 mp298 mp298 238 Sep 30 10:47 reference_data
	
We are not going to talk about permissions in this module, but that is what is denoted in the first column; in addition to permissions, note the first character. The first character denotes whether something is a directory/folder (`d`) or not. Let's move into the raw_fastq directory and see what is in there.

```$ cd raw_fastq/```

```$ ls -l```

None of the six items in this directory have pemissions that start with a `d`, so they are all files. In fact, they are all fastq files (sequence data) as the name suggests.


#### Arguments

Most commands take additional arguments that control their exact behavior. For example, `-l` is an argument to `ls`.  The `ls` command, like many commands, take a lot of arguments. Another useful one is `-h`, which shows the size of the file in human-readable format. There are many ways to figure out what the various arguments for a command are, but Google is your best bet here. (You can also use the `man` command to get the manual for a given command, e.g. `man ls`.)


## The Unix directory file structure (a.k.a. where am I?)
 
As you've already just seen, you can move around in different directories or folders at the command line. 


Like on any computer you have used before the file structure within unix is hierarchical, like an upside down tree with root (/) as the starting point of the tree-like structure:

![Unix](../img/Slide1.jpg)

That root (/) is often also called the 'top' level.

When you log in to a remote computer you are on one of the branches of that tree, your home directory (e.g. /home/username)

> On mac OS, which is a UNIX-based OS, the root level is also "/". On a windows OS, it is drive specific; generally "C:\" is considered root, but it changes to "D:/", if you are on that drive.

#### Moving around the file system

Let's practice moving around a bit.

We're going to work in that `unix-intro` directory.

Type

```
$ pwd    # this is equivalent to getwd() in R

$ cd
```

To go 'back up a level' we can use `..`

Type:

```$ cd ..```

Now do `ls` and `pwd`. 

> `..` denotes parent directory, and you can use it anywhere in the system to go back to the parent directory. Can you think of an example when this won't work?


#### Examining the contents of other directories

By default, the `ls` commands lists the contents of the working directory (i.e. the directory you are in). You can always find the directory you are in using the `pwd` command. However, you can also give `ls` the names of other directories to view. Navigate to the home directory if you are not already there.

Type:

```$ cd```

Then enter the command:

```$ ls unix-intro/```

This will list the contents of the `unix-intro` directory without you having to navigate there.

The `cd` command works in a similar way.

```
$ cd unix-intro/raw_fastq/
$ pwd
```

You should now be in `raw_fastq` and you got there without having to go through the intermediate directory. 

> If you are aware of the directory structure, you can string together as long a list as you like.


## Full vs. Relative Paths

The `cd` command takes an argument which is the directory name. Directories can be specified using either a *relative path* or a *full path*. As we know, the directories on the computer are arranged into a hierarchy. The full path tells you where a directory is in that hierarchy. Navigate to the home directory (`cd`). Now, enter the `pwd` command and you should see:

`$ pwd`

`/home/username`

which is the full path for your home directory. This tells you that you are in a directory called `username`, which sits inside a directory called `home` which sits inside the very top directory in the hierarchy, the *root directory*. So, to summarize: `username` is a directory in `home` which is a directory in `/`.

Now enter the following commands

```
$ cd /home/username/unix-intro/raw_fastq/

$ pwd
```

This jumps to `raw_fastq`. Now go back to the home directory (`cd`). We saw
earlier that the command:

```$ cd unix-intro/raw_fastq/```

had the same effect - it took us to the `raw_fastq` directory. But, instead of specifying the full path (`/home/username/unix-intro/raw_fastq`), we specified a *relative path*. In other words, we specified the path **relative to our current working directory**. 

A *full* path always starts with a `/`, a *relative* path does not!

You can usually use either a full path or a relative path depending on what is most convenient. If we are in the home directory, it is more convenient to just enter the relative path since it involves less typing.

## Creating, moving, copying, and removing

What if we want to do normal things like copy files or move them around or get rid of them. Sure we could do most of these things without the command line, but what fun would that be?! Besides it's often faster to do it at the command line, or you'll be on a remote server like a compute cluster or on the cloud where you won't have another option.

Our raw data in this case is fastq files. We don't want to change the original files,
so let's make a copy to work with.

Lets copy the file using the copy `cp` command. Navigate to the `raw_fastq` directory and enter:

```$ cp Mov10_oe_1.subset.fq Mov10_oe_1.subset-copy.fq```

```$ ls -l```

Now ``Mov10_oe_1.subset-copy.fq`` has been created as a copy of `Mov10_oe_1.subset.fq`

Let's make a 'backup' directory where we can put this file.

The `mkdir` command is used to make a directory. Just enter `mkdir`
followed by a space, then the directory name.

```$ mkdir backup```

> File/directory/program names with spaces in them do not work in unix, use characters like hyphens or underscores instead.

We can now move our backed up file in to this directory. We can move files around using the command `mv`. Enter this command:

```$ mv *copy.fq backup```

```$ ls -al backup```

	drwxrwsr-x 2 mp298 mp298       43 Sep 30 13:59 .
	drwxrwsr-x 8 mp298 mp298      203 Sep 30 13:58 ..
	-rw-rw-r-- 1 mp298 mp298 75706556 Sep 30 13:56 Mov10_oe_1.subset-copy.fq

The `mv` command is also how you rename files and directories.

`$ mv backup fastq_backup  # let's rename backup to something more intuitive`

`$ ls`

> Both `mv` and `cp` require that you specify 2 things after the command on the command line: first is the object being copied, moved or renamed, and the second is the destination it's being moved or copied to, or the new name!

Finally, we decided this was not what we needed to do, and we want to start over

	$ rm fastq_backup/Mov10_oe_1.subset-copy.fq

> The `rm` file permanently removes the file. Be careful with this command. The shell doesn't
just nicely put the files in the Trash. They're really gone.
>
> Same with moving and renaming files. It will **not** ask you if you are sure that you want to "replace existing file".

By default, `rm`, will NOT delete directories, but you use the `-r` option if you are sure that you want to delete the directories and everything within them. 

	$ rm -r fastq_backup
	
> `-r` stands for recursive. 

> We could have just used `rm -r fastq_backup` and gotten rid of the contents of the directory and the directory itself, instead of doing it stepwise.

## Examining Files

We now know how to move around the file system and look at the
contents of directories, but how do we look at the contents of files?

The easiest way to examine a file is to just print out all of the
contents using the command `cat`. Print the contents of `unix-intro/other/sequences.fa` by entering the following command:

`$ cat ~/unix-intro/other/sequences.fa`

This prints out the all the contents of `sequences.fa` to the screen.

> `cat` stands for catenate; it has many uses and printing the contents of a files onto the terminal is one of them.

What does this file contain?

`cat` is a terrific command, but when the file is really big, it can be annoying to use. The command, `less`, is useful for this case. Let's take a look at the raw_fastq files. These files are quite large, so we probably do not want to use the `cat` command to look at them. Instead, we can use the `less` command. 

Move back to our `raw_fastq` directory and enter the following command:

`less Mov10_oe_1.subset.fq`

The `less` command opens the file, and lets you navigate through it. The keys used to move around the file are identical to the `man` command.

<span class="caption">Shortcuts for `less`</span>

| key              | action                 |
| ---------------- | ---------------------- |
| <kbd>SPACE</kbd> | to go forward          |
| <kbd>b</kbd>     | to go backwards        |
| <kbd>g</kbd>     | to go to the beginning |
| <kbd>G</kbd>     | to go to the end       |
| <kbd>q</kbd>     | to quit                |

Hit 'q' to exit the `less` interface and get the command prompt back.

There are many different ways to examin file contents, like `head` and `tail` and they just let you look at the beginning and end of a file respectively.

```$ head Mov10_oe_1.subset.fq ```

```$ tail Mov10_oe_1.subset.fq```

The `-n` argument to either of these commands can be used to print the first or last `n` lines of a file. To print the first/last line of the file use:

```$ head -n 1 Mov10_oe_1.subset.fq```

```$ tail -n 1 Mov10_oe_1.subset.fq```


## Writing files

We've been able to do a lot of work with files that already exist, but what if we want to write our own files. Obviously, we're not going to type in a FASTA file, but you'll see as we go through other tutorials, there are a lot of reasons we'll want to write a file, or edit an existing file (a smaller one).

To write in files, we're going to use the command `nano`. We're going to create
a file that contains some text. We'll name this file 'awesome.txt'.

```$ nano awesome.txt```

Now you have something that looks like

<img src="../img/nano1.png" width="400">

Type in a sentence, something like "nano is for writing and editing small files and scripts"

Now we want to save the file and exit. At the bottom of nano, you see the "\^X Exit". That means that we use <kbd>Ctrl</kbd> then <kbd>x</kbd> to exit. Type  <kbd>Ctrl-x</kbd>. It will ask if you want to save it. Type `y` for yes. Then it asks if you want that file name. Hit 'Enter'.

Now you've written a file. You can take a look at it with less or cat, or open it up again and edit it.

## Saving time with tab completion, wildcards and other shortcuts

#### Tab Completion

Navigate to the home directory. Typing out directory names can waste a lot of time. When you start typing out the name of a directory, then hit the tab key, the shell will try to fill in the rest of the directory name. For example, type `cd` to get back to your home directly, then enter:

```$ cd uni<tab>```

The shell will fill in the rest of the directory name for `unix-intro`. Now go to `unix-intro/raw_fastq` and 

```$ ls Mov10_oe_<tab><tab>```

When you hit the first tab, nothing happens. The reason is that there are multiple directories in the home directory which start with `Mov10_oe_`. Thus, the shell does not know which one to fill in. When you hit tab again, the shell will list the possible choices.

Tab completion can also fill in the names of commands. For example, enter `e<tab><tab>`. You will see the name of every command that starts with an `e`. One of those is `echo`. If you enter `ec<tab>` you will see that tab completion works. 

> **Tab completion is your friend!** It helps prevent spelling mistakes, and speeds up the process of typing in the full command.

#### Wild cards

Navigate to the `/home/username/unix-intro/raw_fastq` directory. This
directory contains FASTQ files from our RNA-Seq experiment. 

The '*' character is a shortcut for "everything". Thus, if you enter `ls *`, you will see all of the contents of a given directory. Now try this command:

```$ ls *fq```

This lists every file that ends with a `fq`. This command:

```$ ls Mov10*fq```

lists only the files that begin with 'Mov10' and end with 'fq'

So how does this actually work? The shell (bash) considers an asterisk "*" to be a wildcard character that can be used to substitute for any other single character or a string of characters. 

> An asterisk/star is only one of the many wildcards in UNIX, but this is the most powerful one and we will be using this one the most for our exercises.

#### Other shortcuts

There are some more shortcuts which you should know about. Dealing with the
home directory is very common. So, in the shell the tilde character,
"~", is a shortcut for your home directory. Navigate to the `raw_fastq`
directory:

```$ cd```

```$ cd unix-intro/raw_fastq```

Then enter the command:

```$ ls ~```

This prints the contents of your home directory, without you having to type the full path because the tilde "~" is equivalent to "/home/username".

Another shortcut that you are already familiar with is the "..":

```$ ls ..```

The shortcut `..` always refers to the directory above your current directory. So, it prints the contents of the `unix-intro`. You can chain these together, so:

```$ ls ../..```

prints the contents of `/home/username` which is your home directory. 

Finally, the special character `.` always refers to your current directory. So, `ls`, `ls .`, and `ls ././././.` all do the same thing, they print the contents of the current directory. This may seem like a useless shortcut right now, but it is very useful for specifying a destination when copying or moving files/folders.

To summarize, while you are in your home directory, the commands `ls ~`, `ls ~/.`, and `ls /home/username` all do exactly the same thing. These shortcuts are not necessary, but they are really convenient!

**Other handy command-related shortcuts**

- <kbd>Ctrl + C</kbd> will cancel the command you are writing, and give you a fresh prompt.
- <kbd>Ctrl + A</kbd> will bring you to the start of the command you are writing.
- <kbd>Ctrl + E</kbd> will bring you to the end of the command.


## Help and resources

### Want to learn more about the shell?

shell cheat sheets:<br>
* [http://fosswire.com/post/2007/08/unixlinux-command-cheat-sheet/](http://fosswire.com/post/2007/08/unixlinux-command-cheat-sheet/)
* [https://github.com/swcarpentry/boot-camps/blob/master/shell/shell_cheatsheet.md](https://github.com/swcarpentry/boot-camps/blob/master/shell/shell_cheatsheet.md)

Explain shell - a web site where you can see what the different components of
a shell command are doing.  
* [http://explainshell.com](http://explainshell.com)
* [http://www.commandlinefu.com](http://www.commandlinefu.com)

Software Carpentry tutorial: [The Unix shell](http://software-carpentry.org/v4/shell/index.html)

General help:
- http://tldp.org/HOWTO/Bash-Prog-Intro-HOWTO.html
- man bash
- Google - if you don't know how to do something, try Googling it. Other people
have probably had the same question.
- Learn by doing. There's no real other way to learn this than by trying it
out.  Write your next paper in nano (really emacs or vi), open pdfs from
the command line, automate something you don't really need to automate.

---
*To share or reuse these materials, please find the attribution and license details at [license.md](https://github.com/hbc/Intro-to-Unix/blob/master/license.md).*

