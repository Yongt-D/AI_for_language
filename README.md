# Dependency-distance representations and word-order transfer across four languages

Code and locked analysis outputs for the manuscript:

> *Dependency-distance representations and word-order transfer across four languages in decoder language models*

This study asks whether dependency-distance information in decoder-only language models behaves like a language-general tree geometry or remains conditioned by surface distance, word order, and dependency relation. It covers English, Mandarin Chinese, Korean, and Japanese.

## Study design

The analysis distinguishes three questions that should not be conflated:

1. **Distance extractability:** can a probe recover the rank ordering of dependency-tree path distances?
2. **Tree recovery:** do predicted distances reconstruct the gold dependency tree under minimum-spanning-tree decoding?
3. **Cross-lingual transfer:** how does the extracted distance information transfer across languages with different ordering profiles and writing systems?

The corrected confirmatory cohort contains three checkpoints. Gemma-4-E2B was run under the same corrected protocol as a separately analysed architectural extension; it is not included in the primary multiplicity family.

## Main findings

- Nonlinear probes outperform the representation-free linear-distance baseline in rank correlation for English and Korean in all three primary checkpoints.
- Rank correlation and dependency-tree recovery dissociate: better distance ranking does not guarantee better UUAS after minimum-spanning-tree decoding.
- Korean-source probes transfer better than Chinese-source probes to sentence-aligned Japanese PUD in all three primary checkpoints, significantly so in both Qwen models.
- The Gemma extension replicates the rank-tree dissociation but not the aggregate Korean-Chinese transfer contrast.
- Transfer differences vary substantially across Universal Dependencies relations, supporting a relation-conditioned account rather than a single SOV/SVO explanation.

These results establish extractability under a specified readout. They do not show that a model explicitly stores or causally computes a complete symbolic dependency tree.

## Models and locked analysis metadata

| Cohort | Checkpoint | Canonical Hugging Face identifier | Revision | Blocks | Hidden size | Locked block (EN/ZH/KO) |
|---|---|---|---|---:|---:|---|
| Primary | Qwen2.5-1.5B | `Qwen/Qwen2.5-1.5B` | `8faed761d45a` | 28 | 1536 | 27 / 27 / 19 |
| Primary | Llama-3.2-3B | `meta-llama/Llama-3.2-3B` | `13afe5124825` | 28 | 3072 | 25 / 20 / 17 |
| Primary | Qwen2.5-7B | `Qwen/Qwen2.5-7B` | `d14972939875` | 28 | 3584 | 28 / 24 / 19 |
| Architectural extension | Gemma-4-E2B | `google/gemma-4-E2B` | `d29ff6b45f08` | 35 | 1536 | 24 / 24 / 24 |

The full revisions, tokenizer classes and SHA-256 hashes, vocabulary sizes, model-weight hashes, data directories, seeds, locked layers, and result-file mappings are recorded in [`configs/model_result_manifest.csv`](configs/model_result_manifest.csv). Model weights are not redistributed.

## Data

The dependency data are from **Universal Dependencies release 2.18**:

- in-language probing: English EWT, Chinese GSD, and Korean KAIST;
- cross-lingual transfer: English, Chinese, Japanese, and Korean PUD.

All sentences contain 2-60 syntactic words. The four PUD treebanks are joined by global sentence identifier before sampling, producing an exact 991-sentence intersection. Confirmatory evaluation uses the same locked sentences for every seed, probe, and comparator.

Behavioural analyses use BLiMP, ZhoBLiMP, and CLiMP. Third-party datasets are not redistributed; obtain them from their original repositories under their upstream licenses. Processed identifiers, flow counts, and checksums in this release document the exact inputs used.

## Corrected protocol

- Model parameters are frozen during activation extraction.
- Models are evaluated in bfloat16; extracted word-level states are stored as float16 arrays.
- A syntactic word is represented by its final subword token.
- Transformer blocks are indexed from 1 to L; the embedding state is indexed separately as 0.
- Layer selection uses development data only and seed 0, after which the selected block is locked.
- Probe training uses a fixed 3,000-sentence pool sampled with seed 42.
- Confirmatory probe seeds are 0, 1, 2, 3, and 4.
- Uncertainty is estimated with 10,000 crossed seed-by-sentence paired bootstrap resamples.
- The primary metrics are macro-averaged sentence-level Spearman correlation and sentence-level UUAS.

## Repository layout

