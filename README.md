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

### ✅ Week 6 | KID-Whisper PTQ Compression Experiments

**Approach:** Compress already-children-adapted KID-Whisper models (Attia et al. AAAI 2024, arXiv:2309.07927) using our PTQ methods. Tests whether quantization preserves domain-specific children's speech features.

**Models:**
- `aadel4/kid-whisper-small-myst` - whisper-small multilingual, 244M params, paper WER: 11.80%
- `aadel4/kid-whisper-small-en-myst` - whisper-small.en English-only, 244M params, paper WER: 9.11%
- `aadel4/kid-whisper-medium-en-myst` - whisper-medium.en English-only, 769M params, paper WER: 8.91%
- `aadel4/kid-whisper-medium-myst` (8.61% best result) - NOT publicly available on HuggingFace

**Key Finding:** Percentile clipping universally HURTS fine-tuned models (opposite of general models).
Naive quantization preserves children's speech features. Rule: fine-tuned models → use naive only.

#### KID-Whisper Small (aadel4/kid-whisper-small-myst) - Complete Results

*Sizes: Actual = what our script stores on disk. Theoretical = true N-bit packing (not achieved without custom CUDA kernels). Non-Linear layers (embed_tokens 0.080GB, positional 0.003GB, Conv1d 0.006GB, LayerNorm 0.001GB) always stay FP16 in our implementation.*

| # | Method | WER% | vs FP16 | Actual Size | Theor. Size | RTF | Storage Note |
|---|--------|------|---------|------------|-------------|-----|------|
| KW-S-1 | FP16 baseline | 11.45% | — | 0.450 GB | 0.450 GB | 0.022 | float16 native |
| **KW-S-2** | **INT8 naive (ours)** | **11.35%** | **-0.10%** | **0.303 GB** | **0.303 GB** | **0.031** | **int8=1byte, actual=theoretical** |
| KW-S-3 | FP8 E4M3 naive (ours) | 11.75% | +0.30% | 0.303 GB | 0.303 GB | 0.031 | uint8=1byte, actual=theoretical |
| KW-S-4 | FP4 E2M1 naive (ours) | 11.79% | +0.34% | 0.303 GB | 0.229 GB | 0.044 | int8 stores 4-bit idx (no PyTorch int4) |
| KW-S-5 | INT4 naive (ours) | 13.08% | +1.63% | 0.303 GB | 0.229 GB | 0.030 | int8 stores 4-bit idx (no PyTorch int4) |
| KW-S-6 | INT8 pct99.9 (ours) | 15.96% | +4.51% | 0.303 GB | 0.303 GB | 0.032 | int8=1byte, pct clips children's features |
| KW-S-7 | FP8 E4M3 pct99.9 (ours) | 16.84% | +5.39% | 0.303 GB | 0.303 GB | 0.033 | uint8=1byte, pct clips children's features |
| KW-S-8 | INT4 pct99.9 (ours) | 35.43% | +23.98% | 0.303 GB | 0.229 GB | 0.036 | int8 stores 4-bit idx, pct catastrophic |
| KW-S-9 | FP4 E2M1 pct99.9 (ours) | 22.39% | +10.94% | 0.303 GB | 0.229 GB | 0.048 | int8 stores 4-bit idx, pct catastrophic |
| KW-S-10 | INT2 naive (ours) | 100.00% | catastrophic | 0.303 GB | 0.193 GB | 0.201 | int8 stores 2-bit idx, too few levels |
| KW-S-11 | FP2 E1M0 naive (ours) | 713.72% | catastrophic | 0.303 GB | 0.193 GB | 0.317 | int8 stores 2-bit idx, no-zero→loops |
| KW-S-12 | INT2/1 pct, FP1 naive/pct | ~100%+ | catastrophic | 0.303 GB | 0.174 GB | — | int8 stores N-bit idx, all fail |
| KW-S-13 | bitsandbytes INT8 (LLM.int8()) | 11.54% | +0.09% | 0.266 GB | 0.303 GB | 0.074 | CUDA mixed-precision, smaller via LLM.int8() |
| **KW-S-14** | **bitsandbytes NF4** | **11.44%** | **-0.01%** | **0.173 GB** | **0.229 GB** | **0.037** | **CUDA true 4-bit + double quant of scales** |
| KW-S-15 | bitsandbytes FP4 | 11.48% | +0.03% | 0.173 GB | 0.229 GB | 0.030 | CUDA true 4-bit packing |

#### Key Findings (Week 6 - KID-Whisper Small)

- **INT8 naive is the best quantized method** (11.35%) - 0.10% better than FP16 (11.45%) while achieving 32.7% size reduction (0.45→0.30 GB). Domain regularization effect exists at INT8 too
- **FP4 naive (11.79%) nearly matches FP8 naive (11.75%)** - only 0.04% gap despite halving bit width from 8 to 4. Exponential FP grid preserves fine-tuned children's speech features at 4-bit
- **Minimum viable bit width: 4-bit** - INT4 naive (13.08%) is the minimum usable INT method. FP4 naive (11.79%) is far better at the same 4-bit count, proving exponential grid superiority
- **Percentile clipping universally destroys fine-tuned model quality** across ALL formats and bit widths:
  - INT8: naive 11.35% → pct 15.96% (+4.51%)
  - FP8:  naive 11.75% → pct 16.84% (+5.39%)
  - INT4: naive 13.08% → pct 35.43% (+22.35%)
  - FP4:  naive 11.79% → pct 22.39% (+10.60%)
  - Damage increases as bit width decreases (fewer levels → more sensitive to scale choice)
