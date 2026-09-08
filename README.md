# 🎓 MTech Thesis - ASR Research

> **Goal:** Efficient ASR System for Children's Speech
> **Duration:** 1 Year | **Started:** May 2026

---

## 📌 Research Direction

Beginning from a broad literature survey of self-supervised and weakly-supervised speech foundation models (Whisper, XLSR, XLS-R, wav2vec 2.0) across children's speech, low-resource languages, and domain adaptation strategies, the research direction narrowed to a focused, well-scoped goal: building an **efficient, deployable ASR system for children's speech**. This is pursued by systematically compressing Whisper models - quantization and pruning, individually and combined - across the full parameter scale from 39M to 769M+, evaluated under a single rigorous, corrected protocol on the MyST children's speech corpus, spanning both vanilla (non-fine-tuned) and domain-fine-tuned checkpoints to isolate the effect of each compression technique cleanly.


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
| train | 57,687 | 136.9h | all 4 filters + Filter 5 (>30s discard) | ✅ Final |
| development | 9,017 | 21.1h | all 4 filters + Filter 5 (>30s discard) | ✅ Final |
| test | 10,415 | 26.12h | all 4 filters only - **Filter 5 (>30s discard) NOT applied** | ✅ Final |

- WER flagging applied to all splits: train (2,823 flagged), dev (1,046 flagged), test (1,067 flagged) - scripts: `generate_flags_partition.py`, `clean_flags_partition.py`
- Filter 5 (>30s discard) added to match KID-Whisper methodology, applied to **train and development only** (per `preprocess_myst.py`'s `PARTITIONS = ["train", "development"]`) - confirmed by max duration: train=30.00s exactly, dev=29.95s
- **Correction (identified during later dataset verification work):** the original framing above ("test ... via chunking") was inaccurate. Filter 5 was never run on the test partition - `filtered_test` retains utterances up to 111.36s, with 2.70% (281 of 10,415) exceeding 30s. Concatenation does not remove these; per the buffer-guard logic in `preprocess_myst.py` Phase 2, a single utterance already >30s passes through as its own standalone chunk, unmodified. This is the direct origin of the 281-chunk (7.07% of `concatenated_test`) truncation issue discovered and fixed in Week 8-10 (see Dataset Composition & Preprocessing Verification section above, and thesis Chapter 2 §2.2.5-2.2.6 for the full mechanism and five-point consistency verification)
- KID-Whisper reports train 132.5h / dev 21h - our results (136.9h / 21.1h) are close; remaining gap due to FP16 vs FP32 flagging difference
- Concatenated filtered test utterances into 30-second chunks for Whisper inference: 3,972 chunks
- `myst_train_text.txt` contains 59,842 lines - matches filtered train count exactly (cross-verified)

---

### ✅ Week 3 | Dataset Composition & Preprocessing Verification

**Script:** `23_dataset_filtering_report.py` (independently verifies corpus statistics via direct file scanning, cross-referenced against `preprocess_myst.py`'s actual filtering/concatenation logic)

#### Corpus Statistics by Split

| Split | #Utterances | Hours | Avg Dur (s) | Avg Words | % Short (<3 words) | % Long (>30s) | Unique Sessions |
|---|---|---|---|---|---|---|---|
| filtered_train | 57,687 | 136.88 | 8.54 | 17.30 | 0.00% | 0.00% | 2,618 |
| filtered_dev | 9,017 | 21.09 | 8.42 | 17.11 | 0.00% | 0.00% | 435 |
| filtered_test | 10,415 | 26.12 | 9.03 | 18.26 | 0.00% | 2.70% | 504 |
| concatenated_test | 3,972 | 26.12 | 23.67 | 47.88 | 0.00% | 7.07% | 504 |

*`%<3wds` reads 0.00% across all splits because the <3-word filter (Filter 4 in `preprocess_myst.py`) was already applied upstream when `filtered/` was generated - `min=3` words confirms nothing below threshold remains. This is the report correctly detecting an already-satisfied filter, not a null result.*

#### Concatenation Algorithm (from `preprocess_myst.py`, Phase 2)

Utterances within each session are buffered and flushed once the running duration crosses 30s:

```
for utterance in session_utterances (in order):
    if buffer_duration + utterance.duration > 30s AND buffer non-empty:
        flush buffer now (WITHOUT this utterance)
    add utterance to buffer
    if buffer_duration >= 30s:
        flush buffer now
```

Critically, the "flush before" check only fires when the buffer already has content. If a single utterance is itself already >30s, the buffer is empty when it arrives, so nothing flushes first - the long utterance passes through as its own standalone chunk, completely unmodified. This is the direct mechanism behind the >30s chunks in `concatenated_test`.

**Note:** `preprocess_myst.py`'s `PARTITIONS = ["train", "development"]` and its `__main__` block explicitly skips Phase 2 for train/dev (`# phase2_concatenate() - skipped - not needed for train/dev`). Only the **test** partition was ever concatenated (via a separate run/script), which is why Wanda calibration in `22_wanda_pruning.py` correctly sources from `data/filtered/train` rather than a nonexistent `data/concatenated/train`.

#### Five-Point Consistency Verification

Cross-checking `filtered_test` against `concatenated_test` confirms the concatenation pipeline preserved all content correctly, with zero loss or cross-session merging:

1. **Total hours preserved exactly**: 26.12h = 26.12h (concatenation is pure regrouping, no audio lost)
2. **Session count preserved exactly**: 504 = 504 (concatenation groups *within* a session, never merges *across* sessions)
3. **Overshoot chunk count matches long-utterance count**: 2.70% of 10,415 ≈ 281 chunks ≈ 7.07% of 3,972 (the >30s chunks in `concatenated_test` are exactly the >30s utterances in `filtered_test`, passed through unchanged per the buffer-guard mechanism above)
4. **Max duration identical pre/post concatenation**: 111.36s = 111.36s (the longest chunk in `concatenated_test` IS the longest raw utterance in `filtered_test`, unmodified)
5. **Average duration arithmetic checks out**: 10,415 / 3,972 = 2.62 utterances/chunk; 2.62 × 9.03s (avg utterance duration) ≈ 23.67s (matches the reported `concatenated_test` average exactly)

This independently confirms the **281-chunk (7.07%) truncation issue** originally discovered manually in Week 8-10 - now verified numerically via a completely separate measurement method (direct corpus scanning vs. the original manual chunk-length inspection), strengthening confidence in the corrected evaluation protocol's necessity and correctness.

---

### ✅ Week 4-6 | Vanilla Whisper PTQ: Comprehensive Quantization Study

#### Overview

Post-training quantization (PTQ) sweep applied to vanilla (non-fine-tuned)
OpenAI Whisper checkpoints spanning the full model family -- Tiny.en, Tiny
(multilingual), Base.en, Base (multilingual), Small.en, Small
(multilingual), Medium.en, Medium (multilingual), Large-v2, and Large-v3 --
using the corrected evaluation protocol (see below). This complements the
KID-Whisper fine-tuned model results by isolating the effect of domain
fine-tuning: same architectures, same PTQ methods, same test set, only the
training regime differs. All ten models are now fully swept, spanning
roughly a 40x range in parameter count (39M to 1,550M).

Scripts: `scripts/20_vanilla_medium_ptq.py` (Tiny.en, Tiny ML, Base.en,
Base ML, Small.en, Small ML, Medium.en, Medium ML -- every model with an
English-only/multilingual pair) and `scripts/27_vanilla_whisper_large_ptq.py`
(Large-v3 and Large-v2, kept separate since neither Large version has an
English-only variant and both need a smaller pipeline batch size to fit
8GB VRAM).

#### Evaluation Protocol

Uses `transformers.pipeline("automatic-speech-recognition", ...)` with
`chunk_length_s=30`, `num_beams=5`, `do_sample=False`, built from a
generator of file paths rather than manually loaded/padded audio tensors.
This is the corrected protocol (matches the fix applied to
`scripts/18_kid_whisper_ptq.py`) that resolved a truncated-chunk problem
present in an earlier manual `processor()` + `model.generate()` loop. WER is
computed with `EnglishTextNormalizer` (Radford et al., 2022) [10] and
`jiwer`, and cross-verified with `calculate_wer.py` on every run.

Test set: MyST children's speech corpus, 3,972 concatenated 30-second test
chunks (same set used throughout the thesis for the KID-Whisper experiments).

The quantization logic (`quantize_model()`, `per_channel_scale()`,
`lut_quantize()`) contains no model-conditional branching -- these functions
take only `(model, method)` and never reference `args.model_id` or any
other model-specific state. The only places `args.model_id` is used are
output naming, processor/checkpoint loading, and the `language`/`task`
generation kwargs for multilingual models. This was verified directly by
grepping the script for every `args.model_id` reference and confirming none
fall inside the quantization functions, so any difference in outcome
between models reflects differences in the models' actual weights, not
differences in how they were processed.

RTF (real-time factor) is `total_time / (n_chunks * 30)`, i.e. processing
time relative to the 30-second duration of each test chunk. Actual Size
(MB) is the measured in-memory footprint of the quantized model (buffers
plus parameters). Theoretical Size (MB) is the minimum possible footprint
at the method's nominal bit-width (`n_params * bits / 8`, converted to MB),
ignoring bias/embedding overhead and, for `bitsandbytes` methods, ignoring
the outlier-isolation exceptions in LLM.int8() [13] that keep a small
fraction of weights at higher precision. Actual size exceeds theoretical
size for the lookup-table-based naive/percentile methods below 8 bits,
since those store dequantization indices in `int8`/`uint8` buffers
regardless of the nominal bit-width; the `_packed` variants (true nibble
packing) and `bitsandbytes` methods both achieve actual sizes close to
theoretical.

#### Paper Reference Baselines

Zero-shot (vanilla, no fine-tuning) WER on the MyST test set, from Attia et
al. (2024), KID-Whisper: Towards Bridging the Performance Gap in Automatic
Speech Recognition for Children's Speech via Domain Adaptation [1], Table 3
and Table 4, verified directly against the paper's PDF text (not the
originally shared table image, which was found to contain one OCR error --
see the note below the table):

| Model | Paper zero-shot WER on MyST |
|---|---|
| Tiny | 21.16% |
| Tiny.en | 18.34% |
| Base | 18.54% |
| Base.en | 15.57% |
| Small (ML) | 14.06% |
| Small.en | 13.93% |
| Medium (ML) | 12.90% |
| Medium.en | 13.23% |
| Large-v2 | 12.80% |
| Large-v3 | 12.60% [2] |

Large-v3's reference is not in Attia et al. (2024)'s Table 3, which covers
Tiny through Large-V2 only, since Large-V3 was released after the paper.
The 12.60% figure instead comes from Fan, Zheng, & Alwan (2024) [2], a
later children's-ASR benchmarking paper that does report Large-v3 zero-shot
performance on MyST. Every other reference in this table is from Attia et
al. (2024) [1] directly. Base's value (18.54%) was originally transcribed
as 20.40% from an OCR read of the shared table image; every other value in
that image row matched the source PDF exactly, but this one cell did not.
The correct value, 18.54%, was confirmed by fetching and reading the
paper's PDF text directly and is used throughout this document and in the
`PAPER_WER` lookup in `scripts/20_vanilla_medium_ptq.py`. The paper does
not state what numerical precision (FP16 vs FP32) was used for its
zero-shot baseline evaluations, nor exact parameter counts for each model
size; it states only that training was performed on Nvidia A6000 (50GB)
GPUs.

#### Reproduction of Paper Baselines (this work)

FP16 baseline WER obtained under the corrected protocol, full 3,972-chunk
test set, compared against the paper's zero-shot numbers:

| Model | Our FP16 WER | Paper WER | Gap |
|---|---|---|---|
| Tiny.en | 19.94% | 18.34% [1] | +1.60 pp |
| Tiny (ML) | 22.72% | 21.16% [1] | +1.56 pp |
| Base.en | 17.20% | 15.57% [1] | +1.63 pp |
| Base (ML) | 19.65% | 18.54% [1] | +1.11 pp |
| Small.en | 14.51% | 13.93% [1] | +0.58 pp |
| Small (ML) | 14.82% | 14.06% [1] | +0.76 pp |
| Medium.en | 13.45% | 13.23% [1] | +0.22 pp |
| Medium (ML) | 13.79% | 12.90% [1] | +0.89 pp |
| Large-v3 | 13.17% | 12.60% [2] | +0.57 pp |
| Large-v2 | 13.26% | 12.80% [1] | +0.46 pp |

All ten reproductions land within about 1.7 percentage points of the
published zero-shot numbers, confirming the corrected evaluation protocol
is sound across the full model family. The three smallest models, Tiny.en,
Tiny (ML), and Base.en, show the three largest gaps (+1.60 pp, +1.56 pp,
and +1.63 pp respectively), consistent with a broader pattern in this
study of smaller models producing more erratic, less predictable behavior
even before any quantization is applied.

#### Quantization Methods Tested

Naive (absmax) and percentile-clipped (99.9th percentile) variants at INT8,
INT4, INT2, INT1, FP8 (E4M3), FP4 (E2M1), FP2 (E1M0), and FP1 (sign-only);
production `bitsandbytes` INT8/NF4/FP4 [14]; and true 4-bit nibble-packed
FP4/INT4 (0.5 bytes/weight, verified bit-identical in accuracy to the
lookup-table equivalents on every model tested, see Finding 3). The
ultra-low-bit INT2/INT1/FP2/FP1 formats were run in full only on the four
smallest models (Tiny.en, Tiny ML, Base.en, Base ML); they were not run on
Small/Medium/Large since near-certain collapse was expected there and
confirmed unnecessary to verify further once collapse was established at
the more moderate bit-widths. Note that `bitsandbytes` has no plain INT4
mode -- its two 4-bit formats are NF4 and FP4 only.

Result tables below list methods in a fixed order for readability: FP16
baseline, then the naive-quantization methods from 8-bit down to 1-bit
(INT8, FP8, BnB INT8, INT4/packed, FP4/packed, BnB FP4, BnB NF4, INT2, FP2,
INT1, FP1), then the percentile-clipped methods in the same bit-width
order. RTF is `total_time / (n_chunks * 30)`; Actual Size (MB) is the
measured model footprint; Theoretical Size (MB) is the minimum possible
footprint at that method's nominal bit-width (see Evaluation Protocol
above for how these are computed and why actual can exceed theoretical for
non-packed low-bit methods).

#### Results: Whisper Tiny.en (22 of 24 methods; INT1/FP1 skipped)

The smallest model in the study (39M parameters).

| Method | WER | Delta vs FP16 | RTF | Actual (MB) | Theoretical (MB) | Status |
|---|---|---|---|---|---|---|
| FP16 baseline | 19.94% | -- | 0.0065 | 72.0 | 72.10 | -- |
| INT8 naive | 19.40% | -0.54 pp | 0.0071 | 75.5 | 36.05 | clean, beats FP16 |
| FP8 naive | 19.18% | -0.76 pp | 0.0071 | 75.5 | 36.05 | clean, beats FP16 |
| BnB INT8 [14][13] | 19.51% | -0.43 pp | 0.0118 | 56.3 | 36.05 | clean, beats FP16 |
| INT4 naive | 210.67% | +190.73 pp | 0.0248 | 75.5 | 18.02 | breakdown |
| INT4 packed | 210.67% | +190.73 pp | 0.0300 | 58.1 | 18.02 | breakdown (identical) |
| FP4 naive | 76.21% | +56.27 pp | 0.0171 | 75.5 | 18.02 | collapsed |
| FP4 packed | 76.21% | +56.27 pp | 0.0191 | 58.1 | 18.02 | collapsed (identical) |
| BnB FP4 [14] | 25.95% | +6.01 pp | 0.0079 | 48.4 | 18.02 | degraded |
| BnB NF4 [14] | 22.98% | +3.04 pp | 0.0082 | 48.4 | 18.02 | degraded |
| INT2 naive | 852.53% | +832.59 pp | 0.0348 | 75.5 | 9.01 | breakdown |
| FP2 naive | 923.40% | +903.46 pp | 0.0408 | 75.5 | 9.01 | breakdown |
| INT1 naive | 100.00% | +80.06 pp | 0.0347 | 75.5 | 4.51 | degenerate |
| FP1 naive | 100.00% | +80.06 pp | 0.0144 | 75.5 | 4.51 | degenerate |
| INT8 percentile99.9 | 174.95% | +155.01 pp | 0.0201 | 75.5 | 36.05 | breakdown |
| FP8 percentile99.9 | 198.73% | +178.79 pp | 0.0211 | 75.5 | 36.05 | breakdown |
| INT4 percentile99.9 | 574.30% | +554.36 pp | 0.0337 | 75.5 | 18.02 | breakdown |
| FP4 percentile99.9 | 459.63% | +439.69 pp | 0.0375 | 75.5 | 18.02 | breakdown |
| INT2 percentile99.9 | 923.27% | +903.33 pp | 0.0345 | 75.5 | 9.01 | breakdown |
| FP2 percentile99.9 | 923.39% | +903.45 pp | 0.0408 | 75.5 | 9.01 | breakdown |
| INT1 percentile99.9 | 100.00% | +80.06 pp | 0.0340 | 75.5 | 4.51 | degenerate |
| FP1 percentile99.9 | 100.00% | +80.06 pp | 0.0143 | 75.5 | 4.51 | degenerate |

Three separate methods beat the FP16 baseline outright on Tiny.en (FP8
naive, INT8 naive, and BnB INT8), all by a meaningful margin -- this
corrects an earlier informal summary made mid-sweep that mistakenly
reported no method beating FP16 on this model; see Finding 5 for the
corrected cross-model picture. Notably, FP4 naive collapses here (76.21%)
where it stayed the "safe" graceful-degradation choice on every larger
model tested (see Finding 2 and Finding 7); at 39M parameters, the
grid-shape protection that FP4 provides at larger scales is no longer
sufficient on its own. BnB INT8's actual size (56.3MB) sits between the
theoretical INT8 size (36.05MB) and the naive/pct LUT-based actual size
(75.5MB), reflecting `bitsandbytes`' outlier-isolation design keeping a
small fraction of weights at higher precision rather than a pure 8-bit
grid.

#### Results: Whisper Tiny (Multilingual) (22 of 24 methods; both INT1 and FP1 run)

| Method | WER | Delta vs FP16 | RTF | Actual (MB) | Theoretical (MB) | Status |
|---|---|---|---|---|---|---|
| FP16 baseline | 22.72% | -- | 0.0065 | 72.0 | 72.10 | -- |
| INT8 naive | 23.00% | +0.28 pp | 0.0075 | 75.5 | 36.05 | clean |
| FP8 naive | 23.46% | +0.74 pp | 0.0076 | 75.5 | 36.05 | clean |
| BnB INT8 [14][13] | 22.83% | +0.11 pp | 0.0125 | 56.3 | 36.05 | clean (does not beat FP16) |
| INT4 naive | 79.93% | +57.21 pp | 0.0097 | 75.5 | 18.02 | collapsed |
| INT4 packed | 79.93% | +57.21 pp | 0.0118 | 58.1 | 18.02 | collapsed (identical) |
| FP4 naive | 120.19% | +97.47 pp | 0.0233 | 75.5 | 18.02 | breakdown |
| FP4 packed | 120.19% | +97.47 pp | 0.0254 | 58.1 | 18.02 | breakdown (identical) |
| BnB FP4 [14] | 61.20% | +38.48 pp | 0.0111 | 48.4 | 18.02 | degraded |
| BnB NF4 [14] | 39.74% | +17.02 pp | 0.0104 | 48.4 | 18.02 | degraded |
| INT2 naive | 101.86% | +79.14 pp | 0.0346 | 75.5 | 9.01 | breakdown |
| FP2 naive | 327.34% | +304.62 pp | 0.0422 | 75.5 | 9.01 | breakdown |
| INT1 naive | 396.17% | +373.45 pp | 0.0342 | 75.5 | 4.51 | breakdown |
| FP1 naive | 100.00% | +77.28 pp | 0.0414 | 75.5 | 4.51 | degenerate |
| INT8 percentile99.9 | 351.56% | +328.84 pp | 0.0290 | 75.5 | 36.05 | breakdown |
| FP8 percentile99.9 | 379.71% | +356.99 pp | 0.0309 | 75.5 | 36.05 | breakdown |
| INT4 percentile99.9 | 380.78% | +358.06 pp | 0.0283 | 75.5 | 18.02 | breakdown |
| FP4 percentile99.9 | 603.37% | +580.65 pp | 0.0414 | 75.5 | 18.02 | breakdown |
| INT2 percentile99.9 | 100.09% | +77.37 pp | 0.0342 | 75.5 | 9.01 | degenerate |
| FP2 percentile99.9 | 893.58% | +870.86 pp | 0.0428 | 75.5 | 9.01 | breakdown |
| INT1 percentile99.9 | 396.17% | +373.45 pp | 0.0355 | 75.5 | 4.51 | breakdown |
| FP1 percentile99.9 | 100.00% | +77.28 pp | 0.0413 | 75.5 | 4.51 | degenerate |

