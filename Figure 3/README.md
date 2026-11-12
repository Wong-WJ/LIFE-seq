# LIFE-seq Figure 3

[Repository overview](../README.md) · [Open notebook](LIFEseq_Figure3.ipynb)

Analysis and plotting code for **Figure 3h, j–k and m–n**. The collection covers anatomical annotation, cell-type deconvolution, spatial expression profiles and gene-level reproducibility. Other Figure 3 panels are outside this notebook.

## Files

- `LIFEseq_Figure3.ipynb` — panel-ordered workflow with brief English/Chinese cell annotations.
- `data/spatial_expression.csv.gz` — native coordinates, sample labels and raw Neurod6/Hpca/Fibcd1 counts for 7,545 spots.
- `data/centerline_control_points.csv` — the ordered control points used for the overview and distance-analysis curves.
- `data/panel_sources.csv` — panel-to-source-cell index; cell numbering starts at one.
- `data/input_provenance.csv` — source identities, roles and SHA-256 checksums.

## Run

Open the notebook from this directory and run cells in order. By default, only **Figure 3j–k** is plotted, using the included small tables. Their saved inline outputs can also be viewed without running Python.

Figure 3h and 3m–n are provided as code workflows. Set their input paths and enable `RUN_FIGURE_H` or `RUN_CORRELATIONS` when the corresponding objects are available. Reference-model fitting and spatial mapping have separate switches, `RUN_REFERENCE_MODEL` and `RUN_SPATIAL_MODEL`, both disabled by default. Existing reference signatures and spatial posterior objects can be used without retraining.

All paths are relative to this directory. Optional image export is enabled by setting the `LIFESEQ_FIGURE_OUTPUT` environment variable; otherwise plots are displayed inline without creating separate figure files.

## Input layout

```text
data/                              # included compact profile data
raw_data/                          # optional external inputs
  magicvs.h5ad                      # optional verification of the profile table
  spatial_annotated.h5ad             # Figure 3h: original clusters or anatomical labels
  spatial_counts.h5ad                # optional cell2location spatial-model input
  spatial_posterior.h5ad             # Figure 3h: fitted cell2location posterior
  reference/
    cell_annotation.csv
    <sample>_filtered_feature_bc_matrix.h5
    inf_aver.csv                    # precomputed signatures may be supplied directly
  correlation/
    Tissue.h5ad
    Replica1.h5ad
    Replica2.h5ad
```

The external filenames above are configurable input names. They are not additional datasets included with this code collection. Training, when enabled, writes new models under `model_output/`.

## Figure 3h

The anatomical map uses the original 13-cluster annotation and palette. It requires `obsm["spatial"]` and the saved `obs["leiden"]` labels, either original IDs 0–12 or their anatomical names. The notebook preserves these labels without reclustering.

The cell2location workflow retains the original reference filters and model settings: exclusion of `Unk_1`, `Unk_2`, `LowQ_1` and `LowQ_2`; a minimum of 10 reference cells per gene; reference-expression filtering at 5 cells, 0.03 cell fraction and 1.12 mean nonzero expression; 250 reference-training epochs; and 1,000 posterior samples. Reference labels are `annotation_1`, with `sample` as the batch field. The original reference dataset is identified as E-MTAB-11115.

Spatial mapping uses raw counts, matching gene IDs, `N_cells_per_location=25`, `detection_alpha=20`, `detection_mean_per_sample=False`, 8,000 training epochs and 1,000 posterior samples. Spatial inputs require `var["Gene_id"]`, `obs["sample"]` and `obsm["spatial"]`.

Visualization reads `obsm["q05_cell_abundance_w_sf"]`, applies log1p, divides by the global maximum across all cell types and spots, and filters normalized intensities below 0.25. The original 24 populations are displayed. Up to 30 dots per spot are allocated in proportion to the retained signal, with a fixed random seed for display positions. These dots encode inferred abundance within spots; they are not measured single-cell coordinates.

## Figure 3j–k

The supplied source table is extracted from the raw three-capture object: MAGIC-seq (2,545 spots), Replica 1 (2,500) and Replica 2 (2,500). Raw gene counts and coordinates are preserved. Set `VERIFY_PROFILE_H5AD=True` to compare them directly with the optional original matrix.

Panel j uses the original cubic overview curves and quadratic distance curves, each sampled at 500 positions. Their control points are stored separately, so each curve is reconstructed from its own coordinates. Spots enter the distance band when they are within four spot units of the centerline and within 1.5 spot units of tangential displacement at its endpoints. Hpca overview colors use the pooled 1st–99th percentiles; distance colors span 0–4 spots.

Panel k shows the three genes in 0.5-spot bins. Empty bins are zero-filled, means are smoothed with a Gaussian kernel of sigma 1 and quadratically interpolated at 1,000 positions, with negative interpolated values clipped to zero. Distances use the original 100-µm spot pitch. Both Replica captures are plotted separately, in the same color, without pooling. Points are binned raw-count means and lines are smoothed profiles.

## Figure 3m–n

The correlation input objects must retain `var["Gene_id"]` and `var["total_counts"]`. Each comparison independently joins common unique gene IDs, then applies `log2(total_counts + 1)`. Pearson R is calculated from all matched genes, and Gaussian kernel density uses bandwidth 0.5. Counts are not converted to CPM or average expression. Panel m places Replica 1 on x and Tissue on y; panel n places Replica 2 on x and Replica 1 on y.

## Dependencies

Default profile workflow: `numpy`, `pandas`, `scipy`, `matplotlib`, `IPython`, and a Jupyter Python kernel. Optional input verification and correlation loading require `scanpy`/`anndata`; correlation coloring also requires `scikit-learn`. Model fitting additionally requires `cell2location`, `torch` and their compatible dependencies. The reference-training call retains the original GPU setting.
