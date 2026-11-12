# LIFE-seq Figure 6 analysis

[Repository overview](../README.md) · [Open notebook](LIFEseq_Figure6_Extended3_4_S11.ipynb)

Spatial transcriptomic analyses for Figure 6, Extended Data Figures 3–4 and Supplementary Figure S11 of the LIFE-seq manuscript. The notebook covers cell-class RNA contributions, injury-associated expression, spatial autocorrelation and coexpression modules.

## Files

- `LIFEseq_Figure6_Extended3_4_S11.ipynb` — analysis and visualization notebook with inline outputs and brief English/Chinese cell annotations.
- `data/` — spatial annotations, fitted model results, network/enrichment tables, reference-cell counts and the registered H&E input.

## Inputs

The main input is `merged_10_LIFEseq.h5ad`, containing **10,270 spots × 38,618 genes** from ten LIFE-seq captures across D0–D3. Raw counts are read from `X`; sample identifiers and native `col`/`row` coordinates are read from `obs`.

Set `INPUT_H5AD` in the configuration cell. Its default is `../../merged_10_LIFEseq.h5ad`, relative to the notebook directory. Keep `data/` beside the notebook. The count h5ad is provided separately from this code folder.

The SCC reference section is identified as `WSC` in the data tables. It contains 1,447 spots; the D2-1 section contains 1,736 spots. The single-cell reference-expression analysis uses the included counts for 14 candidate genes in 6,317 NC2024 reference cells, together with their full-library UMI totals.

### Included data

| File in `data/` | Contents |
|---|---|
| `spatial_metadata.csv.gz` | Spot/sample IDs, native and plotting coordinates, QC fields, six module scores and expression-cluster labels. |
| `WSC_reference_spots.csv.gz` | SCC reference coordinates, region labels, 29 RCTD weights, UMAP and registered H&E pixel positions. |
| `D2_reference_spots.csv.gz` | D2-1 coordinates, transferred regions, 29 RCTD weights, projected UMAP and C1 distances. |
| `reference_celltype_palette.csv` | Mapping of the 29 RCTD class IDs to cell-type names and colors. |
| `reference_region_palette.csv` | Reference-region labels, colors and H&E interpretations. |
| `RCTD_distance_profile_reference.csv` | Mean RNA contributions and RNA-weight totals by C1 distance band. |
| `CSIDE_results.csv.gz` | The full fitted gene–class test table, effect sizes, P values and display classifications. |
| `CSIDE_predictions.csv.gz` | Fitted expression by cell class and distance band, with convergence and eligibility fields. |
| `CSIDE_selected_genes.csv` | Gene–class pairs selected for the expression heatmap and reference dot plot. |
| `gene_filter_reference.csv.gz` | Gene detection and expression criteria, Moran availability and final inclusion flags. |
| `gene_statistics_reference.csv.gz` | Expression changes, per-capture Moran values and A/B gene classifications. |
| `representative_genes.csv` | Gene and category order for the representative Moran heatmap. |
| `gene_heatmap_order.csv.gz` | Ordered A/B-strong genes and per-capture Moran values. |
| `gene_GO_statistics.csv.gz` | A/B-gene GO statistics, overlap genes, backgrounds and representative-term flags. |
| `GO_parent_links.csv.gz` | GO parent links used to remove redundant displayed terms. |
| `spatial_color_limits.csv` | Shared feature color limits, quantiles and normalization definitions. |
| `whole_capture_expression_reference.csv` | All-spot mean log-normalized expression for four genes in ten captures. |
| `network_nodes.csv.gz` | Gene IDs, six module assignments, module membership and network UMAP coordinates. |
| `network_edges.csv` | Displayed gene pairs, TOM weights and edge-selection rules. |
| `module_loadings.csv.gz` | PC1 loadings and metaspot gene means/standard deviations used for activity projection. |
| `module_summary.csv` | Six-module sizes, hub genes, activity/Moran summaries and enrichment annotations. |
| `network_ring_reference.csv` | Regional and temporal module-activity/Moran summaries and valid-observation counts. |
| `network_ring_guide.csv` | Ring order, radii and timepoint colors for the network overview. |
| `M02_hub_nodes.csv` | The 15 M02 hubs, membership values and display ranks. |
| `M02_hub_all_pair_TOM.csv` | All 105 pairwise TOM values among the 15 M02 hubs. |
| `module_GO_statistics.csv.gz` | Complete module GO test family, overlaps, background sizes and global BH-adjusted values. |
| `NC2024_candidate_raw_counts.csv.gz` | Fourteen candidate-gene raw counts, full-library totals and metadata for 6,317 reference cells. |
| `WSC_HE.png` | Registered H&E image for the SCC reference section. |

CSV.GZ tables are read directly by pandas.

## Run

Open the notebook from this folder, select a Python kernel, and run all cells from top to bottom. Figures are displayed inline; saving the notebook retains them for viewing. Shared ten-capture spatial maps are generated once for both main and extended panels.

Main dependencies:

