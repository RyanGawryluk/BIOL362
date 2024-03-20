# BIOLOGY 362

# PRACTICAL SKILLS IN GENOMICS

## Laboratory 10: Taxonomic Resolution

Welcome to Lab 10. In today’s lab, we will begin to explore our sequencing data! We will carry out one of the most important first steps in bioinformatic analysis of amplicon sequence data: determining which taxa are present in our samples. Almost all of the steps carried out in this lab will be done using the QIIME2 analysis suite.

#### **Introduction to QIIME2**

Qiime2 (pronounced ‘chime 2’) is a freely-available bioinformatics platform specifically designed for the analysis of NGS-based microbiome experiments ([https://qiime2.org/](https://qiime2.org/)). QIIME2 integrates a number of standalone software packages into a common ‘environment’ that allows us to process raw sequencing data, determine which species are present in a dataset, and carry out many other powerful analyses that give insights into the structure and diversity of microbial communities. We will be operating the command-line version of QIIME2 via Terminal.

This lab is set up so that you can run all of your commands from one directory - this excludes looking at files in *e.g*., Excel. If you get lost, simply go back to this directory, replacing `NetlinkID` with your own:

```bash
cd /Volumes/labs/BIOL/BIOL362/Students/NetlinkID/Data/
```

Remember: qiime2 must be activated each time that we use it, using the following command:

**Command 10.1**

First, activate qiime

1. Open Terminal

2. Run the command:

```bash
conda init bash
```

3. Close the terminal window and open a new one

4. Run the command:

```bash
conda activate BIOL362
```

#### Import amplicon data as Qiime artifact

Next, we will import the sequence data into qiime2. There are many ways to do this, depending on the type of sequence data you have (single-end, paired end *etc.*).

We will use a “**manifest**” file, `Kombucha_manifest.txt`, to direct the data import which provides QIIME2 with the absolute file paths to our R1 (forward) and R2 (reverse) data read files.

Recall from class that a manifest file is simply a tab-delimited text file that specifies the **absolute path** to each data file in our experiment.

If you’d like to see the manifest file, it can be opened with Excel. Its location is:`/Volumes/labs/BIOL/BIOL362/Students/NetlinkID/Data/Import/Kombucha_manifest.txt`

**Command 10.2**

```bash
qiime tools import --type SampleData[PairedEndSequencesWithQuality] --input-path Import/Kombucha_manifest.txt --output-path Import/V6V8-paired-end-demux.qza --input-format PairedEndFastqManifestPhred33V2
```

The data are now present within a qiime2 ‘artifact’. This file holds data, but it is not something that we can visually inspect.

#### **Removing primer sequences from reads**

Still working in `/Volumes/labs/BIOL/BIOL362/Students/NetlinkID/Data/`, we will now remove primer sequences with CutAdapt, much like we did in the previous lab with the Qiagen dataset.

**Command 10.3**

```bash
qiime cutadapt trim-paired --i-demultiplexed-sequences Import/V6V8-paired-end-demux.qza --p-front-f ^ACGCGHNRAACCTTACC --p-front-r ^ACGGGCRGTGWGTRCAA --p-overlap 7 --p-discard-untrimmed --o-trimmed-sequences Import/V6V8_trimmed.qza --verbose
```

Recall that to better understand the command, we can access the Cutadapt help menu with `cutadapt --help`. In general, we encourage you to explore the options of all commands that you run, which will improve your understanding of the process.

You will see that we are looking for the appropriate universal primer sequence in each R1 and R2 read.  We only want to look for these primers at the 5ʹ end of each read, where we know that they should be, and we indicate this with the '^' symbol before the primer sequence.

Additionally, we specified that a primer must be found in **both** of the R1 and R2 reads of a pair to be retained -- otherwise they are both deleted (`--p-discard-untrimmed`).  

Lastly, it's also very useful to be aware of default parameters of programs, which are also described in the help menus. For instance, Cutadapt defaults to allowing a single mismatch in the primer sequence. Another important default behaviour of Cutadapt is to delete the specified primer sequences, `--action trim`. We do want to use this setting, so I haven't specified it because it's the default; but I have encountered interesting problems when used other settings!

#### Generating a visualization of data quality

Using the below command, we can generate a **visualization**. In Qiime, we use visualizations to inspect the output of a command. This particular one summarizes the read counts per sample and generates interactive positional quality plots (similar to FastQC) that let us decide if we need to trim the primer-trimmed reads further at the 5ʹ and 3ʹ ends due to decreased quality scores.

**Command 10.4**

```bash
qiime demux summarize --i-data Import/V6V8_trimmed.qza --o-visualization Import/V6V8-paired-end-trimmed.qzv
```

**Inspecting the visualization**

To actually see visualizations, we always need to type `qiime tools view` then the file name. This will open up a new window/tab in your web browser.

**Command 10.5**

```bash
qiime tools view Import/V6V8-paired-end-trimmed.qzv
```

The visualization first shows some histograms and statistics about the number of reads in the R1 and R2 files of each dataset.

At the top, there is an **Interactive Quality Plot** tab. Select that tab and look at quality scores by position. We are looking at a representation of quality scores by position summed across all R1 or R2 files, not individual files. 

Notice that the reads are no longer 300 bp, because we've trimmed the adapters. 

You can select an area with the mouse to zoom-in, and double click to zoom-out.

Where does data quality drop off? Is this the same for R1 and R2 reads?

Choosing how to trim the reads can be tricky because we have to take a couple of things into account: **1**) we want to avoid low-quality sequence so that we don’t call sequencing errors actual amplicon differences (Amplicon Sequence Variants, or ASVs) and **2**) we shouldn’t trim more than necessary because we need R1 and R2 reads to overlap. The V6V8 amplicon varies in length, but is typically not longer than ~450 bp, so keep that in mind when trimming.