- **Percentile clipping removes domain-specific outlier weights** - for general models these are adult-speech overspecializations (good to remove); for fine-tuned models these are children-speech adaptations (catastrophic to remove)
- **FP2 E1M0 (no zero) worse than INT2 (has zero)** - FP2 grid {±0.5, ±1.0} has no zero value; all weights are forced non-zero → attention cannot be suppressed → decoder loops → WER 713%. INT2 {-1, 0, +1} at least allows suppression → WER 100% (controlled failure)
- **New Rule (Rule 5):** For fine-tuned domain-specific models, use naive absmax quantization. Percentile calibration is harmful - it removes domain adaptation. This is the inverse of Rule 3 for general models
- **Storage: Actual vs Theoretical sizes differ for sub-8-bit methods:**
  - INT8/FP8 naive: **Actual = Theoretical** (0.303 GB) - int8/uint8 IS 1-byte storage, no gap
  - INT4/FP4 naive: **Actual 0.303 GB > Theoretical 0.229 GB** - PyTorch has no int4 dtype; 4-bit indices (0-14) stored in 8-bit int8, wasting 4 bits per weight; true packing (bitsandbytes-style) would achieve 0.229 GB
  - INT2/FP2 naive: **Actual 0.303 GB > Theoretical 0.193 GB** - 2-bit values (-1,0,+1 or indices 0-3) stored in 8-bit int8, wasting 6 bits per weight
  - INT1/FP1 naive: **Actual 0.303 GB > Theoretical 0.174 GB** - 1-bit sign stored in 8-bit int8, wasting 7 bits per weight
  - bitsandbytes NF4/FP4: **Actual 0.173 GB < Theoretical 0.229 GB** - achieves BELOW theoretical because double quantization also compresses the scale tensors (FP8 scales instead of FP16)
  - Non-Linear layers (embed_tokens 80MB, positional 3MB, Conv1d 6MB, LayerNorm 1MB) always remain FP16 in our implementation since nn.Embedding and Conv1d are not quantized
- **Three clear deployment tiers established:**
  - Tier 1 Near-lossless (<0.1% from FP16): BnB NF4 11.44% 0.173 GB, BnB FP4 11.48% 0.173 GB, INT8 naive 11.35% 0.303 GB
  - Tier 2 Usable (0.3-1.6%): FP8 naive 11.75%, FP4 naive 11.79%, INT4 naive 13.08%
  - Tier 3 Unacceptable (>4%): all percentile methods + INT2/FP2/FP1
- **BnB NF4 achieves best size-quality trade-off:** 11.44% WER at 0.173 GB (61.6% smaller than FP16), only 0.01% from FP16 baseline
- **FP4 naive (11.79%) nearly matches FP8 naive (11.75%) at same bit width** - only 0.04% gap. Exponential grid preserves domain knowledge at 4-bit equally to 8-bit
- **Grounded in:** ACIQ (Banner et al. 2019) - percentile removes outliers which are overspecializations in general models but are task-specific features in fine-tuned models

---

#### KID-Whisper Medium (aadel4/kid-whisper-medium-en-myst) - Results

*Base: whisper-medium.en English-only, 769M params, paper WER: 8.91% (their protocol), our FP16: 11.00%*

| # | Method | WER% | vs FP16 | Size | RTF | Status |
|---|--------|------|---------|------|-----|--------|
*Sizes: Actual stored vs Theoretical (true N-bit). Non-linear layers (0.211 GB) always FP16.*

| # | Method | WER% | vs FP16 | Actual Size | Theor. Size | RTF | Note |
|---|--------|------|---------|------------|-------------|-----|------|
| **KW-M-0** | **BnB FP4** | **10.98%** | **-0.02%** | **0.438 GB** | **0.514 GB** | **0.051** | **BEST - beats FP16** |
| KW-M-1 | FP16 baseline | 11.00% | — | 1.423 GB | 1.423 GB | 0.043 | float16 |
| KW-M-2 | INT8 naive (ours) | 11.00% | +0.005% | 0.817 GB | 0.817 GB | 0.062 | actual=theoretical ✓ |
| KW-M-3 | FP8 naive (ours) | 11.00% | +0.005% | 0.817 GB | 0.817 GB | 0.066 | actual=theoretical ✓ |
| KW-M-4 | BnB NF4 | 11.04% | +0.044% | 0.438 GB | 0.514 GB | 0.062 | true 4-bit + double quant |
| KW-M-5 | INT4 naive (ours) | 11.32% | +0.322% | 0.817 GB | 0.514 GB | 0.061 | faster than FP4, same WER |
| KW-M-6 | FP4 naive (ours) | 11.32% | +0.324% | 0.817 GB | 0.514 GB | 0.103 | int8 stores 4-bit idx |
| KW-M-7 | INT8/FP8/FP4 pct | skipped | harmful | — | — | — | ⏭️ Known harmful from Small |
| KW-M-8 | INT2/FP2 naive | skipped | catastrophic | — | — | — | ⏭️ Known from Small |

**Key Medium Findings:**
- **BnB FP4 (10.98%) beats FP16 (11.00%)** - domain regularization effect: 4-bit quantization noise removes slight overfit patterns, improving generalization. Achieved at 0.438 GB (69.2% smaller than 1.423 GB FP16)
- **INT8 and FP8 are completely lossless** (11.001% vs 11.00%) - only 7 additional word errors in 130,000 words. Unrounded WER difference: +0.00519%. 769M params absorb quantization noise below beam search decision boundary
- **INT4 equals FP4 at 4-bit for Medium** (both 11.32%) - grid choice (exponential vs linear) becomes irrelevant at 769M params. INT4 preferred: 41% faster RTF (0.061 vs 0.103), same WER
- **Medium more robust than Small at every bit width** - Small FP4: +0.34%, Medium FP4: +0.32%; Small INT8: -0.10%, Medium INT8: +0.005%
- **Size note:** Medium (1.423 GB FP16) is 3.16× larger than Small (0.450 GB) but only 0.45% better WER under our 30s concatenated protocol. Paper gap (8.91% vs 11.80%) is larger because individual utterance evaluation favors bigger models

---

## ✅ Week 7 | Corrected Evaluation Protocol + Comprehensive PTQ

**Critical correction from Week 6:** Week 6 used wrong model (multilingual Small) and broken pipeline (truncated >30s chunks). Week 7 establishes correct baselines matching the paper.

**Corrected Protocol:**
- Model: `aadel4/kid-whisper-small-en-myst` (English-only) for Small experiments
- Pipeline: HuggingFace `pipeline(chunk_length_s=30)` handles >30s chunks correctly
- Decoding: Beam-5, batch=4
- This matches the paper's evaluation approach

#### Corrected Baselines vs Paper

| Model | Our FP16 WER | Paper WER | Gap |
|---|---|---|---|
| kid-whisper-small-en-myst | 9.16% | 9.11% | 0.05% |
| kid-whisper-medium-en-myst | 8.94% | 8.91% | 0.03% |

