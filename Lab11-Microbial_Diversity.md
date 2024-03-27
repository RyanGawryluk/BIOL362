# BIOLOGY 362

# PRACTICAL SKILLS IN GENOMICS

### Lab 11: Microbial Diversity – are my samples different from each other?

Welcome to Lab 11. One of the central goals of microbial ecology is to determine whether or not your samples (or sample types) differ in terms of the number and diversity of taxa that they contain. By integrating data visualization, statistics, and metadata, we can gain powerful insights into how microbial diversity differs within and between samples, what factors might shape microbial community structures, and whether or not any taxa are more or less abundant in different samples.

#### The importance of metadata

Most simply, metadata are data about our data. What do we mean by this? Use Microsoft Excel to open the tab-delimited file, `Kombucha_metadata.txt`, which is located in the Metadata folder in your personal folder. Here, the columns present categorical (type) and numeric (continuous) data from various categories about the trees used in our experiment, ranging from their family memberships, to their growth rates, needle retention, and stomatal occlusion scores. All of these values could conceivably have influences on – or be impacted by – the bacterial communities residing within kombucha, and qiime2 has tools to help us examine this.

#### Alpha diversity

**First, activate the qiime2 environment**.  Can you remember how to do this?

Then, **navigate to** `/Volumes/labs/BIOL/BIOL362/Students/NetlinkID/Data` where NetlinkID is your NetlinkID, as in previous labs.

•    We will start out today by examining the diversity of ASVs within individual samples, otherwise known as alpha diversity.
•    However, before we begin, we need to create a phylogenetic tree using only the ASVs found in our experiment (we will not be searching public databases to retrieve further sequences today). The reason that this is necessary today is that some of the ‘phylogeny-aware’ diversity measurements require a phylogenetic tree to work.

**Make a new directory called `Phylo`, then execute the code block below.**

**Command 11.1**

```bash
qiime phylogeny align-to-tree-mafft-iqtree --i-sequences DADA2_out/V6V8-rep-seqs-filtered-dada2.qza --o-alignment Phylo/aligned-V6V8-filtered-seqs-dada2.qza --o-masked-alignment Phylo/masked-aligned-V6V8-filtered-seqs-dada2.qza --o-tree Phylo/V6V8-unrooted-tree.qza --o-rooted-tree Phylo/V6V8-rooted-tree.qza
```

This command has performed several different operations:

•    Using a popular gene alignment tool called MAFFT, qiime2 has aligned all of the V6V8 sequences present in the `V6V8-rep-seqs-filtered-dada2.qza` artifact.
•    Sequence alignment software lines up columns of nucleotides, which are assumed to represent homologous positions within a gene sequence.
•    The homology of some nucleotides across species remains ambiguous, even after multiple sequence alignment. In these cases, it is appropriate to ‘mask’ or remove those ambiguous characters, because we do not want to assume homology when it is not evident. So our output here includes an alignment and a 'masked' alignment.
•    Finally, the qiime2 software generates a maximum likelihood phylogenetic tree by calling upon the program, ‘iqtree’.

The qiime2 ‘diversity’ plugin is a tool that can create data visualizations via Principal Coordinate Analysis (PCoA) and perform statistical tests on a variety of different distance metrics.

However, it is useful to perform such analyses on datasets of the same size. We need to include the same number of ‘reads’ from each sample; for instance, if we have unequal sequencing depth across samples, it’s likely that the number of distinct ASVs will be higher in samples represented by more reads.

To do this, we will subsample feature counts (without replacement) from each sample to the same depth, which is called ‘rarefaction’.

Choosing a depth to rarefy to, specified by --p-max-depth, is sometimes tricky. The goal is to **1)** choose as high of a number as possible, because it corresponds to the number of sequences used for alpha (and later, beta) diversity analyses and visualizations, but, **2)** to retain as many samples as possible. If you choose a value higher than the number of counts in a sample, you will exclude that sample from consideration
•    We recommend that you view the Interactive Sample Detail tab in `DADA2_out/V6V8-table-dada2.qzv` to make this decision.

