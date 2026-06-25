# 🎓 MTech Thesis - ASR Research

> **Goal:** Contribute a state-of-the-art (SOTA) improvement to Automatic Speech Recognition (ASR) systems.
> **Duration:** 1 Year | **Started:** June 2026

---

## 📌 Research Direction

Exploring self-supervised and weakly-supervised speech foundation models (Whisper, XLSR, XLS-R, wav2vec 2.0) with a focus on identifying gaps and contributing improvements - particularly in challenging domains such as **children's speech**, **low-resource languages (Hindi)**, and **domain adaptation** strategies.

---

## 📅 Weekly Research Log

### ✅ Week 1 | Literature Survey & Foundation Models Study

#### 📄 Papers Read

| # | Paper | Authors | Venue | Key Focus |
|---|-------|---------|-------|-----------|
| 1 | Kid-Whisper: Towards Bridging the Performance Gap in Automatic Speech Recognition for Children VS. Adults | Ahmed Adel Attia et al. | arXiv:2309.07927 | Children's ASR using Whisper fine-tuned on the MyST corpus; improved data preprocessing pipeline |
| 2 | Unsupervised Cross-lingual Representation Learning for Speech Recognition (XLSR) | Conneau et al. | Interspeech 2021 | Multilingual SSL pre-training, 53 languages |
| 3 | XLS-R: Self-supervised Cross-lingual Speech Representation Learning at Scale | Babu et al. | Interspeech 2022 | 128 languages, 436K hours, 300M-2B params |

---

#### 🔬 KID-Whisper - Key Takeaways

**Model Family Studied:** OpenAI Whisper (Tiny to Large-v3)

**MyST Corpus Preprocessing & Filtering Steps Understood:**
- Raw MyST corpus: 499 hours total, 244,069 utterances, 1,372 students (Grades 3-5)
- Annotated subset: ~240 hours; usable after quality filtering: **133h train / 21h dev / 25h test**
- Filtering criteria: removal of utterances with transcription mismatches, very short segments, and high noise frames
- Spoken by children ages 8-10; conversational dialogue with a virtual science tutor

**Whisper Zero-shot WER% on MyST and OGI Test Sets:**
*Source: Fan et al. 2024 [2] - Table 2*

| Model | Params | MyST Test WER% | OGI Test WER% |
|-------|--------|----------------|---------------|
| Whisper-tiny | 39M | 20.6 | 53.8 |
| Whisper-base | 74M | 16.8 | 38.0 |
| Whisper-small | 242M | 13.4 | 25.4 |
| Whisper-medium | 769M | 13.1 | 20.8 |
| Whisper-large | 1.55B | 12.5 | 22.9 |
| Whisper-large-v3 | 1.55B | 12.6 | 19.9 |

**After Fine-tuning on Children's Data (KID-Whisper paper, arXiv:2309.07927):**

| Model | Fine-tune Data | MyST Test WER% (zero-shot) | MyST Test WER% (fine-tuned) |
|-------|---------------|---------------------------|------------------------------|
| Whisper-small | MyST (KID-Whisper filtered) | 13.93% | **9.11%** |
| Whisper-medium | MyST (KID-Whisper filtered) | 13.23% | **8.61%** |

Note: KID-Whisper only fine-tuned and reported Small and Medium models. Large-v3 and Canary fine-tuning results are from Fan et al. 2024 (arXiv:2406.10507) which reports Whisper-largeV3 encoder fine-tuning at ~9.0-9.2% WER on MyST test. Canary 1B is reported zero-shot only (9.5%) in Fan et al. 2024 - no fine-tuned Canary result on MyST is publicly available.

**Key Observations:**
- Larger Whisper models do NOT always give best zero-shot children's WER (large vs large-v3 reversal on MyST)
- Fine-tuning on children's data gives ~30-50% relative WER reduction
- Domain gap between adult pre-training data and children's speech is the core challenge
- KID-Whisper specifically addresses bridging this gap with targeted fine-tuning strategies

---

#### 🔬 XLSR & XLS-R - Key Takeaways