#### KID-Whisper Small-EN Complete PTQ Results (aadel4/kid-whisper-small-en-myst)

*Protocol: HuggingFace pipeline, chunk_length_s=30, Beam-5, batch=4, EnglishTextNormalizer*

| Method | WER% | vs FP16 | Actual Size | Theor. Size | RTF |
|---|---|---|---|---|---|
| **FP8 naive (ours)** | **8.99%** | **-0.17%** | 0.303 GB | 0.303 GB | 0.015 |
| BnB INT8 | 9.01% | -0.15% | 0.266 GB | 0.303 GB | 0.027 |
| INT8 naive (ours) | 9.09% | -0.07% | 0.303 GB | 0.303 GB | 0.015 |
| Paper (FP16) | 9.11% | — | — | — | — |
| FP16 baseline | 9.16% | — | 0.450 GB | 0.450 GB | 0.013 |
| **BnB FP4** | **9.29%** | **+0.13%** | **0.173 GB** | **0.229 GB** | **0.014** |
| BnB NF4 | 9.42% | +0.26% | 0.173 GB | 0.229 GB | 0.015 |
| FP4 naive (ours) | 12.33% | +3.17% | 0.303 GB | 0.229 GB | 0.020 |
| INT4 naive (ours) | 24.81% | +15.65% | 0.303 GB | 0.229 GB | 0.020 |

#### KID-Whisper Medium-EN Complete PTQ Results (aadel4/kid-whisper-medium-en-myst)

*Protocol: HuggingFace pipeline, chunk_length_s=30, Beam-5, batch=4, EnglishTextNormalizer*

| Method | WER% | vs FP16 | Actual Size | Theor. Size | RTF |
|---|---|---|---|---|---|
| Paper (FP16) | 8.91% | — | — | — | — |
| **BnB FP4** | **8.93%** | **-0.01%** | **0.438 GB** | **0.514 GB** | **0.035** |
| FP16 baseline | 8.94% | — | 1.423 GB | 1.423 GB | 0.032 |
| FP8 naive (ours) | 9.12% | +0.18% | 0.817 GB | 0.817 GB | 0.038 |
| BnB INT8 | 9.18% | +0.24% | 0.767 GB | 0.817 GB | 0.057 |
| BnB NF4 | 9.19% | +0.25% | 0.438 GB | 0.514 GB | 0.036 |
| INT4 naive (ours) | 9.20% | +0.26% | 0.817 GB | 0.514 GB | 0.039 |
| FP4 naive (ours) | 9.22% | +0.28% | 0.817 GB | 0.514 GB | 0.049 |
| INT8 naive (ours) | 9.25% | +0.31% | 0.817 GB | 0.817 GB | 0.038 |

#### KID-Whisper Small-multilingual Complete PTQ Results (aadel4/kid-whisper-small-myst)

*Corrected protocol (Week 7 re-run of Week 6 experiments). Note: paper reports 11.80% WER for this model.*

| Method | WER% | vs FP16 | Actual Size | Theor. Size | RTF |
|---|---|---|---|---|---|
| **INT8 naive (ours)** | **9.67%** | **-0.24%** | 0.303 GB | 0.303 GB | 0.023 |
| BnB INT8 | 9.74% | -0.17% | 0.266 GB | 0.303 GB | 0.037 |
| **BnB NF4** | **9.76%** | **-0.15%** | **0.173 GB** | **0.229 GB** | **0.022** |
| BnB FP4 | 9.87% | -0.04% | 0.173 GB | 0.229 GB | 0.022 |
| FP16 baseline | 9.91% | — | 0.450 GB | 0.450 GB | 0.021 |
| FP8 naive (ours) | 9.92% | +0.01% | 0.303 GB | 0.303 GB | 0.023 |
| FP4 naive (ours) | 10.12% | +0.21% | 0.303 GB | 0.229 GB | 0.027 |
| INT4 naive (ours) | 11.38% | +1.47% | 0.303 GB | 0.229 GB | 0.024 |
| Paper (FP16, their protocol) | 11.80% | — | — | — | — |

**Small-multilingual key findings:**
- **ALL methods beat paper baseline (11.80%)** - even INT4 naive (11.38%) beats paper by 0.42%
- **Domain regularization strongest at INT8** (-0.24%): INT8 naive 9.67% best WER; multilingual vocabulary benefits most from quantization noise
- **NF4 wins over FP4 for multilingual** (9.76% vs 9.87%): Gaussian quantile grid optimal for near-Gaussian multilingual weights - opposite of EN model where FP4 wins
- **Old protocol underestimated multilingual quality** (Week 6: 11.45% → Week 7 corrected: 9.91%, 1.54% improvement from proper >30s chunk handling)
- **4-bit cliff much smaller than EN**: FP4 +0.21% vs Small-EN +3.17% (15× less severe); INT4 +1.47% vs Small-EN +15.65% (10× less severe)

#### Cross-Model Comparison (All Corrected Protocol)

| Method | Small-multilingual | Small-EN | Medium-EN |
|---|---|---|---|
| FP16 | 9.91% | 9.16% | 8.94% |
| Paper baseline | 11.80% | 9.11% | 8.91% |
| INT8 naive | **9.67%** | 9.09% | 9.25% |
| FP8 naive | 9.92% | **8.99%** | 9.12% |
| FP4 naive | 10.12% | 12.33% | 9.22% |
| INT4 naive | 11.38% | 24.81% | 9.20% |
| BnB INT8 | 9.74% | 9.01% | 9.18% |
| BnB NF4 | **9.76%** | 9.42% | 9.19% |
| BnB FP4 | 9.87% | **9.29%** | **8.93%** |

**Novel Grid Preference Reversal Finding:**
- Small-multilingual: NF4 beats FP4 by 0.11% (near-Gaussian weights → quantile grid wins)
- Small-EN: FP4 beats NF4 by 0.13% (fine-tuning shifts weights → proprietary grid wins)
- Medium-EN: FP4 beats NF4 by 0.26% (fine-tuning shift confirmed at scale)

The grid preference reversal is a signature of weight distribution shape. Multilingual training preserves near-Gaussian distribution; English-only fine-tuning creates specialized non-Gaussian patterns.

#### Week 7 Key Findings

