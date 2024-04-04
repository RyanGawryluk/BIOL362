## BIOLOGY 362

## PRACTICAL SKILLS IN GENOMICS

#### Lab 12: Phylogenetics – how are taxa related to each other?

Welcome to Lab 12. Molecular phylogenetics is the branch of biology that seeks to understand the relationships of organisms – whether microbes, plants, fungi, or animals – based on the information that is present in their DNA and protein sequences. These relationships are typically visualized as a phylogenetic tree. 

We have already briefly encountered phylogenetics in the previous lab in the context of ‘phylogeny-aware’ beta diversity metrics, i.e., UNIFRAC. However, phylogeny was not the focus of that exercise. Today, we will take a somewhat more in-depth investigation of the various steps required for the generation and interpretation of a phylogenetic tree based on some of your V6V8 region ASVs. In particular, we will investigate how much overlap there is between our most abundant ASVs and the specifiec microbes from family Acetobacteraceae that were found in kombucha by other researchers.

**Generating a multiple sequence alignment**

First, get into the below directory, replacing `NetlinkID` with your own:

```bash
cd /Volumes/labs/BIOL/BIOL362/Students/NetlinkID/Data/
```

- Notice that a new directory called `IQtree`  has been created within `Data`.

- Withing IQtree is a sequence file in `FASTA` format.  

- This file contains sequence data from several sources including **1)** the 10 most abundant ASVs from our kombucha samples (at least they are the most abundant in our analysis); these sequences are the ones with the long qiime-style ASV names. **2)** Sequences corresponding to the 16S V6V8 region from species belonging to the family Acetobacteraceae that have been found in kombucha microbiomes in other studies. These sequences have the prefix `Kom-`, for kombucha.  **3)** V6V8 regions from species belonging to the family Acetobacteraceae that have not - to our knowledge - been reported as components of the kombucha microbiome. **4)** Outgroup sequences (with the prefix `OUT-`) from closely related alphaproteobacteria from outside of Acetobacteraceae; these will be the `outgroups` in our tree reconstruction.

- If you want to see the unaligned FASTA file before aligning the sequences, you can do so by typing the below command:

**Command 12.1**

```bash
less IQtree/kombucha_v6v8_tree_seqs.fasta
```

- Now  we can align the V6V8 sequences present in `kombucha_v6v8_tree_seqs.fasta` using the popular alignment program, ‘mafft’.

- Alignment programs align characters in an alignment (i.e., columns) so that the tree reconstruction algortihm performs calculations based on homologous characters.

**Command 12.2**

```bash
mafft-linsi IQtree/kombucha_v6v8_tree_seqs.fasta > IQtree/kombucha_v6v8_tree_seqs_aligned.fasta
```

- The alignment can be viewed online, using the NCBI alignment viewer.

https://www.ncbi.nlm.nih.gov/projects/msaviewer/

- You should see a line that states: `To see your own alignment, Upload  your data`, with a button for uploading.

- Choose ‘upload data’, select ‘Data file’, browse to the file on the labs’s folder, click ‘upload’ and then ‘close’ the popup window. 

- Alternatively, you could open `kombucha_v6v8_tree_seqs_aligned.fasta` in TextEdit and paste it as text.

- The alignment should show in a zoomed-out version, and you can zoom in and out to inspect the alignment.

- The V6V8 region is highly similar among the taxa chosen here; there are few gaps in the alignment, mostly as a result of aligning with the outgroups, and a handful of substitutions between sequences. 

- There is not a lot of genetic distance between the species, so some may not be well resolved by our phylogenetic analysis.

- In a multiple sequence alignment, each column should represent a homologous character. Typically, to prevent the comparison of non-homologous characters, the highly variable regions of an alignment are either masked (e.g., nucleotide character changed to a non-informative character, like X) or trimmed (i.e., columns are deleted).

- However, our V6V8 sequences are so similar that there are few ambiguous sites. We will reconstruct the phylogenetic tree from the whole aligned V6V8 dataset instead.

**Phylogenetic Tree Reconstruction**

- We will use the program IQ-TREE2 to reconstruct the Acetobacteraceae V6V8 phylogeny. 

- IQ-TREE employs the maximum likelihood method, and can be run on nucleotide or protein data; it can automatically detect that our data are nucleotides.

