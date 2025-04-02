## BIOLOGY 362

## PRACTICAL SKILLS IN GENOMICS

#### Lab 12: Phylogenetics – how are taxa related to each other?

Welcome to Lab 12. Molecular phylogenetics is the branch of biology that seeks to understand the relationships of organisms – whether microbes, plants, fungi, or animals – based on the information that is present in their DNA and protein sequences. These relationships are typically visualized as a phylogenetic tree. 

We have already briefly encountered phylogenetics in the previous lab in the context of ‘phylogeny-aware’ beta diversity metrics, *i.e*., UNIFRAC. However, phylogeny was not the focus of that exercise. Today, we will take the steps required to generate and interpret a phylogenetic tree based on the Cox1 gene of *Artemia*. In particular, we will investigate which species group(s) your SeaMonkeys and HydroPets are derived from.

But first we will be performing an analysis of differential taxon abundance that we were meant to do last week.

**Differential Abundance**

In trying to understand the underlying factors shaping different microbiomes, an important aspect to investigate is whether there are any individual taxa that are differentially abundant in different sample types.

Increased or reduced abundance of a taxon in different samples could be correlated with, or even causal to, a phenotype of interest.

Qiime2 offers some programs that can test for differentially abundant taxa, including ancombc, which is included within the Composition plugin. This will allow us to identify, for instance, the ASVs that are more/less abundant in, *e.g.,* SeaMonkeys grown in warm versus cold conditions, or between HydroPets and SeaMonkeys (if they exist).

First, activate qiime2.

**Command 12.1**

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

**Command 12.2**

Next, navigate to the appropriate folder, where ```NetlinkID``` is your NetlinkID, as in previous labs.

```bash
cd /Volumes/labs/BIOL/BIOL362/Students/NetlinkID/Data/
```

**Command 12.3**

Here, we will generate an artifact that will eventually allow us to determine differential abundance as a function of the ```Metadata``` column, ```Full_type``` (if you need, refresh yourself on what this is by opening your ```Metadata``` file with Excel.

Ancombc requires us to define a ```Full_type``` subcategory (an intercept) to compare each other Full_type category to. Because we are most interested in comparing SeaMonkey organisms grown in hot and cold conditions, we will set the intercept to ```SeaMonkey_organism_cool```, so that it is the intercept that all other sample types will be compared to. This is specified with the ```--p-reference-levels Full_type::SeaMonkey_organism_cool``` flag.

```bash
qiime composition ancombc --i-table DADA2_out/V6V8-table-filtered-dada2.qza --m-metadata-file Metadata/Artemia_metadata.txt --p-formula Full_type --p-reference-levels Full_type::SeaMonkey_organism_cool  --output-dir DiffAbundance_vs_SMOC
```

**Command 12.4**

Next we generate a visualization of the associated statistics for differential abundance.

```bash
qiime composition tabulate --i-data DiffAbundance_vs_SMOC/differentials.qza --o-visualization DiffAbundance_vs_SMOC/differentials.qzv
```

**Command 12.5**

```bash
qiime tools view DiffAbundance_vs_SMOC/differentials.qzv
```

Have a look at the visualization. You will see a table, with feature names on the left, and the log fold change values for that feature in each other ```Full_type``` category versus the ```SeaMonkey_organism_cool``` sample type. As noted on the page, ```Positive numbers indicate enrichment relative to the intercept, negative numbers indicate depletion relative to the intercept.``` 

At the top, there are several other tabs. Click on the ```q_value``` tab to view the appropriate statistics for differential abundance. Scroll through the table, with an eye on the ```SeaMonkey_organism_hot``` comparison. 

Do you see any highly significant differences from ```SeaMonkey_organism_cool``` (the intercept)? Are there other columns for that feature that are also different from ```SeaMonkey_organism_cool```? Be careful not to overinterpret your results - there's always a possibility that the tank of origin has a strong impact on the taxa present, *etc.* rather than our test variable (how would you improve the experimental setup?).

**Command 12.6**

Next, we will generate a differential abundance barplot to better summarize the significant changes, defining a q_value of 0.05 as the alpha value.

```bash
qiime composition da-barplot --i-data DiffAbundance_vs_SMOC/differentials.qza --p-significance-threshold 0.05 --o-visualization DiffAbundance_vs_SMOC/da_barplot.qzv
```

**Command 12.7**

```bash
qiime tools view DiffAbundance_vs_SMOC/da_barplot.qzv
```

The visualization gives you the option of looking at several different comparisons (look at them all, if you like), and provides some notes on interpreting the charts. Open up the ```Full_typeSeaMonkey_organism_hot``` option and have a look. You should see some features that are more or less abundant in ```Full_typeSeaMonkey_organism_hot```, represented as "enriched" and "depleted" relative to the reference (```SeaMonkey_organism_cool```), respectively. If you move your cursor over this visualization, you will see the statistics that are the output of Command 12.4, *i.e.*, from the ```differentials.qzv``` file.

These are interesting features to follow up on, *i.e.*, for your Discussion. Have a look at other output files you've already generated, like ```Classify/V6V8_taxonomy.qzv``` to see the phylogenetic classification of that feature ID, or view ```DADA2_out/V6V8-rep-seqs-dada2.qzv``` so that you can grab the feature sequence and BLAST it against the NCBI database (I recommend downloading the FASTA file of feature sequences from that visualization, if you haven't already).

