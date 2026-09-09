# Dependency-distance representations and word-order transfer across four languages in decoder language models

This repository accompanies the manuscript:

> **Dependency-distance representations and word-order transfer across four languages in decoder language models**

The study examines whether multilingual decoder-only language models encode a language-general dependency-tree geometry or instead exhibit surface- and relation-conditioned distance representations. The experiments cover **English, Mandarin Chinese, Korean, and Japanese** and combine structural probing, surface controls, zero-shot cross-lingual transfer, and layer-wise behavioural analyses.

## Overview

The project separates three questions that are often conflated in syntactic probing:

1. **Distance extractability** — can a probe recover the ranking of dependency-tree path distances from hidden representations?
2. **Tree recovery** — do the predicted distances reconstruct the gold dependency tree under minimum-spanning-tree decoding?
3. **Cross-lingual transfer** — how does syntactic-distance information transfer across languages with different word-order and orthographic properties?

A central goal is to distinguish information that is genuinely extractable from model representations from effects that can be explained by surface word distance, sentence length, probe capacity, or evaluation choices.

## Main findings

Across the three primary checkpoints:

- Nonlinear probes outperform the linear word-distance baseline in dependency-distance rank correlation for **English** and **Korean** in all three models.
- **Chinese** also shows positive rank effects, although one full-test contrast is inconclusive after multiplicity correction; exact sentence-length matching yields positive effects in all three primary checkpoints.
- Rank correlation and dependency-tree recovery dissociate: strong distance ranking does **not** imply strong UUAS after minimum-spanning-tree decoding.
- In zero-shot transfer to sentence-aligned **Japanese PUD**, Korean-source probes outperform Chinese-source probes in all three primary checkpoints, with statistically significant advantages in both Qwen models.
- A separately analysed **Gemma-4-E2B** extension replicates the rank–tree dissociation but not the aggregate Korean–Chinese transfer contrast.
- Relation-conditioned analyses indicate that cross-lingual transfer is heterogeneous across Universal Dependencies relations, supporting a relation-conditioned account rather than a single SOV/SVO explanation.

The manuscript therefore treats structural-probe performance as evidence that information is **extractable under a specified readout**, not as proof that a language model explicitly represents or computes a complete symbolic dependency tree.

## Models

### Primary confirmatory cohort

| Model | Model identifier | Blocks | Hidden size | Status |
|---|---|---:|---:|---|
| Qwen2.5-1.5B | `Qwen/Qwen2.5-1.5B` | 28 | 1536 | Primary |
| Llama-3.2-3B | `meta-llama/Llama-3.2-3B` | 28 | 3072 | Primary |
| Qwen2.5-7B | `Qwen/Qwen2.5-7B` | 28 | 3584 | Primary |

### Separately analysed architectural extension

| Model | Model identifier | Blocks | Hidden size | Status |
|---|---|---:|---:|---|
| Gemma-4-E2B | `google/gemma-4-E2B` | 35 | 1536 | Architectural robustness extension |

Other checkpoints may be present in the repository for descriptive or exploratory analyses, but they are not mixed into the corrected primary inferential family.

## Datasets

### Dependency treebanks

The study uses **Universal Dependencies v2.1**.

In-language probing:

- English: **UD English EWT**
- Mandarin Chinese: **UD Chinese GSD**
- Korean: **UD Korean KAIST**

Cross-lingual transfer:

- English PUD
- Chinese PUD
- Korean PUD
- Japanese PUD

For cross-lingual evaluation, the four PUD treebanks are joined by global sentence identifiers, yielding a four-way sentence-aligned intersection before sampling.

### Behavioural benchmarks

Layer-wise behavioural analyses use:

- **BLiMP** for English
- **ZhoBLiMP** for Chinese
- **CLiMP** for Chinese

## Probe design

The main learned probe is a symmetric nonlinear readout over pairwise hidden-state differences:

\[
z_{ij}^{(l)} =
\frac{|h_i^{(l)} - h_j^{(l)}| - \mu_\Delta^{(l)}}{\sigma_\Delta^{(l)}}
\]

Feature standardisation statistics are estimated on training data only and then frozen.

The nonlinear probe is compared with:

- a capacity-controlled structural probe;
- the representation-free linear-order baseline \(|i-j|\);
- a matched-capacity probe trained on permuted labels as a sanity check;
- a gold-depth diagnostic, reported separately because it uses gold-tree information.

Predicted dependency distances are constrained to be symmetric and non-negative.

## Evaluation

### Primary metrics

- **Sentence-level Spearman correlation (\(\rho\))** for dependency-distance ranking
- **Undirected unlabeled attachment score (UUAS)** after minimum-spanning-tree decoding

The two metrics are interpreted separately.

### Layer selection

Evaluation layers are selected using **development data only** and are locked before test-set evaluation.

### Sentence-length controls

Sentences are restricted to:

\[
2 \le N \le 60
\]

