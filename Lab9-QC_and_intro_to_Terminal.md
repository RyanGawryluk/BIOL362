# **BIOLOGY 362**

# **PRACTICAL SKILLS IN GENOMICS**

## Lab 9: Data Quality Control

Welcome to the bioinformatics portion of the lab! Over the next few weeks, we will explore a few computer-based tools that are important in the cleanup, manipulation, and analysis of amplicon sequence data. These computer lab sessions are meant to provide you with some experience in using "command line" software to investigate biological questions using sequence data. We realize that bioinformatics will be new to many of you, and previous experience is not required to complete any exercises.

You may wish to follow the lab manual from [GitHub - RyanGawryluk/BIOL362: A repository for BIOL362, Practical Skills in Genomics](https://github.com/RyanGawryluk/BIOL362)

The actual file is called "Lab9-QC_and_intro_to_Terminal.md". Using this will make it easier to copy and paste commands into Terminal. There aren't a lot of commands this week, but it will be more useful once we have more commands to run.

We will have copies of each bioinformatic lab manual in the same format each week.

## Laboratory 9: Exploring data

One of the most exciting parts about working with sequence data is receiving a notification from the sequencing centre that your data are ready for download (or even better, sequencing your own data). You will want to dive right in and start answering important questions about your experiment. But hold on! Before getting started, it is critical that we inspect, trim, or even 'correct' the reads so that the downstream analyses are evaluating the right data. Failure to account for issues in sequence quality and the retention of non-target (*e.g*., adapter) sequence can have detrimental effects on your analyses. Today, we will learn about some of the basic tools used to assess the quality of sequence data and 'clean them up'.

This lab is divided into 2 parts with the first being an investigation of sequence quality metrics. The second part is meant to provide some experience with inspecting and manipulating actual sequence data in fastq and fasta formats. By the end of today, you will have: **a**) assessed the quality of a sample MiSeq amplicon dataset using a computer program, FastQC; **b**) actively engaged in discussion about quality control considerations; and **c**) partitioned a sample QiaSeq 16S/ITS multi-region fastq dataset into individual amplicon datasets.

**Note that in this today's lab, we are only introducing the 'command line' and some bioinformatic tools and will not be focusing on sequence data that we generated. This section (Exercise 9.2) on basic directory structure and commands may be very familiar to students with previous bioinformatics or other command line experience .**

**We will be exploring a 'toy' dataset, made available by Qiagen, that contains multiple hypervariable regions within one dataset. No data directly related to our project will be inspected today.**

## Laboratory Exercise 9.1: Dataset quality metrics