- **BnB FP4 Medium-EN (8.93%) matches paper (8.91%) at 69.2% smaller size** - 0.438 GB vs 1.423 GB, practically identical WER. Best result of entire study
- **FP8 naive beats paper for Small-EN (8.99% < 9.11%)** - domain regularization from FP8 exponential quantization noise removes slight overfit, improving generalization
- **4-bit cliff much steeper for EN model than multilingual**:
  - Small multilingual: FP4 naive +0.34%, INT4 naive +1.63%
  - Small-EN: FP4 naive +3.17%, INT4 naive +15.65% (10× steeper)
  - English-only fine-tuning creates highly specialized weights needing >4-bit precision
- **BnB FP4 beats BnB NF4 for EN models** (Small: 9.29% vs 9.42%, Medium: 8.93% vs 9.19%) - English fine-tuning shifts weight distribution away from Gaussian, making NF4 Gaussian quantile grid suboptimal vs FP4 proprietary grid
- **Medium more robust than Small at 4-bit**: BnB FP4 Medium +0.01% vs Small +0.13% - larger capacity absorbs 4-bit quantization noise better
- **4-bit grid reversal at scale**: Small-EN FP4 beats INT4 by 12.48% (exponential essential at 244M). Medium-EN INT4 (9.20%) marginally beats FP4 (9.22%) - grid irrelevant at 769M. INT4 also 25% faster RTF (0.039 vs 0.049). Crossover between 244M and 769M params
- **Complete deployment recommendations**: Edge: BnB FP4 Small 9.29% 0.173 GB. Balanced: FP8 naive Small 8.99% 0.303 GB. Best: BnB FP4 Medium 8.93% 0.438 GB (matches paper at 69.2% smaller)
- **K-means codebook quantization FAILS on English-only fine-tuned models** (script 19_kmeans_quantization.py, corrected protocol on Small-EN):
  - kmeans_k16 (200): 519.50% catastrophic; kmeans_k32 (200): 124.97% catastrophic
  - **Small-EN kmeans_k256 (full): 16.01%** - 7% worse than fixed FP8 (8.99%) at same storage
  - **Medium-EN kmeans_k16 (full): 11.60%** - 2.4% worse than fixed FP4 (9.22%)
  - **Medium-EN kmeans_k32 (full): 9.73%** - between fixed INT4/INT8, functional at 5-bit
  - **Medium-EN kmeans_k256 (full): 9.16%** - matches fixed FP8 (9.12%) within noise! Model capacity rescues learned codebooks at 8-bit for larger models
  - **Logarithmic convergence**: Medium k-means WER asymptotes to fixed FP8 as k→∞. Diminishing returns after k=32
  - vs Small-multilingual kmeans_k16 (full 3972 chunks): 12.70% WER (acceptable)
  - Root cause: EN fine-tuning creates highly non-Gaussian weight patterns with heavy tails; k-means with 50K sample (8.5% of weights) cannot capture EN's specialized sparse tail structure; K centroids miss critical outlier weights that carry EN-specific children's speech adaptations
  - Fixed exponential grids (FP4 E2M1: 12.33%) and quantile grids (BnB NF4: 9.42%) succeed because they don't depend on sampling and preserve outlier structure
  - **New finding**: Learned codebook quantization requires near-Gaussian weight distributions; fine-tuned domain-specific models violate this assumption and require hand-designed structural grids

---


### Supplementary: K-means Codebook Quantization Attempts (Week 7)

*Method: MiniBatchKMeans on per-channel normalized weights, k centroids as learned quantization levels. Compared with fixed-grid alternatives on kid-whisper-small-en-myst.*

**Small-EN complete k-means results (full 3972 chunks unless noted):**

| Method | Levels | Storage | Test Set | WER% | vs FP16 (9.16%) | Status |
|---|---|---|---|---|---|---|
| kmeans_k16 (learned) | 16 | 0.192 GB (true 4-bit) | 200 chunks | 519.50% | catastrophic | ❌ Fails |
| kmeans_k32 (learned) | 32 | 0.303 GB (int8) | 200 chunks | 124.97% | catastrophic | ❌ Fails |
| kmeans_k256 (learned) | 256 | 0.303 GB (int8) | **3972 chunks** | **16.01%** | **+6.85%** | ❌ Poor |

**Medium-EN complete k-means results (full 3972 chunks):**

| Method | Levels | Storage | WER% | vs FP16 (8.94%) | Status |
|---|---|---|---|---|---|
| kmeans_k16 (learned) | 16 | 0.464 GB | 11.60% | +2.66% | ⚠️ 4-bit works but loses to fixed |
| kmeans_k32 (learned) | 32 | 0.817 GB | 9.73% | +0.79% | ✅ Between INT4 and INT8 fixed |
| kmeans_k256 (learned) | 256 | 0.817 GB | **9.16%** | **+0.22%** | ✅ Matches fixed FP8 (9.12%) |

**Logarithmic convergence of learned codebook toward fixed FP8:**
- k=16 (4-bit):   11.60% (+2.66% vs FP16)
- k=32 (5-bit):    9.73% (+0.79% vs FP16)
- k=256 (8-bit):   9.16% (+0.22% vs FP16)
- FP8 fixed (8-bit): 9.12% (+0.18% vs FP16, asymptotic limit)

**Small-multilingual complete k-means results (full 3972 chunks):**

| Method | Levels | Storage | WER% | vs FP16 (9.91%) | Status |
|---|---|---|---|---|---|
| kmeans_k16 (learned) | 16 | 0.192 GB (true 4-bit) | 16.20% | +6.29% | ❌ Too few levels |
| kmeans_k32 (learned) | 32 | 0.303 GB (int8) | 10.22% | +0.31% | ✅ Functional |
| **kmeans_k256 (learned)** | 256 | 0.303 GB (int8) | **9.61%** | **-0.30%** | ✅ **BEATS all fixed grids!** |

**Multilingual k-means logarithmic convergence:**
- k=16 (4-bit):   16.20% (+6.29% vs FP16)  ← too few centroids
- k=32 (5-bit):   10.22% (+0.31% vs FP16)  ← functional
- k=256 (8-bit):   9.61% (-0.30% vs FP16)  ← BEATS FP16 and all fixed grids!