What are some of the most differentially abundant features? What kinds of organisms are they? Have previous *Artemia* (or even other invertebrate) microbiome studies identified these as interesting? Might some be known or suspected pathogens? You're unlikely to find something interesting for most features, but you might find something useful!

There are a variety of other comparisons that you could make if you wish. Ancombc is a little bit clunky, and you'd have to run similar commands again if you wish to use another sample type as the intercept. I've included commands to run this again, but with the ```Full_type``` category ```Hydropet_organism_cool``` as the intercept, so that you can compare HydroPet organisms to SeaMonkeys if you want to. You can peform any other comparison by modifying the code, but be sure to give a new directory name to each differential abundance analysis.

**Command 12.8**

```bash
qiime composition ancombc --i-table DADA2_out/V6V8-table-filtered-dada2.qza --m-metadata-file Metadata/Artemia_metadata.txt --p-formula Full_type  --p-reference-levels Full_type::Hydropet_organism_cool  --output-dir DiffAbundance_vs_HPOC
```

**Command 12.9**

```bash
qiime composition tabulate --i-data DiffAbundance_vs_HPOC/differentials.qza --o-visualization DiffAbundance_vs_HPOC/differentials.qzv
```

**Command 12.10**

```bash
qiime tools view DiffAbundance_vs_HPOC/differentials.qzv
```

**Comand 12.11**

```bash
qiime composition da-barplot --i-data DiffAbundance_vs_HPOC/differentials.qza --p-significance-threshold 0.05 --o-visualization DiffAbundance_vs_HPOC/da_barplot.qzv
```

**Command 12.12**

```bash
qiime tools view DiffAbundance_vs_HPOC/da_barplot.qzv
```

#### 

#### **Phylogenetic analysis of Cox1**

**Generating a multiple sequence alignment**

Now we will analyze our Cox1 Sanger data in a phylogenetic framework. First, make sure that you are in the below directory, replacing `NetlinkID` with your own:

**Command 12.13**

```bash
cd /Volumes/labs/BIOL/BIOL362/Students/NetlinkID/Data/
```

- Notice that a new directory called `IQtree`  has been created within `Data`.

- Within IQtree is a sequence file in `FASTA` format.  

- This file contains DNA sequence data from **1)** the Cox1 gene of SeaMonkey and HydroPet *Artemia* specimens, and **2)** other publicly available Cox1 gene sequences from other *Artemia* species.

- If you want to see the unaligned FASTA file before aligning the sequences, you can do so by typing the below command:

**Command 12.14**

```bash
less IQtree/Artemia_Cox1.fasta
```

- Now  we can align the Cox1 sequences present in `Artemia_Cox1.fasta` using the popular alignment program, ‘mafft’.

- Alignment programs align characters in an alignment (i.e., columns) so that the tree reconstruction algortihm performs calculations based on homologous characters.

**Command 12.15**

```bash
mafft-linsi IQtree/Artemia_Cox1.fasta > IQtree/Artemia_Cox1_aligned.fasta
```

- The alignment can be viewed online, using the NCBI alignment viewer.

https://www.ncbi.nlm.nih.gov/projects/msaviewer/

- You should see a line that states: `To see your own alignment, Upload  your data`, with a button for uploading.

- Choose ‘upload data’, select ‘Data file’, browse to the file on the labs’s folder, click ‘upload’ and then ‘close’ the popup window. 

- Alternatively, you could open `Artemia_Cox1_aligned.fasta` in TextEdit and paste it as text.

- The alignment should show in a zoomed-out version, and you can zoom in and out to inspect the alignment.

- The Cox1 region is reasonably similar among the taxa chosen here; there may be a few gaps in the alignment, and a handful of substitutions between sequences. 

- There is not a lot of genetic distance between some of the species (or strain of the same species), so some may not be well resolved by our phylogenetic analysis. That's ok - we basically just need to determine which species group our *Artemia* are from.

- In a multiple sequence alignment, each column should represent a homologous character. Typically, to prevent the comparison of non-homologous characters, the highly variable regions of an alignment are either masked (e.g., nucleotide character changed to a non-informative character, like X) or trimmed (i.e., columns are deleted).

- However, our Cox1 sequences are similar enough that there are few ambiguous sites. We will reconstruct the phylogenetic tree from the whole aligned Cox1 dataset instead.