It is valuable to have bioinformatic tools that allow you to inspect numerous sequence quality metrics in one run. Among the most popular programs is FastQC ([Babraham Bioinformatics - FastQC A Quality Control tool for High Throughput Sequence Data](https://www.bioinformatics.babraham.ac.uk/projects/fastqc/)).

FastQC outputs basic quality information about fastq read sets, and provides additional information about overrepresented sequences, and retained adapter sequences. FastQC analyses, or those from similar programs, are important not only for microbiome-type analyses, but for virtually any NGS approach, including RNA-seq, whole genome analysis *etc*. The interpretation of the output is somewhat different depending on the type of experiment.

Here, we will explore how to use FastQC v0.11.9 to evaluate the quality of forward (R1) and reverse (R2) reads from last year's BIOL36 dataset, the kombucha microbiome. It's not necessary to understand the distinctions between datasets or experimental setup for this exercise.

## Detailed instructions

Unfortunately, we have not been allowed to install FastQC locally ☹; however, FastQC output files have been supplied in your personal directory. This directory can be found at `/Volumes/labs/BIOL/BIOL362/Students/**username**/Lab9/KombuchaFastQC` where **username is your Netlink ID**. The easiest way to access that directory is to click on a globe-like icon on the Finder bar. Don't worry if you have trouble finding this directory -- it may take a minute for us all to get on the same page!

First, open the 'KombuchaFastQC' folder that is located within the Lab9 folder. In this folder, you will find FastQC reports in html format that we generated simply by loading the Illumina MiSeq paired-end amplicon data (*i.e*., R1 and R2 compressed fastq datasets) generated at Dalhousie's IMR into FastQC. Had we been allowed to install FastQC, you would have opened the files, and the program would have automatically generated these reports; but the overall experience would not be much different. Each file contains quality control metrics for reads from the forward (R1) and reverse (R2) reads.

Double-click on at least several of the file names in that folder (maybe you want to check all of them out). A new web browser tab/window will open for each read set and at the side you will see 11 panels presenting different aspects of the read quality analysis. Inspect each panel briefly for the R1 and R2 data sets. The results laid out in these panels help identify any problems with a dataset and help a bioinformatician plan the steps required to clean up the data.

Take 10 minutes, and try to interpret the FastQC data using the documentation found at: [Index of /projects/fastqc/Help/3 Analysis Modules](https://www.bioinformatics.babraham.ac.uk/projects/fastqc/Help/3%20Analysis%20Modules/)

Feel free to work on your own, or with a partner. Afterwards, we will
discuss your ideas as a class.

**Try to answer**:

1. Are there any concerning aspects of the sample sequence data? If so,
   which one(s)?

2. How might the interpretation of these metrics differ for amplicon
   sequencing in comparison with other sequencing methods, like whole
   genome sequencing or RNA-seq?

We will not be trimming reads based on sequence quality today; we will do that in the next lab. But normally, we would trim low quality bases from sequences after an initial inspection with FastQC and then run FastQC again to visualize how the data profile has changed.

**Reflection**

Now imagine that we will trim all reads from our read sets after a median quality score of 25 is reached (median score is specified by thin lines within whisker plots). At what point in the sequence would we trim at, based on a median Q=25? How much of our sequence is lost?

## **Laboratory Exercise 9.2: The Terminal**

You are probably used to interacting with files on your computers through either the Mac Finder or Windows menu. Using these graphical interfaces, you can move between directories, with an intuitive understanding that folders contain files, and some folders are housed within other folders. You find your way to the files or programs that you're interested in and double-click to open them.

In contrast to this, it is common to use the 'command line' in bioinformatics; on your Mac, this is accessible through the 'Terminal' application. The command line is based on text, not on graphical interfaces, but it allows you to move around the directory structure of your computer, just like using the Mac Finder (see Directory Structure diagram, below).

However, Terminal is much more powerful than Finder, because it also integrates a variety of very powerful Unix/Linux tools that allow scientists to view, search, and manipulate data files. For instance, using Terminal you can just look at the first few lines of a file without having to load an entire file into memory; or you can quickly find the number of times that a specific sequence of characters appears in a file; or interface with a computer cluster to submit multiple jobs at once.

This course will not focus on complex Terminal functions, but it will be useful to know a few basic facts and commands, so that we can move around directories and list their files in Terminal and carry out commands with QIIME2 in future labs.

**General Directory structure**

In Linux, the file system follows a hierarchical structure, with the root directory ("/") at the top. Directories (folders) contain files and other directories, forming a tree-like structure. Users navigate this structure using commands such as 'cd' (change directory) to move between directories.

While Linux, Windows, and macOS all have hierarchical file systems, there are differences in their structures and naming conventions. On a Mac using Terminal, we are using a Linux-like environment, but it's not *exactly* the same as Linux (*e.g.*, see below about file naming); however, it functions similarly.

##### Linux:

- Root directory: "/"

- Directory separator: "/"

- Case-sensitive file names: `file.txt` and `File.txt` are considered different files.

##### Windows:

- Root directory: "C:" (on most systems)

- Directory separator: ""

- Case-insensitive file names: `file.txt` and `File.txt` are considered the same file.

##### MacOS:

- Root directory: "/"

- Directory separator: "/"

- Case-insensitive file names: `file.txt` and `File.txt` are considered the same file.

##### **Moving Between Directories**

To move between directories in Linux, you use the 'cd' command followed by the directory you want to navigate to. You can specify directories using either absolute paths or relative paths.

- **Absolute Path:** Specifies the exact location of a directory from the root directory ("/"). For example:

`cd /home/user/Documents`

This command navigates to the `Documents` directory within the `user` directory, which is in the `home` directory.

- **Relative Path:** Specifies the location of a directory relative to the current directory. For example:

`cd Documents`

This command navigates to the `Documents` directory within the current directory. If you're already in the `/home/user` directory, this command achieves the same result as the absolute path example above.

- To move up one directory (to the parent directory):

`cd ..`

This command moves up one level in the directory hierarchy.

- To move to the home directory:

`cd \~`

This command moves to the home directory of the current user. We typically do not want to be in our home directories; the `/Volumes/labs/BIOL/BIOL362/Students/` directory has more storage space and is not erased after every lab session.

**Other useful commands and concepts**

**pwd** (Print Working Directory): Tells us which directory we are currently in. Useful if we get lost!

Usage: `pwd`

Example output:

`/home/user/Documents`

**ls** (List): Lists all files and folders in our current directory.

Usage: `ls`

Example output:

`file1.txt file2.txt folder1 folder2`

**less:** Allows us to read a file, line by line.

Usage: `less filename.txt`

Use arrow keys to navigate. Press "q" to exit.

**zless:** Like 'less', but used to open gzip compressed files.

Usage: `zless compressed_file.gz`

Use arrow keys to navigate. Press "q" to exit.

**nano** (Text Editor): Can be used to edit and/or create a text file in Terminal.

Usage: `nano new_file.txt`

Use arrow keys to navigate. Write or edit text. Press Ctrl + X to exit.

**grep** (Global Regular Expression Print):

`grep pattern filename.txt`

Example output:

line containing pattern another line containing pattern.

**Tab completion**: a time-saving trick -- we can hit the 'tab' character to complete commands or bring up all potential matching options.

Try some commands inside your own directory -- don't worry, I haven't given you any commands that can cause damage ☺

## Laboratory Exercise 9.3: Basic data structure

In most NGS experiments, several DNA libraries are sequenced together, *i.e*., they are 'multiplexed' according to unique index sequences (these were added in our library prep!). Almost always, software integrated into the DNA sequencer demultiplexes the data, and splits the reads according to the index sequences that they are supplied with.

However, there are several other reasons to split or trim reads, including: **1**) splitting multi-amplicon data -- as present in the toy Qiagen dataset -- into single-amplicon datasets; **2**) trimming unwanted sequence (*e.g*., readthrough sequencing adapters); and **3**) removing low-quality sequences. We will address the first use in the present lab. Manipulating sequences based on quality will be addressed with the full dataset in the next lab.