Decide if and/or at which position to cut the R1 and R2 reads at the 3ʹ end. This does not have to be the same position for R1 and R2. In the next command, **you will decide** which trimming parameters to use. (Make sure you keep track of any parameters that you use that are different than in the lab manual - just like in the wet lab!)

#### **Amplicon sequence variant (ASV) resolution**

Make a new directory named DADA2_out; this is where output from our dada2 commands will go. The command should be run from within `/Volumes/labs/BIOL/BIOL362/Students/NetlinkID/Data/` to generate a new folder `/Volumes/labs/BIOL/BIOL362/Students/NetlinkID/Data/DADA2_out`.

**Command 10.6**

```bash
mkdir DADA2_out
```

**DADA2 - denoising, dereplication, chimera detection**

DADA2 is a powerful tool for generating exact ASVs from sequence data. Within the qiime2 platform, we just have to run the below command. 

The ‘--verbose’ option simply outputs more information about the analysis to the screen than the default setting; it can be useful to follow along as the program works.

The three output artifacts (starting with “--o”) specify the names of the representative sequences, feature table, and statistics artifacts, and the folder that they will be written to (‘DADA2_out’, in this case)

It is important to understand that there are several independent operations being carried out below, including:

1) Denoising and error ‘correction’ of R1 and R2 reads
2) Merging paired R1 and R2 reads into a single ASV
3) Detecting likely chimeric ASVs

**Note**: you have to add values for the --p-trunc-len-f and --p-trunc-len-r flags yourself; I have used X and Y as placeholders). Inspect the dada2 help menu to make sure that you understand what those flags do. Having chosen inappropriate values will often be evident in the output of `Command 10.9`; for instance, you might find that you have thrown away a large proportion (>60-70%) of your reads.

Also, beware: the “--p-trunc_len” command removes reads shorter than the specified cutoff; if you don’t want to trim at all, set X and Y to “0”.

Denoise, dereplicate, merge reads into ASVs using DADA2. 

