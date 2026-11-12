# LIFE-seq manuscript analysis code

Analysis notebooks and figure source data for the LIFE-seq manuscript. The notebooks cover spatial annotation, Section–Replica comparisons, longitudinal transcriptomic changes, and injury-associated spatial and coexpression analyses.

Each figure folder contains an annotated notebook, a figure guide and a `data/` directory. Notebook explanations are in English and Chinese, and figures are embedded for browsing.

## Repository contents

| Folder | Manuscript panels | Main analyses |
|---|---|---|
| [Figure 3](Figure%203/README.md) | Fig. 3h, j–k, m–n | Anatomical annotation, cell2location, spatial expression profiles and sample correlations |
| [Figure 4](Figure%204/README.md) | Fig. 4g–i; Extended Data Fig. 1c–g and Fig. 2a–e | Section–Replica differential expression, marker distributions, GO enrichment, detection and spatial profiles |
| [Figure 5](Figure%205/README.md) | Fig. 5b–j; Supplementary Figs. S8c, S9 and S10 | Ten-capture PCA, temporal modules, UMAP/spatial clusters, composition and differential expression |
| [Figure 6](Figure%206/README.md) | Fig. 6a–n; Extended Data Figs. 3–4; Supplementary Fig. S11 | RCTD/C-SIDE, spatial autocorrelation, gene networks and M02 activity |

Panels shared between a main and an extended/supplementary figure are generated once. Each figure guide maps notebook sections to manuscript panels.

## Getting started

1. Clone or download this repository, retaining each notebook's adjacent `data/` folder.
2. Install the plotting dependencies in a Python environment and open the notebooks in Jupyter.
3. Run a notebook with its own figure folder as the working directory, from the first cell onward. The configuration cells define input paths and optional workflows.

Saved outputs can be viewed without running the analyses. The default execution scope is:

| Notebook | Required inputs beyond its `data/` folder | Default behavior |
|---|---|---|
| [Figure 3 notebook](Figure%203/LIFEseq_Figure3.ipynb) | None for j–k | Plots j–k; h and m–n are configurable workflows requiring external inputs |
| [Figure 4 notebook](Figure%204/LIFEseq_Figure4_Extended1_2.ipynb) | None | Plots all included panels from the supplied tables |
| [Figure 5 notebook](Figure%205/LIFEseq_Figure5_S8c_S9_S10.ipynb) | `merged_10_LIFEseq.h5ad` | Computes count-based summaries and plots the included panels |
| [Figure 6 notebook](Figure%206/LIFEseq_Figure6_Extended3_4_S11.ipynb) | `merged_10_LIFEseq.h5ad` | Computes expression, spatial statistics and module activity and plots the included panels |

Figure 3 model fitting is disabled by default. Figure 4 includes optional AnnData extraction and comparison steps. See the corresponding guide before enabling these sections.

## Data layout

The compact CSV/CSV.GZ tables and the reference H&E image are included. Large AnnData matrices are supplied separately. Figure 5 and Figure 6 share the same raw-count object, containing **10,270 spots × 38,618 genes** from ten captures across D0–D3.

The default relative paths expect this layout; the repository directory may have any name:

```text
project/
├── merged_10_LIFEseq.h5ad
└── LIFE-seq-code/                 # repository root
    ├── README.md
    ├── Figure 3/
    │   ├── LIFEseq_Figure3.ipynb
    │   ├── README.md
    │   └── data/
    ├── Figure 4/
    ├── Figure 5/
    └── Figure 6/
```

In Figure 5 and Figure 6, `INPUT_H5AD` defaults to `../../merged_10_LIFEseq.h5ad`, relative to the notebook folder. Change this value or set `LIFESEQ_INPUT_H5AD` to use another location. The notebooks check the matrix identity and align spot/gene identifiers before analysis. The expected file SHA-256 is:

```text
67f4eebaefbec323716fdb1be15184e10d548cc4c366391960f9df953acc99e9
```

Figure 3 and Figure 4 describe their optional `raw_data/` layouts in their own guides. Their `input_provenance.csv` files identify source objects and checksums; `panel_sources.csv` records the source-analysis cells. These indices are provenance records, not additional runtime inputs to download.

The included tables contain both measurements and fitted analysis results. Figure 5 uses saved UMAP/Leiden assignments, module membership and limma-voom/GO statistics. Figure 6 uses fitted RCTD/C-SIDE outputs, transferred region labels, network coordinates and PC1 loadings. These stages are represented by their fitted results rather than retrained by the plotting notebooks. Count-based normalization, summary statistics and spatial calculations are implemented in the notebooks.

## Python environment

The default workflows were run with Python 3.10 and the versions below. They do not require R, a GPU or online enrichment queries. Figure 3's optional cell2location training requires a separate compatible `cell2location`/PyTorch environment; its reference-training call uses a GPU.

```bash
python -m pip install numpy==2.2.6 pandas==2.3.3 scipy==1.15.3 matplotlib==3.10.8 seaborn==0.13.2 scanpy==1.11.5 anndata==0.11.4 scikit-learn==1.7.2 statsmodels==0.14.6 networkx==3.4.2 matplotlib-venn==1.1.2 adjustText==1.3.0 ipython==8.31.0 ipykernel==6.29.5
python -m pip install jupyterlab
```

Use this environment as the notebook kernel. Each notebook also displays its software versions. Arial is used where available; an available sans-serif font may change text appearance without changing the analysis.

## Reading the analyses

- **Measurement scales:** raw counts, normalized expression and regressed/scaled expression are identified in each section and figure guide. Negative scaled values and missing estimates have different meanings from zero expression.
- **Experimental units:** the ten longitudinal captures in Figures 5–6 were collected from one mouse. Captures, spots and reference cells are different observational units; the figure guides specify which unit is summarized.
- **Module names:** Figure 5's five temporal modules and Figure 6's six coexpression modules are separate analyses.
- **Outputs:** default runs display figures inline. Figure 3 and Figure 4 also support optional PDF/SVG/PNG export through `LIFESEQ_FIGURE_OUTPUT`.

中文说明：本仓库按图号组织代码，每个cell均包含简要中英文说明。图3j/k和图4所列面板可直接使用随附数据运行；图5、图6需另行放置共同的h5ad。各文件夹的README提供面板对应、输入字段、分析方法和运行开关。