This session is meant to give a basic introduction to file formats, and preliminary data splitting/trimming.

Open the 'Terminal' program (ask for help if you need help locating it if needed). Expand the viewing window for Terminal by dragging it, to make it nice and big. Using Terminal, go to the QiagenSampleData directory. To do so, simply paste **Command 9.3.1**, remembering that username = your Netlink ID. This command is simply telling Terminal which directory to go to.

**Command 9.3.1:**

```bash
cd /Volumes/labs/BIOL/BIOL362/Students/username/Lab9/QiagenSampleData/
```

NOTE: Remember to change '**username**' to your Netlink ID

Then, to inspect the R1 Qiagen amplicon file, which contains 572,689 raw amplicon reads, copy and paste **Command 9.3.2**. The command 'zless' simply lets you visually inspect a "gzipped" (compressed) file in the Terminal. This command line tool is often used instead of a word processor because it allows a file to be read line-by-line, which helps with viewing large files.

**Command 9.3.2:**

```bash
zless ATCC-S5_S21_L001_R1_001.fastq.gz
```

**Reflection:**

Does the structure of each fastq read (*i.e.*, number of lines, content of lines) correspond to what we talked about in class? Can you find the unique identifier portion of each fastq header line? Hint: compare different lines starting with '@M04362' to see how they differ.

Remember that these data are **paired-end**, meaning that each read in the R1 dataset should have a counterpart in the R2 dataset, derived from the very same amplicon. Let's have a look to see if this is the case.

To do this, we will look at less complex files, presented in a common format called 'fasta', which are like fastq but only have the header line and the sequence, but lack information on sequence quality. The fasta files provided correspond to the first 500 sequences from the fastq files.