Change the '**x**' in the below command to the number that you want to rarefy to. You can try different values iteratively until you get a suitable rarefaction plot.



**Create a ‘Diversity’ directory within the Data directory.**

**Command 11.2**

```bash
qiime diversity alpha-rarefaction --i-table DADA2_out/V6V8-table-filtered-dada2.qza --i-phylogeny Phylo/V6V8-rooted-tree.qza --p-max-depth x --m-metadata-file Metadata/Kombucha_metadata.txt --o-visualization Diversity/V6V8-alpha-rarefaction.qzv
```

**View the `V6V8-alpha-rarefaction.qzv` plot with qiime**. You should see two plots.

The upper plot shows alpha diversity (with 3 different distance metrics and several categorical metadata columns) as a function of sequencing/subsampling depth. 

- Try looking at these curves for individuals (e.g., Sample Metadata Column = Sample_Long) and groups (e.g., Sample Metadata Column = Carbon_Source).

- Focus on the Shannon metric.

- You want to see the curve reaching a plateau prior to your specified rarefaction depth. Reaching a plateau indicates that you have likely sequenced/subsampled deeply enough that few new taxa will be added with increasing depth; you have a good representation of the microbial community.

- The lower plot shows how many samples you have left per category. This number can change depending on which categorical metadata value you are looking at, but you will notice dropped samples here if you have selected a rarefaction depth above the number of sequences in that sample
  
  

- Have you lost any samples in choosing your --p-max-depth value?

- At what sequencing depth does alpha diversity plateau in your analysis?

- If you are happy with your rarefaction curves, proceed to the next step; if not, try running the command again with a different sequencing depth



Next, we will use a command that simultaneously generates artifacts, and in some cases, visualizations, for several commonly used metrics of alpha and beta diversity.

- Note that we point qiime2 towards our phylogenetic tree here. Certain metrics, like UNIFRAC distances, require a phylogenetic tree for calculating diversity; however, most others do not.

- Qiime2 is also accessing our filtered data table, generated with DADA2, which contains information about how many times each feature/ASV was observed in each sample.

- Importantly, qiime2 is also accessing our metadata file. Incorporating metadata into visualizations and statistical analyses can be a powerful tool in understanding the factors that shape microbial communities.
  
  **Note**: the --p-sampling-depth, “x” should be the rarefaction value that you chose in the alpha rarefaction analysis

**Command 11.3**

```bash
qiime diversity core-metrics-phylogenetic --i-phylogeny Phylo/V6V8-rooted-tree.qza  --i-table DADA2_out/V6V8-table-filtered-dada2.qza --p-sampling-depth x --m-metadata-file Metadata/Kombucha_metadata.txt --output-dir Diversity/V6V8-core-metrics-results
```

Next, we will execute statistical tests to determine if alpha diversity metrics are higher or lower in different samples or sample groups. First, we will examine the simplest of alpha diversity measures, the number of observed features in each sample.

- Basically, we are asking whether a sample, or sample type (e.g., carbon source) has a significantly higher or lower number of ASVs than other samples/sample types in our dataset based on the standard statistical alpha level of 0.05.

- Recall that ‘observed features’ is a qualitative approach; we are simply counting the number of features (i.e., ASVs) observed in each sample (richness), giving each equal weight. This metric does not take feature abundance (evenness) into account.

- Run the below commands:

**Command 11.4**

```bash
qiime diversity alpha-group-significance --i-alpha-diversity Diversity/V6V8-core-metrics-results/observed_features_vector.qza --m-metadata-file Metadata/Kombucha_metadata.txt --o-visualization Diversity/V6V8-core-metrics-results/observed_features_vector-significance.qzv
```

**Command 11.5**

```bash
qiime tools view Diversity/V6V8-core-metrics-results/observed_features_vector-significance.qzv
```

You will see a series of box plots that are grouped according to different categorical metadata types.

- By default, the samples are grouped by the Carbon_Source category, which is the most relevant one in our case, but feel free to explore.

