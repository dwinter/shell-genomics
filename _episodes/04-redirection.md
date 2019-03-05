---
title: "Redirection"
teaching: 30
exercises: 15
questions:
- "How can I search within files?"
- "How can I combine existing commands to do new things?"
objectives:
- "Employ the `grep` command to search for information within files."
- "Print the results of a command to a file."
- "Construct command pipelines with two or more stages."
keypoints:
- "`grep` is a powerful search tool with many options for customization."
- "`>`, `>>`, and `|` are different ways of redirecting output."
- "`command > file` redirects a command's output to a file."
- "`command >> file` redirects a command's output to a file without overwriting the existing contents of the file."
- "`command_1 | command_2` redirects the output of the first command as input to the second command."
- "for loops are used for iteration"
- "`basename` gets rid of repetitive parts of names"
---

## Searching files

We discussed in a previous episode how to search within a file using `less`. We can also
search within files without even opening them, using `grep`. `grep` is a command-line
utility for searching plain-text files for lines matching a specific set of 
characters (sometimes called a string) or a particular pattern 
(which can be specified using something called regular expressions). We're not going to work with 
regular expressions in this lesson, and are instead going to specify the strings 
we are searching for.
Let's give it a try!

> ## Nucleotide abbreviations
> 
> The four nucleotides that appear in DNA are abbreviated `A`, `C`, `T` and `G`. 
> Unknown nucleotides are represented with the letter `N`. An `N` appearing
> in a sequencing file represents a position where the sequencing machine was not able to 
> confidently determine the nucleotide in that position. You can think of an `N` as a `NULL` value
> within a DNA sequence. 
> 
{: .callout}

We'll search for strings inside of our fastq files. Let's first make sure we are in the correct 
directory.

~~~
$ cd ~/shell_data/fq
~~~
{: .bash}

Suppose we want to see how many reads in our file have really bad segments containing 10 consecutive unknown nucleoties (Ns). Let's search for the string NNNNNNNNNN in the SRR098026 file.

