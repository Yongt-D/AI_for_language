# Metadata consistency audit

Audit date: 2026-09-10

Scope: mechanical consistency only. No model was retrained and no scientific result was recomputed for this audit.

## Corrected final-analysis crosswalk

| Model | Canonical ID | Exact revision | Architecture | Blocks | Hidden | Tokenizer | Locked EN/ZH/KO | Seeds | Final mapped files |
|---|---|---|---|---:|---:|---|---|---|---:|
| Qwen2.5-1.5B | `Qwen/Qwen2.5-1.5B` | `8faed761d45a263340a0528343f099c05c9a4323` | `Qwen2ForCausalLM` | 28 | 1536 | `Qwen2Tokenizer` | 27/27/19 | 0-4 | 8 |
| Llama-3.2-3B | `meta-llama/Llama-3.2-3B` | `13afe5124825b4f3751f836b40dafda64c1ed062` | `LlamaForCausalLM` | 28 | 3072 | `PreTrainedTokenizerFast` | 25/20/17 | 0-4 | 8 |
| Qwen2.5-7B | `Qwen/Qwen2.5-7B` | `d149729398750b98c0af14eb82c78cfe92750796` | `Qwen2ForCausalLM` | 28 | 3584 | `Qwen2Tokenizer` | 28/24/19 | 0-4 | 8 |
| Gemma-4-E2B | `google/gemma-4-E2B` | `d29ff6b45f081a49ee2733a859c9c9c2d95d1a6f` | `Gemma4ForConditionalGeneration` | 35 | 1536 | `GemmaTokenizer` | 24/24/24 | 0-4 | 8 |

Layer selection used seed 0. Each probe was trained with seeds 0-4 from the fixed training pool sampled with seed 42. Each bootstrap result used 10,000 crossed seed-by-sentence resamples.

## Checks performed

- Canonical identifiers and 12-character revision prefixes agree between the manuscript, README, and manifest.
- Local `config.json` values agree with manifest architecture dimensions.
- Local tokenizer classes and complete `tokenizer.json` SHA-256 hashes agree with the manifest.
- Local model-weight files agree with the official revision's LFS SHA-256 values.
- Development-selected blocks in each locked selection JSON agree with confirmatory and transfer CSV block fields.
- Each confirmatory CSV contains 15 rows; each transfer CSV contains 60 rows; both contain exactly seeds 0-4 and the expected model identifier.
- All 32 manifest-mapped final result files exist.
- All 13 local Universal Dependencies CoNLL-U files match the official **UD release 2.18** tag. The earlier label "UD v2.1" confused the 2017 UD citation with the data release and has been corrected.
- The three prepared-data variants used in the corrected final analysis record the same treebank flow counts and the same 991-sentence four-way PUD intersection.
- The corrected runtime is recorded in `environment/runtime.txt`.

## Corrections made

1. Standardised the Gemma Hub identifier as `google/gemma-4-E2B` (uppercase `E2B`).
2. Corrected the dataset label from UD v2.1 to Universal Dependencies release 2.18.
3. Replaced one stale machine-specific tokenizer path in `data/prepared/flow_counts.json` with a repository-relative path.
4. Restricted the manuscript model table to the four checkpoints represented by corrected final results.
5. Added an explicit manifest, deterministic release checksums, and an executable offline consistency audit.

## Re-run the audit

```bash
python code/eval/audit_metadata_consistency.py
```

Use `--verify-large-model-files` to additionally hash the local model weights. That optional check can take several minutes but still performs no scientific analysis.
