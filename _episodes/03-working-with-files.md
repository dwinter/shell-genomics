---
title: "Working with Files and Directories"
teaching: 30
exercises: 15
questions:
- "How can I view and search file contents?"
- "How can I create, copy and delete files and directories?"
- "How can I control who has permission to modify a file?"
- "How can I repeat recently used commands?"
objectives:
- View, search within, copy, move, and rename files. Create new directories.
- Use wild cards (`*`) to perform operations on multiple files.
- Make a file read only
- Use the `history` command to view and repeat recently used commands.
keypoints:
- "You can view file contents using `less`, `cat`, `head` or `tail`."
- "The commands `cp`, `mv`, and `mkdir` are useful for manipulating existing files and creating new directories."
- "You can view file permissions using `ls -l` and change permissions using `chmod`."
- "The `history` command and the up arrow on your keyboard can be used to repeat recently used commands."
---

## Working with Files

### Our data set: FASTQ files

Now that we know how to navigate around our directory structure, lets
start working with our sequencing files. We did a sequencing experiment and 
have two results files, which are stored in our `fq` directory. 

### Wild cards

Navigate to your `fq` directory.

~~~
$ cd ~/shell_data/fq
~~~
{: .bash}

We are interested in looking at the FASTQ files in this directory. We can list
all files with the .fastq extension using the command:

~~~
$ ls *.fq
~~~
{: .bash}

~~~
Ecan_01.fq  Ecan_02.fq  Ecan_03.fq  Ecan_04.fq  Ecan_05.fq  Ecan_06.fq  Ecan_07.fq  Ecan_08.fq  Ecan_09.fq  Ecan_10.fq  Ecan_11.fq  Ecan_12.fq  Ecan_13.fq  Ecan_14.fq
~~~
{: .output}

The `*` character is a special type of character called a wildcard, which can be used to represent any number of any type of character. 
Thus, `*.fq` matches every file that ends with `.fastq`. 

This command: 

~~~
$ ls *01.fastq
~~~
{: .bash}

~~~
Ecan_01.fq
~~~
{: .output}

lists only the file that ends with `01.fastq`.

We can use the command `echo` to see how the wildcard character is intepreted by the
shell.

~~~
$ echo *.fq
~~~
{: .bash}

~~~
Ecan_01.fq  Ecan_02.fq  Ecan_03.fq  Ecan_04.fq  Ecan_05.fq  Ecan_06.fq  Ecan_07.fq  Ecan_08.fq  Ecan_09.fq  Ecan_10.fq  Ecan_11.fq  Ecan_12.fq  Ecan_13.fq  Ecan_14.fq
~~~
{: .output}

The `*` is expanded to include any file that ends with `.fastq`.

This command:

~~~
$ ls /usr/bin/*.sh
~~~
{: .bash}

~~~
/usr/bin/amuFormat.sh  /usr/bin/gettext.sh  /usr/bin/gvmap.sh
~~~
{: .output}

Lists every file in `/usr/bin` that ends in the characters `.sh`.

> ## Home vs. Root
> 
> The `/` character is another navigational shortcut and refers to your root directory.
> The root directory is the highest level directory in your file system and contains
> files that are important for your computer to perform its daily work, but which you usually won't
> have to interact with directly. In our case,
> the root directory is two levels above our home directory, so `cd` or `cd ~` will take you to `/home/dcuser`
> and `cd /` will take you to `/`, which is equivalent to `~/../../`. Try not to worry if this is confusing,
> it will all become clearer with practice.
> 
> While you will be using the root at the beginning of your absolute paths, it is important that you avoid 
> working with data in these higher-level directories, as your commands can permanently alter files that the 
> operating system needs to function. In many cases, trying to run commands in root directories will require 
> special permissions which are not discussed here, so it's best to avoid it and work within your home directory.
{: .callout}

> ## Exercise
> Do each of the following tasks from your current directory using a single
> `ls` command for each.
> 
> 1.  List all of the files in `/usr/bin` that start with the letter 'c'.
> 2.  List all of the files in `/usr/bin` that contain the letter 'a'. 
> 3.  List all of the files in `/usr/bin` that end with the letter 'o'.
>
> Bonus: List all of the files in `/usr/bin` that contain the letter 'a' or the
> letter 'c'.
> 
> Hint: The bonus question requires a Unix wildcard that we haven't talked about
> yet. Trying searching the internet for information about Unix wildcards to find
> what you need to solve the bonus problem.
> 
> > ## Solution
> > 1. `ls /usr/bin/c*`
> > 2. `ls /usr/bin/*a*`
> > 3. `ls /usr/bin/*o`  
> > Bonus: `ls /usr/bin/*[ac]*`
> > 
> {: .solution}
{: .challenge}


## Command History

If you want to repeat a command that you've run recently, you can access previous
commands using the up arrow on your keyboard to go back to the most recent
command. Likewise, the down arrow takes you forward in the command history.

A few more useful shortcuts: 

- <kbd>Ctrl</kbd>+<kbd>C</kbd> will cancel the command you are writing, and give you a 
fresh prompt.
- <kbd>Ctrl</kbd>+<kbd>R</kbd> will do a reverse-search through your command history.  This
is very useful.
- <kbd>Ctrl</kbd>+<kbd>L</kbd> or the `clear` command will clear your screen.

You can also review your recent commands with the `history` command, by entering:

~~~
$ history
~~~
{: .bash}

to see a numbered list of recent commands. You can reuse one of these commands
directly by referring to the number of that command.

For example, if your history looked like this:

~~~
259  ls *
260  ls /usr/bin/*.sh
261  ls *R1*fastq
~~~
{: .output}

then you could repeat command #260 by entering:

~~~
$ !260
~~~
{: .bash}

Type `!` (exclamation point) and then the number of the command from your history.
You will be glad you learned this when you need to re-run very complicated commands.

> ## Exercise
> Find the line number in your history for the command that listed all the .sh
> files in `/usr/bin`. Rerun that command.
>
> > ## Solution
> > First type `history`. Then use `!` followed by the line number to rerun that command.
> {: .solution}
{: .challenge}


## Examining Files

We now know how to switch directories, run programs, and look at the
contents of directories, but how do we look at the contents of files?

One way to examine a file is to print out all of the
contents using the program `cat`. 

Enter the following command from within the `fq` directory: 

~~~
$ cat Ecan_01.fq
~~~
{: .bash}

This will print out all of the contents of the `SRR098026.fastq` to the screen.


> ## Exercise
> 
> 1. Print out the contents of the `~/shell_data/fq/Ecan_01.fq` file. What is the last line of the file? 
> 2.  From your home directory, and without changing directories,
> use one short command to print the contents of all of the files in
> the `~/shell_data/fq` directory.
> 
> > ## Solution
> > 1. The last line of the file is `AAFFFJJJ-<FJJJJJJJFJFJJJJJJJJJJJJJJJJJJJJJJJJJJJJJFJJJJFJJJJJJJJJ`.
> > 2. `cat ~/shell_data/fq/*`
> {: .solution}
{: .challenge}

`cat` is a terrific program, but when the file is really big, it can
be annoying to use. The program, `less`, is useful for this
case. `less` opens the file as read only, and lets you navigate through it. The navigation commands
are identical to the `man` program.

Enter the following command:

~~~
$ less Ecan_02.fastq
~~~
{: .bash}

Some navigation commands in `less`

| key     | action |
| ------- | ---------- |
| <kbd>Space</kbd> | to go forward |
|  <kbd>b</kbd>    | to go backward |
|  <kbd>g</kbd>    | to go to the beginning |
|  <kbd>G</kbd>    | to go to the end |
|  <kbd>q</kbd>    | to quit |

`less` also gives you a way of searching through files. Use the
"/" key to begin a search. Enter the word you would like
to search for and press `enter`. The screen will jump to the next location where
that word is found. 

**Shortcut:** If you hit "/" then "enter", `less` will  repeat
the previous search. `less` searches from the current location and
works its way forward. Note, if you are at the end of the file and search
for the sequence "CAA", `less` will not find it. You either need to go to the
beginning of the file (by typing `g`) and search again using `/` or you
can use `?` to search backwards in the same way you used `/` previously.

For instance, let's search forward for the sequence `TTTTT` in our file. 
You can see that we go right to that sequence, what it looks like,
and where it is in the file. If you continue to type `/` and hit return, you will move 
forward to the next instance of this sequence motif. If you instead type `?` and hit 
return, you will search backwards and move up the file to previous examples of this motif.

> ## Exercise
>
> What are the next three nucleotides (characters) after the first instance of the sequence quoted above?
> 
> > ## Solution
> > `GTT`
> {: .solution}
{: .challenge}

Remember, the `man` program actually uses `less` internally and
therefore uses the same commands, so you can search documentation
using "/" as well!

There's another way that we can look at files, and in this case, just
look at part of them. This can be particularly useful if we just want
to see the beginning or end of the file, or see how it's formatted.

The commands are `head` and `tail` and they let you look at
the beginning and end of a file, respectively.

~~~
$ head Ecan_02.fastq
~~~
{: .bash}

~~~
@K00325:71:HJCHYBBXX:1:1205:10196:22995
GGAATTGGTGGCTTCCTTCTCGAACTTCTCGATGGTACGCTTGTCAATTCCACCGCACTGGTAGATCAAGTGACCGGTGGTGGTAGACTTGCCAGAGTCGACGTGGCCGATAACGACCACGTTGATGTGAGTCTTTTCGTCCTTACCCAT
+
AA<FFAJJ-<FJJJJJFJJJJJFJJFJJJJJJFJJJFAFFJ<JFJFJJJJJJJ7FJFFJF7AFF<AFA<A-FJJ-F<AFA7JJJJJFFJJJFFJJAJ7FA<JF7<JJ7AAFJFFJJJFJJJJJJFFAAJ<F-7<FF7FJAJJJFJJ7FF<
@K00325:71:HJCHYBBXX:1:1208:8024:34600
CGCCTATTGGCTTCCTTCTCGAACTTCTCGATGGTACGCTTGTCAATTCCACCGCACTGGTAGATCAAGTGACCGGTGGTGGTAGACTTGCCAGAGTCGACGTGGCCGATAACGACCACGTTGATGTGAGTCTTTTCGTCCTTACCCATC
+
AAFFFJJJ7<FJFJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJFJJFAJFJFJJAJJJJJJJJJJJ7
@K00325:71:HJCHYBBXX:1:1224:9932:41000
CTACGTTCTCTTCCTTCTCGAACTTCTCGATGGTACGCTTGTCAATTCCACCGCACTGGTAGATCAAGTGACCGGTGGTGGTAGACTTGCCAGAGTCGACGTGGCCGATAACGACCACGTTGATGTGAGTCTTTTCGTCCTTACCCATCT
~~~
{: .output}

~~~
$ tail Ecan_02.fastq
~~~
{: .bash}

~~~
+
AAFFFJJJ-<<AFJJJJJJJJJJJJJJJJJFJJJJJJJJJJJJJJJJFJJJJJJJJJJJJFJJJJJFJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJFJJJJJJJFJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJA
@K00325:71:HJCHYBBXX:4:1214:3234:27215
CAACGATCTGGCGCCGTTTAATATTCGGCCTCGAGCTCTTCCTCGCCCATGGAGTCGGCAGCAACCTCCTCGTAATCGCGCTCCAAAGCGGCCAAGTCCTCACGGGCCTCAGAGAATTCACCTTCCTCCATACCCTCACCGACATACCAG
+
AAFFFJJJ-<FJJJJJAAJJJJJJJJJ<JJJJJJJJJJJJJJJJJJJJJJJJJJJJFJJJJJJJJJ7JJAJFFJJJJJJFJ7FJJJJJJJJJJJJJFJJJJFJJJJJJFAFJJJJFFFJJJJFJJJJJJJJJJ<FFFFFJJFAFJJFJJ-
@K00325:71:HJCHYBBXX:2:2220:11546:8101
TTCGCCTAACGGCGCCGTTTAATATTCGGCCTCGAGCTCTTCCTCGCCCATGGAGTCGGCAGCAACCTCCTCGTAATCGCGCTCCAAAGCGGCCAAGTCCTCACGGGCCTCAGAGAATTCACCTTCCTCCATACCCTCACCGACATACCA
+
AAFFFFFJ7FFJJJJJJFFJFJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJF<FJJJJJJJJJJJJJJJJJJJFJJJJJJJJJJAFJJJJJAJJJJJJJJJ<AJJJJJ<FJJJJFAFFFJFJJJJJJAFAJFJ7JJJJAAJ<-FJJ<
~~~
{: .output}

The `-n` option to either of these commands can be used to print the
first or last `n` lines of a file. 

~~~
$ head -n 1 Ecan_02.fastq
~~~
{: .bash}

~~~
+
AAFFFJJJ-<<AFJJJJJJJJJJJJJJJJJFJJJJJJJJJJJJJJJJFJJJJJJJJJJJJFJJJJJFJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJFJJJJJJJFJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJA
~~~
{: .output}

~~~
$ tail -n 1 Ecan_02.fastq
~~~
{: .bash}

~~~
AAFFFFFJ7FFJJJJJJFFJFJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJJF<FJJJJJJJJJJJJJJJJJJJFJJJJJJJJJJAFJJJJJAJJJJJJJJJ<AJJJJJ<FJJJJFAFFFJFJJJJJJAFAJFJ7JJJJAAJ<-FJJ<
~~~
{: .output}

## Creating, moving, copying, and removing

Now we can move around in the file structure, look at files, and search files. But what if we want to copy files or move
them around or get rid of them? Most of the time, you can do these sorts of file manipulations without the command line,
but there will be some cases (like when you're working with a remote computer like we are for this lesson) where it will be
impossible. You'll also find that you may be working with hundreds of files and want to do similar manipulations to all 
of those files. In cases like this, it's much faster to do these operations at the command line.

### Copying Files

When working with computational data, it's important to keep a safe copy of that data that can't be accidentally overwritten or deleted. 
For this lesson, our raw data is our FASTQ files.  We don't want to accidentally change the original files, so we'll make a copy of them
and change the file permissions so that we can read from, but not write to, the files.

First, let's make a copy of one of our FASTQ files using the `cp` command. 

Navigate to the `shell_data/fq` directory and enter:

~~~
$ cp Ecan_10.fastq Ecan_10-copy.fq
$ ls -F Ecan_1*
~~~
{: .bash}

~~~
Ecan_10-copy.fq  Ecan_10.fq  Ecan_11.fq  Ecan_12.fq  Ecan_13.fq  Ecan_14.fq
~~~
{: .output}

We now have two copies of the `Ecan_10.fastq` file, one of them named `SRR098026-copy.fastq`. We'll move this file to a new directory
called `backup` where we'll store our backup data files.

### Creating Directories

The `mkdir` command is used to make a directory. Enter `mkdir`
followed by a space, then the directory name you want to create.

~~~
$ mkdir backup
~~~
{: .bash}

### Moving / Renaming 

We can now move our backup file to this directory. We can
move files around using the command `mv`. 

~~~
$ mv Ecan_10-copy.fq backup
$ ls backup
~~~
{: .bash}
 
~~~
Ecan_10-copy.fq
~~~
{: .output}

The `mv` command is also how you rename files. Let's rename this file to make it clear that this is a backup.

~~~
$ cd backup
$ mv Ecan_10-copy.fastq Ecan_10-backup.fastq
$ ls
~~~
{: .bash}

~~~
Ecan_10-backup.fastq
~~~
{: .output}

### File Permissions

We've now made a backup copy of our file, but just because we have two copies doesn't make us safe. We can still accidentally delete or 
overwrite both copies. To make sure we can't accidentally mess up this backup file, we're going to change the permissions on the file so
that we're only allowed to read (i.e. view) the file, not write to it (i.e. make new changes).

View the current permissions on a file using the `-l` (long) flag for the `ls` command. 

~~~
$ ls -l
~~~
{: .bash}

~~~
total 8840
-rw-r--r-- 1 david david 9048945 Mar  4 14:36 Ecan_10-copy.fq
~~~
{: .output}

The first part of the output for the `-l` flag gives you information about the file's current permissions. There are ten slots in the
permissions list. The first character in this list is related to file type, not permissions, so we'll ignore it for now. The next three
characters relate to the permissions that the file owner has, the next three relate to the permissions for group members, and the final
three characters specify what other users outside of your group can do with the file. We're going to concentrate on the three positions
that deal with your permissions (as the file owner). 

![Permissions breakdown](../fig/rwx_figure.svg)

Here the three positions that relate to the file owner are `rw-`. The `r` means that you have permission to read the file, the `w` 
indicates that you have permission to write to (i.e. make changes to) the file, and the third position is a `-`, indicating that you 
don't have permission to carry out the ability encoded by that space (this is the space where `x` or executable ability is stored, we'll 
talk more about this in [a later lesson](http://www.datacarpentry.org/shell-genomics/05-writing-scripts/)).

Our goal for now is to change permissions on this file so that you no longer have `w` or write permissions. We can do this using the `chmod` (change mode) command and subtracting (`-`) the write permission `-w`. 

~~~
$ chmod -w Ecan_10-backup.fastq
$ ls -l 
~~~
{: .bash}

~~~
total 8840
-r--r--r-- 1 david david 9048945 Mar  4 14:36 Ecan_10-copy.fq
~~~
{: .output}

### Removing

To prove to ourselves that you no longer have the ability to modify this file, try deleting it with the `rm` command.

~~~
$ rm Ecan_10-backup.fq
~~~
{: .bash}

You'll be asked if you want to override your file permissions.

~~~
rm: remove write-protected regular file ‘Ecan_10-backup.fq’? 
~~~
{: .output}

If you enter `n` (for no), the file will not be deleted. If you enter `y`, you will delete the file. This gives us an extra 
measure of security, as there is one more step between us and deleting our data files.

**Important**: The `rm` command permanently removes the file. Be careful with this command. It doesn't
just nicely put the files in the Trash. They're really gone.

By default, `rm` will not delete directories. You can tell `rm` to
delete a directory using the `-r` (recursive) option. Let's delete the backup directory
we just made. 

Enter the following command:

~~~
$ cd ..
$ rm -r backup
~~~
{: .bash}

This will delete not only the directory, but all files within the directory. If you have write-protected files in the directory, 
you will be asked whether you want to override your permission settings. 

> ## Exercise
>
> Starting in the `shell_data/fq/` directory, do the following:
> 1. Make sure that you have deleted your backup directory and all files it contains.  
> 2. Create a copy of each of Ecan_12.fq, Ecan_13.fq and Ecan_14.fq (Note: You'll need to do this individually for each of the two FASTQ files. We haven't 
> learned yet how to do this
> with a wild-card.)  
> 3. Use a wildcard to move all of your backup files to a new backup directory.   
> 4. Change the permissions on all of your backup files to be write-protected.  
>
> > ## Solution
> >
> > 1. `rm -r backup`  
> > 2. `cp cp Ecan_13.fq Ecan_13-backup.fq` etc
> > 3. `mkdir backup` and `mv *-backup.fq backup`
> > 4. `chmod -w backup/*-backup.fq`   
> > It's always a good idea to check your work with `ls -l backup`. You should see something like: 
> > 
> > ~~~
> > -r--r--r-- 1 david david 47552 Nov 15 23:06 Ecan_11-backup.fastq
> > -r--r--r-- 1 david david 47552 Nov 15 23:06 Ecan_13-backup.fastq
> > -r--r--r-- 1 david david 43332 Nov 15 23:06 Ecan_14-backup.fastq
> > ~~~
> > {: .output}
> {: .solution}
{: .challenge}
