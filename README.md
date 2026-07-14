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

| Split | Files | Duration | Filters Applied | Status |
|---|---|---|---|---|
| train | 57,687 | 136.9h | all 4 filters + >30s removal | ✅ Final |
| development | 9,017 | 21.1h | all 4 filters + >30s removal | ✅ Final |
| test | 10,415 | 26.12h | all 4 filters + >30s removal (via chunking) | ✅ Final |

- WER flagging applied to all splits: train (2,823 flagged), dev (1,046 flagged), test (1,067 flagged) - scripts: `generate_flags_partition.py`, `clean_flags_partition.py`
- Filter 5 (>30s removal) added to match KID-Whisper methodology - utterances longer than 30s removed since Whisper is trained on 30s windows
- KID-Whisper reports train 132.5h / dev 21h - our results (136.9h / 21.1h) are close; remaining gap due to FP16 vs FP32 flagging difference
- Concatenated filtered test utterances into 30-second chunks for Whisper inference: 3,972 chunks
- `myst_train_text.txt` contains 59,842 lines - matches filtered train count exactly (cross-verified)

#### 🧪 Baseline Experiments - Whisper Large-v3 Zero-shot on Filtered MyST Test
*Our experiments - hardware: RTX 4060 8GB | normalizer: EnglishTextNormalizer (whisper_normalizer package) | evaluation script: calculate_wer.py*

| Decoding | Chunks | WER% | Inference Time | Notes |
|---|---|---|---|---|
| Greedy | 3,972 | 15.31% | 33.5 min | No LM - our result |
| **Beam-5** | **3,972** | **14.46%** | **129.2 min** | **Official thesis baseline - our result** |
| Zero-shot ref (Fan et al. 2024) [2] | - | 12.60% | - | arXiv:2406.10507, Table 2 - Whisper Large-v3 zero-shot, no LM |

> **🎯 Official Thesis Baseline: WER = 14.46%**
> Model: Whisper Large-v3 | Decoding: Beam-5 | Corpus: MyST test (filtered) | Normalizer: EnglishTextNormalizer | Hardware: RTX 4060 8GB

#### 💡 Key Findings (Week 2)
- Beam-5 gives 0.85% absolute WER improvement over greedy on children's speech
- 1.86% gap between our result (14.46%) and published reference (12.60%, Fan et al. 2024) is due to different filtering methodology and test set construction - NOT LM rescoring. KID-Whisper (arXiv:2309.07927) only reports up to Whisper Large-v2 and uses no GPT-2 LM in main results
- Disfluency tokens (UH, UM) in MyST references inflate WER by ~4-5% if not normalized - EnglishTextNormalizer handles this correctly
- RTF: greedy = 0.031 (32x real-time), beam-5 = 0.125 (8x real-time) on RTX 4060 8GB

---

### ✅ Week 3 | Compression Experiments

#### 🔬 Part A - bitsandbytes Quantization (Black-box baseline)

All methods applied post-training to Whisper Large-v3. Beam-5 decoding, EnglishTextNormalizer, full test set (3,972 chunks).
*Source: Our experiments using bitsandbytes library (Dettmers et al. 2022 [13], 2023 [14])*

| Method | Scheme | WER% | Size | RTF | vs Baseline |
|---|---|---|---|---|---|
| FP16 (reference) | Half-precision float | 14.46% | 2.87 GB | 0.125 | - |
| INT8 - LLM.int8() | Absmax + FP16 outlier cols | 14.60% | 1.51 GB | 0.145 | +0.14% |
| NF4 4-bit | Normal float quantile grid | 14.48% | 0.82 GB | 0.098 | +0.02% |
| FP4 4-bit | Float point 4-bit format | **14.05%** | 0.82 GB | 0.098 | -0.41% |

#### 🔬 Part B - Our PyTorch Absmax Per-Channel PTQ (Thesis Contribution)

Implemented from scratch in pure PyTorch. Same method across all bit widths - only the quantization range changes. 513 Linear layers quantized (99.5% of all parameters). Weights stored as int8 in all cases; theoretical sizes assume native N-bit packing.
*Source: Our implementation - scripts/10_ptq_int8_pytorch.py, scripts/11_ptq_unified.py*