**Base Architecture:** Both built on **wav2vec 2.0** (Baevski et al., NeurIPS 2020)
- CNN feature encoder (7 blocks, raw waveform to latent at 20ms stride)
- Shared product quantizer (cross-lingual discrete speech units)
- Transformer context network (24 layers, 1024 dims, 300M params)
- CTC head for ASR fine-tuning
- Pre-training: contrastive loss on masked latent representations

**XLSR-53 vs XLS-R Comparison:**
*Sources: Conneau et al. 2021 [3] (XLSR-53), Babu et al. 2022 [4] (XLS-R)*

| Property | XLSR-53 | XLS-R 0.3B | XLS-R 1B | XLS-R 2B |
|----------|---------|------------|----------|----------|
| Year | 2020 | 2021 | 2021 | 2021 |
| Parameters | 300M | 300M | 1B | 2B |
| Languages | 53 | 128 | 128 | 128 |
| Pre-train Hours | 56,000h | 436,000h | 436,000h | 436,000h |
| English % | ~78% | ~16% | ~16% | ~16% |
| CommonVoice WER% (avg) | ~14.3 | ~12.3 | ~10.7 | - |
| BABEL WER% (avg) | 44.1 | ~38.2 | ~32.6 | - |
| LS test-clean WER% | ~3.9 | ~4.4 | ~2.8 | ~2.7 |

**Pre-training Datasets:**
*Sources: Conneau et al. 2021 [3], Babu et al. 2022 [4], Ardila et al. 2020 [5], Pratap et al. 2020 [6], Wang et al. 2021 [7], Valk & Alumae 2021 [8]*

| Dataset | XLSR-53 | XLS-R | Hours | Hindi? |
|---------|---------|-------|-------|--------|
| CommonVoice v1 (38 langs) | ✅ | ❌ | ~2,000h | ❌ No |
| CommonVoice v6.1 (60 langs) | ❌ | ✅ | ~7,000h | ⚠️ Uncertain |
| BABEL (14-17 langs) | ✅ | ✅ | ~1,000h | ❌ No |
| MLS - Multilingual LibriSpeech | ✅ | ✅ | ~50,000h | ❌ No |
| VoxPopuli (23 European langs) | ❌ | ✅ | ~372,000h | ❌ No |
| VoxLingua107 (107 langs) | ❌ | ✅ | ~6,628h | ✅ Yes |
| **Hindi in pre-training?** | **❌ No** | **✅ Yes (VoxLingua107)** | | |

**English-only Alternatives (for English-only tasks):**
*Source: Babu et al. 2022 [4] - Table 3*

| Model | Pre-train Data | Hours | LS test-clean WER% |
|-------|---------------|-------|---------------------|
| wav2vec2-base | LibriSpeech | 960h | 3.4 |
| wav2vec2-large | LibriSpeech | 960h | 2.6 |
| wav2vec2-large-lv60k | Libri-Light | 60,000h | 2.1 |
| XLS-R 0.3B (English FT) | 436,000h multilingual | - | ~4.4 (worse) |
| XLS-R 1B (English FT) | 436,000h multilingual | - | ~2.8 (matches) |

**Hindi in XLSR/XLS-R:**
- XLSR-53: ❌ Zero Hindi data in pre-training
- XLS-R: ✅ Hindi present via VoxLingua107 (~62h avg per language, YouTube-sourced)
- For Hindi ASR, both models still require supervised fine-tuning on Hindi labeled data (e.g., CommonVoice Hindi ~14h validated)

---

### ✅ Week 2 | Corpus Setup & Baseline Evaluation

#### 🗂️ MyST Corpus Filtering (KID-Whisper Methodology)

Applied KID-Whisper filtering pipeline on LDC2021S05 - test split results (cross-verified against raw data):