`anndata`, `scanpy`, `numpy`, `pandas`, `scipy`, `matplotlib`, `networkx`, `statsmodels`, `IPython`

The notebook was run with Python 3.10. Package versions are listed in its final section. R and online enrichment services are not required for this notebook: RCTD, C-SIDE, reference transfer and network inference are represented by included fitted results and parameters.

## Analyses and manuscript panels

| Analysis | Manuscript panel |
|---|---|
| SCC/D2 RCTD colored dot density | Fig. 6a |
| H&E, reference regions and C1 overlay | Fig. 6b; Extended Data Fig. 4c |
| Reference UMAP, D2 projection and transferred regions | Fig. 6c–e |
| Continuous and binned distance to C1 | Fig. 6f |
| Five-class C-SIDE volcano and predicted-expression heatmap | Fig. 6g–h |
| D3–D0 expression change versus change in Moran's I | Fig. 6i |
| Hmox1, Lyz2, Spp1 and Vim spatial expression across ten captures | Fig. 6j; Extended Data Fig. 3a |
| Whole-capture mean expression of the four genes | Fig. 6k |
| Representative-gene Moran heatmap | Fig. 6l |
| Gene network with regional activity/Moran rings | Fig. 6m |
| M02 hub network and GO BP enrichment | Fig. 6n |
| Ddn/Timp2 spatial expression | Extended Data Fig. 3b |
| Full A/B-strong gene Moran heatmap and GO enrichment | Extended Data Fig. 3c–d |
| Six-class RCTD spatial maps in SCC and D2 | Extended Data Fig. 4a–b |
| RNA composition across C1 distance bands | Extended Data Fig. 4d |
| Single-cell reference expression of C-SIDE candidates | Extended Data Fig. 4e |
| M02 activity with C1 boundary and distance profile | Extended Data Fig. 4f–g |
| M02 spatial activity across ten captures | Fig. S11 |

## Key methods

- **Expression:** normalize each spot to 10,000 counts using its full library, then apply `log1p`. Figure 6k averages all spots, including zeros, within each capture. Gene maps use shared pooled 1st–99th percentile color limits.
- **Spatial autocorrelation:** calculate Moran's I from log-normalized expression using row-normalized four-neighbor graphs within each capture. The D3–D0 comparison retains 18,161 genes. A genes have log2FC ≥ 1 and ΔMoran > 0; B genes have |log2FC| < 0.25 and ΔMoran > 0. Strong highlighting additionally requires ΔMoran in the top 10% of retained genes, yielding 938 A and 136 B genes.
- **RCTD and reference transfer:** use the included 29-class RNA weights and transferred labels. Dot-density views display 15 selected classes with deterministic jitter; the dots represent relative RNA signal rather than measured cell positions or counts. The D2 C1 region contains 59 spots. Distances use the nearest C1 spot center, and proximity is `exp(-distance / 5)`. Composition bars include all 29 classes in the denominator.
- **C-SIDE:** use fitted continuous-proximity effects and predictions. BH correction is evaluated on the complete cell-class test families before technical-gene display exclusions. Significance requires q < 0.01 and |natural-log effect| ≥ 0.4. Heatmap black dots denote significant gene–class tests in the continuous model; gray cells indicate unavailable predictions.
- **Coexpression modules:** use six module assignments, PC1 loadings and the gene embedding from a signed-TOM network with power 9. Project module activity from the count matrix and standardize with equal total weight per capture. The overview includes 13,795 genes and 471 displayed edges. The M02 detail uses 15 hubs, with edges selected by maximum spanning tree plus the strongest 20% of hub-pair TOM values.
- **GO enrichment:** module enrichment uses hypergeometric tests and global BH correction across the complete six-module term family. A/B enrichment uses archived g:Profiler adjusted P values. Graph and hit-set redundancy filters select displayed terms without re-correcting the display subset.
- **Reference expression:** normalize the 14 genes using each reference cell's full-library UMI total. Dot area is the expressing-cell fraction; color is the mean expression including zeros, min–max-scaled per gene across 29 classes. Boxes indicate the selected C-SIDE gene–class pairs.

The ten LIFE-seq captures were collected from one mouse. D2 distance profiles describe variation within one section. Model fitting, embedding and region-label transfer are not rerun by this visualization notebook.

## Data conventions

`spot_id` joins spatial tables to observations; `gene` or `gene_id` joins gene tables. Native spatial distances are in grid spacings, and `HE_pixel_x`/`HE_pixel_y` are registered image pixels. `WSC` is the data identifier for the SCC reference section. RCTD weights sum to one across the 29 classes per spot; percentages are obtained by multiplying by 100. Coexpression modules `M01`–`M06` are distinct from the five temporal modules in Figure 5.

Blank numeric entries indicate unavailable or inapplicable estimates, such as undefined Moran values or failed C-SIDE predictions; they are not expression zeros. C-SIDE heatmaps use `converged`, `error_flag` and finite-value checks, while GO tables can have empty overlap-gene lists for zero-hit terms.
