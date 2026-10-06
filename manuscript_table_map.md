# Manuscript ↔ Notebook Traceability Map

This file maps every table and figure in the manuscript to the notebook
section (and, where applicable, exported figure file) that produced it, so
a reader can locate and re-run the exact computation behind any reported
number.

| Manuscript item | Notebook section | Notes |
|---|---|---|
| Table 1–3 (dataset, channels, tensor dimensions) | Step 1: Feature Extraction, §1.6 Verify Extracted Features | Derived from the manifest produced in §1.5 |
| Table 4 (parameter counts) | Step 3: Model Architectures | Instantiated vs. active parameter counting, see the footnote to Finding 1 |
| Table 5 (hyperparameters) | Step 0 (config) / Step 4 (training functions) | |
| Table 6–7 (overall / per-subject PCC, seed 42) | Step 5.1 (Baseline), Step 5.3 (Hybrid from scratch), Step 7 (Statistical Tests) | Wilcoxon, Cohen's d, CI computed in Step 7 |
| `fig3_overall_performance.png` | Step 5 (all four experiments) + Step 7 | Baseline / FT-Baseline / Two-phase Transfer / Scratch comparison |
| `fig4_per_subject_pcc.png` | Step 7 | Per-subject bar comparison, Baseline vs. Hybrid (Scratch) |
| `fig5_delta_pcc.png` | Step 7 | Per-subject ΔPCC, supports Design Principle 3 ("weaker subjects benefit more") |
| `fig7_scatter.png` | Step 7 (additional cell: correlation of ΔPCC with baseline PCC) | r = −0.89, see Discussion |
| Table 8 (architecture vs. training protocol) | Step 5.2 (two-phase transfer), Step 5.4 (fine-tuned-baseline control) | |
| Table 9 (comprehensive ablation, seed 42) | Step 6: Ablation Study | Conv-Only, No-Pre/Post-LSTM, Capacity-Matched, GroupNorm, No-Embedding, No-Masking |
| `fig6_ablation.png` | Step 6 | |
| Table 10 (ablation across three seeds) | Additional analysis cell, appended after Step 6: **3-seed ablation replication** (`run_variant` / `store4` in the analysis cells) | Resolves R1.3; reports flattened and valid-frame PCC, mean ± SD |
| Table 11 (masking test under non-zero padding) | Additional analysis cell: **`NoisyPadDataset` masking test** (`store3`) | Resolves R1.4; mask ON vs. OFF under Gaussian-noise padding, 3 seeds |
| `fig9_seeds.png` | Step 7 / additional 3-seed cells | Baseline vs. Scratch across seeds 42/123/456 |
| Table 12 (hyperparameter sensitivity grid) | Additional analysis cell: dropout / hidden-size grid | Addresses R4.5 |
| Table 13 (anatomical channel ablation) | Additional analysis cell: subcortical channel zeroing + random-channel control | Addresses R4.3 |
| Table 14 (perceptual metrics: MCD, STOI, PESQ) | Additional analysis cells (Griffin-Lim reconstruction + `pystoi` + `pesq`) | Addresses R1.5, R4.4. Vocoder is Griffin-Lim, **not** HiFi-GAN |
| Table 15 (FLOPs, parameters, inference latency) | Additional analysis cell (`thop` profiling) | Addresses R4.2 |
| `fig10_mrresblock_detail.png` | — (standalone architecture diagram, not code-generated) | Illustrates Eq. (2) / the MR-ResBlock structure described in §2.3 |
| Padding / word-overlap sanity checks | Additional analysis cells: valid-frame PCC definition, train/test word-overlap check | Referenced in Findings 3–5 and the Limitations section |

## Regenerating a specific number

1. Run Step 0–4 once (config, imports, feature extraction, dataset, models,
   training functions).
2. Run the specific experiment cell(s) listed above for the item you want
   to reproduce. Most later analysis cells reuse models already trained
   earlier in the same kernel session (e.g., `baseline`, `scratch`) rather
   than retraining from scratch — check each cell's leading comment for
   what it expects to already be in memory.
3. Cells that train multiple seeds/variants (Table 10, Table 11) are
   resumable: they cache completed results to a local pickle file
   (`check3_mask.pkl`, `check4_ablations.pkl`) and skip anything already
   computed, so an interrupted run can simply be re-executed.