> ## Determining quality
> 
> In this lesson, we're going to be manually searching for strings of `N`s within our sequence
> results to illustrate some principles of file searching. It can be really useful to do this
> type of searching to get a feel for the quality of your sequencing results, however, in you 
> research you will most likely use a bioinformatics tool that has a built-in program for
> filtering out low-quality reads. You'll learn how to use one such tool in 
> [a later lesson](http://www.datacarpentry.org/wrangling-genomics/00-readQC/).
> 
{: .callout}

~~~
$ grep NNN Ecan_13.fastq
~~~
{: .bash}

This command returns a lot of output to the terminal. Every single line in the SRR098026 
file that contains at least 10 consecutive Ns is printed to the terminal, regardless of how long or short the file is. 
We may be interested not only in the actual sequence which contains this string, but 
in the name (or identifier) of that sequence. We discussed in a previous lesson 
that the identifier line immediately precedes the nucleotide sequence for each read
in a FASTQ file. We may also want to inspect the quality scores associated with
each of these reads. To get all of this information, we will return the line 
immediately before each match and the two lines immediately after each match.

We can use the `-B` argument for grep to return a specific number of lines before
each match and the `-A` argument to return a specific number of lines after each matching line. Here we want the line before and the two lines after each 
matching line so we add `-B1 -A2` to our grep command.

~~~
$ grep -B1 -A2 NNN Ecan_13.fq 
~~~
{: .bash}

One of the sets of lines returned by this command is: 

~~~
@K00325:73:HJCGJBBXX:8:1103:16894:1156
CATNAGCATCGATAGGGTATCGCAGGGTGAGAATACCACGCTTGGACTGGGCCTCATCACCGACATAGGAGTCCTTCTGACCCATACCAATCATGNTACCTTGNTGACGGNGACGGCNGNNNATNGANGNNANANCANCTCNNGGNNCGT
+
AAF#FJJJ7<FJFJJJJFJJJJJJJJJFFJJFFFJJJJJJJFJJAFJJFFJJJAJFFJJJJJJJJFJJJJJJJJJFFJJJJJJJJFJJJJJFJJJ#FJJFJJJ#JJJJJJ#JFJJJ<#J###FF#JJ#J##F#A#FJ#J<7##FF##FA7
~~~
{: .output}

> ## Exercise
>
> 1. Search for the sequence `NNAN` in the `Ecan_13.fq` file.
> Have your search return all matching lines and the name (or identifier) for each sequence
> that contains a match.
> 
> 2. Search for the sequence `AAGTT` in both FASTQ files.
> Have your search return all matching lines and the name (or identifier) for each sequence
> that contains a match.
> 
> > ## Solution  
> > 1. `grep -B1 NNAN Ecan_13.fq`  
> > 2. `grep -B1 AAGTT *.fq`
> >
> {: .solution}
{: .challenge}

## Redirecting output

`grep` allowed us to identify sequences in our FASTQ files that match a particular pattern. 
All of these sequences were printed to our terminal screen, but in order to work with these 
sequences and perform other opperations on them, we will need to capture that output in some
way. 

We can do this with something called "redirection". The idea is that
we are taking what would ordinarily be printed to the terminal screen and redirecting it to another location. 
In our case, we want to print this information to a file so that we can look at it later and 
use other commands to analyze this data.

The command for redirecting output to a file is `>`.

Let's try out this command and copy all the records (including all four lines of each record) 
in our FASTQ files that contain 
'NNN' to another file called `bad_reads.txt`.

~~~
$ grep -B1 -A2 NNN Ecan_11.fq > bad_reads.txt
~~~
{: .bash}

> ## File extensions
> 
> You might be confused about why we're naming our output file with a `.txt` extension. After all,
> it will be holding FASTQ formatted data that we're extracting from our FASTQ files. Won't it 
> also be a FASTQ file? The answer is, yes - it will be a FASTQ file and it would make sense to 
> name it with a `.fq` extension. However, using a `.fq` extension will lead us to problems
> when we move to using wildcards later in this episode. We'll point out where this becomes
> important. For now, it's good that you're thinking about file extensions! 
> 
{: .callout}


The prompt should sit there a little bit, and then it should look like nothing
happened. But type `ls`. You should see a new file called `bad_reads.txt`. 

We can check the number of lines in our new file using a command called `wc`. 
`wc` stands for **word count**. This command counts the number of words, lines, and characters
in a file. 

~~~
$ wc bad_reads.txt
~~~
{: .bash}

~~~
  4 3   344 bad_reads.txt
~~~
{: .output}

This will tell us the number of lines, words and characters in the file. If we
want only the number of lines, we can use the `-l` flag for `lines`.

~~~
$ wc -l bad_reads.txt
~~~
{: .bash}

~~~
4 bad_reads.txt
~~~
{: .output}

Because we asked `grep` for all four lines of each FASTQ record, we need to divide the output by
four to get the number of sequences that match our search pattern.

> ## Exercise
>
> How many sequences in `Ecan_13.fq` contain at least 4 consecutive Ts?
>
>> ## Solution
>>  
>>
>> ~~~
>> $ grep TTTT Ecan_13.fq.fastq > quad_T.txt
>> $ wc -l quad_T.txt
>> ~~~
>> {: .bash}
>> 
>> ~~~
>> 8059
>> ~~~
>> {: .output}
>>
> {: .solution}
{: .challenge}

We might want to search multiple FASTQ files for sequences that match our search pattern.
However, we need to be careful, because each time we use the `>` command to redirect output
to a file, the new output will replace the output that was already present in the file. 
This is called "overwriting" and, just like you don't want to overwrite your video recording
of your kid's first birthday party, you also want to avoid overwriting your data files.

~~~
$ grep -B1 -A2 NNN Ecan_13.fastq > bad_reads.txt
$ wc -l bad_reads.txt
~~~
{: .bash}

~~~
4 bad_reads.txt
~~~
{: .output}

~~~
$ grep -B1 -A2 NNNNNNNNNN Ean_02.fastq > bad_reads.txt
$ wc -l bad_reads.txt
~~~
{: .bash}

~~~
0 bad_reads.txt
~~~
{: .output}

Here, the output of our second  call to `wc` shows that we no longer have any lines in our `bad_reads.txt` file. This is 
because the second file we searched (`SRR097977.fastq`) does not contain any lines that match our
search sequence. So our file was overwritten and is now empty.

We can avoid overwriting our files by using the command `>>`. `>>` is known as the "append redirect" and will 
append new output to the end of a file, rather than overwriting it.

~~~
$ grep -B1 -A2 NNN Ecan_13.fastq > bad_reads.txt
$ wc -l bad_reads.txt
~~~
{: .bash}

~~~
1 bad_reads.txt
~~~
{: .output}

~~~
$ grep -B1 -A2 NNN Ecan_01.fastq >> bad_reads.txt
$ wc -l bad_reads.txt
~~~
{: .bash}

~~~
2 bad_reads.txt
~~~
{: .output}

The output of our second call to `wc` shows that we have not overwritten our original data. 

We can also do this with a single line of code by using a wildcard. 

~~~
$ grep -B1 -A2 NNN *.fq > bad_reads.txt
$ wc -l bad_reads.txt
~~~
{: .bash}

~~~
14 bad_reads.txt
~~~
{: .output}

> ## File extensions - part 2
> 
> This is where we would have trouble if we were naming our output file with a `.fq` extension. 
> If we already had a file called `bad_reads.fq` (from our previous `grep` practice) 
> and then ran the command above using a `.fq` extension instead of a `.txt` extension, `grep`
> would give us a warning. 
> 
> ~~~
> grep -B1 -A2 NNN*.fq > bad_reads.fq
> ~~~
> {: .bash}
> 
> ~~~
> grep: input file ‘bad_reads.fq’ is also the output
> ~~~
> {: .output}
> 
> `grep` is letting you know that the output file `bad_reads.fastq` is also included in your
> `grep` call because it matches the `*.fastq` pattern. Be careful with this as it can lead to
> some surprising output.
> 
{: .callout}

So far we've searched for reads containing a long string of at least 10 unknown nucleotides. 
We might also be interested in finding any reads with at least two shorter strings of 5 unknown 
nucleotides, separated by any number of known nucleotides. Reads with more than one region of 
ambiguity like this might be poor enough to not pass our quality filter. We can search for these
reads using a wildcard within our search string for `grep`. 


We've now created two separate files to store the results of our search for reads matching 
particular criteria. Since we might have multiple different criteria we want to search for, 
creating a new output file each time has the potential to clutter up our workspace. We also
so far haven't been interested in the actual contents of those files, only in the number of 
reads that we've found. We created the files to store the reads and then counted the lines in 
the file to see how many reads matched our criteria. There's a way to do this, however, that
doesn't require us to create these intermediate files - the pipe command (`|`).

This is probably not a key on
your keyboard you use very much, so let's all take a minute to find that key. 
What `|` does is take the output that is
scrolling by on the terminal and uses that output as input to another command. 
When our output was scrolling by, we might have wished we could slow it down and
look at it, like we can with `less`. Well it turns out that we can! We can redirect our output
from our `grep` call through the `less` command.

~~~
grep -B1 -A2 ATG Ecan_13.fq  | less
~~~
{: .bash}

We can now see the output from our `grep` call within the `less` interface. We can use the up and down arrows 
to scroll through the output and use `q` to exit `less`.

Redirecting output is often not intuitive, and can take some time to get used to. Once you're 
comfortable with redirection, however, you'll be able to combine any number of commands to
do all sorts of exciting things with your data!

None of the command line programs we've been learning
do anything all that impressive on their own, but when you start chaining
them together, you can do some really powerful things very
efficiently. Let's take a few minutes to practice. 

> ## Exercise
>
> Now that we know about the pipe (`|`), write a single command to find the number of reads 
> in the `Ecan_13.fq` file that contain at the nucleotide sequence "ATG"
> in a row. Do this without creating a new file.
>
>> ## Solution
>> 
>> ~~~
>> grep -B1 -A2 ATG Ecan_13.fq  | wc 0l
>> 
>> ~~~
>> {: .bash}
>>
>> ~~~
>> 38359
>> ~~~
>> {: .output}
>> 
> {: .solution}
{: .challenge}

## File manipulation and more practice with pipes

Let's use the tools we've added to our tool kit so far, along with a few new ones, to example our SRA metadata file. First, let's navigate to the correct directory.

~~~
$ cd
$ cd shell_data/metadata
~~~
{: .bash}

This file contains a lot of information about the samples that we submitted for sequencing. We
took a look at this file in an earlier lesson. Here we're going to use the information in this
file to answer some questions about our samples.

#### How many of the read libraries are paired end?

The samples that we submitted to the sequencing facility were a mix of single and paired end
libraries. We know that we recorded information in our metadata table about which samples used 
which library preparation method, but we don't remember exactly where this data is recorded. 
Let's start by looking at our column headers to see which column might have this information. Our
column headers are in the first row of our data table, so we can use `head` with a `-n` flag to
look at just the first row of the file.

~~~
$ head -n 1 library_info.tsv
~~~
{: .bash}

~~~
file	sample_id	spp	ploidy	treatment	genotype	read_type	insert.size	nreads
~~~
{: .output}

That is only the first line of our file, but because there are a lot of columns, the output
likely wraps around your terminal window and appears as multiple lines. Once we figure out which
column our data is in, we can use a command called `cut` to extract the column of interest.

Because this is pretty hard to read, we can look at just a few column header names at a time by combining the `|` redirect and `cut`.

~~~
$ head -n 1 library_info.tsv | cut -f1-4
~~~
{: .bash}

`cut` takes a `-f` flag, which stands for "field". This flag accepts a list of field numbers,
in our case, column numbers. Here we are extracting the first four column names.

~~~
file	sample_id	spp	ploidy 
~~~
{: .output}

The LibraryLayout_s column looks like it should have the information we want.  Let's look at some
of the data from that column. We can use `cut` to extract only the 3rd column from the file and
then use the `|` operator with `head` to look at just the first few lines of data in that column.

~~~
$ cut -f3 library_info.tsv 
~~~
{: .bash}

~~~
spp
Eam
Eam
Eam
Eam
Eel
Eel
Eel
Eel
H1
H1
H1
H2
H2
H2
~~~
{: .output}

We can see that there 4 species in this data. If we want to count how many
samples there are for a given species we can use grep.
~~~
$ cut -f3 library_info.tsv | grep Eel | wc -l
~~~
{: .bash}

~~~
4
~~~
{: .output}

We can see from this that we have only two paired-end libraries in the samples we submitted for 
sequencing.

> ## Exercise
>
> How many files are there for H1 and H2 combined<? 
>
>> ## Solution
>> ~~~
>> $ cut -f3 library_info.tsv | grep H | wc -l
>> ~~~
>> {: .bash}
>> 
>> ~~~
>> 6
>> ~~~
>> {: .output}
>>
> {: .solution}
{: .challenge}

#### How many of each class of library layout are there?  

We can extract even more information from our metadata table if we add in some new tools: `sort` and `uniq`. The `sort` command will sort the lines of a text file and the `uniq` command will
filter out repeated neighboring lines in a file. You might expect `uniq` to
extract all of the unique lines in a file. This isn't what it does, however, for reasons
involving computer memory and speed. If we want to extract all unique lines, we 
can do so by combining `uniq` with `sort`. We'll see how to do this soon.

For example, if we want to know how many samples of each library type are recorded in our table,
we can extract the third column (with `cut`), and pipe that output into `sort`. 

~~~
$ cut -f3 libary_info.tsv | sort
~~~
{: .bash}

If you look closely, you might see that we have one line that reads "spp". This is the 
header of our column. We can discard this information using the `-v` flag in `grep`, which means 
return all the lines that **do not** match the search pattern.

~~~
$ cut -f3 library_info.tsv | grep -v "spp" | sort
 
~~~
{: .bash}

This command returns a sorted list of the spp column. We can read all of this output fairly
simply, but sometime we deal with very large files. It can be useful to identify
all of the unique elements form a long dat coloum. Let's use the `uniq` command to see a list of all the different species that are present. 

~~~
$ cut -f3 library_info.tsv | grep -v "spp" | sort | uniq
~~~
{: .bash}

~~~
Eam
Eel
H1
H2
~~~
{: .output}

If we want to count how many of each we have, we can use the `-c` (count) flag for `uniq`. 

~~~
$ cut -f3 library_info.tsv | grep -v "spp" | sort | uniq -c
~~~
{: .bash}

~~~
      4 Eam
      4 Eel
      3 H1
      3 H2
~~~
{: .output}

> ## Exercise
>
> 1. How many different polidies are there?   
> 2. What is the largst number of reads for a file?  
> 
>> ## Solution
>>  
>> 1. Let's try this oen together.
>>
>>   
>>
> {: .solution}
{: .challenge}


#### Can we sort the file by number of reads and save that sorted information to a new file?  

We might want to process the smallest files first, and it would
there fore help to order the file from least to most reads. We can use the `-k` 
(key) flag for `sort` to sort based on a particular column. This is similar to 
the `-f` flag for `cut`.

Let's sort based on the ninth column (`-k9`) and redirect our output to a new file.

~~~
$ sort -k9 -n library_info.tsv > libs_read_sorted.tsv
~~~
{: .bash}

#### Can we extract only `Eam` records into a new file?  

We also might want to extract the information for all samples that meet a specific criterion 
(for example, are paired-end) and put those lines of our table in a new file. 
~~~
$ grep Eam library_info.tsv > library_info_Eam_only.tsv 
~~~
{: .bash}


## Writing for loops

Loops are key to productivity improvements through automation as they allow us to execute commands repeatedly. 
Similar to wildcards and tab completion, using loops also reduces the amount of typing (and typing mistakes). 
Loops are helpful when performing operations on groups of sequencing files, such as unzipping or trimming multiple
files. We will use loops for these purposes in subsequent analyses, but will cover the basics of them for now.

When the shell sees the keyword `for`, it knows to repeat a command (or group of commands) once for each item in a list. 
Each time the loop runs (called an iteration), an item in the list is assigned in sequence to the **variable**, and 
the commands inside the loop are executed, before moving on to  the next item in the list. Inside the loop, we call for 
the variable's value by putting `$` in front of it. The `$` tells the shell interpreter to treat the **variable**
as a variable name and substitute its value in its place, rather than treat it as text or an external command. 

Let's write a for loop to show us the first two lines of the fastq files we downloaded earlier. You will notice shell prompt changes from `$` to `>` and back again as we were typing in our loop. The second prompt, `>`, is different to remind us that we haven’t finished typing a complete command yet. A semicolon, `;`, can be used to separate two commands written on a single line.

~~~
$ cd ../fq/
~~~
{: .bash}

~~~
$ for filename in *.fq
> do
> head -n 2 ${filename}
> done
~~~
{: .bash}

The for loop begins with the formula `for <variable> in <group to iterate over>`. In this case, the word `filename` is designated 
as the variable to be used over each iteration. In our case `SRR097977.fastq` and `SRR098026.fastq` will be substituted for `filename` 
because they fit the pattern of ending with .fastq in directory we've specified. The next line of the for loop is `do`. The next line is 
the code that we want to excute. We are telling the loop to print the first two lines of each variable we iterate over. Finally, the 
word `done` ends the loop.

After executing the loop, you should see the first two lines of both fastq files printed to the terminal. Let's create a loop that 
will save this information to a file.

~~~
$ for filename in *.fq
> do
> head -n 2 ${filename} >> seq_info.txt
> done
~~~
{: .bash}

Note that we are using `>>` to append the text to our `seq_info.txt` file. If we used `>`, the `seq_info.txt` file would be rewritten
every time the loop iterates, so it would only have text from the last variable used. Instead, `>>` adds to the end of the file.

### Using Basename in for loops

Basename is a function in UNIX that is helpful for removing a uniform part of a name from a list of files. In this case, we will use
basename to remove the `*.fastq` from the files that we've been working with. Inside our for loop, we create a new `name` variable.
We call the `basename` function inside the parenthesis, then give our variable name from the for loop, in this case `$filename`, 
and finally state that `.fastq` should be removed from the file name. It's important to note that we're not changing the actual files,
we're creating and manipulating a new variable called `name`. The line `> echo $name` will print to the terminal the variable `name`
each time the for loop runs. Because we are iterating over two files, we expect to see two lines of output.

~~~
$ for filename in *.fq
> do
> name=$(basename ${filename} .fq)
> echo ${name}
> done
~~~
{: .bash}

Although the utility of basename may still seem unclear, it will become very useful in subsequent analysis, such as trimming many reads
in a for loop.