- The Y-axis of the box plot denotes the number of unique features found for each Carbon_Source. There is variability because each box contains data from multiple individual samples. 

- The box plots have a lower line outside of the box (10th percentile), the lower bound of the box (25th percentile), a line within the box (50th percentile; median), the upper bound of the box (75th percentile) and an upper line (90th percentile).

- Does visual inspection of the box plots suggest that samples are different in their number of observed features?

- Fortunately, we don’t have to rely on our ‘eye-test’ to determine if the samples are different in terms of observed features. Qiime2 has used a non-parametric test, Kruskal-Wallis, to determine if the samples differ.

- Scroll down on the visualization, and inspect the results of ‘Kruskal-Wallis (all groups)’. This tells us (by comparing the p-value with 0.05) whether there is a significant difference across all groups.
  
  What is the p-value across all groups? Are samples different?

- Qiime2 also performs pairwise Kruskal-Wallis tests between each group in order to test whether individual samples/groups differ from each other in the number of observed features.

- Scroll down further to inspect these results. Notice that there are p-values and another score called q-values. The q-values are meant to account for the multiple comparisons made in the pairwise tests, and are the statistic that we are interested in here. They should be compared to 0.05, as the p-values were previously.
  
  Are any pairwise Kruskal-Wallis tests significant? If so, which ones?

- Next, we will perform virtually the same analysis, but using a different alpha diversity metric, known as Shannon diversity

- Shannon diversity is different from observed features, as it considers *feature evenness* (how many times was each observed feature actually observed in each sample) alongside *richness* (feature number).

- Taking evenness into account could give very different answers than richness alone. For instance, some samples with a high richness score might be dominated by only a couple of taxa, with a small number of reads from other low abundance taxa with relatively small effects on community function. Should these low abundance taxa be given an equal weight in our attempts to understand microbial ecology? 

- Execute the below commands.

**Command 11.6**

```bash
qiime diversity alpha-group-significance --i-alpha-diversity Diversity/V6V8-core-metrics-results/shannon_vector.qza --m-metadata-file Metadata/Kombucha_metadata.txt --o-visualization Diversity/V6V8-core-metrics-results/shannon_vector-significance.qzv
```

**Command 11.7**

```bash
qiime tools view Diversity/V6V8-core-metrics-results/shannon_vector-significance.qzv
```

Inspect the box plots and Kruskal-Wallis test statistics generated in this analysis.



Is alpha diversity significantly different (higher or lower)  as a function of carbon source used when both richness and evenness are taken into consideration, i.e., according to the Shannon diversity metric? 



#### Beta Diversity

- Unlike alpha diversity, which measures richness and/or evenness within a sample, beta diversity tells us about the similarity/dissimilarity *between* different communities.

- The basis of any beta diversity measurement is the generation of a distance matrix, which contains the results of pairwise comparisons between each sample and every other sample.

- As in the case of alpha diversity, there are beta diversity metrics that only consider feature presence/absence (qualitative) and others that consider feature presence/absence and feature abundance. We will also explore one metric, UNIFRAC, that incorporates phylogenetic distances.

- Visual inspection of sample/community clustering using beta diversity matrices and metadata can help us understand which samples contain the microbial communities that are most similar/dissimilar.

- Many of these visualizations have already been generated using an earlier qiime2 command!

- The beta diversity metrics have been transformed using an approach called Principal Coordinates Analysis (PCoA). This is a ‘dimensionality reduction’ approach that splits complex distance matrices into a small number of distinct components that help to spread the data out for visual inspection.

- The PCoA plots are a tool to explore the data; they are not statistical tests.

- Qiime2 calls these visualizations ‘Emperor Plots’ .

- We will now inspect these visualizations for 3 different types of beta diversity indices.

**Command 11.8**

```bash
qiime tools view Diversity/V6V8-core-metrics-results/jaccard_emperor.qzv
```

You will initially see a visualization with red dots on a black background. Each dot represents the microbial community present in a single sample. The data are spread out over three axes.