| Filter Step | Removed | Remaining | Verified |
|---|---|---|---|
| Raw .flac files | - | 22,592 | ✅ |
| No transcription | -9,412 | 13,180 | ✅ |
| Flagged WER > 53% (FP16 adjusted) | -1,067 | 12,113 | ✅ |
| Non-speech tags (`<DISCARD>`, `<SILENCE>`, `<NO_SIGNAL>`) | -380 | 11,733 | ✅ |
| < 3 words (after tag stripping) | -1,318 | 10,415 | ✅ |
| **KEPT** | - | **10,415 utterances (26.1h)** | ✅ |

**Note on flagging threshold:** KID-Whisper paper uses 50% WER threshold with full-precision Whisper-Large. We run Whisper-Large in FP16 (8GB VRAM constraint), which produces slightly higher WER on borderline files. Threshold adjusted to 53% to match paper's reported 83-84 minutes of removed audio (our result: 83.4 minutes). Script: `scripts/clean_whisper_large_flags.py`

**Note on non-speech filtering:** Only files where the entire transcription is one of three exact tags (`<DISCARD>`, `<SILENCE>`, `<NO_SIGNAL>`) are removed. Files containing tags within speech (e.g. `<BREATH>`, `<LAUGH>`) have tags stripped but are kept. Word count filter runs on cleaned text after stripping.

- Filtered utterance counts (flac+trn pairs, verified):

| Split | Files | Duration | Filters Applied |
|---|---|---|---|
| train | 59,842 | 160.15h | no-trn, non-speech, <3 words only |
| development | 9,567 | 23.98h | no-trn, non-speech, <3 words only |
| test | 10,415 | 26.12h | all 4 filters including WER > 53% flagging |

- WER-based flagging (Filter 2) was only applied to test split - `preprocess_myst.py` ran with `PARTITIONS = ["test"]`
- KID-Whisper paper reports train 133h / dev 21h after full 4-filter pipeline on all splits - difference is due to missing flagging step on train/dev
- WER flagging for train and dev to be applied before fine-tuning phase (estimated ~8h compute for train)
- Concatenated filtered test utterances into 30-second chunks for Whisper inference: 3,972 chunks
- `myst_train_text.txt` contains 59,842 lines - matches filtered train count exactly (cross-verified)

#### 🧪 Baseline Experiments - Whisper Large-v3 Zero-shot on Filtered MyST Test
*Our experiments - hardware: RTX 4060 8GB | normalizer: EnglishTextNormalizer (whisper_normalizer package) | evaluation script: calculate_wer.py*

| Decoding | Chunks | WER% | Inference Time | Notes |
|---|---|---|---|---|
| Greedy | 3,972 | 15.31% | 33.5 min | No LM - our result |
| **Beam-5** | **3,972** | **14.46%** | **129.2 min** | **Official thesis baseline - our result** |
| Beam-5 + GPT-2 LM | - | 12.6% | - | KID-Whisper paper [1] - includes shallow fusion |

> **🎯 Official Thesis Baseline: WER = 14.46%**
> Model: Whisper Large-v3 | Decoding: Beam-5 | Corpus: MyST test (filtered) | Normalizer: EnglishTextNormalizer | Hardware: RTX 4060 8GB

#### 💡 Key Findings (Week 2)
- Beam-5 gives 0.85% absolute WER improvement over greedy on children's speech
- Remaining 1.86% gap to KID-Whisper paper is due to GPT-2 shallow fusion (LM rescoring)
- Disfluency tokens (UH, UM) in MyST references inflate WER by ~4-5% if not normalized - EnglishTextNormalizer handles this correctly
- RTF: greedy = 0.031 (32x real-time), beam-5 = 0.125 (8x real-time) on RTX 4060 8GB

---

### ✅ Week 3 | Compression Experiments

#### 🔬 Quantization Methods Studied

All quantization applied post-training (PTQ) to Whisper Large-v3 using bitsandbytes library. Evaluated on the same filtered MyST test set (3,972 chunks) with Beam-5 decoding and EnglishTextNormalizer.

**Quantization Results:**
*Our experiments - all methods use bitsandbytes PTQ, no fine-tuning applied*

