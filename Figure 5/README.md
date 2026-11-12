# LIFE-seq Figure 5 analysis

[Repository overview](../README.md) · [Open notebook](LIFEseq_Figure5_S8c_S9_S10.ipynb)

Transcriptomic analyses for Figure 5b–j and Supplementary Figures S8c, S9 and S10 of the LIFE-seq manuscript, covering sample relationships, dynamic expression programs, spatial clusters and differential expression across ten longitudinal captures.

## Files

- `LIFEseq_Figure5_S8c_S9_S10.ipynb` — analysis notebook with inline figure outputs and brief English/Chinese cell annotations.
- `data/` — cluster coordinates, module and marker annotations, and fitted differential-expression/enrichment statistics.

## Input

The notebook uses `merged_10_LIFEseq.h5ad`, containing **10,270 spots × 38,618 genes** from D0, D1, D2 and D3, with 2, 2, 3 and 3 captures, respectively.

Required information includes raw counts in `X`, sample identifiers and native spatial coordinates (`obs['col']`, `obs['row']`). The correlation analysis uses the batch-specific Gene ID annotations stored in the same object.

Set `INPUT_H5AD` in the configuration cell. Its default is `../../merged_10_LIFEseq.h5ad`, relative to the notebook directory. The accompanying `data/` directory must remain beside the notebook. The count h5ad is provided separately from this code folder.

### Included tables

| File | Contents |
|---|---|
| `umap_coordinates.csv` | Spot identifiers, sample/timepoint labels, integrated UMAP coordinates and six Leiden r0.4 clusters |
| `dynamic_module_genes.csv` | Dynamic genes, five-module membership and heatmap order |
| `marker_genes.csv` | Marker genes and display order |
| `pca_reference.csv` | Reference PCA coordinates and variance percentages for component orientation |
| `S9_differential_expression.csv.gz` | Complete fitted limma-voom results for four comparisons |
| `S9_GO_BP_statistics.csv.gz` | GO BP enrichment statistics and selected display terms |

## Run

Open the notebook from this folder, select a Python kernel, and run all cells from top to bottom. Figures are displayed inline; saving the notebook retains the outputs for viewing. No external helper scripts or network requests are required.

Main dependencies:

`anndata`, `scanpy`, `numpy`, `pandas`, `scipy`, `matplotlib`, `scikit-learn`, `statsmodels`, `IPython`

The notebook was run with Python 3.10. Package versions are listed in its final section.

## Analyses and manuscript panels

| Analysis | Manuscript panel |
|---|---|
| Sample-level PCA | Fig. 5b |
| Dynamic-gene heatmap and five module trajectories | Fig. 5c–d |
| Integrated UMAP colored by timepoint, cluster and sample, shown together | Fig. 5e–f; Fig. S10a |
| Spatial cluster overview of all ten captures, arranged by day | Fig. 5g; Fig. S10b |
| Marker-gene dot plot | Fig. 5h |
| Mean cluster proportions by timepoint | Fig. 5i |
| Cluster–module enrichment | Fig. 5j |
| Ten-sample expression correlations | Fig. S8c |
| D0–D1, D1–D2, D2–D3 and D0–D3 differential expression | Fig. S9a–d |
| GO BP enrichment for genes upregulated in D1/D3 versus D0 | Fig. S9e–f |
| Cluster proportions within individual samples | Fig. S10c |

Figure 5a and Supplementary Figures S8a–b are outside this notebook's scope.

## Key methods

- **PCA:** aggregate raw counts by sample; require CPM ≥ 1 in at least two samples, exclude mt-/Rpl/Rps genes, and use the 3,000 highest-variance genes. PCA centers without variance scaling. Ellipses and arrows are visual guides.
- **Dynamic modules:** normalize each spot to 10,000 counts, apply `log1p`, and average all spots within each capture. Sample-level ANOVA with BH correction identifies 3,339 dynamic genes. Included assignments define five modules of 212, 57, 745, 1,814 and 511 genes. Trajectory shading shows the inter-gene interquartile range; points show capture means.
- **UMAP and spatial clusters:** use the included integrated embedding and Leiden r0.4 assignments. The three UMAP views share coordinates and limits. All ten spatial maps use native `col`/`row` positions and a common cluster palette; each circle diameter equals one grid spacing. Embedding and clustering are not refitted.
- **Markers and composition:** dot area represents the positive-spot fraction and color represents mean expression scaled per gene. Timepoint cluster fractions average captures equally. Module enrichment subtracts each capture's background before averaging and standardizing across clusters.
- **Correlations:** calculate Pearson R on `log2(sample gene-total counts + 1)` for 22,261 Gene IDs shared across all ten samples.
- **Differential expression:** use the included filterByExpr → TMM → limma-voom results for 20,011 genes per contrast. Thresholds are BH-adjusted P < 0.05 and |log2FC| > 1. The notebook calculates classifications from fitted statistics; it does not refit the R model.
- **GO enrichment:** S9e uses local GO BP results and S9f uses precomputed g:Profiler results. Each panel retains its analysis-specific background, query size and adjusted P values. Multiple-testing correction uses the complete tested term family, not the displayed subset.

The ten LIFE-seq captures were collected from one mouse; capture-level summaries do not represent ten independent biological animals.

## Data conventions

Rows in `umap_coordinates.csv` are keyed by `obs_name` and aligned with the count matrix before plotting. `WJD0-1`–`WJD3-3` identify captures; `D0`–`D3` identify days. The five temporal `Module 1`–`Module 5` groups are distinct from the six coexpression modules in Figure 6. In S9 tables, `local` and `online` identify the two supplied enrichment result sets; no network request is made during execution.
