# Project plan and progress

Technical Q&A assistant: how far can Qwen2.5-1.5B-Instruct be improved for Stack Exchange answers, with PEFT only, on a free 16 GB T4 (running on Kaggle)?

**Last updated:** 2026-11-19

**How to read this file**
- **Status:** `done`, `in progress`, `todo`.
- **Estimate** is a planning guess made before the work, not a measurement. One session is about 2 hours of your time.
- **Actual** stays `-` until the real time is known. We never fill it with a guess.
- When a step is finished, update its status (and Actual, if you know it) in this file.

## Phases at a glance

| # | Phase | Status | Estimate |
|---|---|---|---|
| 0 | Foundations and data pipeline | in progress | 6-8 sessions |
| 1 | Baseline and instruction tuning (E0, E1, E2) | todo | 6-9 sessions |
| 2 | Ablations (E3) | todo | 3-4 sessions |
| 3 | Preference alignment (E4 DPO, E5 reward model) | todo | 5-7 sessions |
| 4 | PPO and DPO vs PPO (E6, E7) | todo | 4-6 sessions |
| 5 | Final evaluation, error analysis, MLOps (E8, W&B, CI, evaluation gate) | todo | 5-7 sessions |
| 6 | Serving, demo, README, interview prep | todo | 4-5 sessions |
| | **Total (sum of the phase estimates)** | | **about 33-46 sessions (roughly 65-90 hours of your time)** |

GPU time is not estimated yet. It will be measured after the first training run (E1), then this file gets a real budget.

---

## Phase 0: Foundations and data pipeline

| Step | What | Status | Estimate | Actual |
|---|---|---|---|---|
| L1 | Lesson 1: data origin, vote bias, contamination, licence, LoRA vs freezing | done | - | - |
| V | Verify datasets, library versions, TRL (no PPO; DPO with adapter reference) | done | 1 session | - |
| L2 | Lesson 2: cleaning and leakage-free splitting | done | 1 session | - |
| S | Notebook setup on Kaggle (GPU, internet, save folder) | done | 30 min | - |
| 1 | Look at the raw data | done | 15 min | - |
| 2 | Find the site of each question | done | 15 min | - |
| 3 | Count tokens in an answer | done | 20 min | - |
| 4 | Filter by length | done | 20 min | - |
| 5 | Filter out other bad rows (empty, identical, link-only) | done | 20 min | - |
| 6 | Keep one pair per question | done | 20 min | - |
| 7 | Keep only technical sites (10-site list) | done | 20 min | - |
| 8 | Remove near-duplicate questions | todo | 40 min | - |
| 9 | Split into sft / pref / val / test by question | todo | 30 min | - |
| 10 | Check for leakage | todo | 20 min | - |
| 11 | Run on the full stream and save the files (with real counts) | todo | 40 min | - |
| 12 | Move the notebook logic into `src/`, a config and a script; add tests | todo | 1-2 sessions | - |
| 13 | Version pins in `requirements.txt` (after training libraries are installed) | todo | 20 min | - |

Note: the steps above were each run on only 5 rows. Nothing has been run on the full stream yet.

## Phase 1: Baseline and instruction tuning

| Step | What | Status | Estimate | Actual |
|---|---|---|---|---|
| E0 | Evaluation basics, then baseline of the untrained model on the test set | todo | 2-3 sessions | - |
| L | Lesson: instruction tuning, chat template, loss masking | todo | 1 session | - |
| L | Lesson: LoRA maths, fp16 on a T4, checkpoint and resume | todo | 1 session | - |
| E1 | Instruction tuning with LoRA | todo | 1-2 sessions | - |
| L | Lesson: quantisation and QLoRA | todo | 1 session | - |
| E2 | Instruction tuning with QLoRA, compared with E1 | todo | 1-2 sessions | - |

## Phase 2: Ablations (E3)

| Step | What | Status | Estimate | Actual |
|---|---|---|---|---|
| E3a | LoRA rank 4 / 8 / 16 | todo | 1 session | - |
| E3b | Data size 1k / 3k / 5k | todo | 1 session | - |
| E3c | Epochs 1-3 | todo | 1 session | - |
| E3d | DoRA and IA3 | todo | 1 session | - |

## Phase 3: Preference alignment

| Step | What | Status | Estimate | Actual |
|---|---|---|---|---|
| L | Lesson: RLHF pipeline, Bradley-Terry, DPO derivation | todo | 1 session | - |
| E4 | DPO from the SFT adapter, 2-3 beta values | todo | 2-3 sessions | - |
| L | Lesson: reward models | todo | 1 session | - |
| E5 | Reward model on a 150-200M encoder | todo | 1-2 sessions | - |

## Phase 4: PPO

| Step | What | Status | Estimate | Actual |
|---|---|---|---|---|
| L | Lesson: policy gradients, advantage, PPO objective, KL penalty | todo | 1-2 sessions | - |
| E6 | Miniature PPO / RLHF with a custom loop (0.5B fallback if needed) | todo | 2-3 sessions | - |
| E7 | DPO vs PPO comparison | todo | 1 session | - |

## Phase 5: Evaluation and MLOps

| Step | What | Status | Estimate | Actual |
|---|---|---|---|---|
| E8 | Final evaluation, error analysis, error taxonomy | todo | 2-3 sessions | - |
| M | Weights & Biases tracking | todo | 1 session | - |
| M | Hugging Face Hub: dataset and adapter cards, versioning | todo | 1 session | - |
| M | GitHub Actions (tests and linting on CPU) and the evaluation gate | todo | 1-2 sessions | - |

## Phase 6: Serving and demo

| Step | What | Status | Estimate | Actual |
|---|---|---|---|---|
| S | FastAPI in Docker with per-request logging | todo | 1-2 sessions | - |
| D | Gradio demo on Hugging Face Spaces (swap base / SFT / DPO / PPO adapters) | todo | 1-2 sessions | - |
| R | README with real results, limits, licence note | todo | 1 session | - |
| I | Interview question bank across all topics | todo | 1 session | - |

---

## Decisions and changes so far
- Base model: Qwen2.5-1.5B-Instruct. Fallback 0.5B only if PPO is too slow or unstable.
- PEFT only. Full fine-tuning is out of scope because of the T4 limit.
- Running on **Kaggle** (T4, pinned to one GPU) instead of Colab, since Colab's free runtime gave trouble.
- Learning happens in one notebook, `notebooks/master_notebook.ipynb`. Code moves into modules, configs and scripts only after each step is understood.
- Working rule: every number comes from a real run output. Missing numbers stay empty.
- Check questions are asked in the chat, not written in the notebook.
- Verified (2026-11-18): TRL 1.18 has no PPO trainer, so PPO is a custom loop. DPO accepts a trained adapter as the model and creates its own frozen "ref" copy.
- Allowed sites (Step 7): stackoverflow, superuser, serverfault, askubuntu, unix, softwareengineering, dba, datascience, stats, security. A setting that can be changed.
- Proposed, not yet built: four question-disjoint splits (sft, pref, val, test), one pair kept per question.