This visualization initially does not seem very informative, but this is where metadata become important! Notice that there are various ‘tabs’ on the right side of the screen. Click on the ‘**Select a Colour Category**’ pulldown menu. Here, you can choose a metadata column to assign a colour to the individual data points

Try selecting ‘**Carbon_source**’. You should now see that each sample is coloured according to which carbon source that it was grown with.

- Note that these Emperor plots are 3-dimensional; try selecting the plot and rotating it!

- You can colour the samples by any of the other metadata columns, too!

- What we are looking for here is whether or not the communities within samples cluster together (are similar) according to metadata parameters.

- You can further customize these visualizations in many ways. You can change the colours (under Colour) and shapes (under Shape) associated with the metadata categories, and you can make the background white and axes black (under Axes), or explore other metadata categories.



The Jaccard index is a ***qualitative*** metric.

- Jaccard generates a distance score between two samples based solely on whether or not a feature/ASV is present or absent in those samples.

- If samples are clustered, it would tell us that they have a high proportion of features in common, though it would not take the feature count information into account.



sDo you see any strong evidence for sample clustering? If so, how are samples clustered?



- For any given categorical metadata column (not continuous, numeric data), we can also calculate whether samples (or groups of samples) are statistically different.

- By default, qiime2 compares across all groups to determine if samples are generally different in terms of Jaccard beta diversity using a test called ‘permutational ANOVA’, and we can specify that it also performs pairwise tests between all pairs of groups by activating the --p-pairwise flag.

- Run the below two commands to test whether microbial communities are different using the Jaccard index according to their carbon source.



**Command 11.9**

```bash
qiime diversity beta-group-significance --i-distance-matrix Diversity/V6V8-core-metrics-results/jaccard_distance_matrix.qza --m-metadata-file Metadata/Kombucha_metadata.txt --m-metadata-column Carbon_source --o-visualization Diversity/V6V8-core-metrics-results/jaccard_distance_matrix-significance.qzv --p-pairwise
```

**Command 11.10**

```bash
qiime tools view Diversity/V6V8-core-metrics-results/jaccard_distance_matrix-significance.qzv
```

At the top of this visualization, you will see your test statistics that report whether there is any significant difference in Jaccard beta diversity between the four groups.

In particular, if the p-value is < 0.05, it suggests that there is some significant difference between groups.

However, it does not tell you which groups are actually different!

- To determine which (if any) groups are significantly different, scroll down to the results of the pairwise comparisons and inspect the q-values (again, corrected for multiple comparisons).

- Do the q-values of the pairwise comparisons suggest any significant differences in community structure between family categories? If so, which ones?
  o    Keep in mind that Jaccard is only based on common feature presence/absence between the groups – it is telling us about the proportion of organisms that are not shared between groups.



Next, let's have a look at two additional visualizations generated with different beta diversity indices, Bray-Curtis and Weighted UNIFRAC.

These indices are quite different from Jaccard:

- Bray-Curtis is a ***quantitative*** measure of community dissimilarity. It is analogous to the Shannon index of alpha diversity in that it accounts for both the presence/absence of features, along with their abundances, so dominant features have a larger effect on measures of community dissimilarity than features 

- Weighted UNIFRAC is similar to Bray-Curtis in that it is quantitative, and takes feature presence/absence and abundance into account. But in addition, Weighted UNIFRAC also requires a phylogenetic tree, and incorporates *evolutionary distances* between taxa into the community dissimilarity measures.  The underlying assumption here is that phylogenetically related organisms might carry out similar roles in a microbiome; so, communities made up of more phylogenetically similar microbes would be seen as less dissimilar than those made up of distantly related microbes.

- Have a look at these Emperor plots and colour according to metadata columns. Do these visualizations indicate any interesting community structure among the samples?

**Command 11.11**

```bash
qiime tools view Diversity/V6V8-core-metrics-results/bray_curtis_emperor.qzv
```

**Command 11.12**

```bash
qiime tools view Diversity/V6V8-core-metrics-results/weighted_unifrac_emperor.qzv
```