**Phylogenetic Tree Reconstruction**

- We will use the program IQ-TREE2 to reconstruct the *Artemia* Cox1 phylogeny. 

- IQ-TREE employs the maximum likelihood method, and can be run on nucleotide or protein data; it can automatically detect that our data are nucleotides.

- By default, IQ-TREE2 will execute a program called ‘jmodeltest’; this program allows IQ-TREE2 to test many different models of evolution, one after the other. 

- IQ-TREE2 chooses the model that best fits our data according to a Bayesian Information Criterion (BIC); the details of this are not important here.

- In addition, our command specifies that IQ-TREE2 will carry out 1000 ‘ultrafast’ bootstrap replicates (the -B flag). Ultrafast bootstraps are not quite the same as what we discussed in class; they employ another ‘heuristic’ method, which ultimately means that they take far less time to run, though they tend to exaggerate our confidence in the tree.

- Normally, with more data, this step would take a while, and testing all of the models would take the bulk of the time. But in our case, the small size of the dataset means that it runs very quickly.

**Command 12.16**

```bash
iqtree2 -s IQtree/Artemia_Cox1_aligned.fasta -B 1000
```

- Immediately after starting the IQ-TREE2 job, you will notice data streaming the Terminal app.

- The first output corresponds to finding the model of evolution that best fits the data, using jModelTest.

- IQ-TREE2 subsequently uses the evolutionary model identified by jModelTest and a ‘hill-climbing’ algorithm to generate the statistically most-likely phylogenetic tree. This includes testing various placements for the taxa in the tree and optimizing branch lengths, which represent the amount of sequence divergence since a common ancestor.

- During this process, IQ-TREE2 makes all 1000 of the pseudo-bootstrap replicates. The percentage of times that each ‘split’ in the tree is recovered among the bootstrap replicates will be integrated as statistical support values on the final tree.

- Several different output files are generated by IQ-TREE2.

- These files contain information about the maximum likelihood tree, the bootstrap replicates, details about the run, and the evolutionary model employed. Feel free to look at any of these files if you like, for example through the command `less filename` where filename is the actual file name. Or you can use `zless` instead if files have the .gz suffix.

- The file that we are most interested in is: `IQtree/Artemia_Cox1_aligned.fasta.contree`

- This file contains the maximum likelihood phylogenetic tree with bootstrap supports mapped on to the appropriate bipartitions.

**Viewing the phylogenetic tree**

- To view the phylogenetic tree, we will use a popular program called FigTree. 

- This is a MacOS program, located within Apps, and is not a UNIX command line program.

- Unfortunately, as is common in this computer lab, it is a bit challenging to open FigTree. Below are the instructions that were passed along.

**The launcher for FigTree is bugged**. To open it:
Ctrl+click or right click on the FigTree app icon in /Applications/
Select Show package contents from the menu
Open the Contents folder, then Resources, then Java
Run FigTree.jar by clicking on it.

- Once FigTree is open, use the file menu to open `Artemia_Cox1_aligned.fasta.contree`

- As you are opening the file, a popup box should ask you to give a name to your bootstrap replicates. It doesn’t matter what you call them but give them a name that makes sense to you; we need to find them in a minute!

- We can adjust the way that the tree looks in a variety of ways.

- To root the tree, expand the `Trees` tab on the left, and click the `Root tree` option. 

- Select the branch leading to the four outgroup species from the genera *Streptocephalus*, *Phallocryptus* and *Branchinella*, and root the tree there. These are known close relatives of *Artemia*.

- Select `Order Nodes` within `Trees` and choose increasing or decreasing; whatever looks better to you.

- Then, expand the `Node Labels` tab on the left, and under `Display`, choose whatever name you gave to your rapid bootstrap replicates. This will add the support value for each bipartition on the tree.

- You can also change the font size of the labels (i.e., taxon names) by expanding `Tip Labels` and changing the font size

- You can also play with the layout of the tree, or expand it to suit your tastes - play around!

- If you want to save a copy of the tree, first save the ‘.contree’ file from within FigTree and then export a PDF that you can use later.

- Which other *Artemia* taxa do our SeaMonkeys and Hydropets affiliate with? Are they highly similar to each other?

- Have a look at the bootstrap values for bipartitions in the tree. The higher the values are, the more confident we can be that splits/bipartitions in the tree represent a strong signal in our data.

**State of the data**

- You have completed the lab component of BIOL362!

- You have executed a differential abundance analysis that can help identify features (ASVs) that are more/less abundant in the microbiome of SeaMonkeys grown in cool conditions vs other microbiomes (pay special attention to the SeaMonkeys grown in warmer conditions). Can you use this data to compare to other studies?

- You have used Cox1 genetic data to determine which *Artemia* species group our specimens belong to. Would you expect their microbiomes to be very different based on this alone?