**Cross-model k-means scan (kmeans_k256 at 0.303 GB):**

| Model | Weights | WER% | vs Fixed FP8 | Interpretation |
|---|---|---|---|---|
| Small-multilingual (244M) | Near-Gaussian | **9.61%** | **-0.31%** | ✅ Beats fixed FP8 (9.92%) |
| Medium-EN (769M) | Non-Gaussian, absorbed | 9.16% | +0.04% | ✅ Matches fixed FP8 (9.12%) |
| Small-EN (244M) | Non-Gaussian, fragile | 16.01% | +7.02% | ❌ Fails vs fixed FP8 (8.99%) |

**Critical validation: kmeans_k256 on Small-multilingual (9.61%) becomes the NEW BEST method overall for this model**, beating INT8 naive (9.67%) and even the FP16 baseline (9.91%). This confirms the theoretical hypothesis: when weights are truly near-Gaussian, learned codebooks discover better quantization levels than any hand-designed grid.

### Supplementary: Negative Results - Percentile Clipping and Low-Bit Quantization

To establish the complete boundaries of viable quantization for KID-Whisper models, we systematically documented all failed methods. This section presents percentile clipping (pct) results and sub-4-bit results across all three variants.

#### Percentile Clipping (99.9) Results - All Three Variants

*Percentile clipping truncates weights above the 99.9th percentile before quantization. Effective for pre-trained models (Rule 3), harmful for fine-tuned models (Rule 5).*

| Method | Small-EN Naive | Small-EN pct | Small-multi Naive | Small-multi pct | Medium-EN Naive | Medium-EN pct |
|---|---|---|---|---|---|---|
| INT8 | 9.09% | **29.68% (+20.59%)** | 9.67% | 14.43% (+4.76%) | 9.25% | 9.93% (+0.68%) |
| FP8 | 8.99% | **32.19% (+23.20%)** | 9.92% | 16.01% (+6.09%) | 9.12% | 10.27% (+1.15%) |
| INT4 | 24.81% | **77.53% (+52.72%)** | 11.38% | 33.64% (+22.26%) | 9.20% | 10.05% (+0.85%) |
| FP4 | 12.33% | **40.92% (+28.59%)** | 10.12% | 19.90% (+9.78%) | 9.22% | 10.30% (+1.08%) |

**Percentile clipping damage scaling with model capacity:**
- Small-EN (244M, fine-tuned): +20% to +52% damage - percentile devastating
- Small-multilingual (244M, multilingual): +5% to +22% damage - significantly harmful
- Medium-EN (769M, fine-tuned): +0.68% to +1.15% damage - essentially free
- **Novel finding**: Model capacity nearly eliminates percentile damage. At 769M+ params, percentile clipping becomes essentially cost-free due to weight redundancy

**Cross-model damage ratios:**
- Small-EN pct damage is 2-2.3× worse than Small-multilingual (EN fine-tuning creates fragile outliers)
- Medium-EN pct damage is 30-62× LESS than Small-EN (model capacity absorbs outlier destruction)

#### Sub-4-bit Quantization Results (Small-EN Complete + Medium-EN INT2)

*Documents the lower boundary of viable quantization. All methods below 4-bit are unusable.*

**Small-EN low-bit results (full 3972 chunks):**

| Method | WER% | RTF | Failure Mode |
|---|---|---|---|
| INT2 naive | 928.25% | 0.132 | ❌ Catastrophic hallucination |
| INT2 pct | 100.00% | 0.124 | ❌ Empty output |
| INT1 naive | 200.92% | 0.125 | ❌ 1-bit + linear grid useless |
| FP2 naive | 885.83% | 0.156 | ❌ No-zero hallucination |
| FP2 pct | 636.16% | 0.155 | ❌ Still hallucinates |
| FP1 naive | 137.65% | 0.150 | ❌ "Least worst" 1-bit |

**Medium-EN INT2 naive verification:**
- Medium-EN INT2 naive: 100.00% WER (produces empty/minimal output)
- Contrast: Small-EN INT2 naive 928% (hallucinates), Medium-EN 100% (silences)
- **Model capacity affects failure mode but not viability**: 2-bit is unusable at both 244M and 769M scales

**Key findings on low-bit boundaries:**
1. **INT2 naive worst on Small-EN** (928%) vs FP2 naive (885%) - EN specialized weights need >2-bit precision
2. **FP1 naive least-worst 1-bit** (137%) - exponential single-bit somehow captures more than sign-only INT1 (200%)
3. **Percentile clipping LIMITS hallucination** at 2-bit: fp2_pct 636% < fp2_naive 885% - because less signal remains to hallucinate with
4. **Medium capacity does not rescue 2-bit** - both models 100%+ WER, just different failure modes

**Complete quantization spectrum documented (Small-EN):**

| Bit width | Best Naive | Best pct | Best BnB | Verdict |
|---|---|---|---|---|
| 16-bit | FP16: 9.16% | — | — | ✅ Baseline |
| 8-bit | FP8: 8.99% | INT8: 29.68% | BnB INT8: 9.01% | ✅ Best naive/BnB, ❌ pct |
| 4-bit | FP4: 12.33% | FP4: 40.92% | BnB FP4: 9.29% | ⚠️ Naive cliff, ✅ BnB, ❌ pct |
| 2-bit | FP2: 885% | INT2: 100% | — | ❌ All fail |
| 1-bit | FP1: 137% | — | — | ❌ All fail |

**Practical minimum viable bit-width for children's ASR: 4-bit** (via BnB NF4/FP4 with true nibble packing)

**Cross-model comparison at same bit-width:**

| Bit width | Method | Small-EN WER | Medium-EN WER | Cross-model gap |
|---|---|---|---|---|
| 4-bit | BnB FP4 (fixed) | 9.29% | 8.93% | -0.36% |
| 4-bit | BnB NF4 (fixed) | 9.42% | 9.19% | -0.23% |
| 4-bit | FP4 naive (fixed) | 12.33% | 9.22% | -3.11% |
| 4-bit | kmeans_k16 (learned) | 519.50% (200) | 11.60% | catastrophic vs functional |
| 5-bit | kmeans_k32 (learned) | 124.97% (200) | 9.73% | catastrophic vs competitive |
| 8-bit | FP8 naive (fixed) | 8.99% | 9.12% | +0.13% |
| 8-bit | INT8 naive (fixed) | 9.09% | 9.25% | +0.16% |
| 8-bit | kmeans_k256 (learned) | 16.01% | 9.16% | -6.85% |