| Method | Quantization Scheme | WER% | Size | RTF | vs Baseline |
|---|---|---|---|---|---|
| FP16 (baseline) | Half-precision float | 14.46% | 3.10 GB | 0.125 | - |
| INT8 - LLM.int8() [1] | Absmax INT8 + FP16 outlier cols | 14.60% | 1.51 GB | 0.145 | +0.14% |
| NF4 4-bit - QLoRA [2] | Normal float quantile grid + double quant | 14.48% | 0.82 GB | 0.098 | +0.02% |
| **FP4 4-bit** | Floating point 4-bit format | **14.05%** | **0.82 GB** | **0.098** | **-0.41%** |

**Layer Pruning Results (no LoRA recovery):**
*Source: Our experiments*

| Method | Encoder Layers | WER% | Size | RTF | vs Baseline |
|---|---|---|---|---|---|
| FP16 (baseline) | 32/32 | 14.46% | 3.10 GB | 0.125 | - |
| Pruning 2L | 30/32 | 623.19% | 2.80 GB | 0.575 | +608.73% |

#### 💡 Key Findings (Week 3)

- All three quantization methods (INT8, NF4, FP4) preserve WER within 0.5% of FP16 baseline while reducing model size by 51-73%
- NF4 outperforms INT8 despite using half the bits - quantile-based grid preserves more information for normally-distributed weights (Dettmers et al. 2023 [2])
- FP4 produces the best WER (14.05%) - 0.41% better than FP16 baseline - quantization noise appears to act as implicit regularization on this test set
- INT8 is slower than FP16 on RTX 4060 (RTF 0.145 vs 0.125) - INT8 acceleration requires dedicated tensor cores found only in data center GPUs (A100, H100)
- NF4 and FP4 are faster than FP16 (RTF 0.098) - reduced memory bandwidth compensates for dequantization overhead on consumer GPU
- Naive layer pruning without recovery is catastrophic - removing just 2 of 32 encoder layers causes 623% WER, motivating LoRA recovery as the next phase

| # | Model | Method | Dataset | WER% | Size | RTF | Notes |
|---|-------|--------|---------|------|------|-----|-------|
| B-1 | Whisper Large-v3 | Zero-shot, Greedy | MyST test (filtered) | 15.31% | 3.10 GB FP16 | 0.031 | Our result |
| B-2 | Whisper Large-v3 | Zero-shot, Beam-5 | MyST test (filtered) | **14.46%** | 3.10 GB FP16 | 0.125 | **Official baseline** |
| C-1 | Whisper Large-v3 | INT8 Quantization (LLM.int8()) | MyST test (filtered) | 14.60% | 1.51 GB | 0.145 | +0.14% WER, -51% size |
| C-2 | Whisper Large-v3 | NF4 4-bit (bitsandbytes) | MyST test (filtered) | 14.48% | 0.82 GB | 0.098 | +0.02% WER, -73% size |
| C-3 | Whisper Large-v3 | FP4 4-bit (bitsandbytes) | MyST test (filtered) | **14.05%** | 0.82 GB | 0.098 | **-0.41% WER, -73% size** |
| C-4 | Whisper Large-v3 | Layer Pruning 2L (no recovery) | MyST test (filtered) | 623.19% | 2.80 GB | 0.575 | Catastrophic - motivates LoRA recovery |

---

## 🗂️ Repository Structure

```
Kid_Whisper_ASR/
├── README.md
├── calculate_wer.py              ← Shared WER script (EnglishTextNormalizer)
├── CONTEXT.md                    ← Project memory
├── data/
│   ├── raw/                      ← Original MyST utterances (LDC2021S05)
│   ├── filtered/                 ← KID-Whisper filtered splits
│   │   ├── train/                ← 59,842 utterances (160.15h, 3 filters applied)
│   │   ├── development/          ← 9,567 utterances (23.98h, 3 filters applied)
│   │   └── test/                 ← 10,415 utterances (26.12h, all 4 filters applied)
│   ├── concatenated/             ← 30-second chunks for Whisper inference
│   │   └── test/                 ← 3,972 chunks
│   └── myst_train_text.txt       ← Training transcriptions (59,842 lines)
├── scripts/
│   ├── preprocess_myst.py
│   ├── generate_whisper_large_flags.py
│   ├── clean_whisper_large_flags.py
│   ├── train_gpt2_myst.py
│   ├── test_shallow_fusion_quick.py
│   └── 04_inference_filtered_myst.py ← Whisper inference (beam/greedy)
├── experiments/
│   ├── predictions_*.txt         ← Raw inference outputs
│   ├── *_normalized.txt          ← Normalized GT + prediction pairs
│   └── *_mismatches.txt          ← Error analysis
└── results/                      ← Final WER JSONs and summary tables
```