No method beats FP16 on Tiny (ML); the closest is BnB INT8, landing 0.11
percentage points worse. INT4 naive is far less severe here (79.93%) than
on Tiny.en (210.67%) -- an early sign, confirmed more broadly in Finding 7,
that severity at this smallest scale does not track a simple EN-versus-ML
or size-based rule.

#### Results: Whisper Base.en (22 of 24 methods; both INT1 and FP1 run)

| Method | WER | Delta vs FP16 | RTF | Actual (MB) | Theoretical (MB) | Status |
|---|---|---|---|---|---|---|
| FP16 baseline | 17.20% | -- | 0.0096 | 138.4 | 138.47 | -- |
| INT8 naive | 17.38% | +0.18 pp | 0.0109 | 122.1 | 69.24 | clean |
| FP8 naive | 17.61% | +0.41 pp | 0.0104 | 122.1 | 69.24 | clean |
| BnB INT8 [14][13] | 17.02% | -0.18 pp | 0.0171 | 96.5 | 69.24 | clean, beats FP16 |
| INT4 naive | 518.42% | +501.22 pp | 0.0528 | 122.1 | 34.62 | breakdown |
| INT4 packed | 518.42% | +501.22 pp | 0.0625 | 88.4 | 34.62 | breakdown (identical) |
| FP4 naive | 31.18% | +13.98 pp | 0.0157 | 122.1 | 34.62 | degraded |
| FP4 packed | 31.18% | +13.98 pp | 0.0175 | 88.4 | 34.62 | degraded (identical) |
| BnB FP4 [14] | 21.11% | +3.91 pp | 0.0106 | 75.5 | 34.62 | degraded |
| BnB NF4 [14] | 18.16% | +0.96 pp | 0.0108 | 75.5 | 34.62 | clean |
| INT2 naive | 102.24% | +85.04 pp | 0.0550 | 122.1 | 17.31 | breakdown |
| FP2 naive | 884.27% | +867.07 pp | 0.0640 | 122.1 | 17.31 | breakdown |
| INT1 naive | 893.03% | +875.83 pp | 0.0534 | 122.1 | 8.65 | breakdown |
| FP1 naive | 919.35% | +902.15 pp | 0.0626 | 122.1 | 8.65 | breakdown |
| INT8 percentile99.9 | 171.92% | +154.72 pp | 0.0330 | 122.1 | 69.24 | breakdown |
| FP8 percentile99.9 | 178.23% | +161.03 pp | 0.0326 | 122.1 | 69.24 | breakdown |
| INT4 percentile99.9 | 396.04% | +378.84 pp | 0.0506 | 122.1 | 34.62 | breakdown |
| FP4 percentile99.9 | 236.68% | +219.48 pp | 0.0466 | 122.1 | 34.62 | breakdown |
| INT2 percentile99.9 | 133.44% | +116.24 pp | 0.0542 | 122.1 | 17.31 | breakdown |
| FP2 percentile99.9 | 933.73% | +916.53 pp | 0.0637 | 122.1 | 17.31 | breakdown |
| INT1 percentile99.9 | 893.03% | +875.83 pp | 0.0532 | 122.1 | 8.65 | breakdown |
| FP1 percentile99.9 | 919.35% | +902.15 pp | 0.0631 | 122.1 | 8.65 | breakdown |

INT4 naive (518.42%) is the single worst naive-4-bit result of any English-
only model in the study, exceeding even Tiny.en's INT4 naive (210.67%)
despite Base.en having nearly twice as many parameters -- see Finding 7 for
the cross-model discussion of this non-monotonic pattern. This is also the
only one of the four smallest models where INT4 naive is worse than INT4
percentile clipping (518.42% vs 396.04%), the same directional reversal
documented at much larger scale on Large-v2 and Large-v3 (see Finding 6);
see Finding 8 for the combined discussion.

#### Results: Whisper Base (Multilingual) (22 of 24 methods; both INT1 and FP1 run)

| Method | WER | Delta vs FP16 | RTF | Actual (MB) | Theoretical (MB) | Status |
|---|---|---|---|---|---|---|
| FP16 baseline | 19.65% | -- | 0.0097 | 138.4 | 138.47 | -- |
| INT8 naive | 19.54% | -0.11 pp | 0.0109 | 122.1 | 69.24 | clean, beats FP16 |
| FP8 naive | 20.82% | +1.17 pp | 0.0111 | 122.1 | 69.24 | clean |
| BnB INT8 [14][13] | 18.82% | -0.83 pp | 0.0166 | 96.5 | 69.24 | clean, beats FP16 |
| INT4 naive | 134.47% | +114.82 pp | 0.0282 | 122.1 | 34.62 | breakdown |
| INT4 packed | 134.47% | +114.82 pp | 0.0333 | 88.4 | 34.62 | breakdown (identical) |
| FP4 naive | 113.41% | +93.76 pp | 0.0310 | 122.1 | 34.62 | breakdown |
| FP4 packed | 113.41% | +93.76 pp | 0.0329 | 88.4 | 34.62 | breakdown (identical) |
| BnB FP4 [14] | 26.19% | +6.54 pp | 0.0114 | 75.5 | 34.62 | degraded |
| BnB NF4 [14] | 23.97% | +4.32 pp | 0.0121 | 75.5 | 34.62 | degraded |
| INT2 naive | 916.66% | +897.01 pp | 0.0552 | 122.1 | 17.31 | breakdown |
| FP2 naive | 100.00% | +80.35 pp | 0.0647 | 122.1 | 17.31 | degenerate |
| INT1 naive | 232.31% | +212.66 pp | 0.0526 | 122.1 | 8.65 | breakdown |
| FP1 naive | 100.00% | +80.35 pp | 0.0615 | 122.1 | 8.65 | degenerate |
| INT8 percentile99.9 | 83.45% | +63.80 pp | 0.0223 | 122.1 | 69.24 | breakdown |
| FP8 percentile99.9 | 87.59% | +67.94 pp | 0.0233 | 122.1 | 69.24 | breakdown |
| INT4 percentile99.9 | 250.55% | +230.90 pp | 0.0448 | 122.1 | 34.62 | breakdown |
| FP4 percentile99.9 | 387.31% | +367.66 pp | 0.0584 | 122.1 | 34.62 | breakdown |
| INT2 percentile99.9 | 933.70% | +914.05 pp | 0.0545 | 122.1 | 17.31 | breakdown |
| FP2 percentile99.9 | 100.00% | +80.35 pp | 0.0646 | 122.1 | 17.31 | degenerate |
| INT1 percentile99.9 | 232.31% | +212.66 pp | 0.0527 | 122.1 | 8.65 | breakdown |
| FP1 percentile99.9 | 100.00% | +80.35 pp | 0.0616 | 122.1 | 8.65 | degenerate |

Base (ML) shows the largest bnb-beats-FP16 margin of any small model (BnB
INT8 at -0.83 pp) and is the only model in the study where both FP1 *and*
FP2 land at exactly 100.00% WER for both naive and percentile variants --
see the discussion of this pattern's inconsistency across models in
Finding 9.

#### Results: Whisper Small.en (14 methods)

| Method | WER | Delta vs FP16 | RTF | Actual (MB) | Theoretical (MB) | Status |
|---|---|---|---|---|---|---|
| FP16 baseline | 14.51% | -- | 0.0198 | 461.1 | 461.01 | -- |
| INT8 naive | 14.64% | +0.13 pp | 0.0227 | 310.6 | 230.50 | clean |
| FP8 naive | 14.89% | +0.38 pp | 0.0229 | 310.6 | 230.50 | clean |
| BnB INT8 [14][13] | 14.45% | -0.06 pp | 0.0363 | 272.1 | 230.50 | clean, beats FP16 |
| INT4 naive | 56.65% | +42.14 pp | 0.0519 | 310.6 | 115.25 | collapsed |
| INT4 packed | 56.65% | +42.14 pp | 0.0610 | 197.0 | 115.25 | collapsed (identical) |
| FP4 naive | 19.28% | +4.77 pp | 0.0285 | 310.6 | 115.25 | degraded |
| FP4 packed | 19.28% | +4.77 pp | 0.0316 | 197.0 | 115.25 | degraded (identical) |
| BnB FP4 [14] | 14.52% | +0.01 pp | 0.0218 | 177.6 | 115.25 | clean |
| BnB NF4 [14] | 15.06% | +0.55 pp | 0.0229 | 177.6 | 115.25 | clean |
| INT8 percentile99.9 | 54.61% | +40.10 pp | 0.0464 | 310.6 | 230.50 | collapsed |
| FP8 percentile99.9 | 55.56% | +41.05 pp | 0.0484 | 310.6 | 230.50 | collapsed |
| INT4 percentile99.9 | 115.72% | +101.21 pp | 0.0742 | 310.6 | 115.25 | total breakdown |
| FP4 percentile99.9 | 71.15% | +56.64 pp | 0.0651 | 310.6 | 115.25 | collapsed |

#### Results: Whisper Small (Multilingual) (12 methods)

| Method | WER | Delta vs FP16 | RTF | Actual (MB) | Theoretical (MB) | Status |
|---|---|---|---|---|---|---|
| FP16 baseline | 14.82% | -- | 0.0199 | 461.1 | 461.01 | -- |
| INT8 naive | 15.09% | +0.27 pp | 0.0225 | 310.6 | 230.50 | clean |
| FP8 naive | 15.59% | +0.77 pp | 0.0245 | 310.6 | 230.50 | clean |
| BnB INT8 [14][13] | 15.38% | +0.56 pp | 0.0384 | 272.1 | 230.50 | clean |
| INT4 naive | 37.55% | +22.73 pp | 0.0421 | 310.6 | 115.25 | collapsed |
| FP4 naive | 19.47% | +4.65 pp | 0.0307 | 310.6 | 115.25 | degraded |
| BnB FP4 [14] | 16.34% | +1.52 pp | 0.0220 | 177.6 | 115.25 | clean |
| BnB NF4 [14] | 14.53% | -0.29 pp | 0.0214 | 177.6 | 115.25 | clean, beats FP16 |
| INT8 percentile99.9 | 68.63% | +53.81 pp | 0.0549 | 310.6 | 230.50 | collapsed |
| FP8 percentile99.9 | 73.06% | +58.24 pp | 0.0596 | 310.6 | 230.50 | collapsed |
| INT4 percentile99.9 | 189.11% | +174.29 pp | 0.0968 | 310.6 | 115.25 | total breakdown |
| FP4 percentile99.9 | 107.76% | +92.94 pp | 0.0874 | 310.6 | 115.25 | total breakdown |

#### Results: Whisper Medium.en (14 methods, all clean)

The only model in the entire study that stays clean at every configuration
tested.

| Method | WER | Delta vs FP16 | RTF | Actual (MB) | Theoretical (MB) | Status |
|---|---|---|---|---|---|---|
| FP16 baseline | 13.45% | -- | 0.0462 | 1456.9 | 1457.02 | -- |
| INT8 naive | 13.58% | +0.13 pp | 0.0527 | 836.7 | 728.51 | clean |
| FP8 naive | 13.69% | +0.24 pp | 0.0525 | 836.7 | 728.51 | clean |
| BnB INT8 [14][13] | 13.70% | +0.25 pp | 0.0793 | 784.9 | 728.51 | clean |
| INT4 naive | 14.10% | +0.65 pp | 0.0542 | 836.7 | 364.26 | clean |
| INT4 packed | 14.10% | +0.65 pp | 0.0642 | 475.4 | 364.26 | clean (identical) |
| FP4 naive | 13.48% | +0.03 pp | 0.0650 | 836.7 | 364.26 | clean |
| FP4 packed | 13.48% | +0.03 pp | 0.0712 | 475.4 | 364.26 | clean (identical) |
| BnB FP4 [14] | 13.91% | +0.46 pp | 0.0504 | 448.9 | 364.26 | clean |
| BnB NF4 [14] | 13.53% | +0.08 pp | 0.0511 | 448.9 | 364.26 | clean |
| INT8 percentile99.9 | 15.65% | +2.20 pp | 0.0560 | 836.7 | 728.51 | clean |
| FP8 percentile99.9 | 15.71% | +2.26 pp | 0.0570 | 836.7 | 728.51 | clean |
| INT4 percentile99.9 | 15.90% | +2.45 pp | 0.0555 | 836.7 | 364.26 | clean |
| FP4 percentile99.9 | 18.45% | +5.00 pp | 0.0732 | 836.7 | 364.26 | clean |

#### Results: Whisper Medium (Multilingual) (14 methods)

| Method | WER | Delta vs FP16 | RTF | Actual (MB) | Theoretical (MB) | Status |
|---|---|---|---|---|---|---|
| FP16 baseline | 13.79% | -- | 0.0477 | 1456.9 | 1457.02 | -- |
| INT8 naive | 13.45% | -0.34 pp | 0.0542 | 836.7 | 728.51 | clean, beats FP16 |
| FP8 naive | 14.17% | +0.38 pp | 0.0551 | 836.7 | 728.51 | clean |
| BnB INT8 [14][13] | 13.63% | -0.16 pp | 0.0829 | 784.9 | 728.51 | clean, beats FP16 |
| INT4 naive | 29.33% | +15.54 pp | 0.0814 | 836.7 | 364.26 | collapsed |
| INT4 packed | 29.33% | +15.54 pp | 0.0932 | 475.4 | 364.26 | collapsed (identical) |
| FP4 naive | 16.85% | +3.06 pp | 0.0772 | 836.7 | 364.26 | degraded |
| FP4 packed | 16.85% | +3.06 pp | 0.0829 | 475.4 | 364.26 | degraded (identical) |
| BnB FP4 [14] | 13.27% | -0.52 pp | 0.0509 | 448.9 | 364.26 | clean, beats FP16 |
| BnB NF4 [14] | 13.53% | -0.26 pp | 0.0533 | 448.9 | 364.26 | clean, beats FP16 |
| INT8 percentile99.9 | 104.76% | +90.97 pp | 0.1753 | 836.7 | 728.51 | total breakdown |
| FP8 percentile99.9 | 108.72% | +94.93 pp | 0.1822 | 836.7 | 728.51 | total breakdown |
| INT4 percentile99.9 | 97.12% | +83.33 pp | 0.1640 | 836.7 | 364.26 | total breakdown |
| FP4 percentile99.9 | 106.73% | +92.94 pp | 0.2172 | 836.7 | 364.26 | total breakdown |

Manual inspection of the INT8-percentile prediction file confirmed 93.5% of
the 3,972 test chunks showed mismatches, with prediction lengths running to
4,310 characters -- the same repetition-loop generation pathology documented
on the Small models, not a distinct failure mode. Note the sharp RTF jump
on all four percentile methods here (0.16-0.22) versus every clean method
on this model (0.05-0.09), consistent with the same collapse-driven
generation-length increase documented elsewhere in this study.

#### Results: Whisper Large-v3 (14 methods)

No English-only variant of Large-v3 exists (OpenAI never released a
`large-v3.en` checkpoint), so this is evaluated as a single multilingual
model rather than an EN/ML pair.

| Method | WER | Delta vs FP16 | RTF | Actual (MB) | Theoretical (MB) | Status |
|---|---|---|---|---|---|---|
| FP16 baseline | 13.17% | -- | 0.0667 | 2944.0 | 2943.99 | -- |
| INT8 naive | 13.20% | +0.03 pp | 0.0870 | 1609.1 | 1472.00 | clean |
| FP8 naive | 12.90% | -0.27 pp | 0.0879 | 1609.1 | 1472.00 | clean, beats FP16 |
| BnB INT8 [14][13] | 13.20% | +0.03 pp | 0.1174 | 1544.0 | 1472.00 | clean |
| INT4 naive | 657.04% | +643.87 pp | 0.5725 | 1609.1 | 736.00 | catastrophic |
| INT4 packed | 657.04% | +643.87 pp | 0.7457 | 877.5 | 736.00 | catastrophic (identical) |
| FP4 naive | 14.25% | +1.08 pp | 0.1477 | 1609.1 | 736.00 | clean |
| FP4 packed | 14.25% | +1.08 pp | 0.1621 | 877.5 | 736.00 | clean (identical) |
| BnB FP4 [14] | 13.06% | -0.11 pp | 0.0737 | 844.0 | 736.00 | clean, beats FP16 |
| BnB NF4 [14] | 12.65% | -0.52 pp | 0.0755 | 844.0 | 736.00 | clean, beats FP16 |
| INT8 percentile99.9 | 12.78% | -0.39 pp | 0.0851 | 1609.1 | 1472.00 | clean, beats FP16 |
| FP8 percentile99.9 | 12.79% | -0.38 pp | 0.0892 | 1609.1 | 1472.00 | clean, beats FP16 |
| INT4 percentile99.9 | 18.87% | +5.70 pp | 0.0932 | 1609.1 | 736.00 | clean |
| FP4 percentile99.9 | 12.54% | -0.63 pp | 0.1358 | 1609.1 | 736.00 | clean, best result |

Twelve of the fourteen methods stayed clean, and six of those beat the
FP16 baseline outright. The only failures are INT4 naive and its packed
equivalent, both collapsing to an identical 657.04% WER -- the single worst
result across the entire ten-model study, and by far the largest RTF in
the study (0.57-0.75, roughly 7-11x every other method on this model).
Manual inspection of the INT4 naive prediction file confirmed 3,934 of
3,972 chunks (99.05%) showed mismatches, with prediction lengths running to
7,490 characters, the most severe repetition-loop signature observed in
the study; the `int4_packed` run took 24 hours 41 minutes to complete
(versus roughly 2.2-4.9 hours for clean runs), the longest single run
recorded.

##### Verification that the INT4-naive failure on Large-v3 is a genuine
##### quantization result and not a script bug

Because 657.04% is by far the most extreme number in the study, it is
reported here alongside the specific checks performed to rule out an
implementation error before treating it as a finding rather than a defect:

1. **The same code path is used for every model and every naive-quantization
   run.** `quantize_model()`, `per_channel_scale()`, and `lut_quantize()`
   take only `(model, method)` and contain no `args.model_id` or other
   model-conditional branching (verified directly by grepping the script for
   every `args.model_id` reference and confirming none fall inside the
   quantization functions -- see Evaluation Protocol above). The INT4-naive
   code path that produces 14.10% (clean) on Medium.en and 29.33% on Medium
   (ML) is line-for-line the same code path that produces 657.04% on
   Large-v3. Nothing in the quantization logic is Large-v3-specific.