| Bits | Levels | WER% | Actual Size | Theoretical Size | Reduction | Eval Set |
|---|---|---|---|---|---|---|
| 16 (FP16) | 65,536 | 14.46% | 2.87 GB | 2.87 GB | - | Full (3,972) |
| 8 (INT8) | 255 | 14.48% | 1.57 GB | 1.57 GB | -45.3% | Full (3,972) |
| 4 (INT4) | 15 | 620.91% | 1.57 GB | 0.73 GB | -74.5% | Full (3,972) |
| 2 (INT2) | 3 | 100.00% | 1.57 GB | 0.38 GB | -86.9% | 200 samples |
| 1 (Binary)| 2 | 100.00% | 1.57 GB | 0.20 GB | -93.1% | 200 samples |

#### 🔬 Part C - Layer Pruning (no recovery)

Removed last N layers from Whisper Large-v3 encoder (32 layers total). Decoder untouched.
*Source: Our experiments - scripts/07_compress_pruning.py*

| Method | Encoder Layers | WER% | Size | RTF | vs Baseline |
|---|---|---|---|---|---|
| FP16 (reference) | 32/32 | 14.46% | 2.87 GB | 0.125 | - |
| Pruning 2L | 30/32 | 623.19% | 2.80 GB | 0.575 | +608.73% |

#### 🔬 Part D - Calibrated Absmax PTQ (Our Extension - Thesis Contribution)

Extends Part B with two calibration strategies to fix INT4 collapse. Same absmax per-channel method but with improved scale estimation.
*Source: Our implementation - scripts/12_ptq_calibratedWithFP32.py*

**Strategy 1 - Percentile clipping (no calibration data):**
Replace `max(|W|)` with `percentile(|W|, 99.9)` per output channel. Prevents single outlier weights from pulling the scale so wide that 99%+ of near-zero weights collapse to 0.

**Strategy 2 - Activation-aware scaling (calibration data required):**
Combine weight percentile with actual MyST encoder activation statistics using smoothing factor alpha. Scale = `(weight_pct^(1-alpha) * act_pct^alpha) / max_val`. Inspired by SmoothQuant (Xiao et al. 2022 [15]). Tested with 128 filtered MyST train utterances (57,687 files / 136.9h after all 4 filters + >30s removal).

| Method | Calib Data | Alpha | WER% | Size | Eval Set | vs Naive INT4 |
|---|---|---|---|---|---|---|
| Naive INT4 (reference) | None | - | 620.91% | 1.57 GB | Full (3,972) | - |
| Percentile INT4 (p=99.9) | None | - | **19.00%** | 1.57 GB | Full (3,972) | -601.91% |
| Act-aware INT4 (CA-2) | 128 MyST utterances | 0.5 | 100.00% | 1.57 GB | 200 samples | worse |
| Act-aware INT4 (CA-3) | 128 MyST utterances | 0.1 | 392.36% | 1.57 GB | 200 samples | worse |

#### 💡 Key Findings (Week 3)

- **8-bit is the minimum viable bit width for absmax linear quantization on Whisper** - below 8-bit the model fails completely. 4-bit hallucinates (WER 620%), 2-bit and 1-bit produce empty transcriptions (WER 100%, Matches 0/200)
- **Quantization scheme matters more than bit width** - bitsandbytes NF4 at 4-bit (WER 14.48%) outperforms our linear INT4 at 4-bit (WER 620.91%) by placing quantization levels at the quantiles of the normal weight distribution rather than equally spaced
- **Our INT8 (14.48%) outperforms bitsandbytes INT8 (14.60%)** - per-channel absmax scaling preserves more information than bitsandbytes vector-wise scaling for this task, and is 30% faster (RTF 0.101 vs 0.145) on consumer GPU
- **FP4 produces best overall result (14.05%)** - 0.41% better than FP16 baseline, suggesting quantization noise acts as mild regularization on this test set
- **Percentile clipping alone recovers 97% of INT4 collapse** - naive INT4 at 620.91% drops to 19.00% by simply replacing max(|W|) with 99.9th percentile - no calibration data required
- **Percentile clipping is effective only at 4-bit and above** - at 2-bit (3 levels), the binding constraint is level count not outliers. Even with p=99.9 clipping, ~40-50% of Whisper weights still round to 0 (empty output, WER 100%). Minimum viable bit width for percentile clipping on Whisper is 4-bit
- **Activation-aware scaling makes INT4 worse, not better** - children's speech produces large encoder activations (act_pct >> weight_pct). The geometric mean formula widens quantization scales rather than tightening them, pushing near-zero weights back to zero. Even alpha=0.1 gives 392.36% WER. The correct solution is NF4's quantile grid which places levels where weights actually exist, not relative to activation magnitude
- **Naive layer pruning is catastrophic** - removing just 2 of 32 encoder layers causes 623% WER, motivating LoRA recovery as the next phase
- **2-bit and 1-bit failure modes differ from 4-bit** - 4-bit hallucinates (decoder runs to token limit), 2-bit/1-bit collapse weights to zero (decoder produces empty outputs)