**Head-to-head at 8-bit for Medium-EN (0.817 GB):**
- FP8 naive (fixed exp):         9.12% WER
- kmeans_k256 (learned):         9.16% WER (+0.04%, essentially equivalent)
- INT8 naive (fixed linear):     9.25% WER

**Key k-means findings:**
1. **k=256 learned codebook matches fixed FP8 for Medium-EN** (9.16% vs 9.12%) - proving learned codebooks CAN work when given sufficient model capacity and enough centroids
2. **Small-EN k-means catastrophically fails at every k** - 244M params insufficient to absorb sampling noise from EN fine-tuned distributions
3. **Medium-EN k-means at 4-bit still loses by 2.66%** vs fixed FP4 - grid choice matters even for larger models at 4-bit
4. **Multilingual > EN for k-means resilience** (multilingual kmeans_k16: 12.70% acceptable, EN k=16: 519% catastrophic)

**Novel thesis contribution:** Learned codebook quantization (k-means, DeepCompression-style) viability requires the combination of:
- Near-Gaussian weight distribution (fine-tuning creates non-Gaussian sharp distributions)
- Sufficient model capacity to absorb sampling noise (244M insufficient, 769M rescues at 8-bit)
- Enough centroids (k=16, k=32 insufficient for EN; k=256 sufficient for Medium)

Fine-tuned domain-specific models require hand-designed structural grids (FP4 E2M1) or mathematical quantile grids (NF4) that don't depend on stochastic sampling. This is the first documented case of k-means quantization failure characterization across model sizes for fine-tuned children's ASR models.


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

## 📊 Progress Tracker

| Week | Activity | Status |
|------|----------|--------|
| Week 1 | Literature survey: KID-Whisper, XLSR, XLS-R, MyST & CSLU datasets. GitHub repo setup. | ✅ Done |
| Week 2 | MyST corpus filtering (KID-Whisper methodology), baseline WER = 14.46% confirmed, evaluation pipeline built. | ✅ Done |
| Week 3 | Full compression suite: bitsandbytes PTQ (INT8/NF4/FP4), our PyTorch absmax PTQ (all bit widths), layer pruning, calibrated PTQ (percentile INT4=19%, percentile INT2=100%). Key findings: scheme > bit-width; percentile clipping effective only at 4-bit+; 4-bit is minimum viable with calibration; activation-aware scaling fails for children's speech. | ✅ Done |
| Week 4 | Floating point quantization (FP8 E4M3 naive 14.45%, FP8 E4M3+pct 13.98%, FP8 E5M2 100%, FP4 E2M1 naive 15.87%, FP4 E2M1+pct 14.03%). Key findings: mantissa bits > exponent range; percentile universally beneficial for all formats; FP8 E4M3+pct is best result overall; FP4 E2M1+pct confirms bitsandbytes FP4 internal grid. Grounded in ACIQ (Banner et al. 2019). | ✅ Done |
| Week 5 | Custom FP4 E2M1+pct + LoRA fine-tuning. Optimal: r=16 q+v 500 steps → 13.77% WER (-0.26% vs zero-shot). Key findings: exposure bias limits training to ~500 steps; use_reentrant=False required for gradient checkpointing; larger LoRA (r=32+fc1) worse due to 4× amplification. Scheduled sampling (embedding mixing) did not help. | ✅ Done |
| Week 6 | KID-Whisper multilingual Small + Medium PTQ (old protocol). Small-myst best: INT8 naive 11.35% / BnB NF4 11.44% 0.173 GB. Medium-en best: BnB FP4 10.98% (beats FP16!). Key rules: naive > pct for fine-tuned; INT8/FP8 lossless for 769M; grid irrelevant at scale; FP2 no-zero → 713% WER. | ✅ Done |
| Week 7 | Corrected protocol + K-means codebook. Small-EN best: FP8 naive 8.99% beats paper. Medium-EN best: BnB FP4 8.93% matches paper at 0.438 GB. K-means: Small-EN catastrophic all k; Medium-EN kmeans_k256 9.16% matches FP8 (fixed grids still win at 4-bit). Novel finding: learned codebooks need Gaussian distribution + model capacity + enough centroids. | ✅ Done |

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

*Last updated: Week 7 (Corrected protocol + Small-EN + Medium-EN complete) - August 2026*




# Vanilla Whisper PTQ: Comprehensive Quantization Study

## Overview

Post-training quantization (PTQ) sweep applied to vanilla (non-fine-tuned)
OpenAI Whisper checkpoints -- Small.en, Small (multilingual), and Medium.en --
using the corrected evaluation protocol (see below). This complements the
KID-Whisper fine-tuned model results by isolating the effect of domain
fine-tuning: same architectures, same PTQ methods, same test set, only the
training regime differs.

Script: `scripts/20_vanilla_medium_ptq.py`

## Evaluation Protocol

Uses `transformers.pipeline("automatic-speech-recognition", ...)` with
`chunk_length_s=30`, `batch_size=4`, `num_beams=5`, `do_sample=False`, built
from a generator of file paths rather than manually loaded/padded audio
tensors. This is the corrected protocol (matches the fix applied to
`scripts/18_kid_whisper_ptq.py`) that resolved a truncated-chunk problem
present in an earlier manual `processor()` + `model.generate()` loop. WER is
computed with `EnglishTextNormalizer` (Radford et al., 2022) [1] and `jiwer`,
and cross-verified with `calculate_wer.py` on every run.

Test set: MyST children's speech corpus, 3,972 concatenated 30-second test
chunks (same set used throughout the thesis for the KID-Whisper experiments).

## Paper Reference Baselines

Zero-shot (vanilla, no fine-tuning) WER on the MyST test set, from Attia et
al. (2024), KID-Whisper: Towards Bridging the Performance Gap in Automatic
Speech Recognition for Children's Speech via Domain Adaptation [2], Table 3
and Table 4:

| Model | Paper zero-shot WER on MyST |
|---|---|
| Small (ML) | 14.06% |
| Small.en | 13.93% |
| Medium (ML) | 12.90% |
| Medium.en | 13.23% |