2. **Two independent implementations of INT4 naive quantization agree
   exactly.** `int4_naive` (lookup-table-based storage, 1,609.1MB) and
   `int4_packed` (true nibble-packed storage, 877.5MB) are separate code
   paths -- one dequantizes via a lookup table, the other via bitwise nibble
   unpacking (`pack_nibbles`/`unpack_nibbles`) -- yet both produced
   identical WER to two decimal places (657.04% each) and, on manual
   inspection, the same prediction lengths and mismatch pattern. Two
   differently-implemented quantization paths reproducing an identical,
   highly unusual result independently is strong evidence the result
   reflects the underlying model weights and INT4 grid, not a bug specific
   to either implementation.

3. **The failure mode is qualitatively identical to failures already
   established as genuine on other models.** The prediction file shows the
   same repetition-loop pathology (a single phrase repeated dozens of times
   until `max_new_tokens` is reached) documented and manually verified on
   Small.en, Small (ML), and Medium (ML) at their respective collapse
   points. It is not a crash, an empty output, garbled tokens, or any other
   signature that would suggest a numerical error (such as an overflow) is
   unique to Large-v3's architecture; it is the same generation-level
   failure mode observed at smaller scales under different quantization
   settings, just triggered here by a different configuration (naive INT4
   rather than percentile clipping).

4. **The run's wall-clock time is independently consistent with genuine
   model-level collapse, not a stalled or hung process.** The run completed
   normally (3,972 of 3,972 chunks processed, `calculate_wer.py` cross-check
   matched the inline WER) in 24 hours 41 minutes, an order of magnitude
   longer than any clean run (2.2-4.9 hours) but proportionate to the
   degree of repetition observed in the predictions (chunks looping toward
   `max_new_tokens` cost roughly proportionally more generation steps),
   and directly reflected in the RTF column above (0.57-0.75 for the
   collapsed runs versus 0.07-0.16 for every clean method on this model).
   This is the expected signature of a model generating very long,
   repetitive output, not of the run failing to progress.

5. **The result sits at the extreme end of an otherwise smooth, explicable
   trend, rather than appearing in isolation.** As detailed in Finding 6,
   INT4 naive's failure is the most severe point on a consistent
   naive-versus-percentile reversal that holds across every bit-width and
   grid type tested on this model. A script bug specific to one method-model
   combination would not be expected to sit on such a consistent trend line.

Taken together, these checks support treating 657.04% as a genuine, if
extreme, quantization outcome specific to combining Large-v3's scale with
naive INT4 absmax quantization, rather than as a defect in the evaluation
pipeline.

**Why this happens.** Naive absmax quantization sets its per-channel scale
from the single most extreme weight value in that channel, then divides the
INT4 grid's 15 available levels across the full range implied by that
extreme value. Prior work on outlier-aware quantization (Dettmers et al.,
2022, LLM.int8() [13]) established that transformer language models develop
a small number of disproportionately large-magnitude "outlier" weights, and
that these outliers grow more prominent as model scale increases -- this is
the entire motivation for `bitsandbytes`' explicit outlier-isolation design
in its own INT8 implementation. At 1,550M parameters, Large-v3 is
substantially larger than any Small/Medium model in this study, and if even
one weight in a channel is a severe outlier, naive absmax stretches that
channel's entire quantization step size to accommodate it. With only 15
levels available at INT4, this leaves almost no resolution for the
remaining, typical-magnitude weights in that channel -- they collapse
toward one or two of the 15 levels near zero, destroying most of the useful
signal those weights carried. Percentile clipping at the 99.9th percentile
avoids this by deliberately excluding the most extreme 0.1% of values when
computing the scale, so the remaining weights retain full 15-level
resolution at the cost of clipping (and thus corrupting) only the rare
outliers themselves -- consistent with why INT4-percentile stays clean
(18.87%) on the same model where INT4-naive collapses. This mechanism is
discussed further in Finding 6 below. It remains a well-motivated
hypothesis grounded in established quantization literature rather than a
mechanism directly confirmed for this specific model, since it has not been
verified by inspecting Large-v3's actual weight-distribution statistics
(for example, per-channel outlier magnitude or kurtosis).

#### Results: Whisper Large-v2 (14 methods)

Evaluated as a follow-up to Large-v3 specifically to test whether Finding
6's naive-versus-percentile reversal is a property of the ~1,550M-parameter
scale in general, or specific to the Large-v3 checkpoint. Large-v2 is the
same architecture and parameter count as Large-v3, differing mainly in
training data and recipe refinements between the two OpenAI releases.

| Method | WER | Delta vs FP16 | RTF | Actual (MB) | Theoretical (MB) | Status |
|---|---|---|---|---|---|---|
| FP16 baseline | 13.26% | -- | 0.0678 | 2943.6 | 2943.99 | -- |
| INT8 naive | 13.31% | +0.05 pp | 0.0892 | 1608.7 | 1472.00 | clean |
| FP8 naive | 13.80% | +0.54 pp | 0.0903 | 1608.7 | 1472.00 | clean |
| BnB INT8 [14][13] | 13.39% | +0.13 pp | 0.1155 | 1543.6 | 1472.00 | clean |
| INT4 naive | 18.62% | +5.36 pp | 0.1101 | 1608.7 | 736.00 | degraded, not collapsed |
| INT4 packed | 18.62% | +5.36 pp | 0.1444 | 877.1 | 736.00 | degraded, not collapsed (identical) |
| FP4 naive | 14.43% | +1.17 pp | 0.1376 | 1608.8 | 736.00 | clean |
| FP4 packed | 14.43% | +1.17 pp | 0.1545 | 877.2 | 736.00 | clean (identical) |
| BnB FP4 [14] | 13.67% | +0.41 pp | 0.0741 | 843.6 | 736.00 | clean |
| BnB NF4 [14] | 13.58% | +0.32 pp | 0.0772 | 843.6 | 736.00 | clean |
| INT8 percentile99.9 | 14.46% | +1.20 pp | 0.0950 | 1608.7 | 1472.00 | clean |
| FP8 percentile99.9 | 14.26% | +1.00 pp | 0.0971 | 1608.7 | 1472.00 | clean |
| INT4 percentile99.9 | 14.43% | +1.17 pp | 0.0946 | 1608.7 | 736.00 | clean |
| FP4 percentile99.9 | 15.20% | +1.94 pp | 0.1484 | 1608.8 | 736.00 | clean |

No catastrophic collapse occurs anywhere in the Large-v2 sweep. The worst
result, INT4 naive (and its packed equivalent, identical at 18.62%), is a
real but moderate cost, nowhere near Large-v3's 657.04% at the same
setting, and every RTF in this table stays in a normal 0.07-0.15 range
with no extreme runtime blowup of the kind seen on Large-v3's collapsed
runs.

At three of the four bit-width/grid combinations tested, Large-v2 follows
the "normal" pattern seen on every model except Large-v3 -- naive beats
percentile clipping (INT8: 13.31% vs 14.46%; FP8: 13.80% vs 14.26%; FP4:
14.43% vs 15.20%). At the fourth combination, INT4, the ordering reverses
exactly as it did on Large-v3: naive (18.62%) is worse than percentile
clipping (14.43%). The reversal on Large-v2 is real but far milder than on
Large-v3 -- a 4.19 percentage-point gap at INT4, compared to Large-v3's
638.17 percentage-point gap at the identical setting.

#### Key Findings

##### Finding 1 (headline result): quantization stability at the
##### 769M-parameter scale is not a function of capacity alone -- it is the
##### interaction of sufficient capacity AND English-only training

An initial hypothesis, based on the first three models evaluated (both
Small variants and Medium.en), was that a simple capacity threshold around
769M parameters explained why percentile-clipped quantization collapsed
into repetition-loop hallucination on Small models but stayed clean on
Medium.en. Testing Medium (Multilingual) -- the same 769M-parameter
architecture as Medium.en, differing only in training data -- disproves
that simpler hypothesis: Medium (ML) collapses just as severely as either
Small model under percentile clipping at every bit-width tested, and even
under aggressive naive 4-bit quantization where Medium.en stays essentially
lossless.

| Model | Params | Language | INT8 pct | FP8 pct | FP4 pct | INT4 pct | INT4 naive |
|---|---|---|---|---|---|---|---|
| Small.en | 244M | EN | 54.61% | 55.56% | 71.15% | 115.72% | 56.65% |
| Small (ML) | 244M | ML | 68.63% | 73.06% | 107.76% | 189.11% | 37.55% |
| Medium.en | 769M | EN | 15.65% | 15.71% | 18.45% | 15.90% | 14.10% |
| Medium (ML) | 769M | ML | 104.76% | 108.72% | 106.73% | 97.12% | 29.33% |

Medium.en is the only 769M/244M configuration in the study that remains
clean across every method tested, including the most aggressive combined
setting (4-bit plus percentile clipping). Medium (ML), despite identical
parameter count, collapses to total breakdown (WER exceeding 100%) at all
four percentile-clipped bit-widths tested, and shows real degradation even
under naive INT4 (29.33% WER, +15.54 pp) where Medium.en costs only +0.65
pp. Collapsed runs on Medium (ML) also took markedly longer to evaluate
(RTF 0.16-0.22 versus 0.05-0.08 for Medium.en's clean runs at the same
settings), and manual inspection of the INT8-percentile prediction file
confirmed 93.5% of chunks showed mismatches with prediction lengths
running to 4,310 characters -- matching the repetition-loop pathology
documented on the Small models (92.9-96.6% mismatch rates there).

The conclusion, restricted to the 244M-769M range where clean EN/ML pairs
exist: model capacity alone does not predict quantization robustness at
this scale. Robustness instead depends on the interaction between capacity
and training specialization -- English-only training at 769M provides a
stability margin that neither a smaller English-only model nor a larger
(same-size) multilingual model provides on its own. Grid type (INT vs FP)
remains irrelevant to whether collapse occurs within this range: on every
model tested, the four percentile-clipped bit-widths land within a
relatively narrow band of each other regardless of collapse severity (a
2.26 pp spread across all four grids/bit-widths on Medium.en; a 6.60 pp
spread on Medium (ML)), while the EN-vs-ML gap on the identical grid can be
enormous (Medium.en 15.65% vs Medium (ML) 104.76% at INT8-percentile, an
89.11 percentage-point difference). This is the central thesis contribution
of the vanilla-model PTQ study. It is scoped explicitly to the 244M-769M
range: Finding 7 shows the pattern does not extend cleanly down to Tiny and
Base scale, and Finding 6 shows a related but distinct outlier-driven
mechanism dominates at the ~1,550M Large scale instead.

A plausible (not directly confirmed) mechanism: multilingual models must
route decoding across roughly 99 languages through a shared vocabulary and
embedding space, and prior work on outlier-aware quantization (Dettmers et
al., 2022, LLM.int8() [13]) has shown that a small number of large-magnitude
weights carry disproportionate importance in transformer language models,
motivating the explicit outlier-isolation scheme in `bitsandbytes`' INT8
implementation. Percentile clipping at 99.9% specifically removes exactly
these extreme-magnitude weights. If multilingual training concentrates more
of its critical signal into such outlier weights than English-only training
does, that would explain why percentile clipping is catastrophic for both
multilingual models at this scale, while English-only models degrade more
gracefully. Confirming this mechanism directly would require
weight-distribution analysis beyond what this PTQ sweep alone demonstrates,
and is noted here as a direction for further investigation rather than an
established result.

##### Finding 2: grid shape (integer vs float) provides partial protection
##### against collapse at naive 4-bit quantization on fragile models in the
##### 244M-769M range, though this protection is not universal at smaller
##### scales (see Finding 7)

At naive 4-bit quantization, INT4 (evenly-spaced integer grid) collapses
more severely than FP4 (E2M1 floating-point grid, wider dynamic range,
non-uniform spacing) on every 244M-769M model that shows any fragility:

| Model | INT4 naive | FP4 naive | Protection gap |
|---|---|---|---|
| Small.en | 56.65% | 19.28% | 37.37 pp |
| Small (ML) | 37.55% | 19.47% | 18.08 pp |
| Medium.en | 14.10% | 13.48% | 0.62 pp |
| Medium (ML) | 29.33% | 16.85% | 12.48 pp |

The protection gap scales with how fragile the underlying model already is
(largest on Small.en, near-zero on the uniquely stable Medium.en,
intermediate on Small ML and Medium ML), regardless of whether that
fragility comes from small scale or from multilingual training within this
range -- FP4's grid shape is a general-purpose mitigation here, not one
specific to a particular cause of instability. Under combined
4-bit-plus-clipping stress, this protection is inconsistent across models:
FP4-percentile is clearly less severe than INT4-percentile on both Small
models; the two are nearly tied on Medium (ML); and on Medium.en the
pattern reverses outright, with FP4-percentile (18.45%) worse than
INT4-percentile (15.90%). As Finding 7 shows, this grid-shape protection
also fails to hold at the smallest scales tested (Tiny.en, 39M parameters):
FP4 naive collapses there too (76.21%), so the protection documented in
this Finding is specific to the 244M-769M range rather than universal
across all model sizes.

##### Finding 3: true bit-packing preserves WER exactly relative to
##### lookup-table quantization on every model tested, without exception

`fp4_packed` (true nibble packing, 0.5 bytes/weight) produced WER identical
to `fp4_naive` (lookup-table based) on every model where both were tested,
across the full size range: 76.21% on Tiny.en, 120.19% on Tiny (ML), 31.18%
on Base.en, 113.41% on Base (ML), 19.28% on Small.en, 13.48% on Medium.en,
16.85% on Medium (ML), 14.25% on Large-v3, and 14.43% on Large-v2.
Likewise, `int4_packed` matched `int4_naive` exactly on every model tested
(for example, 657.04% on Large-v3 and 210.67% on Tiny.en), including
reproducing collapse and total-breakdown behavior wherever present. The
packed variants also consistently achieve actual sizes much closer to the
theoretical bit-width minimum than the LUT-based naive/percentile methods
do (for example, on Small.en, INT4 naive stores at 310.6MB actual against
a 115.25MB theoretical minimum, while INT4 packed achieves 197.0MB --
closer to, though still somewhat above, the theoretical figure due to
residual per-channel scale and lookup-table overhead). This confirms the
nibble-packing implementation (`pack_nibbles`/`unpack_nibbles`) is
numerically correct across the entire ten-model, 40x parameter-count range
tested -- it changes only the storage representation, not the dequantized
values used in the forward pass -- and demonstrates that the entire gap
between naive/packed quantization and `bitsandbytes`' equivalents (see
Finding 4) is attributable to `bitsandbytes`' calibration and per-block
scaling strategy, not to the bit budget or packing format itself. This is
the most consistently confirmed result in the study, holding without a
single exception across all ten models.

##### Finding 4 (stated as a genuine negative result): BnB FP4 vs NF4
##### preference does not follow a predictable pattern by model size or
##### language coverage

`bitsandbytes` FP4 [14] and NF4 [14] were compared across all ten vanilla
models:

| Model | BnB FP4 | BnB NF4 | Winner |
|---|---|---|---|
| Tiny.en | 25.95% | 22.98% | NF4 |
| Tiny (ML) | 61.20% | 39.74% | NF4 |
| Base.en | 21.11% | 18.16% | NF4 |
| Base (ML) | 26.19% | 23.97% | NF4 |
| Small.en | 14.52% | 15.06% | FP4 |
| Small (ML) | 16.34% | 14.53% | NF4 |
| Medium.en | 13.91% | 13.53% | NF4 |
| Medium (ML) | 13.27% | 13.53% | FP4 |
| Large-v3 | 13.06% | 12.65% | NF4 |
| Large-v2 | 13.67% | 13.58% | NF4 |

NF4 wins on eight of the ten models, with FP4 winning only on Small.en and
Medium (ML). No consistent pattern emerges by language (English-only models
split FP4 and NF4 as winners) or by model size (multilingual models split
across both winners at different sizes). All four of the smallest models
(Tiny.en, Tiny ML, Base.en, Base ML) favor NF4 by a wide margin, which
might suggest a size-based rule at the low end, except that this pattern
does not hold at Small or Medium: Small.en and Medium (ML) both favor FP4,
breaking any monotonic size-based story. The honest conclusion is that
FP4-versus-NF4 preference on vanilla Whisper models depends on some other
property of each specific checkpoint's weight distribution not fully
captured by model size or language coverage, though there may be a weak
tendency for very small models to favor NF4 specifically that would benefit
from testing on more checkpoints at that scale before treating it as
established. This is reported as a genuine inconclusive finding rather than
forced into a stronger narrative than the complete data supports.
Regardless of which grid wins on a given model, both `bitsandbytes` 4-bit
variants substantially outperform the custom naive/packed FP4
implementation at every model scale (see Finding 3), confirming
`bitsandbytes`' double-quantization and per-block calibration [14]
materially improve on naive absmax-based 4-bit quantization independent of
which grid is used.

##### Finding 5 (corrected): whether any quantization method beats the FP16
##### baseline outright varies by model without a clean rule, and is not
##### simply a function of model size

An earlier informal summary made mid-study incorrectly stated that neither
Tiny.en nor Tiny (ML) had any method beat their FP16 baseline. This was
wrong for Tiny.en specifically: three separate methods there beat FP16 by a
real margin (FP8 naive at -0.76 pp, INT8 naive at -0.54 pp, and BnB INT8 at
-0.43 pp). The corrected picture across all ten models:

| Model | Best method | Margin vs FP16 | Beats FP16? |
|---|---|---|---|
| Tiny.en | FP8 naive | -0.76 pp | yes |
| Tiny (ML) | BnB INT8 | +0.11 pp | no (closest, still worse) |
| Base.en | BnB INT8 | -0.18 pp | yes |
| Base (ML) | BnB INT8 | -0.83 pp | yes |
| Small.en | BnB INT8 | -0.06 pp | yes |
| Small (ML) | INT8 naive | +0.27 pp | no (closest, still worse) |
| Medium.en | FP4 naive | +0.03 pp | no (closest, still worse) |
| Medium (ML) | BnB FP4 | -0.52 pp | yes |
| Large-v3 | FP4 percentile99.9 | -0.63 pp | yes |
| Large-v2 | INT8 naive | +0.05 pp | no (closest, still worse) |

Six of the ten models have at least one method that beats FP16 outright;
four do not. Restricting to the four models with a genuine English-only
versus multilingual pair at the same architecture (Tiny, Base, Small,
Medium), the English-only variant beats FP16 in three of four cases (Tiny,
Base, Small) while the multilingual variant beats FP16 in only one of four
(Medium). This is a mild directional tendency for English-only models to
more often have some quantization method that outright improves on FP16,
but the sample is small (four pairs) and Medium's result runs the opposite
direction from Tiny, Base, and Small, so this is reported as a weak
tendency rather than a firm rule. Medium (ML)'s cluster of four methods
beating FP16 simultaneously, all within a tight 0.36 percentage-point band
(BnB FP4, naive INT8, BnB NF4, BnB INT8, see the Medium (ML) results table),
coincides with that model also showing the largest gap to its own paper
zero-shot reference among the four Small/Medium models (+0.89 pp),
suggesting at least part of this effect may reflect headroom in the FP16
baseline evaluation itself on that specific model, rather than a genuine
quantization-driven accuracy improvement. This is noted as a plausible
partial explanation rather than a settled conclusion, and does not obviously
extend to the other five models that beat FP16.

##### Finding 6 (final form, refined twice as Large-v2 data arrived): outlier
##### severity behaves as a continuum across the two Large checkpoints, with
##### Large-v3 possessing it far more severely than Large-v2 at every grid
##### and Large-v2 showing the same effect only at the single coarsest grid

On every 244M-769M model, naive (unclipped absmax) quantization was
consistently safer than percentile clipping at matched bit-width: naive
stayed clean while percentile-clipped configurations ranged from mildly
costly (Medium.en) to catastrophic (Small.en, Small (ML), Medium (ML)).
Large-v3 inverts this pattern completely and symmetrically across every
bit-width and grid type tested:

| Bit-width | Grid | Naive WER | Percentile WER | Percentile advantage |
|---|---|---|---|---|
| 8-bit | INT | 13.20% | 12.78% | 0.42 pp |
| 8-bit | FP | 12.90% | 12.79% | 0.11 pp |
| 4-bit | FP | 14.25% | 12.54% | 1.71 pp |
| 4-bit | INT | 657.04% | 18.87% | 638.17 pp |

Percentile clipping wins at every single combination on Large-v3, with the
margin growing sharply as precision drops. Because the reversal holds for
both integer and floating-point grids, it cannot be explained as a
grid-type artifact.

The full Large-v2 sweep, evaluated specifically to test whether this
reversal is a property of the shared ~1,550M-parameter architecture or
specific to the Large-v3 checkpoint, gives a more precise answer than
either simple alternative:

| Model | Grid | Naive WER | Percentile WER | Winner | Gap |
|---|---|---|---|---|---|
| Large-v3 | INT8 | 13.20% | 12.78% | percentile | 0.42 pp |
| Large-v2 | INT8 | 13.31% | 14.46% | naive | 1.15 pp |
| Large-v3 | FP8 | 12.90% | 12.79% | percentile | 0.11 pp |
| Large-v2 | FP8 | 13.80% | 14.26% | naive | 0.46 pp |
| Large-v3 | FP4 | 14.25% | 12.54% | percentile | 1.71 pp |
| Large-v2 | FP4 | 14.43% | 15.20% | naive | 0.77 pp |
| Large-v3 | INT4 | 657.04% | 18.87% | percentile | 638.17 pp |
| Large-v2 | INT4 | 18.62% | 14.43% | percentile | 4.19 pp |

Large-v2 follows the "normal" naive-safer pattern at INT8, FP8, and FP4.
At INT4, however, Large-v2 also reverses: naive quantization (18.62%) is
worse than percentile clipping (14.43%), the same direction as Large-v3's
reversal at the identical setting, just far milder in magnitude (a 4.19
percentage-point gap rather than 638.17). Critically, INT4 naive on
Large-v2 degrades to a real but moderate 18.62% WER rather than collapsing
catastrophically, so this is a much gentler version of the same phenomenon,
not a second unrelated failure.

An earlier version of this finding, based on incomplete Large-v2 data (only
INT8 and FP8 tested at the time), concluded the reversal was specific to
the Large-v3 checkpoint and absent from Large-v2 entirely. The completed
INT4 result shows this was too strong a conclusion: Large-v2 exhibits the
same directional effect, just confined to the single coarsest grid tested,
where naive absmax quantization has the least resolution to spare. The
most consistent interpretation across both checkpoints is that outlier
weight magnitude -- the same mechanism discussed in the Large-v3 results
section above -- behaves as a continuum rather than a binary property.
Large-v3 appears to possess substantially more severe outliers than
Large-v2, severe enough that naive quantization's outlier-driven distortion
outweighs its usual advantage at every bit-width tested, including 8-bit.
Large-v2 appears to possess milder outliers, sufficient to tip the balance
only at INT4, the grid with the least resolution to absorb the distortion.
Grid shape continues to determine how severely naive quantization suffers
once the balance tips, consistent with Finding 2: INT4's uniform, narrow
grid turns Large-v3's severe outliers into total collapse and Large-v2's
milder outliers into a moderate cost, while FP4's wider, non-uniform grid
keeps Large-v3's naive-quantization cost merely moderate and does not tip
the balance for Large-v2 at all.

Notably, as Finding 8 details, this same directional reversal (naive worse
than percentile at INT4) also appears in isolation on Base.en, a model
roughly 20 times smaller than the two Large checkpoints -- suggesting the
underlying outlier-driven mechanism is not exclusive to very large models,
even though it is far more prevalent and severe there.

A plausible mechanism (detailed in the Large-v3 results section above)
extends the same outlier-weight literature cited in Finding 1: naive
absmax quantization's scale is set by each channel's single most extreme
weight, and prior work on outlier-aware quantization (Dettmers et al.,
2022, LLM.int8() [13]) shows outlier weight magnitude can grow with model
scale in transformer language models generally. The Large-v2/Large-v3
comparison suggests this magnitude is not simply a function of parameter
count, since the two checkpoints share an identical architecture and
parameter count yet show sharply different degrees of the effect --
something about Large-v3's specific training data or recipe appears to
have produced more severe outliers than Large-v2's. This remains an open
question for further investigation (for example, direct comparison of
per-channel outlier magnitude or kurtosis between the two checkpoints), and
the mechanism is reported here as a hypothesis consistent with the observed
continuum rather than a confirmed explanation.

This complicates rather than contradicts the mechanism proposed for Finding
1: Medium (ML)'s failure mode is percentile clipping *removing* outlier
signal that a multilingual model's cross-lingual routing apparently
depends on, while the Large models' failure mode is naive quantization
being *warped* by outlier magnitude whose severity varies by checkpoint.
These appear to be two distinct, checkpoint-and-scale-dependent
outlier-related failure modes rather than a single universal rule, and
distinguishing them with confidence would require direct weight-distribution
analysis beyond what this PTQ sweep alone demonstrates.

##### Finding 7: naive INT4 quantization severity does not decrease
##### monotonically with model size at the smallest scales, breaking the
##### intuitive expectation that bigger models are always more robust

Comparing naive INT4 quantization across the four smallest English-only
models plus Medium.en:

| Model | Params | INT4 naive WER |
|---|---|---|
| Tiny.en | 39M | 210.67% |
| Base.en | 74M | 518.42% |
| Small.en | 244M | 56.65% |
| Medium.en | 769M | 14.10% |

Base.en, despite having nearly twice as many parameters as Tiny.en,
produces a substantially *worse* INT4-naive result (518.42% versus
210.67%) -- the single worst naive-4-bit result of any English-only model
in the study. Only once parameter count reaches Small.en (244M) does the
expected pattern of improving robustness with scale resume, continuing
cleanly through Medium.en (769M). The same non-monotonicity appears on the
multilingual side, though less dramatically: Tiny (ML) INT4 naive is
79.93%, Base (ML) is 134.47% (worse despite being larger), and Small (ML)
drops back down to 37.55%.

This result is consistent with the broader theme running through Findings
4 and 6: raw parameter count is not, by itself, a reliable predictor of
quantization behavior. Whatever combination of weight-distribution
properties determines a given checkpoint's naive-4-bit robustness appears
to vary in a checkpoint-specific way that a monotonic capacity-based model
cannot capture, at least at the smallest end of the Whisper family, where
architectural changes between Tiny/Base/Small (differing numbers of layers
and hidden dimensions, not just uniform scaling) may plausibly interact
with outlier formation in ways this study did not directly investigate.

##### Finding 8: the naive-worse-than-percentile reversal documented on the
##### Large models (Finding 6) also appears, in isolated form, at a much
##### smaller scale

Among the four smallest models, exactly one INT4 comparison reverses the
usual naive-safer ordering: on Base.en, INT4 naive (518.42%) is worse than
INT4 percentile clipping (396.04%), the same direction as the Large-v2/v3
reversal, though the underlying WER values themselves are far higher (both
in breakdown territory, unlike Large-v2's comparatively mild 18.62% vs
14.43%). Every other INT4 naive-versus-percentile comparison among the
four smallest models follows the normal pattern (naive better than
percentile): Tiny.en (210.67% vs 574.30%), Tiny (ML) (79.93% vs 380.78%),
and Base (ML) (134.47% vs 250.55%) all show naive winning by a wide margin.

This isolated appearance on a single small model, rather than consistently
across all four, reinforces the checkpoint-specific framing established in
Finding 6 rather than suggesting a broader small-model version of the same
rule: the reversal does not track model size, language coverage, or any
other variable tested here in a way that would let it be predicted in
advance. It is best understood as further evidence that the underlying
outlier-driven mechanism proposed in Finding 6 can occur at essentially any
scale in this model family, given the right (still not fully characterized)
combination of checkpoint-specific weight properties, rather than being an
exclusively large-model phenomenon.

##### Finding 9 (reported as unresolved): an exact-100.00% WER pattern
##### appears inconsistently across the four smallest models and does not
##### resolve into a clear rule

Several ultra-low-bit configurations (FP1, FP2, INT1, INT2, always in
matched naive/percentile pairs) produced a WER of exactly 100.00% on one or
more of the four smallest models, rather than the very high but non-round
percentages typical of other collapsed configurations elsewhere in the
study:

| Model | Configs at exactly 100.00% |
|---|---|
| Tiny.en | FP1 naive, FP1 percentile99.9, INT1 naive, INT1 percentile99.9 (all four) |
| Tiny (ML) | FP1 naive, FP1 percentile99.9 only (INT1 lands at 396.17%, not 100%) |
| Base.en | none (FP1 lands at 919.35%, INT1 at 893.03%) |
| Base (ML) | FP1 naive, FP1 percentile99.9, FP2 naive, FP2 percentile99.9 (four configs), and INT2 percentile99.9 lands close (100.09%) though not exact, on Tiny (ML) |

No consistent rule explains this pattern. FP1 hits exactly 100.00% on
three of the four models (all except Base.en); FP2 does so only once (Base
(ML)); INT1 does so only once (Tiny.en), and INT2 never does exactly,
though it comes very close once (Tiny (ML) percentile, 100.09%). An exact
100.00% WER is most plausibly explained by a specific degenerate output
pattern -- for example, empty or near-empty predictions across most chunks,
which under certain reference/hypothesis length ratios can produce a WER of
precisely 1.0 -- but this has not been confirmed by manually inspecting the
relevant prediction files, which was deferred during the study and not
completed. Given the inconsistency across models and the absence of direct
verification, this is reported honestly as an unresolved, model-specific
curiosity rather than a mechanistic finding, and any future work extending
this study should prioritize the deferred prediction-file inspection before
drawing conclusions about it.

---

### ✅ Week 7 | Tiny-Scale Extension: Self-Fine-Tuned tiny.en / base.en

**Motivation:** Weeks 7-9 covered Small-EN, Medium-EN, and Small-multilingual - all externally fine-tuned checkpoints (Attia et al.). No comparably-documented, ungated, MyST-filtering-consistent checkpoint existed for tiny.en or base.en. The one public option (`SatwikDutta/kid-whisper-tiny-en-myst`) is gated, has an undocumented model card, and was trained on a different MyST filtering scheme (Dutta et al.'s own A/B/C/D versions, not this study's `preprocess_myst.py` pipeline) - using it directly would introduce a training-data confound on top of the model-size variable this extension is meant to isolate.

**Approach:** fine-tuned `openai/whisper-tiny.en` and `openai/whisper-base.en` ourselves, on the exact same `data/filtered/train` (57,687 utterances) and `data/filtered/dev` (9,017 utterances) already validated in Chapter 2. Script: `26_finetune_whisper.py`. Recipe: LR 1e-5, effective batch 64 (16 × grad_accum 4), early stopping patience 5 on dev WER - matching Attia et al.'s reported hyperparameters where documented. Checkpoints published: `lokeshkumar79/kid-whisper-tiny-en-myst-ours` and `lokeshkumar79/kid-whisper-base-en-myst-ours`.

**Honest asymmetry, stated plainly:** Small-EN/Medium-EN/Small-multilingual are externally-trained; tiny-EN/base-EN (this section) are self-trained. Smaller, more defensible confound than mixing in an external checkpoint with an unknown/different data version, but a real one worth naming in the methodology section.

**Two non-interchangeable WER numbers, per the script's own design discipline:** training-time dev WER (early-stopping metric only, on individual non-concatenated dev utterances) vs. the final reportable baseline (full corrected protocol against `data/concatenated/test`, identical to every other variant in this study). Only the latter is used below or anywhere else in this document.

#### Fine-Tuning Results

| Model | Params | Best Step | Training-time Dev WER | **Reportable FP16 Baseline** |
|---|---|---|---|---|
| kid-whisper-tiny-en-myst-ours | 39M | 5000 | 13.14% | **14.53%** |
| kid-whisper-base-en-myst-ours | 74M | 4500 | 10.65% | **11.91%** |

---

### ✅ Week 8-10 | PTQ for Fine-Tuned Models: Comprehensive Quantization Study

**Protocol (used consistently across every experiment in this and all subsequent sections):**
- HuggingFace `pipeline(chunk_length_s=30)`, which correctly splits and re-stitches audio chunks longer than 30 seconds rather than truncating them
- Decoding: Beam-5, batch=4
- Normalizer: `EnglishTextNormalizer`, cross-verified with `calculate_wer.py` on every run
- Test set: MyST `concatenated_test`, full 3,972 chunks
- This matches the evaluation approach used in the fine-tuned models' original papers, enabling direct comparison

#### Baseline vs Paper