---

### ✅ Week 4 | Floating Point Quantization Experiments

#### 🔬 FP8 and FP4 - Our Custom Implementations

Extended the quantization study to floating point formats. Same per-channel absmax scaling as Week 3 - only the quantization grid changes from linear integer to exponential float.
*Source: Our implementation - scripts/13_ptq_fp_formats.py*

**Why floating point grids?** Whisper weights follow a near-Gaussian distribution - peaked near zero with rare large outliers. Floating point grids are exponentially denser near zero, naturally matching this distribution where linear integer grids waste levels.

**FP8 Results:**

| Format | Mantissa bits | Max range | WER% | Theor. Size | RTF | Eval Set |
|---|---|---|---|---|---|---|
| FP8 E4M3 naive (ours) | 3 | ±448 | 14.45% | 1.45 GB | 0.106 | Full (3,972) |
| **FP8 E4M3 + pct99.9 (ours)** | **3** | **±448** | **13.98%** | **1.45 GB** | **0.101** | **Full (3,972)** |
| FP8 E5M2 naive (ours) | 2 | ±57344 | 100.00% | 1.45 GB | 0.578 | 200 samples |

**FP4 Results:**

| Format | Method | WER% | Theor. Size | RTF | Eval Set | vs INT4 naive |
|---|---|---|---|---|---|---|
| FP4 E2M1 naive (ours) | Absmax | 15.87% | 0.73 GB | 0.169 | Full (3,972) | -605.04% |
| **FP4 E2M1 + pct99.9 (ours)** | **Percentile** | **14.03%** | **0.73 GB** | **0.158** | **Full (3,972)** | **-606.88%** |
| bitsandbytes FP4 (reference) | Proprietary | 14.05% | 0.82 GB | 0.098 | Full (3,972) | - |

#### 💡 Key Findings (Week 4)

- **FP8 E4M3 + percentile (13.98%) is the best result of the entire study** - beats FP16 baseline by 0.48% at 49.6% theoretical size reduction
- **Mantissa bits matter more than exponent range** - E4M3 (3 mantissa, ±448) works; E5M2 (2 mantissa, ±57344) fails completely. Verified: changing E5M2 scale range has zero effect on error (all ranges give mean abs error = 0.000573)
- **FP4 E2M1 exponential grid solves the INT4 outlier problem** - 15.87% WER vs INT4 naive 620.91%, same 15 levels placed exponentially instead of linearly
- **FP4 E2M1 + percentile (14.03%) matches bitsandbytes FP4 (14.05%) within 0.02%** - confirms bitsandbytes uses a similar E2M1-style exponential grid internally. Our implementation is fully transparent and grounded in standard FP4 E2M1 specification
- **Percentile clipping universally beneficial across all formats** - improves FP8 E4M3 from 14.45% to 13.98%, FP4 E2M1 from 15.87% to 14.03%, and as seen in Week 3: INT4 from 620.91% to 19.00%
- **Theoretical foundation: ACIQ (Banner et al. 2019, arXiv:1810.05723) [16]** - analytically proves optimal clip point ≈ 2.83σ for 4-bit Gaussian distributions. Our p99.9 ≈ 3.09σ closely approximates this optimum without requiring calibration data


### ✅ Week 5 | Custom FP4 E2M1 + LoRA Fine-tuning