- By default, IQ-TREE2 will execute a program called ‘jmodeltest’; this program allows IQ-TREE2 to test many different models of evolution, one after the other. 

- IQ-TREE2 chooses the model that best fits our data according to a Bayesian Information Criterion (BIC); the details of this are not important here.

- In addition, our command specifies that IQ-TREE2 will carry out 1000 ‘ultrafast’ bootstrap replicates (-B flag). Ultrafast bootstraps are not quite the same as what we discussed in class; they employ another ‘heuristic’ method, which ultimately means that they take far less time to run, though they tend to exaggerate our confidence in the tree

- Normally, with more data, this step would take a while, and testing all of the models would take the bulk of the time. But in our case, the small size of the dataset means that it runs very quickly.

**Command 12.3**

```bash
iqtree2 -s IQtree/kombucha_v6v8_tree_seqs_aligned.fasta -B 1000
```

- Immediately after starting the IQ-TREE2 job, you will notice data streaming the Terminal app.

- The first output corresponds to finding the model of evolution that best fits the data, using jModelTest.

- IQ-TREE2 subsequently uses the evolutionary model identified by jModelTest and a ‘hill-climbing’ algorithm to generate the statistically most-likely phylogenetic tree. This includes testing various placements for the taxa in the tree and optimizing branch lengths, which represent the amount of sequence divergence since a common ancestor.

- During this process, IQ-TREE2 makes all 1000 of the pseudo-bootstrap replicates. The percentage of times that each ‘split’ in the tree is recovered among the bootstrap replicates will be integrated as statistical support values on the final tree.

- Several different output files are generated by IQ-TREE2.

- These files contain information about the maximum likelihood tree, the bootstrap replicates, details about the run, and the evolutionary model employed. Feel free to look at any of these files if you like, for example through the command `less filename` where filename is the actual file name. Or you can use `zless` instead if files have the .gz suffix.

- The file that we are most interested in is: `IQtree/kombucha_v6v8_tree_seqs_aligned.fasta.contree`

- This file contains the maximum likelihood phylogenetic tree with bootstrap supports for bipartitions included.

**Viewing the phylogenetic tree**

- To view the phylogenetic tree, we will use a popular program called FigTree. 

- This is a MacOS program, located within Apps, and is not a UNIX command line program.

- Unfortunately, as is common in this computer lab, it is a bit challenging to open FigTree. Below are the instructions that were passed along.

**The launcher for FigTree is bugged**. To open it:
Ctrl+click or right click on the FigTree app icon in /Applications/
Select Show package contents from the menu
Open the Contents folder, then Resources, then Java
Run FigTree.jar by clicking on it.

- Once FigTree is open, use the file menu to open `kombucha_v6v8_tree_seqs_aligned.fasta.contree`

- As you are opening the file, a popup box should ask you to give a name to your bootstrap replicates. It doesn’t matter what you call them but give them a name that makes sense to you; we need to find them in a minute!

- We can adjust the way that the tree looks in a variety of ways.

- To root the tree, expand the `Trees` tab on the left, and click the `Root tree` option. 

- If our pre-selected outgroups are not showing up as the root, simply select the branch leading to those sequences on the tree itself and click the `Reroot` button at the top.

- Select `Order Nodes` within `Trees` and choose increasing or decreasing; whatever looks better to you.

- Then, expand the `Node Labels` tab on the left, and under `Display`, choose whatever name you gave to your rapid bootstrap replicates. This will add the support value for each bipartition on the tree.

- You can also change the font size of the labels (i.e., taxon names) by expanding `Tip Labels` and changing the font size

- You can also play with the layout of the tree, or expand it to suit your tastes - play around!

- If you want to save a copy of the tree, first save the ‘.contree’ file from within FigTree and then export a PDF that you can visualize later.

- Does it appear that the V6V8 ASVs that we identified in our kombucha amplicon analysis overlap with other Acetobacteraceae members previously found to be common in kombucha? 

- Or do our ASVs group with other members of Acetobacteraceae? 

- Have a look at the bootstrap values for bipartitions in the tree. The higher the values are, the more confident we can be that splits/bipartitions in the tree represent a strong signal in our data.

- Do bootstrap values tend to be higher nearer the tips of the tree (more recent) or deep within the tree (more ancient splits)?
