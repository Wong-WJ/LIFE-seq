# LIFE-seq Figure 4 and Extended Data Figures 1–2

[Repository overview](../README.md) · [Open notebook](LIFEseq_Figure4_Extended1_2.ipynb)

Code and source data for **Figure 4g–i**, **Extended Data Figure 1c–g**, and **Extended Data Figure 2a–e** of the LIFE-seq manuscript. The analyses compare Section and Replica measurements of the Sham and TBI captures, including differential-expression signatures, marker expression, gene detection and spatial expression profiles.

## Files

- `LIFEseq_Figure4_Extended1_2.ipynb` — analysis and plotting notebook, arranged in manuscript order, with inline outputs and brief English/Chinese cell annotations.
- `data/` — fitted statistics, measured expression values, gene-level summaries, fixed spatial control points and source indices.

## Run

Open the notebook from this directory, select a Python kernel, and run cells in order. All panels in this collection can be reproduced from the included tables without downloading the large AnnData objects or querying an online service. The saved notebook contains its executed outputs for browsing.

Main dependencies: `numpy`, `pandas`, `scipy`, `matplotlib`, `seaborn`, `statsmodels`, `matplotlib-venn`, `adjustText`, `IPython`.

The optional AnnData verification cells additionally require `scanpy` and `anndata`. Package versions used for plotting are displayed in the notebook. To export separate PDF/SVG/PNG files, set the `LIFESEQ_FIGURE_OUTPUT` environment variable to a chosen output directory; otherwise figures are displayed inline only.

## Panels

| Panel | Analysis |
|---|---|
| Figure 4g | Upregulated and downregulated gene overlaps between methods |
| Figure 4h | Cluster 3 versus other-spot expression for the same 20 marker genes in both TBI captures |
| Figure 4i | GO biological-process enrichment comparison |
| Extended Data 1c | Raw-count gene detection versus mean abundance |
| Extended Data 1d–e | Transcript length versus expression and Replica/Section expression ratio |
| Extended Data 1f | Sham Hpca maps, fixed centerlines and six-spot distance bands |
| Extended Data 1g | Neurod6, Hpca and Fibcd1 expression across the centerline |
| Extended Data 2a–b | Section and Replica MA plots for TBI versus Sham |
| Extended Data 2c | Agreement of gene fold changes between methods |
| Extended Data 2d–e | GO cellular-component and molecular-function enrichment comparisons |

Figure 4a–f/j and Extended Data Figure 1a–b/h are outside this collection.

## Included source data

| File | Contents |
|---|---|
| `differential_expression.csv.gz` | Gene-level DE statistics, condition-wise normalized means, method and GO-query filter membership |
| `GO_enrichment.csv.gz` | The 20 precomputed GO terms per method and ontology, including exact adjusted P values and overlap genes |
| `marker_genes.csv` | Original Section-derived marker ranking and display order |
| `marker_expression.csv.gz` | The 20 marker values and frozen cluster labels for all 3,702 TBI spots |
| `gene_detection.csv.gz` | Mean raw count, detected-spot count and denominator for each gene and method |
| `transcript_length_expression.csv.gz` | Pooled linear normalized means (10,000 counts per spot) and the frozen gene-length annotation |
| `sham_spatial_expression.csv.gz` | Native coordinates and raw Neurod6/Hpca/Fibcd1 counts for all 3,902 Sham spots |
| `centerline_control_points.csv` | Ordered manual control points defining the two Sham centerlines |
| `input_provenance.csv` | Input object identities, SHA-256 checksums and analysis roles |
| `panel_sources.csv` | Panel-to-source-cell mapping; cell numbers count from one |

## Key methods

- **Differential expression:** the original spot-level Scanpy Wilcoxon tests compare TBI with Sham separately for Section and Replica, using log-normalized `raw.X`, `tie_correct=False`, and BH correction over the tested gene family. The final name filter excludes predicted numeric `Gm` identifiers while retaining other genes that start with `Gm`. The retained sets contain 1,494/1,144 upregulated and 78/203 downregulated genes for Section/Replica; 862 upregulated and 44 downregulated genes are shared. The notebook includes the calculation used to obtain the stored statistics.
- **MA plots:** abundance is the equal-condition mean after reversing `log1p` in `raw.X`. Fold changes come from the original fitted test. Triangles indicate values outside the displayed ±6 range. The concordance panel retains the original absolute-fold-change cutoff of 10 for both methods and displays axes from −8 to 8.
- **Cluster markers:** Figure 4h uses batches 3 and 4 in `TBI-4_pp.h5ad`, its frozen Leiden labels, and the original first 20 Section-derived markers. Expression comes from regressed/scaled `X`; negative values are expected. The saved marker ranking is used without reselecting genes or reclustering.
- **GO enrichment:** panels use precomputed Enrichr top-20 outputs from query sets of 1,491/1,141 upregulated genes, selecting the top ten terms per method. The GO query sets are defined by `original_filter_included` in the DE table and differ from the gene sets used in the MA/Venn panels. The displayed coordinate/color is the original `−log10(FDR + 1e−10)`, which approaches 10 for very small FDR; exact values remain in the table. CC color encodes this FDR score, not gene ratio.
- **Detection and gene length:** detection uses unprocessed integer counts, with each method's zero-mean genes omitted separately and a `1e−4` offset before log10. Logistic curves fit gene-level detection fractions. Transcript-length plots use linear expression normalized to 10,000 counts per spot, pool Section batches 1/3 and Replica batches 2/4, retain genes with mean expression > 0.05 in both methods and available positive length, and use the original LOWESS settings. The frozen lengths reflect the original external annotation lookup.
- **Spatial profiles:** manual control points define quadratic centerlines sampled at 500 positions. Spots are included within six spot units and at most 1.5 spot units of tangential displacement. Raw-count means use 0.5-spot bins; empty bins are zero-filled, smoothed with a Gaussian kernel of sigma 1, then quadratically interpolated. Distance conversion retains the original 100-µm pitch. Spatial-map expression color limits use pooled Sham 1st–99th percentiles; distance colors are fixed at 0–6 spots.

The spatial panels use fixed control points stored in `centerline_control_points.csv`.

The four captures contain 1,682 Sham-section, 2,220 Sham-replica, 1,426 TBI-section and 2,276 TBI-replica spots. Spots are the observations used in the inherited tests; the displayed P values are not tests across independent biological replicates.

## Optional original matrices

The matrices are supplied separately from this code collection. For read-only verification, place them under the following relative paths and set `VERIFY_H5AD = True` in the optional section:

```text
raw_data/
  TBI-4/TBI-4.h5ad
  TBI-4/TBI-4_pp.h5ad
  section vs/section vs.h5ad
  section vs/section vs_pp.h5ad
  replica vs/replica vs.h5ad
  replica vs/replica vs_pp.h5ad
```

The unprocessed four-capture object contains 7,604 spots × 28,434 genes. Its preprocessed counterpart retains 3,197 variables in `X` plus full-gene log-normalized values in `raw.X`. Figure 4h uses the cluster labels and scaled expression from `TBI-4_pp.h5ad`.

## Data conventions

`method` uses `section` and `replica`. In the four-capture objects, batches 1–4 correspond to Sham-section, Sham-replica, TBI-section and TBI-replica. Blank `Length` values denote genes without a length annotation and are excluded by the length-analysis filter. Negative marker-expression values are valid regressed/scaled values. `original_filter_included` records the gene-name filter used for the GO queries; it is not an additional significance threshold. Blank `shape` entries in the provenance table refer to files without an AnnData matrix shape.