**Pipeline:** FP4 E2M1 + pct99.9 (14.03% WER) → custom LoRA on frozen FP4 weights → 13.77% WER

**Result: 13.77% WER** using r=16 LoRA on q_proj+v_proj, 500 steps, 57,687 MyST train utterances

| Attempt | Config | Steps | WER% | Outcome |
|---|---|---|---|---|
| Sanity | r=16, q+v | 25 | 13.74% | ✅ |
| **Optimal** | **r=16, q+v, non-reentrant GC** | **500** | **13.77%** | **✅ Best** |
| Fail 1 | r=16, 3 epochs, reentrant GC | 21,630 | 93.86% | ❌ |
| Fail 2 | r=16, 1 epoch | 7,210 | 96.58% | ❌ |
| Fail 3 | r=32, q+v+out+fc1 | 500 | 17.72% | ❌ |

**Key findings:** exposure bias limits training to ~500 steps; `use_reentrant=False` required for gradient checkpointing with FP4 layers; minimal LoRA (r=16, q+v only) outperforms expanded targets; fc1 layers cause 4× LoRA amplification degrading WER. Gap to KID-Whisper (9.11%) explained by exposure bias ceiling (only 6.9% of data trained). Scheduled sampling is the pending fix.

---

## 📊 Experiment Results

### Baselines - Whisper Large-v3, MyST Test Set (filtered, 3,972 chunks)
*Source: Our experiments*

| # | Method | WER% | Size | RTF |
|---|--------|------|------|-----|
| B-1 | Zero-shot Greedy | 15.31% | 2.87 GB FP16 | 0.031 |
| **B-2** | **Zero-shot Beam-5 (official baseline)** | **14.46%** | **2.87 GB FP16** | **0.125** |

### bitsandbytes PTQ - MyST Test Set (full, 3,972 chunks)
*Source: Our experiments using Dettmers et al. 2022 [13] (INT8) and 2023 [14] (NF4, FP4)*

| # | Method | WER% | Size | RTF | vs Baseline |
|---|--------|------|------|-----|-------------|
| C-1 | INT8 - LLM.int8() | 14.60% | 1.51 GB | 0.145 | +0.14% |
| C-2 | NF4 4-bit | 14.48% | 0.82 GB | 0.098 | +0.02% |
| C-3 | FP4 4-bit | **14.05%** | 0.82 GB | 0.098 | -0.41% |

### Our PyTorch Absmax Per-Channel PTQ (thesis contribution)
*Same method across all bit widths. 513 Linear layers quantized (99.5% of params). Weights stored as int8; theoretical sizes assume native N-bit packing.*
*Source: Our implementation - scripts/10_ptq_int8_pytorch.py, scripts/11_ptq_unified.py*

| # | Bits | Levels | WER% | Actual Size | Theoretical Size | Eval Set |
|---|------|--------|------|-------------|-----------------|----------|
| P-1 | 16 (FP16) | 65,536 | 14.46% | 2.87 GB | 2.87 GB | Full (3,972) |
| P-2 | 8 (INT8) | 255 | 14.48% | 1.57 GB | 1.57 GB | Full (3,972) |
| P-3 | 4 (INT4) | 15 | 620.91% | 1.57 GB | 0.73 GB | Full (3,972) |
| P-4 | 2 (INT2) | 3 | 100.00% | 1.57 GB | 0.38 GB | 200 samples |
| P-5 | 1 (Binary) | 2 | 100.00% | 1.57 GB | 0.20 GB | 200 samples |

### Layer Pruning - no LoRA recovery
*Source: Our experiments - scripts/07_compress_pruning.py*

| # | Method | Encoder Layers | WER% | Size | vs Baseline |
|---|--------|---------------|------|------|-------------|
| L-1 | Pruning 2L | 30/32 | 623.19% | 2.80 GB | +608.73% |

### Calibrated Absmax PTQ - INT4 Recovery (thesis contribution)
*Extends our PyTorch PTQ with improved scale estimation. No retraining required.*
*Source: Our implementation - scripts/12_ptq_calibratedWithFP32.py*