This step will take a while.

**Command 10.7**

```bash
qiime dada2 denoise-paired --i-demultiplexed-seqs Import/V6V8_trimmed.qza --p-trunc-len-f X --p-trunc-len-r Y --verbose --o-representative-sequences DADA2_out/V6V8-rep-seqs-dada2.qza --o-table DADA2_out/V6V8-table-dada2.qza --o-denoising-stats DADA2_out/V6V8-stats-dada2.qza
```

Create a visualization of DADA2 stats artifact.

**Command 10.8**

```bash
qiime metadata tabulate --m-input-file DADA2_out/V6V8-stats-dada2.qza --o-visualization DADA2_out/V6V8-stats-dada2.qzv
```

View the output visualization file.

**Command 10.9**

```bash
qiime tools view DADA2_out/V6V8-stats-dada2.qzv
```

We need to view the results of the DADA2 process!

Are we losing a lot of sequences because they are of low-quality?

Do most R1 and R2 sequences overlap?

Are many of the ASVs chimeric?

We can get much more information from the DADA2 output! Run the below code.

Note that this is the first appearance of the `metadata` file in our code.

- Metadata (`Metadata/V6V8_metadata.txt` ; can be opened in Excel) includes potentially important information about our samples that we recorded that may help us understand the relationship between microbial community composition and Kombucha growth conditions.

- The metadata will feature more prominently next lab, but will also be used below in presenting a taxonomic bar chart.

Make a visualization of the DADA2 table artifact.

**Command 10.10**

```bash
qiime feature-table summarize --i-table DADA2_out/V6V8-table-dada2.qza --m-sample-metadata-file Metadata/Kombucha_metadata.txt --o-visualization DADA2_out/V6V8-table-dada2.qzv
```

View the DADA2 output table file.

**Command 10.11**

```bash
qiime tools view DADA2_out/V6V8-table-dada2.qzv
```

The `Overview` (default) tab shows various statistics about the ASV data.

- How many distinct ASVs (number of features) are there in total?

- How many read pairs (total frequency) are there across samples?

- What are the mean and maximum frequencies per feature?

Look at the `Feature Detail` tab.

- The long alphanumeric strings are the names of individual features (= ASVs).

- ‘Frequency’ relates to how often each feature (read pair) is found across the experiment.

- On the right we see the ‘# of Samples Observed In’. This indicates how many samples an ASV was found in (out of 18 samples, including our negative control ... which actually wasn't really negative!)

- Scroll through this page; look at how widely the frequencies vary. Are all features found in all samples?

- This is very important information that many subsequent steps are built upon. We will export this table below as a ‘BIOM’ file that can be opened in Excel.

- Do you think that this number of ASVs (proxy for species/strains) are living in kombucha?

We have not actually seen any of the ASV sequences yet. Do they look how we expect (i.e., like bona fide bacterial V6V8 amplicon sequences)?

Make a visualization of DADA2 sequences artifact.

**Command 10.12**

```bash
qiime feature-table tabulate-seqs --i-data DADA2_out/V6V8-rep-seqs-dada2.qza --o-visualization DADA2_out/V6V8-rep-seqs-dada2.qzv
```

View the output file containing ASV sequences.

**Command 10.13**

```bash
qiime tools view DADA2_out/V6V8-rep-seqs-dada2.qzv
```

The visualization file allows you to download all ASVs as a FASTA file if you choose to.

Also, each feature/ASV is linked to an NCBI blast submission!

For each of the top five ASV sequences (listed from highest to lowest frequency):

- Click on the sequence; then click ‘View Report’ on the NCBI blast page.

- Look through the top several blast hits to get a sense of what organism the ASV is from.

- Try clicking on the link next to ‘Sequence ID’ for a hit.

- In the resulting page, look under ‘Organism’ to determine what the phylogenetic affiliation of the V6V8 blast hit is. Is it a bacterium? What taxonomic group is it from?