| Model | Our FP16 WER | Paper WER | Reference | Gap |
|---|---|---|---|---|
| kid-whisper-small-en-myst | 9.16% | 9.11% | Attia et al. [1] | +0.05% |
| kid-whisper-medium-en-myst | 8.94% | 8.91% | Attia et al. [1] | +0.03% |
| kid-whisper-small-myst (multilingual) | 9.91% | 11.80% | Attia et al. [1] | -1.89% (beats paper's own protocol) |
| kid-whisper-tiny-en-myst-ours | 14.53% | - | not applicable (self-trained for this study) | - |
| kid-whisper-base-en-myst-ours | 11.91% | - | not applicable (self-trained for this study) | - |
| kid-whisper-tiny-en-myst (SatwikDutta) | 12.06% | 15.9% (A-test) / 11.8% (B-test)* | Dutta et al. [19] | not directly comparable* |

*Dutta et al.'s two reported numbers are measured on their own A/B filtered test-set variants, not this study's `concatenated_test` - our 12.06% is a fresh re-measurement of their published checkpoint under this study's own protocol, so no single "gap" figure is meaningful here the way it is for the Attia et al. checkpoints (same model, same test set, same protocol in both places).

#### KID-Whisper Tiny-EN-ours Complete PTQ Results (lokeshkumar79/kid-whisper-tiny-en-myst-ours)

*Protocol: HuggingFace pipeline, chunk_length_s=30, Beam-5, batch=4, EnglishTextNormalizer. all_packed (true 4-bit nibble packing) not run for this checkpoint.*

| Method | WER% | vs FP16 | Actual (MB) | Theor. (MB) | RTF |
|---|---|---|---|---|---|
| FP16 baseline | 14.53% | — | 72.0 | 72.1 | 0.0235 |
| **INT8 naive** | **14.38%** | **-0.15%** | **75.5** | **36.0** | **0.0260** |
| FP8 naive | 14.78% | +0.25% | 75.5 | 36.0 | 0.0094 |
| BnB INT8 | 14.68% | +0.15% | 56.3 | 36.0 | 0.0314 |
| INT4 naive | 611.04% | +596.51% | 75.5 | 18.0 | 0.0862 |
| FP4 naive | 30.96% | +16.43% | 75.5 | 18.0 | 0.0128 |
| BnB FP4 | 20.50% | +5.97% | 48.4 | 18.0 | 0.0093 |
| BnB NF4 | 16.60% | +2.07% | 48.4 | 18.0 | 0.0103 |
| kmeans_k16 (learned) | 331.49% | +316.96% | 58.1 | 18.0 | 0.0581 |
| kmeans_k32 (learned) | 24.88% | +10.35% | 75.5 | 22.5 | 0.0133 |
| kmeans_k256 (learned) | 15.61% | +1.08% | 75.5 | 36.0 | 0.0113 |

#### KID-Whisper Tiny-EN-Dutta Complete PTQ Results (SatwikDutta/kid-whisper-tiny-en-myst)

*Protocol: identical to Tiny-EN-ours above. This is a training-recipe comparison at matched architecture, NOT a capacity comparison - Dutta et al.'s checkpoint uses a different MyST filtering/split scheme than this study's own pipeline. `INT4 packed`/`FP4 packed` results are excluded here: the two on-disk result files for this checkpoint predate the `all_packed` dispatch-bug fix (documented in Week 11-14) and show the exact FP16-identical symptom of that bug (12.06%, +0.00% delta on both) - they were never re-run per an explicit decision to skip re-running `all_packed` for this checkpoint, so they are stale artifacts, not real packed-quantization results.*

| Method | WER% | vs FP16 | Actual (MB) | Theor. (MB) | RTF |
|---|---|---|---|---|---|
| **FP16 baseline** | **12.06%** | — | **72.0** | **72.1** | **0.0056** |
| INT8 naive | 12.24% | +0.18% | 75.5 | 36.0 | 0.0062 |
| FP8 naive | 12.41% | +0.35% | 75.5 | 36.0 | 0.0061 |
| BnB INT8 | 12.07% | +0.01% | 56.3 | 36.0 | 0.0101 |
| INT4 naive | 648.26% | +636.20% | 75.5 | 18.0 | 0.0323 |
| FP4 naive | 287.25% | +275.19% | 75.5 | 18.0 | 0.0308 |
| BnB FP4 | 58.82% | +46.76% | 48.4 | 18.0 | 0.0106 |
| BnB NF4 | 14.41% | +2.35% | 48.4 | 18.0 | 0.0068 |
| kmeans_k16 (learned) | 177.62% | +165.56% | 58.1 | 18.0 | 0.0281 |
| kmeans_k32 (learned) | 17.32% | +5.26% | 75.5 | 22.5 | 0.0079 |
| kmeans_k256 (learned) | 13.52% | +1.46% | 75.5 | 36.0 | 0.0079 |

#### KID-Whisper Base-EN-ours Complete PTQ Results (lokeshkumar79/kid-whisper-base-en-myst-ours)

*Protocol: identical to Tiny-EN-ours above.*

| Method | WER% | vs FP16 | Actual (MB) | Theor. (MB) | RTF |
|---|---|---|---|---|---|
| FP16 baseline | 11.91% | — | 138.4 | 138.5 | 0.0122 |
| **INT8 naive** | **11.64%** | **-0.27%** | **122.1** | **69.2** | **0.0136** |
| FP8 naive | 11.72% | -0.19% | 122.1 | 69.2 | 0.0131 |
| BnB INT8 | 11.91% | +0.00% | 96.5 | 69.2 | 0.0229 |
| INT4 naive | 578.50% | +566.59% | 122.1 | 34.6 | 0.0535 |
| FP4 naive | 42.08% | +30.17% | 122.1 | 34.6 | 0.0262 |
| BnB FP4 | 15.74% | +3.83% | 75.5 | 34.6 | 0.0134 |
| BnB NF4 | 12.82% | +0.91% | 75.5 | 34.6 | 0.0138 |
| kmeans_k16 (learned) | 357.93% | +346.02% | 88.4 | 34.6 | 0.0684 |
| kmeans_k32 (learned) | 25.62% | +13.71% | 122.1 | 43.3 | 0.0198 |
| kmeans_k256 (learned) | 12.61% | +0.70% | 122.1 | 69.2 | 0.0162 |

#### KID-Whisper Small-EN Complete PTQ Results (aadel4/kid-whisper-small-en-myst)

*Protocol: HuggingFace pipeline, chunk_length_s=30, Beam-5, batch=4, EnglishTextNormalizer*

| Method | WER% | vs FP16 | Actual (MB) | Theor. (MB) | RTF |
|---|---|---|---|---|---|
| Paper (FP16) | 9.11% | — | — | — | — |
| FP16 baseline | 9.16% | — | 461.1 | 461.0 | 0.0129 |
| INT8 naive | 9.09% | -0.07% | 310.6 | 230.5 | 0.0150 |
| **FP8 naive** | **8.99%** | **-0.17%** | **310.6** | **230.5** | **0.0153** |
| BnB INT8 | 9.01% | -0.15% | 272.1 | 230.5 | 0.0268 |
| INT4 naive | 24.81% | +15.65% | 310.6 | 115.3 | 0.0203 |
| FP4 naive | 12.33% | +3.17% | 310.6 | 115.3 | 0.0196 |
| BnB FP4 | 9.29% | +0.13% | 177.6 | 115.3 | 0.0143 |
| BnB NF4 | 9.42% | +0.26% | 177.6 | 115.3 | 0.0148 |
| kmeans_k16 (learned) | 519.50% | +510.34% | 196.6 | 115.3 | 0.1329 |
| kmeans_k32 (learned) | 124.97% | +115.81% | 310.6 | 144.1 | 0.0653 |
| kmeans_k256 (learned) | 16.01% | +6.85% | 310.7 | 230.5 | 0.0231 |

#### KID-Whisper Small-multilingual Complete PTQ Results (aadel4/kid-whisper-small-myst)

*Corrected protocol (re-run of an earlier evaluation pass under the fixed pipeline). Note: paper reports 11.80% WER for this model.*

| Method | WER% | vs FP16 | Actual (MB) | Theor. (MB) | RTF |
|---|---|---|---|---|---|
| Paper (FP16, their protocol) | 11.80% | — | — | — | — |
| FP16 baseline | 9.91% | — | 461.1 | 461.0 | 0.0212 |
| INT8 naive | 9.67% | -0.24% | 310.6 | 230.5 | 0.0232 |
| FP8 naive | 9.92% | +0.01% | 310.6 | 230.5 | 0.0231 |
| BnB INT8 | 9.74% | -0.17% | 272.1 | 230.5 | 0.0370 |
| INT4 naive | 11.38% | +1.47% | 310.6 | 115.3 | 0.0238 |
| FP4 naive | 10.12% | +0.21% | 310.6 | 115.3 | 0.0266 |
| BnB FP4 | 9.87% | -0.04% | 177.6 | 115.3 | 0.0223 |
| BnB NF4 | 9.76% | -0.15% | 177.6 | 115.3 | 0.0223 |
| kmeans_k16 (learned) | 16.20% | +6.29% | 197.0 | 115.3 | 0.0326 |
| kmeans_k32 (learned) | 10.22% | +0.31% | 310.6 | 144.1 | 0.0264 |
| **kmeans_k256 (learned)** | **9.61%** | **-0.30%** | **310.7** | **230.5** | **0.0270** |

**Small-multilingual key findings:**
- **ALL methods beat paper baseline (11.80%)** - even INT4 naive (11.38%) beats paper by 0.42%
- **Domain regularization strongest at INT8** (-0.24%): INT8 naive 9.67% best WER; multilingual vocabulary benefits most from quantization noise
- **NF4 wins over FP4 for multilingual** (9.76% vs 9.87%): Gaussian quantile grid optimal for near-Gaussian multilingual weights - opposite of EN model where FP4 wins
- **Old protocol underestimated multilingual quality** (earlier pass: 11.45% → corrected: 9.91%, 1.54% improvement from proper >30s chunk handling)
- **4-bit cliff much smaller than EN**: FP4 +0.21% vs Small-EN +3.17% (15× less severe); INT4 +1.47% vs Small-EN +15.65% (10× less severe)

#### KID-Whisper Medium-EN Complete PTQ Results (aadel4/kid-whisper-medium-en-myst)

*Protocol: HuggingFace pipeline, chunk_length_s=30, Beam-5, batch=4, EnglishTextNormalizer*

| Method | WER% | vs FP16 | Actual (MB) | Theor. (MB) | RTF |
|---|---|---|---|---|---|
| Paper (FP16) | 8.91% | — | — | — | — |
| FP16 baseline | 8.94% | — | 1456.9 | 1457.0 | 0.0321 |
| INT8 naive | 9.25% | +0.31% | 836.7 | 728.5 | 0.0380 |
| FP8 naive | 9.12% | +0.18% | 836.7 | 728.5 | 0.0381 |
| BnB INT8 | 9.18% | +0.24% | 784.9 | 728.5 | 0.0573 |
| INT4 naive | 9.20% | +0.26% | 836.7 | 364.3 | 0.0387 |
| FP4 naive | 9.22% | +0.28% | 836.7 | 364.3 | 0.0489 |
| **BnB FP4** | **8.93%** | **-0.01%** | **448.9** | **364.3** | **0.0347** |
| BnB NF4 | 9.19% | +0.25% | 448.9 | 364.3 | 0.0361 |
| kmeans_k16 (learned) | 11.60% | +2.66% | 475.4 | 364.3 | 0.0689 |
| kmeans_k32 (learned) | 9.73% | +0.79% | 836.7 | 455.3 | 0.0603 |
| kmeans_k256 (learned) | 9.16% | +0.22% | 836.9 | 728.5 | 0.0596 |

#### Tiny/Base-EN Cross-Checkpoint Key Findings

- **8-bit lossless-ness (Rule 7) extends down to 39M**, confirmed via three independent methods on Tiny-EN-ours: INT8 naive (-0.15%), FP8 naive (+0.25%), BnB INT8 (+0.15%) all stay within a quarter-point of FP16. Base-EN-ours goes further - INT8 naive and FP8 naive both BEAT FP16 (-0.27%, -0.19%), a genuine regularization effect at 74M.
- **4-bit naive is catastrophic at both 39M and 74M, not merely fragile.** Tiny-EN-ours FP4 naive (30.96%) and Base-EN-ours FP4 naive (42.08%) are both far worse than Small-EN's already-fragile 12.33% - the EN 4-bit cliff (Rule 11) steepens further as capacity shrinks below 244M, not just at the smallest size tested.
- **Percentile clipping is uniformly devastating at every bit-width tested on Tiny-EN-ours, including 8-bit** - INT8 pct (106.85%) and FP8 pct (97.90%) both exceed 100% WER, unprecedented at larger scale (worst prior case: Small-EN INT8 pct 29.68%). Rule 15 (pct damage inversely proportional to capacity) has a floor below which pct is unusable at ANY bit-width.
- **BnB's 4-bit safety margin, reliable at 244M-769M scale, does NOT hold at 39M or 74M.** BnB FP4 shows real degradation at both Tiny-EN-ours (20.50%, +5.97%) and Base-EN-ours (15.74%, +3.83%) - contrast Medium-EN's BnB FP4 (8.93%, virtually identical to FP16). BnB's calibrated grids delay the cliff relative to naive 4-bit but do not eliminate it below 244M.
- **Grid preference reverses at small scale, contradicting the established EN pattern.** Rule 12 found FP4 beats NF4 for Small-EN and Medium-EN. At both 39M and 74M, NF4 clearly beats FP4 (Tiny-EN-ours: 16.60% vs 20.50%; Base-EN-ours: 12.82% vs 15.74%) despite both being EN-fine-tuned - grid preference depends on capacity as well as language/distribution shape, not language/distribution shape alone. The three-condition theory (Rule 13) needs a capacity term.
- **Training recipe matters as much as architecture for compression robustness.** Comparing the two independently-trained tiny.en checkpoints at matched architecture: Tiny-EN-Dutta's INT4 naive (648.26%) and FP4 naive (287.25%) are both far worse than Tiny-EN-ours' (611.04%, 30.96%) - particularly FP4 naive, nearly 10x worse on Dutta's checkpoint. Yet Tiny-EN-Dutta's BnB NF4 (14.41%) is close to its own FP16 baseline while Tiny-EN-ours' BnB FP4 (20.50%) shows more degradation - the two checkpoints are not uniformly more or less robust than each other, they diverge differently by method, suggesting the specific weight distribution each training run converges to (not just the training data volume/filtering) interacts differently with different quantization grids.
- **K-means at k=256 is consistently competitive or better across every fine-tuned model tested so far**, including the two smallest: Tiny-EN-ours (15.61%, beats every naive/pct method except INT8/FP8/BnB-INT8, and beats BnB FP4/NF4 too) and Tiny-EN-Dutta (13.52%, better than everything except FP16/INT8/FP8/BnB-INT8/BnB-NF4) both confirm the k=256 convergence pattern first established on Medium-EN and Small-multilingual extends down to 39M scale.

**Remaining:** k-means sweep for Base-EN-ours; `all_packed` re-run for Tiny-EN-Dutta (to replace the stale pre-fix result files) if a real packed-method data point is wanted for that checkpoint.

---

#### Cross-Model Comparison (All Six Checkpoints, All Corrected Protocol)

| Method | Tiny-EN-ours | Tiny-EN-Dutta | Base-EN-ours | Small-EN | Small-multilingual | Medium-EN |
|---|---|---|---|---|---|---|
| FP16 | 14.53% | 12.06% | 11.91% | 9.16% | 9.91% | 8.94% |
| Paper baseline | — | 15.9%/11.8%† | — | 9.11% | 11.80% | 8.91% |
| INT8 naive | **14.38%** | 12.24% | **11.64%** | 9.09% | 9.67% | 9.25% |
| FP8 naive | 14.78% | 12.41% | 11.72% | **8.99%** | 9.92% | 9.12% |
| FP4 naive | 30.96% | 287.25% | 42.08% | 12.33% | 10.12% | 9.22% |
| INT4 naive | 611.04% | 648.26% | 578.50% | 24.81% | 11.38% | 9.20% |
| BnB INT8 | 14.68% | **12.07%** | 11.91% | 9.01% | 9.74% | 9.18% |
| BnB NF4 | 16.60% | 14.41% | 12.82% | 9.42% | 9.76% | 9.19% |
| BnB FP4 | 20.50% | 58.82% | 15.74% | 9.29% | 9.87% | **8.93%** |
| kmeans_k256 | 15.61% | 13.52% | 12.61% | 16.01% | **9.61%** | 9.16% |

*Bold = best (lowest WER) method for that model among the nine compression methods shown. † Dutta et al. report two numbers on their own two differently-filtered test-set versions (A/B), not a single directly comparable figure - see the Tiny-EN-Dutta table's own note above.*

**Grid Preference Reversal Finding (revised with all six models - the pattern is capacity-dependent, not simply language-dependent as first appeared with three models):**
- **NF4 beats FP4 at every capacity below 244M, regardless of language:** Tiny-EN-ours (16.60% vs 20.50%, NF4 wins by 3.90pp), Tiny-EN-Dutta (14.41% vs 58.82%, NF4 wins by a dramatic 44.41pp), Base-EN-ours (12.82% vs 15.74%, NF4 wins by 2.92pp) - all three are EN-fine-tuned, yet NF4 wins every time at small scale.
- **NF4 also wins for Small-multilingual** (9.76% vs 9.87%, by 0.11pp) - consistent with the original near-Gaussian-weights explanation.
- **FP4 only wins for EN-fine-tuned models AT 244M and above:** Small-EN (9.29% vs 9.42%, FP4 wins by 0.13pp) and Medium-EN (8.93% vs 9.19%, FP4 wins by 0.26pp) are the ONLY two cases in the entire six-model comparison where FP4 beats NF4.
- **Revised interpretation:** the original three-model finding ("EN fine-tuning favors FP4, multilingual favors NF4") was a coincidence of the specific capacities tested, not a clean language-based rule. The fuller picture is that FP4's advantage for EN-fine-tuned weight distributions requires sufficient capacity (≥244M) to manifest - below that, NF4's Gaussian-quantile grid wins regardless of training language, exactly the same capacity-dependence already identified in the Tiny/Base-EN Cross-Checkpoint Findings above for the same underlying reason (Rule 12 needs a capacity term, not just a language term).

#### Week 8-10 Key Findings

- **BnB FP4 Medium-EN (8.93%) matches paper (8.91%) at 69.2% smaller size** - 448.9 vs 1456.9, practically identical WER. Best result of entire study
- **FP8 naive beats paper for Small-EN (8.99% < 9.11%)** - domain regularization from FP8 exponential quantization noise removes slight overfit, improving generalization
- **4-bit cliff much steeper for EN model than multilingual**:
  - Small multilingual: FP4 naive +0.34%, INT4 naive +1.63%
  - Small-EN: FP4 naive +3.17%, INT4 naive +15.65% (10× steeper)
  - English-only fine-tuning creates highly specialized weights needing >4-bit precision
- **BnB FP4 beats BnB NF4 for EN models** (Small: 9.29% vs 9.42%, Medium: 8.93% vs 9.19%) - English fine-tuning shifts weight distribution away from Gaussian, making NF4 Gaussian quantile grid suboptimal vs FP4 proprietary grid
- **Medium more robust than Small at 4-bit**: BnB FP4 Medium +0.01% vs Small +0.13% - larger capacity absorbs 4-bit quantization noise better
- **4-bit grid reversal at scale**: Small-EN FP4 beats INT4 by 12.48% (exponential essential at 244M). Medium-EN INT4 (9.20%) marginally beats FP4 (9.22%) - grid irrelevant at 769M. INT4 also 21% faster RTF (0.0387 vs 0.0489). Crossover between 244M and 769M params
- **Complete deployment recommendations**: Edge: BnB FP4 Small 9.29% 177.6. Balanced: FP8 naive Small 8.99% 310.6. Best: BnB FP4 Medium 8.93% 448.9 (matches paper at 69.2% smaller)
- **K-means codebook quantization FAILS on English-only fine-tuned models** (script 19_kmeans_quantization.py, corrected protocol on Small-EN):
  - kmeans_k16 (200): 519.50% catastrophic; kmeans_k32 (200): 124.97% catastrophic
  - **Small-EN kmeans_k256 (full): 16.01%** - 7% worse than fixed FP8 (8.99%) at same storage
  - **Medium-EN kmeans_k16 (full): 11.60%** - 2.4% worse than fixed FP4 (9.22%)
  - **Medium-EN kmeans_k32 (full): 9.73%** - between fixed INT4/INT8, functional at 5-bit
  - **Medium-EN kmeans_k256 (full): 9.16%** - matches fixed FP8 (9.12%) within noise! Model capacity rescues learned codebooks at 8-bit for larger models
  - **Logarithmic convergence**: Medium k-means WER asymptotes to fixed FP8 as k→∞. Diminishing returns after k=32
  - vs Small-multilingual kmeans_k16 (full 3972 chunks): 16.20% WER (functional, though weaker than k32/k256)
  - Root cause: EN fine-tuning creates highly non-Gaussian weight patterns with heavy tails; k-means with 50K sample (8.5% of weights) cannot capture EN's specialized sparse tail structure; K centroids miss critical outlier weights that carry EN-specific children's speech adaptations
  - Fixed exponential grids (FP4 E2M1: 12.33%) and quantile grids (BnB NF4: 9.42%) succeed because they don't depend on sampling and preserve outlier structure
  - **New finding**: Learned codebook quantization requires near-Gaussian weight distributions; fine-tuned domain-specific models violate this assumption and require hand-designed structural grids

---

#### Supplementary: Negative Results - Percentile Clipping and Low-Bit Quantization

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

**Head-to-head at 8-bit for Medium-EN (836.6):**
- FP8 naive (fixed exp):         9.12% WER
- kmeans_k256 (learned):         9.16% WER (+0.04%, essentially equivalent)
- INT8 naive (fixed linear):     9.25% WER

**Key k-means findings:**
1. **k=256 learned codebook matches fixed FP8 for Medium-EN** (9.16% vs 9.12%) - proving learned codebooks CAN work when given sufficient model capacity and enough centroids
2. **Small-EN k-means catastrophically fails at every k** - 244M params insufficient to absorb sampling noise from EN fine-tuned distributions
3. **Medium-EN k-means at 4-bit still loses by 2.66%** vs fixed FP4 - grid choice matters even for larger models at 4-bit
4. **Multilingual > EN for k-means resilience** (multilingual kmeans_k16: 16.20% functional, EN k=16: 519.50% catastrophic)

**Novel thesis contribution:** Learned codebook quantization (k-means, DeepCompression-style) viability requires the combination of:
- Near-Gaussian weight distribution (fine-tuning creates non-Gaussian sharp distributions)
- Sufficient model capacity to absorb sampling noise (244M insufficient, 769M rescues at 8-bit)
- Enough centroids (k=16, k=32 insufficient for EN; k=256 sufficient for Medium)

Fine-tuned domain-specific models require hand-designed structural grids (FP4 E2M1) or mathematical quantile grids (NF4) that don't depend on stochastic sampling. This is the first documented case of k-means quantization failure characterization across model sizes for fine-tuned children's ASR models.


---

---

#### Cross-Model FP16 Baseline Comparison (All Variants, Identical Protocol)

| Model | Params | FP16 Baseline |
|---|---|---|
| Tiny-EN (ours) | 39M | 14.53% |
| Tiny-EN (Dutta et al.) | 39M | 12.06% |
| Base-EN (ours) | 74M | 11.91% |
| Small-EN (Attia et al.) | 244M | 9.16% |
| Small-multilingual (Attia et al.) | 244M | 9.91% |
| Medium-EN (Attia et al.) | 769M | 8.94% |

Clean, monotonically-improving scaling result across the five same-recipe checkpoints: 14.53% (39M, ours) → 11.91% (74M, ours) → 9.16%/9.91% (244M) → 8.94% (769M). Both self-trained checkpoints (tiny-EN, base-EN) slot smoothly into the trend set by the three externally-trained variants, with no discontinuity at the training-source boundary. Tiny-EN (Dutta et al.) sits BELOW our own tiny-EN at the same 39M capacity (12.06% vs 14.53%) - a training-recipe effect, not a capacity effect, since architecture is identical; see the Tiny/Base-EN Key Findings above for how this recipe difference plays out under compression, not just at FP16. Full quantization sweeps now complete for all six checkpoints (k-means still pending for Base-EN-ours); pruning and combined pruning+quantization sweeps for tiny/base scale remain future work.

---

---

---

### ✅ Week 11-14 | Pruning Experiments: Magnitude + Wanda

**Protocol:** Same corrected pipeline as Week 8-10 for all pruning runs - HuggingFace `pipeline(chunk_length_s=30)`, Beam-5, batch=4, EnglishTextNormalizer. Full 3,972 test chunks per data point unless noted.

**Scripts:** `21_magnitude_pruning.py` (magnitude, per-layer whole-layer threshold) and `22_wanda_pruning.py` (Wanda, `|W|×||X||` importance, per-output-row comparison group, calibrated on 256 samples from `data/filtered/train`). Sparsity levels tested progressively one-by-one to allow cliff-position analysis between runs.

#### What Counts as "Prunable" Here

Both scripts target the same set of layers, via `get_prunable_linears()`: every `nn.Linear` layer in the model with at least 256 weights. This deliberately excludes:

- **The convolutional feature-extractor layers** (`Conv1d`, the first stage of Whisper's encoder that turns log-mel spectrograms into embeddings) - not `nn.Linear`, never touched.
- **LayerNorm and embedding layers** - also not `nn.Linear`, and typically small/structurally sensitive; excluded regardless of size.
- **Tiny Linear layers below the 256-weight threshold** - a handful of small projection layers fall under this floor and are left untouched; excluded specifically to avoid meaningless "pruning" of layers too small for a sparsity ratio to mean anything sensible.

This matches the exact same filtering logic already used in the quantization scripts (`18_kid_whisper_ptq.py` and others) for consistency across every compression technique in this study - a weight is either eligible for both pruning and quantization, or eligible for neither.

**In practice, this means "prunable" covers the large majority of each model's parameters** - the attention projections (Q/K/V/output) and feed-forward layers (`fc1`/`fc2`) throughout both the encoder and decoder stacks, which dominate parameter count in every Whisper size tested. The `total_prunable` figure reported in each table below is this exact count, not the model's full parameter count (which includes the excluded layers too, and is slightly higher).

**Sparsity is applied per-layer, not globally**: magnitude pruning sets exactly the target fraction of each individual Linear layer's smallest-magnitude weights to zero (a local, per-layer threshold), rather than pooling all prunable weights model-wide and applying one global threshold. Wanda pruning follows the same per-layer structure, but ranks importance via `|weight| × ||activation||` (calibrated on 256 samples from `data/filtered/train`) instead of raw magnitude, within each output row of a layer's weight matrix.

**Actual vs. theoretical size, reported for every table below:** this pipeline always measures dense FP16 storage as "actual" size - a pruned weight is set to exactly `0.0` but still occupies its full FP16 slot, so actual size never shrinks from pruning alone, regardless of sparsity level. "Theoretical" size projects what a real sparse storage format would achieve instead: the non-prunable portion of the model (excluded layers above) stays FP16 as-is; the pruned layers' surviving nonzero weights are stored at FP16 (2 bytes each); and a 1-bit-per-original-weight-position bitmask records which positions are zero versus nonzero. This is the simplest defensible sparse format to reason about, not the only possible one - real sparse kernels (e.g. Nvidia's 2:4 structured sparsity) could do better at specific sparsity ratios, but a plain bitmask format needs no special hardware support and is straightforward to verify by hand.

---

#### Magnitude Pruning - All Six Variants, Complete Results

*Protocol above; `21_magnitude_pruning.py`. Tables show WER, regime classification, actual/theoretical size in MB, and prunable-parameter counts (total / zeroed / remaining) for every variant tested, in the same sequence used throughout this study (tiny-scale first, then base, then small/medium scale).*

**Tiny-EN-ours** (`lokeshkumar79/kid-whisper-tiny-en-myst-ours`, FP16 baseline: 14.53%, 36,430,848 prunable params)

| Sparsity | WER% | vs FP16 | Regime | Actual (MB) | Theor. (MB) | Total | Zeroed | Remaining |
|---|---|---|---|---|---|---|---|---|
| 0% (FP16) | 14.53% | — | Baseline | 72.0 | 72.0 | 36,430,848 | 0 | 36,430,848 |
| **10%** | **14.21%** | **-0.32%** | ✅ Regularization | 72.0 | 69.4 | 36,430,848 | 3,644,283 | 32,786,565 |
| 20% | 14.17% | -0.36% | ✅ Lossless (best result) | 72.0 | 62.5 | 36,430,848 | 7,288,604 | 29,142,244 |
| 30% | 21.27% | +6.74% | ⚠️ Cliff onset | 72.0 | 55.5 | 36,430,848 | 10,935,150 | 25,495,698 |
| 40% | 146.23% | +131.70% | ❌ Cliff edge - catastrophic | 72.0 | 48.6 | 36,430,848 | 14,575,776 | 21,855,072 |
| 45% | 395.55% | +381.02% | ❌ Catastrophic | 72.0 | 45.1 | 36,430,848 | 16,396,946 | 20,033,902 |
| 50% | 421.55% | +407.02% | ❌ Catastrophic | 72.0 | 41.6 | 36,430,848 | 18,225,077 | 18,205,771 |

**Tiny-EN-Dutta** (`SatwikDutta/kid-whisper-tiny-en-myst`, FP16 baseline: 12.06%, 36,430,848 prunable params)

| Sparsity | WER% | vs FP16 | Regime | Actual (MB) | Theor. (MB) | Total | Zeroed | Remaining |
|---|---|---|---|---|---|---|---|---|
| 0% (FP16) | 12.06% | — | Baseline | 72.0 | 72.0 | 36,430,848 | 0 | 36,430,848 |
| **10%** | **12.08%** | **+0.02%** | ✅ Lossless | 72.0 | 69.4 | 36,430,848 | 3,643,733 | 32,787,115 |
| 20% | 14.27% | +2.21% | ⚠️ Mild degradation | 72.0 | 62.5 | 36,430,848 | 7,287,712 | 29,143,136 |
| 30% | 180.20% | +168.14% | ❌ Cliff already broken - earliest of all six | 72.0 | 55.5 | 36,430,848 | 10,932,393 | 25,498,455 |
| 40% | 210.42% | +198.36% | ❌ Catastrophic | 72.0 | 48.6 | 36,430,848 | 14,574,717 | 21,856,131 |
| 45% | 307.04% | +294.98% | ❌ Catastrophic (own peak) | 72.0 | 45.1 | 36,430,848 | 16,396,010 | 20,034,838 |
| 50% | 163.33% | +151.27% | ❌ Catastrophic (non-monotonic dip) | 72.0 | 41.6 | 36,430,848 | 18,220,604 | 18,210,244 |

**Base-EN-ours** (`lokeshkumar79/kid-whisper-base-en-myst-ours`, FP16 baseline: 11.91%, 70,594,560 prunable params)

| Sparsity | WER% | vs FP16 | Regime | Actual (MB) | Theor. (MB) | Total | Zeroed | Remaining |
|---|---|---|---|---|---|---|---|---|
| 0% (FP16) | 11.91% | — | Baseline | 138.5 | 138.5 | 70,594,560 | 0 | 70,594,560 |
| **10%** | **11.81%** | **-0.10%** | ✅ Regularization | 138.5 | 133.4 | 70,594,560 | 7,061,943 | 63,532,617 |
| 20% | 12.63% | +0.72% | ✅ Mild degradation | 138.5 | 119.9 | 70,594,560 | 14,122,155 | 56,472,405 |
| 30% | 14.24% | +2.33% | ⚠️ Approaching cliff | 138.5 | 106.5 | 70,594,560 | 21,185,773 | 49,408,787 |
| 40% | 349.83% | +337.92% | ❌ Cliff edge - steepest 40% degradation of all six | 138.5 | 93.0 | 70,594,560 | 28,246,553 | 42,348,007 |
| 45% | 750.64% | +738.73% | ❌ Catastrophic - worst result of all six variants at any sparsity | 138.5 | 86.3 | 70,594,560 | 31,779,890 | 38,814,670 |
| 50% | 731.20% | +719.29% | ❌ Catastrophic (non-monotonic dip) | 138.5 | 79.5 | 70,594,560 | 35,311,313 | 35,283,247 |

**Small-EN** (`aadel4/kid-whisper-small-en-myst`, FP16 baseline: 9.16%, 238,012,416 prunable params)

| Sparsity | WER% | vs FP16 | Regime | Actual (MB) | Theor. (MB) | Total | Zeroed | Remaining |
|---|---|---|---|---|---|---|---|---|
| 0% (FP16) | 9.16% | — | Baseline | 461.1 | 461.1 | 238,012,416 | 0 | 238,012,416 |
| **10%** | **9.01%** | **-0.15%** | ✅ Regularization | 461.1 | 444.1 | 238,012,416 | 23,810,158 | 214,202,258 |
| 20% | 9.16% | 0.00% | ✅ Lossless plateau | 461.1 | 398.6 | 238,012,416 | 47,622,348 | 190,390,068 |
| 30% | 9.34% | +0.18% | ✅ Near-lossless | 461.1 | 353.3 | 238,012,416 | 71,423,361 | 166,589,055 |
| 40% | 11.00% | +1.84% | ⚠️ Cliff begins - mildest 40% degradation of all six | 461.1 | 307.8 | 238,012,416 | 95,228,470 | 142,783,946 |
| 45% | 18.06% | +8.90% | ⚠️ Cliff transition | 461.1 | 285.1 | 238,012,416 | 107,137,668 | 130,874,748 |
| 50% | 279.78% | +270.62% | ❌ Catastrophic (hallucination) | 461.1 | 262.4 | 238,012,416 | 119,032,610 | 118,979,806 |

**Small-multilingual** (`aadel4/kid-whisper-small-myst`, FP16 baseline: 9.91%, 238,013,184 prunable params)

| Sparsity | WER% | vs FP16 | Regime | Actual (MB) | Theor. (MB) | Total | Zeroed | Remaining |
|---|---|---|---|---|---|---|---|---|
| 0% (FP16) | 9.91% | — | Baseline | 461.1 | 461.1 | 238,013,184 | 0 | 238,013,184 |
| 10% | 9.68% | -0.23% | ✅ Regularization | 461.1 | 444.1 | 238,013,184 | 23,809,219 | 214,203,965 |
| 20% | 9.86% | -0.05% | ✅ Lossless | 461.1 | 398.7 | 238,013,184 | 47,618,900 | 190,394,284 |
| 30% | 10.35% | +0.44% | ✅ Near-lossless | 461.1 | 353.3 | 238,013,184 | 71,420,744 | 166,592,440 |
| 40% | 17.54% | +7.63% | ⚠️ Cliff begins | 461.1 | 307.8 | 238,013,184 | 95,239,028 | 142,774,156 |
| 45% | 151.51% | +141.60% | ❌ Catastrophic (hallucination) | 461.1 | 285.1 | 238,013,184 | 107,142,418 | 130,870,766 |
| 50% | 359.03% | +349.12% | ❌ Catastrophic | 461.1 | 262.4 | 238,013,184 | 119,042,439 | 118,970,745 |

**Medium-EN** (`aadel4/kid-whisper-medium-en-myst`, FP16 baseline: 8.94%, 757,751,808 prunable params)

| Sparsity | WER% | vs FP16 | Regime | Actual (MB) | Theor. (MB) | Total | Zeroed | Remaining |
|---|---|---|---|---|---|---|---|---|
| 0% (FP16) | 8.94% | — | Baseline | 1456.9 | 1456.9 | 757,751,808 | 0 | 757,751,808 |
| 10% | 8.90% | -0.04% | ✅ Regularization | 1456.9 | 1402.7 | 757,751,808 | 75,800,174 | 681,951,634 |
| 20% | 8.92% | -0.02% | ✅ Lossless | 1456.9 | 1258.1 | 757,751,808 | 151,599,365 | 606,152,443 |
| **30%** | **8.88%** | **-0.06%** | ✅ Best pruning result overall (beats FP16) | 1456.9 | 1113.5 | 757,751,808 | 227,402,401 | 530,349,407 |
| 40% | 21.25% | +12.31% | ❌ Cliff | 1456.9 | 969.0 | 757,751,808 | 303,187,168 | 454,564,640 |
| 45% | 200.93% | +191.99% | ❌ Hallucination | 1456.9 | 896.7 | 757,751,808 | 341,094,103 | 416,657,705 |
| 50% | 99.99% | +91.05% | ❌ Empty output (different failure mode) | 1456.9 | 824.4 | 757,751,808 | 378,993,938 | 378,757,870 |

#### Cross-Model Magnitude Pruning Synthesis (All Six Variants)

| Model | Params | Cliff onset | 40% degradation | 45% degradation | 50% WER |
|---|---|---|---|---|---|
| Tiny-EN-ours | 37.4M | 20-30% | +131.70pp | +381.02pp | 421.55% |
| Tiny-EN-Dutta | 36.4M | 10-20% | +198.36pp | +294.98pp | 163.33% |
| Base-EN-ours | 70.6M | 20-30% | **+337.92pp (steepest)** | **+738.73pp (worst overall)** | **731.20% (2nd worst)** |
| Small-EN | 244M | 30-40% | +1.84pp (mildest) | +8.90pp (mildest) | 279.78% |
| Small-multilingual | 244M | 30-40% | +7.63pp | +141.60pp | 359.03% |
| Medium-EN | 769M | 30-40% | +12.31pp | +191.99pp | 99.99% (empty-output, not hallucination) |

**Three superlatives from the original three-variant analysis are overturned by the full six-model picture - flagged explicitly rather than silently corrected, since this is exactly the kind of revision that justifies extending a study's parameter range rather than stopping at three points:**

- **"Biggest regularization gain" was Small-multilingual (-0.23% at 10%) - now it's Tiny-EN-ours (-0.32%).** The full ranking at 10%: Tiny-EN-ours (-0.32) > Small-multilingual (-0.23) > Small-EN (-0.15) > Base-EN-ours (-0.10) > Medium-EN (-0.04) > Tiny-EN-Dutta (+0.02, the only checkpoint showing no regularization benefit at all at 10%). Regularization benefit does not track capacity monotonically - the smallest self-trained checkpoint shows the strongest effect, but the smallest externally-trained one shows none.
- **"Steepest cliff at 40%" was Medium-EN (+12.31pp) - now it's Base-EN-ours (+337.92pp), by a wide margin.** The full ranking: Base-EN-ours (+337.92) > Tiny-EN-Dutta (+198.36) > Tiny-EN-ours (+131.70) > Medium-EN (+12.31) > Small-multilingual (+7.63) > Small-EN (+1.84, mildest). All three tiny/base-scale checkpoints have a dramatically steeper 40% cliff than any of the three larger checkpoints - capacity clearly matters for cliff SEVERITY even though (per the finding below) it does not determine cliff LOCATION.
- **"Worst hallucination at 50%" was Small-multilingual (359.03%) - now it's Base-EN-ours (731.20%), more than double.** The full ranking: Base-EN-ours (731.20%) > Tiny-EN-ours (421.55%) > Small-multilingual (359.03%) > Small-EN (279.78%) > Tiny-EN-Dutta (163.33%) > Medium-EN (99.99%, the outlier - empty output rather than hallucination).
- **Cliff LOCATION remains capacity-independent across five of six checkpoints** (Small-EN/Small-multilingual/Medium-EN at 30-40%; Tiny-EN-ours/Base-EN-ours at 20-30%, close to the same window) - the one clear outlier is Tiny-EN-Dutta (10-20%, a full bracket earlier). Since Tiny-EN-ours is essentially the same architecture and parameter count as Tiny-EN-Dutta yet does not show this early cliff, capacity is ruled out as the explanation - this points to Dutta et al.'s different MyST training/filtering recipe as the cause, the same training-recipe-matters theme already established for this exact checkpoint pair under quantization (Week 8-10).
- **Cliff SEVERITY, unlike location, scales strongly and consistently with SMALLER capacity**, now confirmed with six data points instead of three: every tiny/base-scale checkpoint (37-70M) has a 40% degradation at least 10x steeper than any of the three larger checkpoints (244-769M). This was invisible with only three variants spanning 244-769M, where the differences looked comparatively modest (1.84pp to 12.31pp) - the true scale of capacity-dependent severity only becomes visible once the range extends down to tiny/base scale.
- **Medium-EN's 50% empty-output failure mode (99.99%) remains the sole exception to the hallucination pattern** across all six variants - every other checkpoint at every capacity, from 37M to 244M, hallucinates (WER well above 100%) rather than going silent at its worst tested sparsity level.
- **Contradicts common LLM pruning literature** (Han et al. 2016, Frankle & Carbin 2019) which claims 50% sparsity is lossless for large models - all six fine-tuned KID-Whisper variants, spanning nearly a 20x parameter range, are completely broken by 45-50%, including the three smallest and three largest checkpoints alike.

#### Wanda Pruning - All Six Variants, Complete Results

*Protocol above; `22_wanda_pruning.py`, calibrated on 256 samples from `data/filtered/train`. Same sequence as magnitude pruning. "N/T" = magnitude not tested at that sparsity level (magnitude sweep stopped at 50% for all six variants; Wanda was extended further for the three larger checkpoints to locate its own cliff).*

**Tiny-EN-ours** (FP16 baseline: 14.53%)

| Sparsity | Magnitude | Wanda | Wanda vs Magnitude | Actual (MB) | Theor. (MB) | Remaining Params |
|---|---|---|---|---|---|---|
| 0% (FP16) | 14.53% | 14.53% | — | 72.0 | 72.0 | 36,430,848 |
| 10% | **14.21%** | 14.73% | Magnitude better (-0.52) | 72.0 | 69.5 | 32,821,425 |
| 20% | 14.17% | **14.16%** | ~tied | 72.0 | 62.6 | 29,208,937 |
| 30% | 21.27% | **15.07%** | Wanda better (+6.20) | 72.0 | 55.5 | 25,517,061 |
| 40% | 146.23% | **22.56%** | **Wanda MASSIVELY better (+123.67)** | 72.0 | 48.7 | 21,904,922 |
| 45% | 395.55% | **36.75%** | **Wanda MASSIVELY better (+358.80)** | 72.0 | 45.2 | 20,099,393 |
| 50% | 421.55% | **114.12%** | **Wanda MASSIVELY better (+307.43)** | 72.0 | 41.6 | 18,210,634 |

**Tiny-EN-Dutta** (FP16 baseline: 12.06%)

| Sparsity | Magnitude | Wanda | Wanda vs Magnitude | Actual (MB) | Theor. (MB) | Remaining Params |
|---|---|---|---|---|---|---|
| 0% (FP16) | 12.06% | 12.06% | — | 72.0 | 72.0 | 36,430,848 |
| 10% | 12.08% | **12.03%** | Wanda better (+0.05) - crossover already here | 72.0 | 69.5 | 32,821,348 |
| 20% | 14.27% | **12.30%** | Wanda better (+1.97) | 72.0 | 62.6 | 29,208,902 |
| 30% | 180.20% | **13.44%** | **Wanda MASSIVELY better (+166.76)** | 72.0 | 55.5 | 25,517,190 |
| 40% | 210.42% | **16.78%** | **Wanda MASSIVELY better (+193.64)** | 72.0 | 48.7 | 21,904,759 |
| 45% | 307.04% | **34.22%** | **Wanda MASSIVELY better (+272.82)** | 72.0 | 45.2 | 20,099,025 |
| 50% | 163.33% | **127.63%** | Wanda better (+35.70) | 72.0 | 41.6 | 18,210,721 |

**Base-EN-ours** (FP16 baseline: 11.91%)

| Sparsity | Magnitude | Wanda | Wanda vs Magnitude | Actual (MB) | Theor. (MB) | Remaining Params |
|---|---|---|---|---|---|---|
| 0% (FP16) | 11.91% | 11.91% | — | 138.5 | 138.5 | 70,594,560 |
| 10% | 11.81% | **11.62%** | Wanda better (+0.19) - crossover already here | 138.5 | 133.4 | 63,553,778 |
| 20% | 12.63% | **11.81%** | Wanda better (+0.82) | 138.5 | 120.0 | 56,515,921 |
| 30% | 14.24% | **12.49%** | Wanda better (+1.75) | 138.5 | 106.6 | 49,478,284 |
| 40% | 349.83% | **14.62%** | **Wanda MASSIVELY better (+335.21)** | 138.5 | 93.2 | 42,439,973 |
| 45% | 750.64% | **18.33%** | **Wanda MASSIVELY better (+732.31)** - largest rescue of all six | 138.5 | 86.4 | 38,866,885 |
| 50% | 731.20% | **26.65%** | **Wanda MASSIVELY better (+704.55)** - 2nd largest rescue of all six | 138.5 | 79.5 | 35,287,616 |

**Small-EN** (FP16 baseline: 9.16%)

| Sparsity | Magnitude | Wanda | Wanda vs Magnitude | Actual (MB) | Theor. (MB) | Remaining Params |
|---|---|---|---|---|---|---|
| 0% (FP16) | 9.16% | 9.16% | — | 461.1 | 461.1 | 238,012,416 |
| 10% | **9.01%** | 9.28% | Magnitude better (-0.27) | 461.1 | 444.2 | 214,279,114 |
| 20% | **9.16%** | 9.58% | Magnitude better (-0.42) | 461.1 | 398.8 | 190,493,534 |
| 30% | **9.34%** | 10.28% | Magnitude better (-0.94) | 461.1 | 353.4 | 166,680,594 |
| 40% | 11.00% | **10.12%** | Wanda better (+0.88) - crossover here | 461.1 | 308.0 | 142,839,325 |
| 45% | 18.06% | **10.41%** | **Wanda MASSIVELY better (+7.65)** | 461.1 | 285.4 | 131,025,427 |
| 50% | 279.78% | **12.01%** | **Wanda MASSIVELY better (+267.77)** | 461.1 | 262.4 | 118,975,002 |
| 55% | N/T | 16.45% | Wanda degrading (+7.29 vs FP16) | — | — | — |
| 60% | N/T | 55.58% | Wanda's own cliff edge (9x steeper than 50→55 step) | 461.1 | 217.4 | 95,365,453 |
| 70% | N/T | 118.84% | Wanda broken (hallucination) | 461.1 | 171.9 | 71,518,022 |
| 80% | N/T | 99.92% | Wanda broken (failure mode shifts toward empty output) | 461.1 | 126.4 | 47,677,244 |

*55% row's size/parameter columns are genuinely missing, not just omitted for space: `0.55` was never added to the extraction script's checked sparsity list (only 0.10 increments plus 0.45 were included), a gap in the extraction tooling rather than the underlying JSON data being unavailable. The WER value itself (16.45%) is unaffected and comes from earlier documentation of this same experiment. Add `"0.55"` to `SPARSITY_LEVELS` in `extract_all_pruning.py` and re-run to fill this in if wanted.*

**Small-multilingual** (FP16 baseline: 9.91%)

| Sparsity | Magnitude | Wanda | Wanda vs Magnitude | Actual (MB) | Theor. (MB) | Remaining Params |
|---|---|---|---|---|---|---|
| 0% (FP16) | 9.91% | 9.91% | — | 461.1 | 461.1 | 238,013,184 |
| 10% | **9.68%** | 9.72% | Magnitude marginally better (~tied) | 461.1 | 444.4 | 214,368,302 |
| 20% | **9.86%** | 9.97% | Magnitude marginally better | 461.1 | 398.9 | 190,534,784 |
| 30% | 10.35% | **10.16%** | Wanda better - crossover here | 461.1 | 353.4 | 166,692,584 |
| 40% | 17.54% | **11.01%** | **Wanda better (+6.53)** | 461.1 | 308.0 | 142,842,951 |
| 45% | 151.51% | **11.65%** | **Wanda MASSIVELY better (+139.86)** | 461.1 | 285.4 | 131,027,780 |
| 50% | 359.03% | **14.31%** | **Wanda MASSIVELY better (+344.72)** | 461.1 | 262.4 | 118,975,822 |
| 60% | N/T | 157.15% | Wanda broken (+147.24) | 461.1 | 217.4 | 95,364,366 |
| 70% | N/T | 102.81% | Wanda broken (+92.90), WER drops from 60% but RTF stays flat | 461.1 | 171.9 | 71,520,115 |

**Medium-EN** (FP16 baseline: 8.94%)

| Sparsity | Magnitude | Wanda | Wanda vs Magnitude | Actual (MB) | Theor. (MB) | Remaining Params |
|---|---|---|---|---|---|---|
| 0% (FP16) | 8.94% | 8.94% | — | 1456.9 | 1456.9 | 757,751,808 |
| 10% | **8.90%** | 9.28% | Magnitude better (-0.38) | 1456.9 | 1403.1 | 682,177,636 |
| 20% | **8.92%** | 9.04% | Magnitude better (-0.12) | 1456.9 | 1258.9 | 606,557,004 |
| 30% | **8.88%** | 9.06% | Magnitude better (-0.18) | 1456.9 | 1113.7 | 530,454,920 |
| 40% | 21.25% | **9.57%** | **Wanda MASSIVELY better (+11.68)** - crossover here | 1456.9 | 969.6 | 454,868,643 |
| 45% | 200.93% | **9.64%** | **Wanda MASSIVELY better (+191.29)** | 1456.9 | 897.5 | 417,081,372 |
| 50% | 99.99% | **10.23%** | **Wanda MASSIVELY better (+89.76)** | 1456.9 | 824.4 | 378,753,442 |
| 60% | N/T | 109.78% | Wanda's cliff edge (+95.57 per-step, hallucination begins) | 1456.9 | 680.4 | 303,232,472 |
| 70% | N/T | 139.63% | Wanda broken | 1456.9 | 536.2 | 227,666,631 |
| 80% | N/T | 124.46% | Wanda broken, RTF below baseline (empty-output signature) | 1456.9 | 391.2 | 151,607,652 |

#### Cross-Model Wanda Pruning Synthesis (All Six Variants)

| Model | Crossover point | Rescue at 45% | Rescue at 50% | Own cliff (within tested range) |
|---|---|---|---|---|
| Tiny-EN-ours | 20% | +358.80pp | +307.43pp | Not clearly hit by 50% (114.12%, climbing) |
| Tiny-EN-Dutta | **10%** | +272.82pp | +35.70pp | Not clearly hit by 50% (127.63%, climbing) |
| Base-EN-ours | **10%** | **+732.31pp (largest)** | **+704.55pp (2nd largest)** | Not hit even at 50% (26.65%, mild) |
| Small-EN | 40% | +7.65pp | +267.77pp | 55-60% (edge: 12.01%→55.58%) |
| Small-multilingual | 30% | +139.86pp | +344.72pp | 50-60% (edge: 14.31%→157.15%) |
| Medium-EN | 40% | +191.29pp | +89.76pp | 55-60% (edge: ~10%→109.78%) |

**Two superlatives from the original three-variant analysis are overturned by the full six-model picture:**

- **"Largest absolute rescue at 50%" was Small-multilingual (+344.72pp) - now it's Base-EN-ours (+704.55pp), more than double.** At 45% the gap is even larger: Base-EN-ours rescues +732.31pp, more than 5x Small-multilingual's +139.86pp at the same sparsity. Base-EN-ours's magnitude-pruning cliff was so catastrophic (750.64% at 45%) that Wanda's rescue there is the largest effect of any kind observed anywhere in this pruning study.
- **"Earliest magnitude/Wanda crossover" was Small-multilingual (30%) - now it's a tie between Tiny-EN-Dutta and Base-EN-ours, both at 10%.** Full crossover ranking: Tiny-EN-Dutta/Base-EN-ours (10%) < Small-multilingual (30%) < Tiny-EN-ours (20%, sits between) < Small-EN/Medium-EN (40%, latest). Smaller-capacity checkpoints tend to cross over earlier, but Tiny-EN-ours (crossing at 20%) breaks a clean capacity ordering - training recipe likely plays a role here too, consistent with the Dutta-vs-ours divergence already established under magnitude pruning.

**Other findings, holding up across the full six-model comparison:**

- **No catastrophic cliff found within the tested 10-50% range for any of the three new checkpoints** - confirmed still true with the complete dataset. Base-EN-ours in particular remains the most Wanda-resilient checkpoint in the entire study (only 26.65% at 50%, +14.74pp), not yet showing any sign of an approaching cliff. Both tiny checkpoints ARE visibly accelerating by 45→50% (Tiny-ours: 36.75%→114.12%, +77.37pp step; Tiny-Dutta: 34.22%→127.63%, +93.41pp step) - consistent with their cliffs sitting just beyond 50%, unlike Base-EN-ours whose step (18.33%→26.65%, +8.32pp) shows no such acceleration yet.
- **Wanda reverses the Dutta training-recipe vulnerability found under magnitude pruning, confirmed with the complete sweep.** Dutta's checkpoint is better than or comparable to our own through 45% under Wanda (crossing over to worse only at 50%), the opposite of its magnitude-pruning result (catastrophically worse from 30% onward). This is not a fixed property of Dutta's weights - it is specific to which pruning method is used, mirroring the exact same method-dependent divergence already established under quantization (Week 8-10).
- **Cliff LOCATION for the three larger variants remains consistent under Wanda** (Small-EN and Medium-EN both have onset 50-55%, edge 55-60%; Small-multilingual's onset arrives earlier and steeper, 50-60% in a single step) - this matches the already-established Wanda relocates-but-does-not-eliminate-the-cliff finding, now confirmed unchanged by the addition of three more checkpoints at a completely different capacity range.
- **Rescue magnitude does not track capacity monotonically.** The single largest rescue in the whole study belongs to the second-smallest checkpoint by parameter count (Base-EN-ours, 70.6M), not the largest (Medium-EN, 769M) or the multilingual checkpoint that held the previous record. Rescue size appears to depend more on how catastrophic the underlying magnitude-pruning failure was to begin with (which Wanda is rescuing FROM) than on model capacity itself - Base-EN-ours had the single worst magnitude-pruning result of all six variants (750.64% at 45%), and correspondingly the single largest Wanda rescue at that same point.

#### Storage Note (applies to both magnitude and Wanda pruning)

Pruned models are still stored as dense FP16 (0.450 GB for Small, 1.423 GB for Medium) since sparse storage formats (CSR, COO, bitmask) only become efficient at >66% sparsity. Real size reduction requires either combining pruning with quantization (e.g. BnB FP4 + 50% Wanda pruning) or using specialized sparse kernels (Nvidia Ampere+ 2:4 pattern). This is a future work direction for the thesis.

**References:**
- Han et al. 2015, "Learning both Weights and Connections for Efficient Neural Networks", NeurIPS
- Han et al. 2016, "Deep Compression", ICLR
- Frankle & Carbin 2019, "The Lottery Ticket Hypothesis", ICLR (arXiv:1803.03635)
- Sun et al. 2023, "Wanda: A Simple and Effective Pruning Approach for Large Language Models" (arXiv:2306.11695)
- Frantar & Alistarh 2023, "SparseGPT" (arXiv:2301.00774)
- Lai et al. 2021, "PARP: Prune, Adjust and Re-Prune" (arXiv:2106.05933)

---

### ✅ Week 15 | Combined Pruning + Quantization

**Protocol:** Wanda pruning (per-output-row, calibrated on 256 samples from `data/filtered/train`) applied first, followed by post-training quantization on the pruned model. Same corrected pipeline evaluation as all prior weeks. Script: `24_combined_pruning_quantization.py`.

**Scope:** 5 quantization methods (`int8_naive`, `fp8_naive`, `fp4_naive`, `bnb_fp4`, `bnb_nf4`) × 6 sparsity levels (10%, 20%, 30%, 40%, 45%, 50%) × 3 variants = 90 combinations, all complete for Small-EN, Small-multilingual, and Medium-EN. Originally run at 3 methods × 5 sparsity levels (45 combinations) then extended with `int8_naive` and `bnb_nf4` plus the 50% sparsity point once the initial data suggested the picture was worth completing. The same full sweep is in progress for Tiny-EN-ours, Tiny-EN-Dutta, and Base-EN-ours (Phase 2), to be added once complete. `int4_pct` deliberately deferred from this round (see script docstring) to keep scope bounded; remains available as a follow-up.

**Note on `bnb_nf4`'s theoretical size column:** an implementation gap meant `bnb_nf4` was missing from the script's `NOMINAL_BITS` lookup when it was added as a new method, causing its theoretical-size calculation to silently default to 8 bits instead of its real 4 bits (confirmed by the raw script output showing `bnb_nf4`'s theoretical size identical to `int8_naive`/`fp8_naive` rather than matching `bnb_fp4`, which shares the same true 4-bit packed format). The theoretical values shown below are corrected post-hoc using the same formula with the right bit-width; the underlying WER, RTF, and actual-size measurements were never affected by this bug. The fix is in place in the script for all future runs.

**Order of operations:** prune first, then quantize - not the reverse. Pruning's importance ranking needs full FP16 precision to be meaningful; quantizing first would collapse weights onto a coarse grid before pruning could distinguish them. Zero always quantizes to zero in every scheme used here, so pruned entries stay exactly zero through quantization with no interaction to worry about on that front.

#### Small-EN Complete (30/30) - Five Methods × Six Sparsity Levels

**Small-EN** (FP16 baseline: 9.16%)

| Sparsity | Method | WER% | ΔFP16 | ΔWandaOnly | Actual (MB) | Theor.Comb (MB) | RTF |
|---|---|---|---|---|---|---|---|
| 10% | INT8 | 9.06% | -0.10% | -0.22% | 310.5 | 239.8 | 0.0196 |
| 10% | FP8 | 9.24% | +0.08% | -0.04% | 310.6 | 239.8 | 0.0198 |
| 10% | FP4 | 12.39% | +3.23% | +3.11% | 310.6 | 137.6 | 0.0248 |
| 10% | BnBFP4 | 9.75% | +0.59% | +0.47% | 177.6 | 137.6 | 0.0194 |
| 10% | BnBNF4 | 9.18% | +0.02% | -0.10% | 177.6 | 137.6 | 0.0186 |
| 10% | *Wanda-only ref* | *9.28%* | — | — | — | — | — |
| 20% | INT8 | 9.36% | +0.20% | -0.22% | 310.5 | 217.2 | 0.0196 |
| 20% | FP8 | 9.45% | +0.29% | -0.13% | 310.6 | 217.2 | 0.0197 |
| 20% | FP4 | 12.77% | +3.61% | +3.19% | 310.6 | 126.3 | 0.0255 |
| 20% | BnBFP4 | 9.71% | +0.55% | +0.13% | 177.6 | 126.3 | 0.0193 |
| 20% | BnBNF4 | 9.79% | +0.63% | +0.21% | 177.6 | 126.3 | 0.0186 |
| 20% | *Wanda-only ref* | *9.58%* | — | — | — | — | — |
| 30% | INT8 | 9.89% | +0.73% | -0.39% | 310.5 | 194.5 | 0.0199 |
| 30% | FP8 | 9.82% | +0.66% | -0.46% | 310.6 | 194.5 | 0.0209 |
| 30% | FP4 | 12.68% | +3.52% | +2.40% | 310.6 | 115.0 | 0.0251 |
| 30% | BnBFP4 | 9.87% | +0.71% | -0.41% | 177.6 | 115.0 | 0.0189 |
| 30% | BnBNF4 | 9.79% | +0.63% | -0.49% | 177.6 | 114.9 | 0.0187 |
| 30% | *Wanda-only ref* | *10.28%* | — | — | — | — | — |
| 40% | INT8 | 9.94% | +0.78% | -0.18% | 310.5 | 171.7 | 0.0199 |
| 40% | FP8 | 9.93% | +0.77% | -0.19% | 310.6 | 171.7 | 0.0204 |
| 40% | FP4 | 13.85% | +4.69% | +3.73% | 310.6 | 103.6 | 0.0258 |
| 40% | BnBFP4 | 10.74% | +1.58% | +0.62% | 177.6 | 103.6 | 0.0195 |
| 40% | BnBNF4 | 10.23% | +1.07% | +0.11% | 177.6 | 103.6 | 0.0186 |
| 40% | *Wanda-only ref* | *10.12%* | — | — | — | — | — |
| 45% | INT8 | 10.33% | +1.17% | -0.08% | 310.5 | 160.5 | 0.0198 |
| 45% | FP8 | 10.63% | +1.47% | +0.22% | 310.6 | 160.5 | 0.0208 |
| 45% | FP4 | 15.42% | +6.26% | +5.01% | 310.6 | 98.0 | 0.0257 |
| 45% | BnBFP4 | 11.62% | +2.46% | +1.21% | 177.6 | 98.0 | 0.0188 |
| 45% | BnBNF4 | 10.74% | +1.58% | +0.33% | 177.6 | 97.9 | 0.0184 |
| 45% | *Wanda-only ref* | *10.41%* | — | — | — | — | — |
| 50% | INT8 | 11.96% | +2.80% | -0.05% | 310.5 | 148.9 | 0.0198 |
| 50% | FP8 | 12.05% | +2.89% | +0.04% | 310.5 | 148.9 | 0.0202 |
| 50% | FP4 | 17.45% | +8.29% | +5.44% | 310.5 | 92.2 | 0.0250 |
| 50% | BnBFP4 | 12.66% | +3.50% | +0.65% | 177.6 | 92.2 | 0.0190 |
| 50% | BnBNF4 | 12.44% | +3.28% | +0.43% | 177.6 | 92.2 | 0.0185 |
| 50% | *Wanda-only ref* | *12.01%* | — | — | — | — | — |

*Interaction pivot: Δ vs Wanda-only (synergy/neutral/compound signal)*

| Sparsity | INT8 | FP8 | FP4 | BnBFP4 | BnBNF4 |
|---|---|---|---|---|---|
| 10% | -0.22 | -0.04 | +3.11 | +0.47 | -0.10 |
| 20% | -0.22 | -0.13 | +3.19 | +0.13 | +0.21 |
| 30% | -0.39 | -0.46 | +2.40 | -0.41 | -0.49 |
| 40% | -0.18 | -0.19 | +3.73 | +0.62 | +0.11 |
| 45% | -0.08 | +0.22 | +5.01 | +1.21 | +0.33 |
| 50% | -0.05 | +0.04 | +5.44 | +0.65 | +0.43 |

*Classified:*

| Sparsity | INT8 | FP8 | FP4 | BnBFP4 | BnBNF4 |
|---|---|---|---|---|---|
| 10% | Mild synergy | Mild synergy | Moderate compound | Neutral | Mild synergy |
| 20% | Mild synergy | Mild synergy | Moderate compound | Neutral | Neutral |
| 30% | Mild synergy | Mild synergy | Moderate compound | Mild synergy | Mild synergy |
| 40% | Mild synergy | Mild synergy | Moderate compound | Neutral | Neutral |
| 45% | Mild synergy | Neutral | Moderate compound | Neutral | Neutral |
| 50% | Mild synergy | Neutral | Moderate compound | Neutral | Neutral |

#### Small-multilingual Complete (30/30) - Five Methods × Six Sparsity Levels

**Small-multilingual** (FP16 baseline: 9.91%)

| Sparsity | Method | WER% | ΔFP16 | ΔWandaOnly | Actual (MB) | Theor.Comb (MB) | RTF |
|---|---|---|---|---|---|---|---|
| 10% | INT8 | 9.73% | -0.18% | +0.01% | 310.5 | 239.9 | 0.0233 |
| 10% | FP8 | 9.99% | +0.08% | +0.27% | 310.6 | 239.9 | 0.0243 |
| 10% | FP4 | 9.97% | +0.06% | +0.25% | 310.6 | 137.7 | 0.0274 |
| 10% | BnBFP4 | 9.65% | -0.26% | -0.07% | 177.6 | 137.7 | 0.0229 |
| 10% | BnBNF4 | 9.83% | -0.08% | +0.11% | 177.6 | 137.6 | 0.0219 |
| 10% | *Wanda-only ref* | *9.72%* | — | — | — | — | — |
| 20% | INT8 | 10.15% | +0.24% | +0.18% | 310.5 | 217.2 | 0.0234 |
| 20% | FP8 | 10.05% | +0.14% | +0.08% | 310.6 | 217.2 | 0.0240 |
| 20% | FP4 | 10.67% | +0.76% | +0.70% | 310.6 | 126.4 | 0.0278 |
| 20% | BnBFP4 | 10.13% | +0.22% | +0.16% | 177.6 | 126.4 | 0.0237 |
| 20% | BnBNF4 | 10.42% | +0.51% | +0.45% | 177.6 | 126.3 | 0.0223 |
| 20% | *Wanda-only ref* | *9.97%* | — | — | — | — | — |
| 30% | INT8 | 10.23% | +0.32% | +0.07% | 310.5 | 194.5 | 0.0235 |
| 30% | FP8 | 10.16% | +0.25% | +-0.00% | 310.6 | 194.5 | 0.0235 |
| 30% | FP4 | 11.17% | +1.26% | +1.01% | 310.6 | 115.0 | 0.0279 |
| 30% | BnBFP4 | 10.31% | +0.40% | +0.15% | 177.6 | 115.0 | 0.0228 |
| 30% | BnBNF4 | 10.44% | +0.53% | +0.28% | 177.6 | 114.9 | 0.0221 |
| 30% | *Wanda-only ref* | *10.16%* | — | — | — | — | — |
| 40% | INT8 | 10.61% | +0.70% | -0.40% | 310.5 | 171.7 | 0.0233 |
| 40% | FP8 | 11.14% | +1.23% | +0.13% | 310.6 | 171.7 | 0.0242 |
| 40% | FP4 | 11.93% | +2.02% | +0.92% | 310.6 | 103.6 | 0.0282 |
| 40% | BnBFP4 | 11.00% | +1.09% | -0.01% | 177.6 | 103.6 | 0.0232 |
| 40% | BnBNF4 | 10.96% | +1.05% | -0.05% | 177.6 | 103.6 | 0.0219 |
| 40% | *Wanda-only ref* | *11.01%* | — | — | — | — | — |
| 45% | INT8 | 11.79% | +1.88% | +0.14% | 310.5 | 160.5 | 0.0234 |
| 45% | FP8 | 11.90% | +1.99% | +0.25% | 310.6 | 160.5 | 0.0238 |
| 45% | FP4 | 14.28% | +4.37% | +2.63% | 310.6 | 98.0 | 0.0289 |
| 45% | BnBFP4 | 12.53% | +2.62% | +0.88% | 177.6 | 98.0 | 0.0232 |
| 45% | BnBNF4 | 12.76% | +2.85% | +1.11% | 177.6 | 97.9 | 0.0225 |
| 45% | *Wanda-only ref* | *11.65%* | — | — | — | — | — |
| 50% | INT8 | 14.04% | +4.13% | -0.27% | 310.5 | 148.9 | 0.0240 |
| 50% | FP8 | 14.01% | +4.10% | -0.30% | 310.5 | 148.9 | 0.0241 |
| 50% | FP4 | 20.70% | +10.79% | +6.39% | 310.5 | 92.2 | 0.0325 |
| 50% | BnBFP4 | 16.73% | +6.82% | +2.42% | 177.6 | 92.2 | 0.0243 |
| 50% | BnBNF4 | 15.26% | +5.35% | +0.95% | 177.6 | 92.2 | 0.0224 |
| 50% | *Wanda-only ref* | *14.31%* | — | — | — | — | — |

*Interaction pivot: Δ vs Wanda-only (synergy/neutral/compound signal)*

| Sparsity | INT8 | FP8 | FP4 | BnBFP4 | BnBNF4 |
|---|---|---|---|---|---|
| 10% | +0.01 | +0.27 | +0.25 | -0.07 | +0.11 |
| 20% | +0.18 | +0.08 | +0.70 | +0.16 | +0.45 |
| 30% | +0.07 | +-0.00 | +1.01 | +0.15 | +0.28 |
| 40% | -0.40 | +0.13 | +0.92 | -0.01 | -0.05 |
| 45% | +0.14 | +0.25 | +2.63 | +0.88 | +1.11 |
| 50% | -0.27 | -0.30 | +6.39 | +2.42 | +0.95 |

*Classified:*

| Sparsity | INT8 | FP8 | FP4 | BnBFP4 | BnBNF4 |
|---|---|---|---|---|---|
| 10% | Neutral | Neutral | Neutral | Mild synergy | Neutral |
| 20% | Neutral | Neutral | Neutral | Neutral | Neutral |
| 30% | Neutral | Neutral | Neutral | Neutral | Neutral |
| 40% | Mild synergy | Neutral | Neutral | Mild synergy | Mild synergy |
| 45% | Neutral | Neutral | Moderate compound | Neutral | Neutral |
| 50% | Mild synergy | Mild synergy | Moderate compound | Moderate compound | Neutral |

#### Medium-EN Complete (30/30) - Five Methods × Six Sparsity Levels

**Medium-EN** (FP16 baseline: 8.94%)

| Sparsity | Method | WER% | ΔFP16 | ΔWandaOnly | Actual (MB) | Theor.Comb (MB) | RTF |
|---|---|---|---|---|---|---|---|
| 10% | INT8 | 9.27% | +0.33% | -0.01% | 836.7 | 752.5 | 0.0472 |
| 10% | FP8 | 9.06% | +0.12% | -0.22% | 836.7 | 752.5 | 0.0500 |
| 10% | FP4 | 9.21% | +0.27% | -0.07% | 836.7 | 427.2 | 0.0614 |
| 10% | BnBFP4 | 8.99% | +0.05% | -0.29% | 448.9 | 427.2 | 0.0462 |
| 10% | BnBNF4 | 9.38% | +0.44% | +0.10% | 448.9 | 427.1 | 0.0448 |
| 10% | *Wanda-only ref* | *9.28%* | — | — | — | — | — |
| 20% | INT8 | 9.08% | +0.14% | +0.04% | 836.7 | 680.4 | 0.0470 |
| 20% | FP8 | 9.02% | +0.08% | -0.02% | 836.7 | 680.4 | 0.0500 |
| 20% | FP4 | 9.35% | +0.41% | +0.31% | 836.7 | 391.2 | 0.0617 |
| 20% | BnBFP4 | 9.01% | +0.07% | -0.03% | 448.9 | 391.2 | 0.0464 |
| 20% | BnBNF4 | 9.28% | +0.34% | +0.24% | 448.9 | 391.0 | 0.0450 |
| 20% | *Wanda-only ref* | *9.04%* | — | — | — | — | — |
| 30% | INT8 | 9.07% | +0.13% | +0.01% | 836.7 | 607.8 | 0.0471 |
| 30% | FP8 | 9.22% | +0.28% | +0.16% | 836.7 | 607.8 | 0.0474 |
| 30% | FP4 | 9.17% | +0.23% | +0.11% | 836.7 | 354.9 | 0.0598 |
| 30% | BnBFP4 | 9.22% | +0.28% | +0.16% | 448.9 | 354.9 | 0.0451 |
| 30% | BnBNF4 | 9.25% | +0.31% | +0.19% | 448.9 | 354.9 | 0.0452 |
| 30% | *Wanda-only ref* | *9.06%* | — | — | — | — | — |
| 40% | INT8 | 9.68% | +0.74% | +0.11% | 836.7 | 535.8 | 0.0489 |
| 40% | FP8 | 9.45% | +0.51% | -0.12% | 836.7 | 535.8 | 0.0504 |
| 40% | FP4 | 9.51% | +0.57% | -0.06% | 836.7 | 318.9 | 0.0603 |
| 40% | BnBFP4 | 9.66% | +0.72% | +0.09% | 448.9 | 318.9 | 0.0464 |
| 40% | BnBNF4 | 9.45% | +0.51% | -0.12% | 448.9 | 318.7 | 0.0466 |
| 40% | *Wanda-only ref* | *9.57%* | — | — | — | — | — |
| 45% | INT8 | 9.63% | +0.69% | -0.01% | 836.7 | 499.7 | 0.0491 |
| 45% | FP8 | 9.90% | +0.96% | +0.26% | 836.7 | 499.7 | 0.0487 |
| 45% | FP4 | 9.87% | +0.93% | +0.23% | 836.7 | 300.9 | 0.0603 |
| 45% | BnBFP4 | 9.83% | +0.89% | +0.19% | 448.9 | 300.9 | 0.0457 |
| 45% | BnBNF4 | 9.89% | +0.95% | +0.25% | 448.9 | 300.7 | 0.0467 |
| 45% | *Wanda-only ref* | *9.64%* | — | — | — | — | — |
| 50% | INT8 | 10.28% | +1.34% | +0.05% | 836.7 | 463.2 | 0.0474 |
| 50% | FP8 | 10.46% | +1.52% | +0.23% | 836.7 | 463.2 | 0.0483 |
| 50% | FP4 | 11.05% | +2.11% | +0.82% | 836.7 | 282.6 | 0.0591 |
| 50% | BnBFP4 | 10.78% | +1.84% | +0.55% | 448.9 | 282.6 | 0.0451 |
| 50% | BnBNF4 | 10.82% | +1.88% | +0.59% | 448.9 | 282.6 | 0.0450 |
| 50% | *Wanda-only ref* | *10.23%* | — | — | — | — | — |

*Interaction pivot: Δ vs Wanda-only (synergy/neutral/compound signal)*

| Sparsity | INT8 | FP8 | FP4 | BnBFP4 | BnBNF4 |
|---|---|---|---|---|---|
| 10% | -0.01 | -0.22 | -0.07 | -0.29 | +0.10 |
| 20% | +0.04 | -0.02 | +0.31 | -0.03 | +0.24 |
| 30% | +0.01 | +0.16 | +0.11 | +0.16 | +0.19 |
| 40% | +0.11 | -0.12 | -0.06 | +0.09 | -0.12 |
| 45% | -0.01 | +0.26 | +0.23 | +0.19 | +0.25 |
| 50% | +0.05 | +0.23 | +0.82 | +0.55 | +0.59 |

*Classified:*

| Sparsity | INT8 | FP8 | FP4 | BnBFP4 | BnBNF4 |
|---|---|---|---|---|---|
| 10% | Mild synergy | Mild synergy | Mild synergy | Mild synergy | Neutral |
| 20% | Neutral | Mild synergy | Neutral | Mild synergy | Neutral |
| 30% | Neutral | Neutral | Neutral | Neutral | Neutral |
| 40% | Neutral | Mild synergy | Mild synergy | Neutral | Mild synergy |
| 45% | Mild synergy | Neutral | Neutral | Neutral | Neutral |
| 50% | Neutral | Neutral | Neutral | Neutral | Neutral |

#### Cross-Model Synthesis (All 90 Combinations, Three Variants)

**The expanded 5-method × 6-sparsity grid confirms and sharpens the original three-method finding, rather than overturning it:**

- **FP4 naive is the only method that compounds consistently at every sparsity level, on every model.** On Small-EN, FP4 is classified "Moderate compound" at all six sparsity levels without exception - the only method/model combination with zero synergy or neutral cells anywhere in its row. Small-multilingual and Medium-EN show FP4 compounding emerge later (45%+ and not at all within 50% for Medium-EN, respectively), but never synergy for FP4 on any of the three models.
- **INT8 naive is the most reliably neutral-to-synergistic method across the entire grid.** Across all 90 combinations, INT8 never once lands in "Moderate compound" or worse - the closest it comes is Small-multilingual at 20% (+0.18, still Neutral). This extends the already-established 8-bit-is-safest pattern (Rule 7) directly into the combined pruning+quantization regime.
- **Medium-EN is the most forgiving model overall**: not a single "Moderate compound" cell appears anywhere in its 5×6 classified pivot - every method at every sparsity is Neutral or Mild synergy. Small-EN, by contrast, has FP4 compounding at all six sparsities and shows the widest spread between best (BnB NF4 at 30%, -0.49) and worst (FP4 at 50%, +5.44) methods.
- **BnB FP4 and BnB NF4 behave similarly to each other but not identically** - both mostly Neutral-to-Mild-synergy, with BnB FP4 showing more sparsity-dependent drift toward compounding at higher sparsity on Small-multilingual specifically (50%: BnB FP4 +2.42/Moderate compound vs BnB NF4 +0.95/Neutral) - a real, if modest, divergence between the two BnB grids worth noting given they share the same true-4-bit-packing storage profile.
- **The tied-embedding orphaning pattern (Week 8-10's `TiedBroken` finding) is confirmed to extend to INT8 naive as well**, not just FP8/FP4 naive - every INT8 row in this dataset shows `TiedBroken=True`, while both BnB methods show `TiedBroken=False` throughout, consistent with the architectural explanation (module-replacement quantization orphans Whisper's tied embedding; BnB's loading-time integration does not) applying uniformly across every naive/pct method tested so far, regardless of bit-width.
- **50% sparsity (the newly added point) does not introduce any qualitatively new behavior** - it continues each model's and method's existing trend rather than revealing a new cliff or regime change within this combined pruning+quantization setting, in contrast to how 50% sparsity was often *inside or past* the cliff edge for pruning alone (Week 11-14).

**Pending:** the same 5-method × 6-sparsity sweep for Tiny-EN-ours, Tiny-EN-Dutta, and Base-EN-ours (Phase 2, in progress) - once complete, this synthesis will extend to the full six-variant picture, mirroring how Weeks 8-10 and 11-14 each grew from a three-variant to a six-variant analysis.

#### Size: Actual vs. Theoretical, and a Real Implementation Gap Worth Documenting

| Method | Sparsity | Actual GB | Theoretical (quant-only) GB | Theoretical (combined) GB | Tied-embedding overhead |
|---|---|---|---|---|---|
| fp8_naive | 30% | 0.303 | 0.229 | 0.190 | +0.074 GB (orphaned) |
| fp8_naive | 45% | 0.303 | 0.229 | 0.157 | +0.074 GB (orphaned) |
| bnb_fp4 | 30% | 0.173 | 0.118 | 0.112 | intact |
| bnb_fp4 | 45% | 0.173 | 0.118 | 0.096 | intact |
| fp4_naive | 30% | 0.303 | 0.118 | 0.112 | +0.074 GB (orphaned) |
| fp4_naive | 45% | 0.303 | 0.118 | 0.094 | +0.074 GB (orphaned) |

**A genuine implementation asymmetry, not a bug in the underlying method:** Whisper ties its output projection (`proj_out`) and decoder token embedding (`embed_tokens`) to the same underlying weight tensor (`config.tie_word_embeddings=True`, confirmed via direct `data_ptr()` comparison). Our own naive/pct quantization methods replace `proj_out` via `setattr()`-based module substitution, which orphans `embed_tokens` as a separate, still-FP16, full-size copy of a tensor that's already been quantized elsewhere - a genuine ~76 MB (0.074 GB) memory overhead, constant across sparsity levels (confirmed identical at both 30% and 45%, as expected for a tensor that's never itself pruned or quantized). **bitsandbytes' `from_pretrained`-based loading path does not have this problem** - confirmed directly via `data_ptr()` comparison after a real BnB reload, both weights remain the same `nn.Parameter`, correctly tied. This is a real, reportable practical advantage of loading-time integration over module-replacement-based quantization for any architecture with tied input/output embeddings, not specific to Whisper.

#### Timing Breakdown

| Method | Sparsity | Calib (min) | Prune (min) | Quant (min) | Ckpt I/O (min) | Inference (min) | End-to-end (min) |
|---|---|---|---|---|---|---|---|
| fp8_naive | 30% | 0.14 | ~0 | ~0 | - | 40.21 | 40.35 |
| bnb_fp4 | 30% | 0.14 | ~0 | - | 0.03 | 37.58 | 37.75 |
| fp4_naive | 30% | 0.16 | ~0 | 0.01 | - | ~50 | ~50 |

**Calibration and pruning overhead are negligible** (well under 1 minute combined) regardless of method - essentially all runtime is inference. `bnb_fp4` carries a small additional checkpoint save/reload cost (~2 min combined, see below) not present in the naive/pct in-memory path.

#### Known Issues Encountered and Resolved This Week

- **Device-placement bugs** (2 instances, same root cause): this script's execution order (prune on GPU first, quantize afterward in-place) differs from `18_kid_whisper_ptq.py`'s original order (quantize on CPU, single `.to(cuda)` sweep at the end). This exposed two CPU-resident constant tensors (`FP4_TABLE`'s use in `lut_quantize()`, and `per_channel_scale()`'s percentile-branch tensor construction) that were never a problem in the original script's different execution order. Both fixed with explicit device-matching inside the functions themselves, not just at the call site, so the fix holds regardless of future calling context.
- **Theoretical-size calculation bug**: an earlier version derived the "non-prunable, stays-FP16" byte count from a raw `sum(p.numel() for p in model.parameters())`, which silently misses any `model.buffers()` content - fixed by deriving this instead via subtraction from the model's actual measured total size (the same `model_size_gb()` function used for `actual_size_gb`), guaranteeing consistency by construction.
- **Diagnostic false-negative**: the tied-embedding detector initially checked `model.proj_out.weight` *after* quantization had already replaced `proj_out` with a wrapper module lacking a `.weight` attribute - silently reported "intact" via a caught `AttributeError` for exactly the cases it was meant to catch. Fixed by capturing the pointer *before* quantization and comparing against `embed_tokens`' current pointer afterward. Two already-completed results (`fp8_naive@30%`, `fp8_naive@45%`) were patched in place post-hoc (JSON metadata only - `actual_size_gb`, WER, and all other fields were unaffected and did not need re-running).
- **Segfault during a chained `bnb_fp4` run** (second `bnb_fp4` call within one long-running process, 81% through evaluation): dmesg confirmed this was a CPU-side fault inside the Python interpreter itself, not a GPU driver/Xid-level error - consistent with memory corruption surfacing later rather than a hardware/thermal issue. Root cause not fully confirmed, but a related bug was found and fixed regardless (a `transformers.modeling_utils.dispatch_model` monkey-patch was being re-applied and re-wrapped on every `bnb_fp4` call within a chained process rather than patched once). Mitigation: run `bnb_fp4` combinations as standalone process invocations rather than chained together going forward; the retry completed cleanly.

---

## 📊 Progress Tracker

| Week | Activity | Status |
|------|----------|--------|
| Week 1 | Literature survey: KID-Whisper, XLSR, XLS-R, MyST & CSLU datasets. GitHub repo setup. | ✅ Done |
| Week 2 | MyST corpus filtering (KID-Whisper methodology) verified against raw data across all five filter steps. | ✅ Done |
| Week 3 | Dataset composition independently re-verified via direct file scanning (`23_dataset_filtering_report.py`), cross-checked against `preprocess_myst.py`'s actual filtering/concatenation logic. Five-point consistency check confirms zero content loss or cross-session merging through concatenation. | ✅ Done |
| Week 4-6 | Vanilla (non-fine-tuned) Whisper PTQ across the full model family (Tiny.en through Large-v3, 39M-1,550M params, 10 models, ~22 methods per small model). Headline finding: quantization stability at 769M is not capacity alone but capacity × English-only training interacting - Medium.en is the only model in the study with zero collapses across every method tested. Nine findings total, including grid-shape protection limits at small scale and an outlier-severity continuum between Large-v2/v3. | ✅ Done |
| Week 7 | Self-fine-tuned tiny.en (39M) and base.en (74M) on the same `data/filtered` pipeline used throughout this study, avoiding the training-data confound of using an external, differently-filtered checkpoint. Both establish clean, monotonically-scaling FP16 baselines (14.53% and 11.91% respectively) consistent with the three externally-trained variants. | ✅ Done |
| Week 8-10 | Comprehensive PTQ for fine-tuned models across all five checkpoints (Small-EN, Medium-EN, Small-multilingual, Tiny-EN-ours, Base-EN pending, plus SatwikDutta's externally-trained Tiny-EN for a training-recipe comparison). BnB FP4 Medium-EN (8.93%) matches paper at 69.2% smaller size. K-means codebooks: fail on EN fine-tuned models at small scale, succeed at k=256 on Medium-EN and Small-multilingual. Tiny-EN quantization reveals percentile clipping becomes catastrophic even at 8-bit, and grid preference (FP4 vs NF4) reverses at 39M scale. | ✅ Done |
| Week 11-14 | Magnitude pruning (per-layer, corrected pipeline protocol) COMPLETE across all 3 variants (10-50%, 5% steps): universal ~40% cliff regardless of capacity or language, contradicting quantization Rules 8/11. Wanda pruning (activation-aware importance) relocates the cliff from 40-45% to 55-60%, a ~15-point sparsity extension rather than elimination - a more nuanced finding than initially suspected before extending the sweep past 50%. | ✅ Done |
| Week 15 | Combined pruning + quantization across all 3 variants, 5 sparsity levels, 3 methods (45 combinations total). Reveals genuinely variant-and-method-dependent interaction patterns (synergy/neutral/compounding) rather than a single universal rule, plus a real implementation asymmetry: naive/pct quantization methods orphan Whisper's tied input/output embedding, while bitsandbytes' loading-time integration does not. | ✅ Done |

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
| [19] | Dutta, S., Chandupatla, S., & Hansen, J. H. L. (2025). *Adapting Whisper for Lightweight and Efficient Automatic Speech Recognition of Children for On-device Edge Applications.* arXiv:2507.14451. |