```text
.
|-- README.md
|-- code/
|   |-- data/                         # UD preparation and PUD alignment
|   |-- probes/                       # probing, transfer, and bootstrap code
|   |-- eval/                         # audits and table/figure generation
|   `-- tests/                        # pipeline validation tests
|-- configs/
|   |-- model_result_manifest.csv     # paper-config-result crosswalk
|   `-- artifact_checksums.sha256     # source-data and locked-result hashes
|-- environment/
|   `-- runtime.txt                   # corrected-rerun software versions
|-- data/
|   |-- ud/                           # obtain upstream; not redistributed
|   `-- prepared*/                    # processed metadata and sentence IDs
|-- results/
|   `-- rerun_3090/                   # locked corrected-protocol outputs
`-- paper_SR/                         # manuscript source and generated figures
```

## Verify the released metadata

The audit below is mechanical: it does not retrain a probe or recompute a scientific result. It verifies the chain from canonical model identifier to architecture, tokenizer, dataset release, locked layer, seeds, and final result files.

```bash
python code/eval/audit_metadata_consistency.py
```

To additionally hash locally available model files (slow and not required for ordinary result verification):

```bash
python code/eval/audit_metadata_consistency.py --verify-large-model-files
```

Run the pipeline validation tests and regenerate manuscript displays from the locked outputs with:

```bash
python -m pytest code/tests -q -p no:cacheprovider
python code/eval/generate_all_figures_and_tables.py
```

The corrected reruns used Python 3.10.20, PyTorch 2.5.1, CUDA 12.1, Transformers 5.9.0, NumPy 1.26.4, and SciPy 1.15.3. See [`environment/runtime.txt`](environment/runtime.txt).

## Full GPU rerun

Full reruns require the upstream model weights, prepared datasets, and a CUDA GPU. Paths are resolved from the repository root. The scripts below encode the locked sampling and inference protocol:

```bash
# Primary cohort (run one model at a time)
bash code/queue_remote_3090.sh Qwen2.5-1.5B
bash code/queue_remote_3090.sh Llama-3.2-3B
bash code/queue_remote_3090.sh Qwen2.5-7B

# Separately analysed architectural extension
bash code/queue_gemma_unified_3090.sh
```

Model directories are intentionally excluded from the repository. Before running, place compatible snapshots at the paths expected by the queue scripts or edit those paths for your system.

## Reproducibility status

- All 25 automated pipeline tests pass.
- Gold dependency-distance matrices recover the gold tree with UUAS = 1.000.
- The four PUD languages are aligned to the same 991 sentence identifiers before sampling.
- The metadata audit passes for all four corrected-protocol checkpoints and all mapped locked result files.
- SHA-256 checksums are provided for exact source-data files and locked analysis outputs.

## Authors

- Yunru Chen - School of Foreign Languages, Civil Aviation Flight University of China
- Pan Jiang - School of Economics and Management, Southwest University of Science and Technology
- Yongtao Deng (corresponding author) - School of Computer Science and Technology, Chongqing University of Posts and Telecommunications
- Yu Li - School of Foreign Languages, Mianyang Teachers' College
- Qiru Zi - School of Media and Communications, Mianyang Teachers' College

Correspondence: [d240201009@stu.cqupt.edu.cn](mailto:d240201009@stu.cqupt.edu.cn)

## Funding

This research was supported by the Fundamental Research Funds for the Central Universities (Grant No. 26CAFUC03063).

## Data, code, and model licenses

The repository is available at <https://github.com/Yongt-D/AI_for_language>. Dataset and model licenses remain those of their original providers. No code license has yet been selected; add a `LICENSE` file before the archived public release so that reuse terms are explicit.

## Citation

Until a journal citation or repository DOI is available, cite the manuscript as:

```bibtex
@unpublished{chen2026dependencydistance,
  title  = {Dependency-distance representations and word-order transfer across four languages in decoder language models},
  author = {Chen, Yunru and Jiang, Pan and Deng, Yongtao and Li, Yu and Zi, Qiru},
  year   = {2026},
  note   = {Manuscript submitted for publication}
}
```

## Use of generative AI

An AI-assisted coding and writing tool was used during manuscript revision for code auditing, figure prototyping, and language editing. All generated material was reviewed by the authors, who remain responsible for the analysis, claims, citations, and final manuscript. AI-generated content was not treated as experimental data or scientific evidence.