## Reproduction of Paper Baselines (this work)

FP16 baseline WER obtained under the corrected protocol, full 3,972-chunk
test set, compared against the paper's zero-shot numbers [2]:

| Model | Our FP16 WER | Paper WER [2] | Gap |
|---|---|---|---|
| Small.en | 14.51% | 13.93% | +0.58 pp |
| Small (ML) | 14.82% | 14.06% | +0.76 pp |
| Medium.en | 13.45% | 13.23% | +0.22 pp |

All three reproductions land within 1 percentage point of the published
zero-shot numbers, confirming the corrected evaluation protocol is sound.
Prior to the protocol correction, Medium.en's manually-evaluated FP16
baseline measured 14.73% (a 1.50 pp gap to paper), so the pipeline-based
correction closed roughly 85% of that gap.

## Quantization Methods Tested

Naive (absmax) and percentile-clipped (99.9th percentile) variants at INT8,
INT4, FP8 (E4M3), and FP4 (E2M1); production `bitsandbytes` INT8/NF4/FP4 [3];
and true 4-bit nibble-packed FP4/INT4 (0.5 bytes/weight, verified
bit-identical in accuracy to the lookup-table equivalents, see Finding 3).

## Results: Whisper Small.en (complete sweep)

| Method | WER | Δ vs FP16 | Size (GB) | Status |
|---|---|---|---|---|
| BnB FP4 [3] | 14.52% | +0.01 pp | 0.173 | clean |
| BnB INT8 [3][4] | 14.45% | -0.06 pp | 0.266 | clean |
| FP16 baseline | 14.51% | -- | 0.450 | -- |
| INT8 naive | 14.64% | +0.13 pp | 0.303 | clean |
| FP8 naive | 14.89% | +0.38 pp | 0.303 | clean |
| BnB NF4 [3] | 15.06% | +0.55 pp | 0.173 | clean |
| FP4 naive | 19.28% | +4.77 pp | 0.303 | degraded |
| FP4 packed (nibble) | 19.28% | +4.77 pp | 0.192 | degraded |
| INT8 percentile99.9 | 54.61% | +40.10 pp | 0.303 | **collapsed** |
| FP8 percentile99.9 | 55.56% | +41.05 pp | 0.303 | **collapsed** |
| INT4 naive | 56.65% | +42.14 pp | 0.303 | **collapsed** |
| INT4 packed (nibble) | 56.65% | +42.14 pp | 0.192 | **collapsed** |
| FP4 percentile99.9 | 71.15% | +56.64 pp | 0.303 | **collapsed** |
| INT4 percentile99.9 | 115.72% | +101.21 pp | 0.303 | **total breakdown** |

## Results: Whisper Small (Multilingual) (complete sweep)

| Method | WER | Δ vs FP16 | Size (GB) | Status |
|---|---|---|---|---|
| BnB NF4 [3] | 14.53% | -0.29 pp | 0.173 | clean |
| FP16 baseline | 14.82% | -- | 0.450 | -- |
| INT8 naive | 15.09% | +0.27 pp | 0.303 | clean |
| BnB INT8 [3][4] | 15.38% | +0.56 pp | 0.266 | clean |
| FP8 naive | 15.59% | +0.77 pp | 0.303 | clean |
| BnB FP4 [3] | 16.34% | +1.52 pp | 0.173 | clean |
| FP4 naive | 19.47% | +4.65 pp | 0.303 | degraded |
| INT4 naive | 37.55% | +22.73 pp | 0.303 | **collapsed** |
| INT8 percentile99.9 | 68.63% | +53.81 pp | 0.303 | **collapsed** |
| FP8 percentile99.9 | 73.06% | +58.24 pp | 0.303 | **collapsed** |
| FP4 percentile99.9 | 107.76% | +92.94 pp | 0.303 | **total breakdown** |
| INT4 percentile99.9 | 189.11% | +174.29 pp | 0.303 | **total breakdown** |

## Results: Whisper Medium.en (INT sweep complete, corrected protocol)

| Method | WER | Δ vs FP16 | Size (GB) | Status |
|---|---|---|---|---|
| FP16 baseline | 13.45% | -- | 1.423 | -- |
| INT8 naive | 13.58% | +0.13 pp | 0.817 | clean |
| INT4 naive | 14.10% | +0.65 pp | 0.817 | clean |
| INT8 percentile99.9 | 15.65% | +2.20 pp | 0.817 | clean |
| INT4 percentile99.9 | 15.90% | +2.45 pp | 0.817 | clean |

FP8, FP4, `bitsandbytes` (INT8/NF4/FP4), and true nibble-packed (FP4/INT4)
methods for Medium.en are pending under the corrected protocol.

## Key Findings

### Finding 1: Percentile clipping triggers repetition-loop hallucination
### collapse at the 244M-parameter scale, but not at 769M -- a clean
### capacity-threshold effect, independent of quantization grid

Across both Small models (244M parameters), every percentile-99.9-clipped
configuration tested collapsed into repetition loops -- predictions running
to thousands of characters with the same phrase repeated dozens of times
(verified by manual inspection of prediction files; corroborated by
inference time roughly doubling to tripling on collapsed runs relative to
naive runs at the same bit-width, consistent with degenerate generations
running toward `max_new_tokens` on affected chunks). This held across every
bit-width and grid type tested:

| Config | Small.en WER | Small (ML) WER | Medium.en WER |
|---|---|---|---|
| INT8 percentile99.9 | 54.61% | 68.63% | 15.65% |
| FP8 percentile99.9 | 55.56% | 73.06% | (pending) |
| FP4 percentile99.9 | 71.15% | 107.76% | (pending) |
| INT4 percentile99.9 | 115.72% | 189.11% | 15.90% |

Naive (unclipped absmax) quantization at the same bit-widths stayed stable
in every case at both 8-bit and, for Medium.en, even 4-bit. At the Medium.en
scale (769M parameters), the identical configurations that produced total
breakdown (WER exceeding 100%) on Small models never exceeded a 2.45
percentage-point cost, confirmed under the corrected evaluation protocol.