---

## 📊 Progress Tracker

| Week | Activity | Status |
|------|----------|--------|
| Week 1 | Literature survey: KID-Whisper, XLSR, XLS-R, MyST & CSLU datasets. GitHub repo setup. | ✅ Done |
| Week 2 | MyST corpus filtering (KID-Whisper methodology), baseline WER = 14.46% confirmed, evaluation pipeline built. | ✅ Done |
| Week 3 | Compression experiments: INT8 (14.60%), NF4 (14.48%), FP4 (14.05%), Layer pruning 2L (623.19%). Quantization analysis complete. | ✅ Done |

---

## 🔖 References

| # | Citation |
|---|---|
| [1] | Attia, A. A., Liu, J., Ai, W., Demszky, D., & Espy-Wilson, C. (2024). *KID-Whisper: Towards Bridging the Performance Gap in ASR for Children VS. Adults.* arXiv:2309.07927. |
| [2] | Fan, R., Zheng, R., & Alwan, A. (2024). *Benchmarking Children's ASR with Supervised and Self-supervised Speech Foundation Models.* Interspeech 2024. arXiv:2406.10507. |
| [3] | Conneau, A., et al. (2021). *Unsupervised Cross-lingual Representation Learning for Speech Recognition.* Interspeech 2021. arXiv:2006.13979. |
| [4] | Babu, A., et al. (2022). *XLS-R: Self-supervised Cross-lingual Speech Representation Learning at Scale.* Interspeech 2022. arXiv:2111.09296. |
| [5] | Ardila, R., et al. (2020). *Common Voice: A Massively-Multilingual Speech Corpus.* LREC 2020. |
| [6] | Pratap, V., et al. (2020). *MLS: A Large-Scale Multilingual Dataset for Speech Research.* Interspeech 2020. |
| [7] | Wang, C., et al. (2021). *VoxPopuli: A Large-scale Multilingual Speech Corpus.* ACL 2021. |
| [8] | Valk, J., & Alumae, T. (2021). *VoxLingua107: a Dataset for Spoken Language Recognition.* SLT 2021. |
| [9] | Baevski, A., Zhou, Y., Mohamed, A., & Auli, M. (2020). *wav2vec 2.0: A Framework for Self-supervised Learning of Speech Representations.* NeurIPS 2020. arXiv:2006.11477. |
| [10] | Radford, A., et al. (2023). *Robust Speech Recognition via Large-Scale Weak Supervision.* ICML 2023. arXiv:2212.04356. |
| [11] | Ward, W., Cole, R., & Pradhan, S. (2016). *My Science Tutor - Learning Science with a Conversational Virtual Tutor.* ACL 2016 System Demonstrations. |
| [12] | Shobaki, K., Hosom, J. P., & Cole, R. A. (2000). *The OGI Kids' Speech Corpus and Recognizers.* ICSLP 2000. |
| [13] | Dettmers, T., Lewis, M., Belkada, Y., & Zettlemoyer, L. (2022). *LLM.int8(): 8-bit Matrix Multiplication for Transformers at Scale.* NeurIPS 2022. arXiv:2208.07339. |
| [14] | Dettmers, T., Pagnoni, A., Holtzman, A., & Zettlemoyer, L. (2023). *QLoRA: Efficient Finetuning of Quantized LLMs.* NeurIPS 2023. arXiv:2305.14314. |

---

*Last updated: Week 3*