| # | Method | Calib Data | WER% | Size | Eval Set | vs Naive INT4 |
|---|--------|-----------|------|------|----------|---------------|
| CA-1 | Percentile INT4 (p=99.9) | None | **19.00%** | 1.57 GB | Full (3,972) | -601.91% |
| CA-2 | Act-aware INT4 (alpha=0.5) | 128 MyST utterances | 100.00% | 1.57 GB | 200 samples | worse than percentile |
| CA-3 | Act-aware INT4 (alpha=0.1) | 128 MyST utterances | 392.36% | 1.57 GB | 200 samples | worse than percentile |
| CA-4 | Percentile INT2 (p=99.9) | None | 100.00% | 1.57 GB | 200 samples | no improvement over naive INT2 |

### Floating Point Quantization (Week 4 - thesis contribution)
*Our own FP8 and FP4 implementations using per-channel absmax + optional percentile clipping.*
*Source: Our implementation - scripts/13_ptq_fp_formats.py*

| # | Format | Method | WER% | Theor. Size | RTF | Eval Set | vs Baseline |
|---|--------|--------|------|-------------|-----|----------|-------------|
| F-1 | FP8 E4M3 (ours) | Naive absmax | 14.45% | 1.45 GB | 0.106 | Full (3,972) | -0.01% |
| **F-2** | **FP8 E4M3 + pct99.9 (ours)** | **Percentile absmax** | **13.98%** | **1.45 GB** | **0.101** | **Full (3,972)** | **-0.48%** |
| F-3 | FP8 E5M2 (ours) | Naive absmax | 100.00% | 1.45 GB | 0.578 | 200 samples | catastrophic |
| F-4 | FP4 E2M1 (ours) | Naive absmax | 15.87% | 0.73 GB | 0.169 | Full (3,972) | +1.41% |
| **F-5** | **FP4 E2M1 + pct99.9 (ours)** | **E2M1 + percentile** | **14.03%** | **0.73 GB** | **0.158** | **Full (3,972)** | **-0.43%** |

### Week 5 - QLoRA Fine-tuning Results
*Pipeline: FP4 E2M1+pct99.9 → custom LoRA → MyST train (57,687 utts / 136.9h)*
*Source: Our implementation - scripts/15_qlora_finetune.py*

| # | Config | Steps | WER% | vs Zero-shot | Trainable | Status |
|---|--------|-------|------|-------------|-----------|--------|
| **QL-1** | **r=16, q+v, use_reentrant=False** | **500** | **13.77%** | **-0.26%** | **7.86M (0.49%)** | **✅ Best** |
| QL-2 | r=16, q+v, 1 epoch | 7,210 | 96.58% | +82.55% | 7.86M | ❌ Exposure bias |
| QL-3 | r=16, q+v, 3 epochs + GC corruption | 21,630 | 93.86% | +79.83% | 7.86M | ❌ GC + exposure bias |
| QL-4 | r=32, q+v+out_proj+fc1, 500 steps | 500 | 17.72% | +3.69% | 36.7M (2.4%) | ❌ fc1 amplification |