#### **Filtering the dataset**

It’s likely that rare and sparsely distributed features/ASVs exist within the dataset. Rare components of the microbiome may represent contaminants, minor variants of true ASVs, or ASVs generated from errors in PCR/sequencing/error-correction.

These features can be removed from feature tables via **contingency filtering**, for instance, requiring that features are observed at least X times in total, in a minimum of Y different samples. This can help speed up later commands and possibly avoid making conclusions about irrelevant, sometimes non-existent taxa, but there is also some risk of losing interesting data.

Sometimes it can also be useful to apply **phylogenetic filters** to remove reads from endosymbiotic organelles, like mitochondria and chloroplasts, whose 'bacterial' 16S rRNA genes can be amplified by universal bacterial primers. Or, if contaminants are identified in our **negative control**, those ASVs can be removed as well.

To begin the process of filtering, we will generate two files below. One is a file (BIOM file) that reports how many times each ASV/feature was found in each sample. This will help us understand the extent of contamination in our negative control. We will also assign taxonomy to each ASV to deduce which phylogenetic groups our contaminants come from.

We will create a BIOM feature table file of the dataset.

Make a new folder called BIOM.

**Command 10.14**

```bash
mkdir BIOM
```

Export table for making BIOM ASV count file.

**Command 10.15**

```bash
qiime tools export --input-path DADA2_out/V6V8-table-dada2.qza --output-path BIOM
```

Export the BIOM ASV count file as a tab-delimited text file that can be viewed in Excel.

**Command 10.16**

```bash
biom convert -i BIOM/feature-table.biom -o BIOM/feature-table.tsv --to-tsv
```

Open the BIOM file `BIOM/feature-table.tsv` in Excel. Inspect the feature frequency. 

Which are likely contaminant ASVs? 

Are those ASVs also present in our actual samples?

#### **Assign taxonomy**

It is very useful to assign a taxonomic identity to all features in a high throughput way.

To do so, we use our features to query curated databases of marker gene sequences that have associated taxonomic information.

For bacterial and archaeal 16S genes, the Silva (more comprehensive) and GreenGenes (more curated) databases are often used.

Here, we will use a version of the Silva database wherein sequences are clustered at a 99% identity threshold.

Next, we will classify our dataset using a Bayesian classifier approach with the Silva database.

Classify the table.

**Command 10.17**

```bash
qiime feature-classifier classify-sklearn --i-classifier Classify/silva-138-99-nb-classifier.qza --i-reads DADA2_out/V6V8-rep-seqs-dada2.qza --o-classification Classify/V6V8_taxonomy.qza
```

Tabulate blast classification of ASVs.

**Command 10.18**

```bash
qiime metadata tabulate --m-input-file Classify/V6V8_taxonomy.qza --o-visualization Classify/V6V8_taxonomy.qzv
```

With the next command, we will visualize a table of V6V8 ASV classifications.

The classifications are presented as: Kingdom, Phylum, Class, Order, Family, Genus, Species.

Notice that few taxa can be classified to a species level.

View output classification file.

**Command 10.19**

```bash
qiime tools view Classify/V6V8_taxonomy.qzv
```

Now compare your taxonomy visualization and the BIOM file in Excel. Is there a particular taxonomic group that is found commonly in the negative control? If so, what is it?

Create a new Excel spreadsheet. In the first cell, copy and paste "feature-id" and then copy the ASV IDs (long alphanumeric string) of your candidate contaminants into the spreadsheet, with one ID per row. When complete, save this file as a tab-delimited text file (not a .xlsx file) called `to_exclude.txt` within the `Classify` folder. We will tell qiime to delete the features/ASVs in `to_exclude.txt` in the next command.

Also, inspect the abundance of ASVs in all of your samples. If you scroll down a bit, you will notice that many ASVs in our samples (besides the negative control) are found in very low frequency, and may only be in a couple of samples. These ASVs are unlikely to be having a large effect on community structure in kombucha. Perhaps we should filter some out, based on their total frequency across datasets and the number of samples that they are found in.