- In the below four commands, we will carry out two additional beta group significance tests using Bray-Curtis and Weighted UNIFRAC, just as we did for Jaccard previously.

- Are there significant differences between all groups? What about pairwise comparisons? Do these beta group significance tests tell you anything that the test using Jaccard told you?



**Command 11.13**

```bash
qiime diversity beta-group-significance --i-distance-matrix Diversity/V6V8-core-metrics-results/bray_curtis_distance_matrix.qza --m-metadata-file Metadata/Kombucha_metadata.txt --m-metadata-column Carbon_source --o-visualization Diversity/V6V8-core-metrics-results/bray-curtis_distance_matrix-significance.qzv --p-pairwise
```

**Command 11.14**

```bash
qiime tools view Diversity/V6V8-core-metrics-results/bray-curtis_distance_matrix-significance.qzv
```

**Command 11.15**

```bash
qiime diversity beta-group-significance --i-distance-matrix Diversity/V6V8-core-metrics-results/weighted_unifrac_distance_matrix.qza --m-metadata-file Metadata/Kombucha_metadata.txt --m-metadata-column Carbon_source --o-visualization Diversity/V6V8-core-metrics-results/weighted_unifrac_status-significance.qzv --p-pairwise
```

**Command 11.16**

```bash
qiime tools view Diversity/V6V8-core-metrics-results/weighted_unifrac_status-significance.qzv
```

#### 

#### Differential Abundance

- In trying to understand the underlying factors shaping different microbiomes, an important aspect to investigate is whether there are any taxa that are differentially abundant in different sample types.

- Increased or reduced abundance of a taxon in different samples could be correlated with, or even causal to, a phenotype of interest.

- Qiime2 offers some programs that can test for differentially abundant taxa, including ANCOM, which is included within the Composition plugin.  

**Create a new folder called ‘DiffAbundance’ in the ‘Data’ directory**.

- ANCOM reads feature abundances from the feature table (i.e., the BIOM file generated in the previous lab).

- ANCOM cannot deal with zero counts in the feature table, so before we proceed with the analysis, we need to add ‘pseudocounts’ – a common value – to each feature count in the table, using the below command.

**Command 11.17**

```bash
qiime composition add-pseudocount --i-table DADA2_out/V6V8-table-filtered-dada2.qza --o-composition-table DiffAbundance/comp-V6V8-table-filtered-dada2.qza
```

- Next, we can run ANCOM on our filtered feature table.

- We are explicitly testing whether or not there are differentially abundant features according to the metadata column ‘Carbon_source’, though we could theoretically test any categorical metadata variable here.

- Execute the below command

**Command 11.18**

```bash
qiime composition ancom --i-table DiffAbundance/comp-V6V8-table-filtered-dada2.qza --m-metadata-file Metadata/Kombucha_metadata.txt --m-metadata-column Carbon_source --o-visualization DiffAbundance/filtered-ancom-Carbon_source.qzv
```

- You will see a chart called a Volcano Plot at the top of the output.

- The actual interpretation of this test is complex and unintuitive, but ANCOM is basically executing many pairwise tests of log ratio abundances of different features in different samples.

- The x-axis is summarizing the effect size difference of the given species between your treatment groups (clr; centred log ratio), and the y-axis is the strength of the ANCOM test statistic (W).

- Consistently differentially expressed taxa should appear in the top right of the Volcano plot.
  
  - If you scroll down the page, you can see percentile-based counts for various features in the different family categories.
  
  - Do you see any differentially abundant taxa? If so, open BIOM/feature-table.tsv with Excel and have a look for the feature name(s) and the abundance of that feature among samples. Do the ANCOM results seem to make sense?
  
  - If you have differentially abundant taxa, view your representative sequence visualization. Can you find the differentially abundant taxon there? Can you use BLAST to roughly determine its taxonomic affiliation?
  
  - Is the differentially abundant feature/ASV abundant? Would you guess that it is a dominant and important microbe that would contribute significantly to microbiome structure and function? Or a more minor contributor?