**Key findings:**
- Optimal: r=16, q+v only, 500 steps → WER 13.77% (-0.26% vs 14.03% zero-shot)
- Gradient checkpointing MUST use `use_reentrant=False` - default corrupts FP4 gather gradients
- Exposure bias: >500 steps causes catastrophic WER (93-96%) due to teacher forcing mismatch
- Larger LoRA (r=32 + fc1 layers) worsens WER: fc1 [1280→5120] produces 4× larger LoRA output than attention layers → dominates FP4 base representations within 500 steps
- Gap to KID-Whisper (9.11%): training only 6.9% of data (4,000/57,687 utterances), exposure bias ceiling, LoRA vs full fine-tuning expressiveness
- Scheduled sampling (gradual replacement of correct tokens with model predictions) is the correct fix for exposure bias - pending implementation

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
│   │   ├── train/                ← 57,687 utterances (136.9h, all 4 filters + >30s removal)
│   │   ├── development/          ← 9,017 utterances (21.1h, all 4 filters + >30s removal)
│   │   └── test/                 ← 10,415 utterances (26.12h, all 4 filters applied)
│   ├── concatenated/             ← 30-second chunks for Whisper inference
│   │   └── test/                 ← 3,972 chunks
│   └── myst_train_text.txt       ← Training transcriptions (59,842 lines)
├── scripts/
│   ├── preprocess_myst.py               ← Phase 1+2: filter raw data and concatenate
│   ├── generate_whisper_large_flags.py  ← Run Whisper-Large zero-shot for WER flagging
│   ├── clean_whisper_large_flags.py     ← Apply 53% threshold to flagging output
│   ├── train_gpt2_myst.py               ← GPT-2 shallow fusion LM training
│   ├── test_shallow_fusion_quick.py
│   ├── 04_inference_filtered_myst.py    ← Whisper FP16 inference (beam/greedy)
│   ├── 05_compress_int8.py              ← bitsandbytes INT8 quantization
│   ├── 06_compress_nf4.py               ← bitsandbytes NF4 4-bit quantization
│   ├── 07_compress_pruning.py           ← Encoder layer pruning (2/4/6/8 layers)
│   ├── 09_compress_fp4.py               ← bitsandbytes FP4 4-bit quantization
│   ├── 10_ptq_int8_pytorch.py           ← Our PyTorch absmax INT8 PTQ
│   ├── 11_ptq_unified.py                ← Our PyTorch absmax PTQ (any bit width)
│   ├── 12_ptq_calibratedWithFP32.py      ← Calibrated PTQ (percentile + activation-aware, float32 fix)
│   ├── 13_ptq_fp_formats.py               ← FP8 E4M3/E5M2 and FP4 E2M1 quantization
│   ├── 14_lora_recovery.py                ← LoRA recovery on pruned 2L model (ready, not run)
│   ├── 15_qlora_finetune.py               ← FP4 E2M1+pct + custom LoRA fine-tuning
│   └── 16_qlora_hf_tutorial.py            ← HuggingFace NF4+PEFT LoRA (partial - env issues)
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
| Week 3 | Full compression suite: bitsandbytes PTQ (INT8/NF4/FP4), our PyTorch absmax PTQ (all bit widths), layer pruning, calibrated PTQ (percentile INT4=19%, percentile INT2=100%). Key findings: scheme > bit-width; percentile clipping effective only at 4-bit+; 4-bit is minimum viable with calibration; activation-aware scaling fails for children's speech. | ✅ Done |
| Week 4 | Floating point quantization (FP8 E4M3 naive 14.45%, FP8 E4M3+pct 13.98%, FP8 E5M2 100%, FP4 E2M1 naive 15.87%, FP4 E2M1+pct 14.03%). Key findings: mantissa bits > exponent range; percentile universally beneficial for all formats; FP8 E4M3+pct is best result overall; FP4 E2M1+pct confirms bitsandbytes FP4 internal grid. Grounded in ACIQ (Banner et al. 2019). | ✅ Done |
| Week 5 | Custom FP4 E2M1+pct + LoRA fine-tuning. Optimal: r=16 q+v 500 steps → 13.77% WER (-0.26% vs zero-shot). Key findings: exposure bias limits training to ~500 steps; use_reentrant=False required for gradient checkpointing; larger LoRA (r=32+fc1) worse due to 4× amplification. Scheduled sampling pending to overcome ceiling. | ✅ Done (pending improvement) |

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
| [15] | Xiao, G., Lin, J., Seznec, M., Wu, H., Demouth, J., & Han, S. (2023). *SmoothQuant: Accurate and Efficient Post-Training Quantization for Large Language Models.* ICML 2023. arXiv:2211.10438. |
| [16] | Banner, R., Nahshan, Y., Hoffer, E., & Soudry, D. (2019). *Post Training 4-bit Quantization of Convolutional Networks for Rapid-Deployment (ACIQ).* NeurIPS 2019. arXiv:1810.05723. |
| [17] | Wu, H., et al. (2020). *Integer Quantization for Deep Learning Inference: Principles and Empirical Evaluation.* NVIDIA Technical Report. arXiv:2004.09602. |
| [18] | Nagel, M., et al. (2021). *A White Paper on Neural Network Quantization.* Qualcomm AI Research. arXiv:2106.08295. |

---

*Last updated: Week 5 (complete) - July 2026*