From Applications, open the text editor program, TextEdit, and open the files `ATCC-S5_S21_L001_R1_001_500.fasta` and `ATCC-S5_S21_L001_R2_001_500.fasta`. Compare the first FASTA entry in the R1 and R2 files. **These two reads form a read pair**. Each R1 read among the millions or billions in an unprocessed paired-end Illumina sequencing run will have a corresponding R2 read -- this is true of any paired-end library, whether for RNA-seq or whole genome sequencing. This would not be true of single-end sequencing.

**Reflection:**

Compare the header (*i.e.*, non-sequence) lines for the paired R1 and R2 reads. Are they exactly the same? If not, how are they different? Are the sequences the same? Should they be? (Think of how R1 and R2 reads are related.)

**Splitting original datasets into individual amplicons**

This Qiagen toy dataset -- generated with the QIAseq 16S/ITS Screening Panel -- includes 3 'primer panels' that amplify multiple hypervariable regions from the 16S rRNA gene (V1V2, V2V3, V3V4, V4V5, V5V7, V7V9), along with fungal ITS1.

To analyze each hypervariable region properly and separately, we would have to **a**) remove any remaining sequencing adapters, and **b**) split the amplicons (into *e.g.*, V1V2, ITS1) based on their distinguishing features, *i.e*, the primer sequences corresponding to the conserved rRNA gene regions.

Let's assume that we only want to use data from two variable regions, **V1V2 and ITS1**, throughout this exercise. V1V2 and ITS1 amplicons would allow us to assess prokaryotic and fungal diversity in these sample datasets. But before we split the amplicons into their own individual files in a high-throughput manner, let's look manually so that we understand what the data look like, and why bioinformatic software makes your life much easier.

'AGAGTTTGATTATGGCTC' is a forward primer that matches the V1V2 region in
many species.

Look at the file `ATCC-S5_S21_L001_R1_001_500.fasta` that you already have open in TextEdit, and, using the 'find' (Command-F) function of that program, determine how many times that primer sequence is present in the dataset of 500 reads.

That was pretty easy, right? So, if we wanted to extract all the V1V2 R1 reads from a standard sequencing experiment, could we just go through a data file using 'find' and extract all the reads that include that primer?

In short, no. Remember that Illumina sequencers can produce millions (or even billions) of sequences in a single run. It probably would take a long time to just open these files in TextEdit (if they open at all...). But it's even more than just that ... software can allow us to find and trim adapters/primers that are **imperfectly matched** to our query sequence, for example, primers that contain sequencing errors, or to
look for sequences with heterogeneity, like degenerate primers (see below).

Use TextEdit to open the file called `QIAseq_common_primers.fasta`, located in the same folder. This file contains the forward and reverse primer sequences used to amplify each hypervariable region.

Notice that most of the primer sequences contain non-standard DNA characters (other than A, C, T, or G) in them. The inclusion of characters like N, R, M, W, B and Y in the primer sequences means that Qiagen has used 'degenerate' primers for each variable region, which means that each variable region 'primer' is a pool of several slightly different primers. The degenerate primer (primer pool) that will be found in V1V2 R1 reads is (degenerate positions bold):

AG**R**GTTTGAT**YM**TGGCTC

R = purine = A/G

Y = pyrimidine = C/T

M = amino = A/C

A **non-exhaustive** list of primers within this pool includes the below
primers:

AG**A**GTTTGAT**CA**TGGCTC

AG**A**GTTTGAT**TA**TGGCTC

AG**A**GTTTGAT**TC**TGGCTC

AG**G**GTTTGAT**CA**TGGCTC

AG**G**GTTTGAT**TA**TGGCTC

AG**G**GTTTGAT**TC**TGGCTC ...

How many V1V2 sequences can you find manually in `ATCC-S5_S21_L001_R1_001_500.fasta` in **1 minute**?

**Reflection:**

Why do you think that Qiagen has used degenerate primers to amplify variable regions? Note that our V6V8 primers are also degenerate (sequences below).

B969F = ACGCG**H**N**R**AACCTTACC

BA1406R = ACGGGC**R**GTG**W**GT**R**CAA

Obviously, we can't inspect the data manually for millions of reads. That's what computers are for.