As a contingency filter, I have suggested to delete any ASV present in 3 or fewer samples, with a total frequency of less than 100 in the command. But you can choose your own thresholds, too, just be sure to write them down!

Filter, and write a new filtered table artifact.

**Command 10.20**

```bash
qiime feature-table filter-features --i-table DADA2_out/V6V8-table-dada2.qza --m-metadata-file Classify/to_exclude.txt --p-exclude-ids --p-min-samples 3 --p-min-frequency 100 --o-filtered-table DADA2_out/V6V8-table-filtered-dada2.qza
```

Output a new Feature Table (sequence) artifact. This updates the sequence dataset so that it no longer contains the filtered sequences; we will use this in downstream analyses.

**Command 10.21**

```bash
qiime feature-table filter-seqs --i-data DADA2_out/V6V8-rep-seqs-dada2.qza --i-table DADA2_out/V6V8-table-filtered-dada2.qza --o-filtered-data DADA2_out/V6V8-rep-seqs-filtered-dada2.qza
```

The data are now filtered how we have specified and we have generated an updated feature table artifact and representative sequences artifact.

If you want to quickly check on what filtering did, generate a visualization of the filtered data table artifact.

**Command 10.22**

```bash
qiime feature-table summarize --i-table DADA2_out/V6V8-table-filtered-dada2.qza --m-sample-metadata-file Metadata/Kombucha_metadata.txt --o-visualization DADA2_out/V6V8-filtered-table-dada2.qzv
```

And view the visualization.

**Command 10.23**

```bash
qiime tools view DADA2_out/V6V8-filtered-table-dada2.qzv
```

How many features do we have now?

Look in the feature detail tab: are there any features present less than 100 times and less than 3 samples remaining?

Can you find any of the putative contaminants (*i.e*., feature names) you identified in the negative control? Hopefully not!

Lastly, we will create a taxonomic bar plot of our filtered Kombucha V6V8 features. 

Keep in mind that this chart shows the **relative abundance** of different taxonomic groups (at different taxonomic levels) for our samples. We are simply seeing the proportion of reads from each sample that a given feature makes up.

Create a taxonomic bar plot visualization of V6V8 ASVs based on filtered classification.

**Command 10.24**

```bash
qiime taxa barplot --i-table DADA2_out/V6V8-table-filtered-dada2.qza --i-taxonomy Classify/V6V8_taxonomy.qza --m-metadata-file Metadata/Kombucha_metadata.txt --o-visualization Classify/V6V8_filtered_taxon_barplot.qzv
```

View the interactive bar chart visualization

**Command 10.25**

```bash
qiime tools view Classify/V6V8_filtered_taxon_barplot.qzv
```

The bar chart can be displayed in many different ways.

**Taxonomic Level**

You can display the chart at various taxonomic levels (Level 1 = Kingdom … Level 7 = species). Try this out.

**Color Palette**

You can choose between several different colour palettes.

**Sort Samples By**

The default setting often does not make a lot of sense.

You can organize bars by sample name, *e.g.*, 'Sample Long'

You can organize the bars according to different metadata categories, such as the delta pH, or the SCOBY weight.

**Other**

You can make the bars wider for easier visualization.

You can click on boxes in the legend to highlight a particular taxonomic group.

You can save a vector (.svg) image and legend.

Note: Decide on an image of the bar chart that you would like to eventually include in your report and save it and its legend as svg files.

- Consider which taxonomic levels make sense (to you) to report.

- Consider how the data look most presentable, and what metadata category to group them by.

**State of the data:**

- We have trimmed the raw sequence reads.

- We have generated features/ASVs along with a feature (count) table.

- We have assigned ASVs to taxonomic groups.

- We have generated a taxonomic bar plot taking some metadata into account.
