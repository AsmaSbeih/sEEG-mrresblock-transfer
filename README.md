# Architectural Design Adaptation of Multi-Receptive Residual Blocks for sEEG Mel-Spectrogram Reconstruction

[![DOI](https://zenodo.org/badge/DOI/10.5281/zenodo.XXXXXXX.svg)](https://doi.org/10.5281/zenodo.XXXXXXX)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)
[![Python 3.10+](https://img.shields.io/badge/python-3.10%2B-blue)](https://www.python.org/)

Code and analysis notebook accompanying the manuscript:

> **Architectural Design Adaptation of Multi-Receptive Residual Blocks from
> Non-Invasive to Invasive Neural Mel-Spectrogram Reconstruction**
> Asma Sbaih et al., submitted to *Medical & Biological Engineering & Computing* (2026).

> Replace the DOI badge above with your actual Zenodo DOI once the repository is
> archived (see [Citing this work](#citing-this-work) below), and add the
> published paper's DOI/link here once available.

## Overview

This repository adapts the **Multi-Receptive Residual Block (MR-ResBlock)**
architecture — originally proposed for non-invasive scalp EEG mel-spectrogram
reconstruction in the NeuroTalk framework (Lee et al., 2023) — to invasive
stereo-electroencephalography (sEEG). No pretrained weights are transferred:
the architectural design pattern (parallel multi-kernel convolutions with
residual merging) is integrated into a CNN-BiLSTM backbone and trained from
scratch on sEEG data.

The pipeline:
1. Extracts high-gamma envelope features and 80-band log-mel spectrograms
   from raw sEEG (NWB format).
2. Builds per-trial train/validation/test splits across 10 subjects.
3. Trains a CNN-BiLSTM baseline and the MR-ResBlock hybrid model, under
   multiple training strategies (from-scratch, two-phase transfer,
   fine-tuned-baseline control) and multiple random seeds.
4. Runs a systematic ablation study (component placement, a capacity-matched
   single-kernel control, GroupNorm vs. BatchNorm, channel-masking tests
   under zero and non-zero padding, subject-embedding ablation).
5. Evaluates with PCC (flattened and padding-corrected valid-frame), MSE,
   MCD, STOI and PESQ (via Griffin-Lim reconstruction), FLOPs, and inference
   latency.
6. Produces all figures and tables reported in the manuscript.

## Repository structure

```
.
├── notebooks/
│   └── Complete_Pipeline.ipynb   # End-to-end pipeline (extraction → training → evaluation → figures)
├── scripts/
│   ├── run_checks_3_4.py         # three-seed ablations + noise-padding masking test (paste into the notebook)
│   └── revision_helpers.py       # per-seed tables, per-subject perceptual tests, figure regeneration, splits export
├── splits.csv                    # train/val/test assignment per trial (no local paths)
├── figures/                      # Exported figures used in the manuscript
├── docs/
│   └── manuscript_table_map.md   # Maps each manuscript table/figure to the notebook cell that produced it
├── requirements.txt
├── environment/                  # (optional) exact package versions from the environment used for the paper
├── LICENSE
├── CITATION.cff
└── README.md
```

## Data

This work uses the **SingleWordProductionDutch-iBIDS** dataset
(Verwoert et al., 2022): 10 subjects with intracranial depth-electrode
(sEEG) recordings and simultaneous audio during overt single-word
production, released under CC-BY 4.0.

- Dataset paper: Verwoert, M., Ottenhoff, M.C., Goulis, S. et al.
  *Dataset of Speech Production in intracranial Electroencephalography.*
  Sci Data 9, 434 (2022). https://doi.org/10.1038/s41597-022-01542-9
- Data access: https://osf.io/nrgx6
- Reference conversion/analysis code: https://github.com/neuralinterfacinglab/SingleWordProductionDutch

The raw NWB files are **not redistributed in this repository** (per the
original dataset's own hosting terms); download them from the OSF link
above and point `DATA_ROOT` (see [Configuration](#configuration)) at the
local copy.

## Installation

```bash
git clone https://github.com/<your-username>/<repo-name>.git
cd <repo-name>
python -m venv .venv
source .venv/bin/activate        # Windows: .venv\Scripts\activate
pip install -r requirements.txt
```

Tested with **Python 3.10–3.11**. A CUDA-capable GPU is recommended for
training but not required (the pipeline falls back to CPU automatically).

## Configuration

Before running the notebook, set the dataset and output paths. Open
`notebooks/Complete_Pipeline.ipynb`, locate the **Step 0: Configuration**
cell, and either:

- edit `DATA_ROOT` and `OUTPUT_DIR` directly, or
- set them as environment variables before launching Jupyter:

```bash
export SEEG_DATA_ROOT=/path/to/SingleWordProductionDutch-iBIDS
export SEEG_OUTPUT_DIR=/path/to/extracted_data
jupyter lab
```

The configuration cell reads these environment variables if present and
falls back to the local paths otherwise — see the cell itself for the
exact variable names.

## Running the pipeline

Open `notebooks/Complete_Pipeline.ipynb` and run cells top to bottom. The
notebook is organized into eight steps (feature extraction, dataset/loaders,
model architectures, training functions, the four main experiments,
the ablation study, statistical summary, and figure generation), each under
its own markdown header. Later analysis cells (three-seed ablation
replication, the non-zero-padding masking test, the anatomical channel
ablation, PESQ/FLOPs/latency) are appended after the main pipeline and are
labelled by the manuscript finding or reviewer point they address; see
`docs/manuscript_table_map.md`.

Feature extraction (Step 1) is the slowest stage and only needs to run
once; its outputs are cached to `OUTPUT_DIR` and reused by all later steps.

## Reproducibility notes

- All experiments reported in the manuscript use seeds `{42, 123, 456}`
  unless stated otherwise; single-seed ablation variants use seed 42 and
  this is disclosed in the manuscript's Limitations section.
- Ablation ordering, per-subject results, and the exact numeric results
  reported in each manuscript table/figure are traceable to specific
  notebook cells — see `docs/manuscript_table_map.md`.
- STOI and PESQ are computed on audio reconstructed via a **Griffin-Lim**
  vocoder (not HiFi-GAN); this is stated explicitly in the manuscript and
  reflected in the notebook's perceptual-evaluation cells.

## Citing this work

If you use this code, please cite both the manuscript and this software
release:

```bibtex
@article{sbeih2026architectural,
  title   = {Architectural Design Adaptation of Multi-Receptive Residual
             Blocks from Non-Invasive to Invasive Neural
             Mel-Spectrogram Reconstruction},
  author  = {Sbaih, Asma and Garc{\'i}a-Guti{\'e}rrez, Jorge and
             Benavides, David},
  journal = {Medical \& Biological Engineering \& Computing},
  year    = {2026},
  note    = {Under review}
}

@software{sbeih2026code,
  title   = {Code for: Architectural Design Adaptation of
             Multi-Receptive Residual Blocks for sEEG
             Mel-Spectrogram Reconstruction},
  author  = {Sbaih, Asma},
  year    = {2026},
  doi     = {10.5281/zenodo.XXXXXXX},
  url     = {https://github.com/<your-username>/<repo-name>}
}
```

Also see `CITATION.cff` (GitHub renders this automatically as a "Cite this
repository" button).

Please also cite the underlying dataset (Verwoert et al., 2022, above) and
the source architecture (Lee et al., 2023, NeuroTalk, AAAI) if you build on
this work.

## License

Code in this repository is released under the [MIT License](LICENSE). The
SingleWordProductionDutch-iBIDS dataset is separately licensed under
CC-BY 4.0 by its original authors; this repository does not redistribute
it.

## Acknowledgments

This work adapts the Multi-Receptive Residual Block design introduced by
Lee et al. (2023) in the NeuroTalk framework, and builds on the
SingleWordProductionDutch-iBIDS dataset released by Verwoert et al. (2022).
