# Is Taxon Delineation Harder in the Marine Realm?

**Marine vs. freshwater DNA barcode sequence clustering patterns**

A computational study of whether species boundaries are harder to resolve from DNA
barcodes in marine organisms than in freshwater ones. Using COI-5P barcode sequences
from [BOLD](https://www.boldsystems.org/) (the Barcode of Life Data System), this
analysis measures how cleanly sequences cluster into species-level groups, and compares
that clustering strength between marine and freshwater members of the same taxonomic
families.

> Graduate research project (University of Guelph, 2021). Written in R as a single
> reproducible R Markdown document.

## Background

DNA barcoding identifies species from a short, standardized gene region (COI-5P for
animals). It works best when sequences from the same species cluster tightly together
and separate cleanly from other species. If barcodes cluster less cleanly in one
environment than another, species delineation is effectively "harder" there. This
project tests whether the marine realm shows weaker clustering than freshwater, across
fish and several invertebrate groups.

## Approach

```
BOLD COI-5P records
      |
      v
[ filter ]   keep COI-5P only, valid nucleotides, <=1% Ns, >=500 bp, has a BIN
      |
      v
[ classify habitat ]   join to WoRMS taxonomy to label marine vs. freshwater
      |
      v
[ align ]   multiple sequence alignment per group (DECIPHER / muscle)
      |
      v
[ cluster ]   distance-based clustering (TN93 model) at a divergence threshold
      |
      v
[ score ]   silhouette values measure how clean each cluster is
      |
      v
[ compare ]   marine vs. freshwater silhouette, per family, with significance tests
```

Clustering strength is summarized with the **average silhouette value** (higher = cleaner,
more separable clusters). Marine and freshwater silhouette values are compared per family,
with a Wilcoxon test for significance. The analysis is run at two divergence thresholds
(0.02 and 0.03) to check that findings are not an artifact of one threshold choice.

Four taxonomic groups are covered: **Actinopterygii** (ray-finned fish), **Bivalvia**,
**Branchiopoda**, and **Malacostraca** (crustaceans).

## Figures

| Figure | What it shows |
|---|---|
| `W_DumbbellPlotcolored.png` | Average silhouette value (cluster cleanliness) for freshwater vs. marine members of each family, as paired points joined by a line. The length of each line is the marine/freshwater gap; color marks habitat type. The quick visual read of the whole study. |
| `W_AllTableResults2.png` | Full results table at the 0.02 threshold: BIN count, number of clusters, record counts, average silhouette for each habitat, the freshwater-minus-marine difference, and the Wilcoxon p-value per family. |
| `W_AllTableResults003_2.png` | The same results table at the 0.03 divergence threshold (sensitivity check). |
| `W_AllTableResultsComp2.png` | Comparison view across the threshold/parameter settings. |
| `W_DumbbellPlot003.png`, `W_DumbbellPlotComp.png` | Dumbbell plots for the 0.03 threshold and the comparison run. |

## What the analysis finds

There is **no single direction** that holds across all groups, which is itself the
finding: marine clustering is not uniformly worse than freshwater. The picture is
family-specific. A few families show significantly weaker freshwater clustering
(e.g. Cottidae and Oxudercidae among the fish, where marine clusters are cleaner), while
others run the other way. Most families do not differ significantly once thresholds and
multiple comparisons are accounted for, and the direction of several differences is
consistent across both the 0.02 and 0.03 thresholds. The dumbbell plot is the fastest way
to see which families have the largest marine/freshwater gaps.

## Repository layout

| Path | What it is |
|---|---|
| `MarineFW_TaxonDelineation.Rmd` | The full analysis: filtering, habitat classification, alignment, clustering, scoring, and plotting |
| `Data/` | Input BOLD data and saved intermediate/result R objects (`.RData`) |
| `W_*.png` | Result figures (tables and dumbbell plots) |
| `W_*TableResults*.RData` | Saved result tables |

## Data and tools

- **Data:** public COI-5P records from BOLD; marine/freshwater status from the
  World Register of Marine Species (WoRMS).
- **Key R packages:** tidyverse, Biostrings, DECIPHER, muscle, ape, phangorn, cluster,
  clValid, vegan, factoextra, sf.
- **Method parameters:** TN93 distance model, single-linkage clustering, divergence
  thresholds of 0.02 and 0.03, sequences filtered to >=500 bp and <=1% ambiguous bases.

## Reproducing

Open `MarineFW_TaxonDelineation.Rmd` in RStudio and knit, or run the chunks in order.
The saved `.RData` objects in `Data/` let you reload intermediate steps without
re-running the full pipeline (alignment and clustering on the larger groups are the
slow steps).