This isolates the failure to the percentile-clipping mechanism interacting
with model capacity, rather than to bit-width or grid type alone: FP8 naive
and INT8 naive (same 8-bit budget, different grids) both stayed clean at
every model scale, while FP8 percentile and INT8 percentile (same budget,
clipping added) both collapsed to comparable severity on both Small models.
The multilingual Small model collapsed consistently worse than the
English-only Small model at every clipped configuration, and the gap between
the two widened sharply as bit-width dropped (roughly 14-18 percentage
points apart at 8-bit, 73-81 percentage points apart at 4-bit) -- suggesting
multilingual weight-sharing further reduces the stability margin already
strained by percentile clipping at this parameter count.

### Finding 2: Naive INT4 collapses at the Small scale while naive FP4
### degrades gracefully, isolating grid shape rather than bit count as the
### determining factor -- and both become irrelevant at Medium scale

At 4-bit naive quantization, INT4 (evenly-spaced integer grid, -7 to +7)
collapsed into the same repetition-loop pathology as percentile clipping
(56.65% WER on Small.en, 37.55% on Small ML), while FP4 (E2M1
floating-point grid: 0, +/-0.5, +/-1, +/-1.5, +/-2, +/-3, +/-4, +/-6)
degraded gracefully with no collapse on either Small variant (19.28% WER
on Small.en, 19.47% on Small ML -- nearly identical across English-only
and multilingual training). Since both formats use the same nominal 4-bit
budget and the same naive (unclipped) calibration, the difference is
attributable to the shape of the quantization grid: FP4's wider dynamic
range and non-uniform spacing preserves enough representational capacity
to avoid the collapse INT4's uniform, narrow-range grid triggers at this
model scale. FP4's protection also holds partially even under percentile
clipping -- FP4-percentile was less severe than INT4-percentile on both
Small models (71.15% vs 115.72% on Small.en; 107.76% vs 189.11% on
Small ML) -- though not enough to prevent eventual breakdown when both
stresses (4-bit and clipping) are combined.

At Medium.en (769M parameters), this distinction disappears entirely: INT4
naive quantization stays clean (14.10% WER, +0.65 pp), confirming that grid
shape only matters below some capacity threshold; once a model has enough
parameters, both bit-width and grid type stop being meaningfully
predictive of failure.

### Finding 3: True bit-packing preserves WER exactly relative to
### lookup-table quantization, isolating storage format from accuracy

`fp4_packed` (true nibble packing, 0.5 bytes/weight, 0.192GB) produced WER
identical to `fp4_naive` (lookup-table based, 0.303GB) at 19.28% on
Small.en. Likewise, `int4_packed` (0.192GB) matched `int4_naive` (0.303GB)
exactly at 56.65%, including reproducing the same collapse behavior. This
confirms the nibble-packing implementation (`pack_nibbles`/`unpack_nibbles`)
is numerically correct -- it changes only the storage representation, not
the dequantized values used in the forward pass -- and demonstrates that
the substantial gap between naive/packed FP4 (19.28%) and `bitsandbytes`
FP4 (14.52%, see Finding 4) on Small.en is entirely attributable to
`bitsandbytes`' calibration and per-block scaling strategy, not to the
4-bit budget or packing format itself.

### Finding 4: BnB FP4 and BnB NF4 [3] swap ranking between English-only
### and multilingual Small models, extending a pattern seen on fine-tuned
### models to vanilla, non-fine-tuned weights

On Small.en, `bitsandbytes` FP4 quantization [3] outperformed NF4 (14.52%
vs 15.06% WER, both at 0.173GB). On Small (ML), the ranking inverted: NF4
outperformed FP4 (14.53% vs 16.34% WER, same size). Both `bitsandbytes`
4-bit variants substantially outperformed the custom naive/packed FP4
implementation (19.28-19.47% WER) at the same model scale, underscoring
that `bitsandbytes`' double-quantization and per-block calibration [3]
materially improve on naive absmax-based 4-bit quantization regardless of
which grid wins.

The FP4-over-NF4 pattern on Small.en mirrors an equivalent finding from the
fine-tuned KID-Whisper English models (BnB FP4 outperforming BnB NF4,
attributed there to fine-tuning shifting weight distributions away from
the Gaussian assumption NF4 [3] is optimized for). The reversal on Small
(ML) suggests this is not purely a fine-tuning effect: a vanilla,
non-fine-tuned English-only model's weight distribution already diverges
from the Gaussian prior NF4 assumes, sufficiently to favor FP4's grid,
while the vanilla multilingual model's weights -- spread across many
languages rather than specialized to English lexical/phonetic patterns --
apparently remain closer to the Gaussian distribution NF4 is designed for
[3], preserving NF4's theoretical advantage in that setting.

### Finding 5: BnB INT8 essentially matches or beats FP16 baseline accuracy
### on Small.en specifically, but not on the multilingual variant

`bitsandbytes` INT8 [3][4] achieved 14.45% WER on Small.en, marginally
better than the FP16 baseline itself (14.51%), at 0.266GB versus FP16's
0.450GB (a 41% size reduction). This did not hold on Small (ML), where
BnB INT8 (15.38%) landed worse than both the FP16 baseline (14.82%) and
naive INT8 (15.09%), suggesting `bitsandbytes`' outlier-isolation
calibration [4] is comparatively better tuned for English-only weight
distributions than multilingual ones at this model scale.

## References

[1] Radford, A., Kim, J. W., Xu, T., Brockman, G., McLeavey, C., &
    Sutskever, I. (2022). Robust Speech Recognition via Large-Scale Weak
    Supervision. arXiv:2212.04356.

[2] Attia, S. et al. (2024). KID-Whisper: Towards Bridging the Performance
    Gap in Automatic Speech Recognition for Children's Speech via Domain
    Adaptation. AAAI 2024. (MyST zero-shot baselines: Table 3, Table 4.)

[3] Dettmers, T., Pagnoni, A., Holtzman, A., & Zettlemoyer, L. (2023).
    QLoRA: Efficient Finetuning of Quantized LLMs. arXiv:2305.14314. (NF4
    data type, Appendix E; double quantization, Section 3.)

[4] Dettmers, T., Lewis, M., Belkada, Y., & Zettlemoyer, L. (2022).
    LLM.int8(): 8-bit Matrix Multiplication for Transformers at Scale.
    Advances in Neural Information Processing Systems 35 (NeurIPS 2022).