Length sensitivity is examined using prespecified bins and an exact length-matched analysis over the shared 10–30 word support.

### Statistical inference

Primary checkpoints are evaluated across **five random seeds (0–4)**.

Uncertainty is estimated using **10,000 crossed seed-by-sentence paired bootstrap resamples**. The analysis reports:

- paired effect estimates;
- 95% percentile confidence intervals;
- two-sided p values with finite-resample correction;
- Holm-adjusted p values for the prespecified primary family.

## Reproducibility package

The release is intended to contain the materials needed to reproduce the reported analyses, including:

- environment specifications;
- model checkpoint identifiers;
- dataset configurations;
- random seeds;
- processed sentence identifiers;
- per-sentence predictions;
- locked analysis outputs;
- scripts used to regenerate manuscript tables and figures.

Paths in the analysis code should be resolved relative to the repository root.

## Repository structure

Update this section if your local directory names differ.

```text
.
├── README.md
├── environment/          # Environment / dependency specifications
├── configs/              # Dataset, model, probe, and analysis configurations
├── data/                  # Processed metadata / sentence IDs; not necessarily raw corpora
├── src/                   # Activation extraction, probing, transfer, and evaluation code
├── scripts/               # End-to-end analysis and figure/table regeneration scripts
├── results/               # Locked aggregate and per-sentence outputs
├── figures/               # Manuscript figures
└── tables/                # Manuscript tables
```

## Reproducing the analyses

The exact commands depend on the final repository layout. Before public release, replace the placeholders below with the commands used in this repository.

### 1. Clone the repository

```bash
git clone <REPOSITORY_URL>
cd <REPOSITORY_NAME>
```

### 2. Create the environment

Use the environment specification included in the repository.

```bash
# Example only — replace with the command used by this repository
<ENVIRONMENT_SETUP_COMMAND>
```

### 3. Prepare datasets

Raw datasets should be obtained from their original public sources and processed using the repository scripts/configurations.

```bash
<DATA_PREPARATION_COMMAND>
```

### 4. Extract hidden states

All language-model parameters remain frozen during activation extraction.

```bash
<ACTIVATION_EXTRACTION_COMMAND>
```

### 5. Train probes and select layers on development data

```bash
<PROBE_TRAINING_COMMAND>
```

Layer selection must be performed on development data only.

### 6. Run locked test-set evaluation

```bash
<TEST_EVALUATION_COMMAND>
```

### 7. Run cross-lingual transfer analyses

```bash
<CROSSLINGUAL_TRANSFER_COMMAND>
```

### 8. Run behavioural analyses

```bash
<BEHAVIOURAL_ANALYSIS_COMMAND>
```

### 9. Regenerate manuscript figures and tables

```bash
<FIGURE_AND_TABLE_COMMAND>
```

## Reproducibility checks

The analysis pipeline includes checks for:

- identical preprocessing transformations across training, development, and test data;
- exchange-invariant probe predictions;
- non-negative predicted distances;
- consistent transformer-block indexing;
- deterministic handling of ties and non-finite values in MST decoding;
- recovery of the gold tree from gold dependency-distance matrices with **UUAS = 1.000**;
- strict alignment of sentence/block identifiers across probing and behavioural analyses.

The manuscript reports that the revised pipeline passes all automated validation tests used in the study.

## Data availability

All source datasets used in the study are publicly available from their respective repositories.

Processed sentence identifiers and per-sentence model predictions associated with the manuscript should be included with this release.

**Before publication/submission, add permanent links or accession identifiers here where applicable.**

- Repository: `<REPOSITORY_URL>`
- Archived release / DOI: `<ZENODO_OR_OTHER_ARCHIVE_DOI>`
- Universal Dependencies: https://universaldependencies.org/

## Code availability

The reproducible codebase, configuration files, and figure-generation scripts associated with the manuscript are provided in this repository.

Please cite the archived release when using the code after a DOI has been assigned.

## Use of generative AI

An AI-assisted coding and writing tool was used during manuscript revision for code auditing, figure prototyping, and language editing. All generated material was reviewed by the author, who remains responsible for the analysis, claims, citations, and final manuscript. AI-generated content was not treated as experimental data or scientific evidence.

## Citation

A formal citation will be added after publication. Until then, please cite the manuscript and/or archived repository release.

```bibtex
@article{<CITATION_KEY>,
  title   = {Dependency-distance representations and word-order transfer across four languages in decoder language models},
  author  = {<AUTHOR_NAME>},
  journal = {Scientific Reports},
  year    = {<YEAR>},
  doi     = {<DOI>}
}
```

## License

Code license: `<CODE_LICENSE>`

Dataset licenses are determined by the original data providers. In particular, the Universal Dependencies treebanks used in this study retain their respective upstream licenses.

## Contact

For questions about the code or analyses:

**<AUTHOR_NAME>**  
<INSTITUTION>  
<EMAIL>