To use our hypervariable region primer sequences to split our original data files into separate R1 and R2 datasets for the V1V2 and ITS1 amplicons, we will use a program called CutAdapt v3.5. CutAdapt can do a variety of things. It can cut off adapters (hence the name), trim sequences by quality scores, and can take the forward and reverse region-specific PCR primer sequences as input, and split amplicons into separate R1 and R2 files. This is like what the MiSeq does to 'demultiplex' raw sequence data.

The version of CutAdapt that we are using is integrated into the QIIME2 analysis package, which we will be using for most subsequent analyses in this course. To use it, we first must activate our QIIME2 environment in Terminal. **Command 9.3.3** (a series of commands) will have to be executed each time that we need to activate QIIME2.

We will use CutAdapt to generate new R1 and R2 files containing only the V1V2 or ITS1 sequences. For the purposes of this lab, we will not worry about readthrough adapter sequences, though we would want to eliminate them from our real dataset.

**Command 9.3.3**

**1:** Open Terminal

**2**: Run the command:

```bash
conda init
```

**Note**. There may be a lengthy error report and you'll be asked if you
want to submit it to the developers.

Enter "N" and press return. These unfortunate complications are caused
by incompatibilities between QIIME2 and the new MacOS installed on lab
computers.

**3**: Open a new Terminal window by typing Command+N.

**4**: Run the command:

```bash
conda activate biol362
```

**Note:** There may be a delay of ~60 s while qiime2 optimizes the environment; then you will have control over the bash shell again.

To make R1 and R2 files containing reads from only the **V1V2 amplicon**, paste **Command 9.3.4** into Terminal:

**Command 9.3.4**

```bash
cutadapt --action trim --pair-filter both --trimmed-only --pair-adapters -g AGRGTTTGATYMTGGCTC -G CTGCTGCCTYCCGTA -o v1v2.R1.fastq.gz -p v1v2.R2.fastq.gz ATCC-S5_S21_L001_R1_001.fastq.gz ATCC-S5_S21_L001_R2_001.fastq.gz > v1v2_cutadapt_trim_stats
```

**Reflection:**

What do all these commands actually do? It's important to understand the commands that we are passing on to the computer, as we might want to change the commands in different circumstances. To learn more, open the file 'cutadapt_help_menu' using TextEdit. To generate this help menu, I simply typed 'cutadapt --help' into the Terminal (try it out!); all command-line bioinformatics software comes with help documentation that can be brought up in similar ways.

What does "--action trim" do?

What does "--pair-filter both" do?

What does "--trimmed-only" do?

What are we doing with "-g" and "-G"?

Note that the '>' just before 'v1v2_cutadapt_trim_stats' is a Terminal command (not part of CutAdapt) that directs the output of the command -- in this case, stats on trimming process -- to a new file called 'v1v2_cutadapt_trim_stats'. Open that file with TextEdit to quickly view the statistics of how many v1v2 read pairs were found.

Can you use the same approach to make R1 and R2 files containing only the **ITS1 amplicon**?

Give it a try, using the ITS1 primer sequences (F = CTTGGTCATTTAGAGGAAGTAA; R = GCTGCGTTCTTCATCGATGC) to parse the `noAdapter.R1.fastq.gz` and `noAdapter.R2.fastq.gz` files, as in **Command 9.3.4**.

Remember to give the ITS R1 and R2 files **different** **names** than the other files have that reflect what they are (*i.e.,* include that they are ITS, and R1 or R2).

**If you don't give the output files different names than the original,
Terminal will overwrite the originals!**

Now we have the R1 and R2 datasets from the V1V2 16S hypervariable region and ITS1 that are free of primer sequences! These files would then be used for import into the data analysis pipeline. We will perform these tasks on your real data prior to next lab.

**State of the data:**

- We have extracted paired-end reads from two amplicons, V1V2
  (prokaryotic) and ITS1 (fungal) from the QIAseq multi-amplicon
  dataset

- Data like these could then be imported into the QIIIME2 pipeline for
  further analysis.

- Note that quality trimming would typically be applied as well.

**What have we learned?**

- How to visualize and interpret NGS sequence quality scores with
  FastQC.

- Some basic usage of Terminal commands and directory structure.

- How to trim adapter/primer sequences and the value of bioinformatics
  software programs.
