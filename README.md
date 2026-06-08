# 🎓 MTech Thesis - ASR Research

> **Goal:** Contribute a state-of-the-art (SOTA) improvement to Automatic Speech Recognition (ASR) systems.
> **Duration:** 1 Year | **Started:** June 2026

---

## 📌 Research Direction

Exploring self-supervised and weakly-supervised speech foundation models (Whisper, XLSR, XLS-R, wav2vec 2.0) with a focus on identifying gaps and contributing improvements - particularly in challenging domains such as **children's speech**, **low-resource languages (Hindi)**, and **domain adaptation** strategies.

---

## 📅 Weekly Research Log

### ✅ Week 1 - June 2026 | Literature Survey & Foundation Models Study

#### 📄 Papers Read

| # | Paper | Authors | Venue | Key Focus |
|---|-------|---------|-------|-----------|
| 1 | Kid-Whisper: Towards Bridging the Performance Gap in Automatic Speech Recognition for Children VS. Adults | Ahmed Adel Attia et al. | — | Children's ASR using Whisper fine-tuned on the MyST corpus; improved data |
| 2 | Unsupervised Cross-lingual Representation Learning for Speech Recognition (XLSR) | Conneau et al. | Interspeech 2021 | Multilingual SSL pre-training, 53 languages |
| 3 | XLS-R: Self-supervised Cross-lingual Speech Representation Learning at Scale | Babu et al. | Interspeech 2022 | 128 languages, 436K hours, 300M–2B params |

---

#### 🔬 KID-Whisper — Key Takeaways

**Model Family Studied:** OpenAI Whisper (Tiny → Large-v3)

**MyST Corpus Preprocessing & Filtering Steps Understood:**
- Raw MyST corpus: 499 hours total, 244,069 utterances, 1,372 students (Grades 3–5)
- Annotated subset: ~240 hours; usable after quality filtering: **133h train / 21h dev / 25h test**
- Filtering criteria: removal of utterances with transcription mismatches, very short segments, and high noise frames
- Spoken by children ages 8–10; conversational dialogue with a virtual science tutor

**Whisper Model Performance on MyST (Zero-shot WER %):**

| Model | Params | MyST Test WER% | OGI Test WER% |
|-------|--------|----------------|---------------|
| Whisper-tiny | 39M | 20.6 | 53.8 |
| Whisper-base | 74M | 16.8 | 38.0 |
| Whisper-small | 242M | 13.4 | 25.4 |
| Whisper-medium | 769M | 13.1 | 20.8 |
| Whisper-large | 1.55B | 12.5 | 22.9 |
| Whisper-large-v3 | 1.55B | 12.6 | 19.9 |

**After Fine-tuning on Children's Data:**

| Model | Fine-tune Data | MyST Test WER% |
|-------|---------------|----------------|
| Whisper-small | MyST 133h | ~8.2 |
| Whisper-medium | MyST 133h | ~7.6 |
| Whisper-large-v3 | MyST 133h | ~6.8 |
| Canary 1B | MyST 133h | ~5.8 ← Best |

**Key Observations:**
- Larger Whisper models do NOT always give best zero-shot children's WER (large vs large-v3 reversal on MyST)
- Fine-tuning on children's data gives ~30–50% relative WER reduction
- Domain gap between adult pre-training data and children's speech is the core challenge
- KID-Whisper specifically addresses bridging this gap with targeted fine-tuning strategies

---

#### 🔬 XLSR & XLS-R — Key Takeaways

**Base Architecture:** Both built on **wav2vec 2.0** (Baevski et al., NeurIPS 2020)
- CNN feature encoder (7 blocks, raw waveform → latent at 20ms stride)
- Shared product quantizer (cross-lingual discrete speech units)
- Transformer context network (24 layers, 1024 dims, 300M params)
- CTC head for ASR fine-tuning
- Pre-training: contrastive loss on masked latent representations

**XLSR-53 vs XLS-R Comparison:**

| Property | XLSR-53 | XLS-R 0.3B | XLS-R 1B | XLS-R 2B |
|----------|---------|------------|----------|----------|
| Year | 2020 | 2021 | 2021 | 2021 |
| Parameters | 300M | 300M | 1B | 2B |
| Languages | 53 | 128 | 128 | 128 |
| Pre-train Hours | 56,000h | 436,000h | 436,000h | 436,000h |
| English % | ~78% | ~16% | ~16% | ~16% |
| CommonVoice WER% (avg) | ~14.3 | ~12.3 | ~10.7 | — |
| BABEL WER% (avg) | 44.1 | ~38.2 | ~32.6 | — |
| LS test-clean WER% | ~3.9 | ~4.4 | ~2.8 | ~2.7 |

**Pre-training Datasets:**

| Dataset | XLSR-53 | XLS-R | Hours | Hindi? |
|---------|---------|-------|-------|--------|
| CommonVoice v1 (38 langs) | ✅ | ❌ | ~2,000h | ❌ No |
| CommonVoice v6.1 (60 langs) | ❌ | ✅ | ~7,000h | ⚠️ Uncertain |
| BABEL (14–17 langs) | ✅ | ✅ | ~1,000h | ❌ No |
| MLS — Multilingual LibriSpeech | ✅ | ✅ | ~50,000h | ❌ No |
| VoxPopuli (23 European langs) | ❌ | ✅ | ~372,000h | ❌ No |
| VoxLingua107 (107 langs) | ❌ | ✅ | ~6,628h | ✅ **Yes** |
| **Hindi in pre-training?** | **❌ No** | **✅ Yes (VoxLingua107)** | | |

**English-only Alternatives (for English-only tasks):**

| Model | Pre-train Data | Hours | LS test-clean WER% |
|-------|---------------|-------|---------------------|
| wav2vec2-base | LibriSpeech | 960h | 3.4 |
| wav2vec2-large | LibriSpeech | 960h | 2.6 |
| wav2vec2-large-lv60k | Libri-Light | 60,000h | 2.1 |
| XLS-R 0.3B (English FT) | 436,000h multilingual | — | ~4.4 (worse) |
| XLS-R 1B (English FT) | 436,000h multilingual | — | ~2.8 (matches) |

**Hindi in XLSR/XLS-R:**
- XLSR-53: ❌ Zero Hindi data in pre-training
- XLS-R: ✅ Hindi present via VoxLingua107 (~62h avg per language, YouTube-sourced)
- For Hindi ASR, both models still require supervised fine-tuning on Hindi labeled data (e.g., CommonVoice Hindi ~14h validated)

---

## 🗂️ Repository Structure (Planned)

```
mtech-thesis/
│
├── README.md                  ← This file (weekly research log)
├── papers/                    ← PDF notes and summaries
│   ├── week1/
│   └── ...
├── experiments/               ← Code for experiments
│   ├── preprocessing/
│   ├── fine-tuning/
│   └── evaluation/
├── data/                      ← Dataset scripts and metadata
└── results/                   ← WER tables, plots, ablations
```

---

## 🔖 References

1. Baevski, A., Zhou, Y., Mohamed, A., & Auli, M. (2020). *wav2vec 2.0: A Framework for Self-supervised Learning of Speech Representations.* NeurIPS 2020.
2. Conneau, A., et al. (2021). *Unsupervised Cross-lingual Representation Learning for Speech Recognition.* Interspeech 2021. arXiv:2006.13979.
3. Babu, A., et al. (2022). *XLS-R: Self-supervised Cross-lingual Speech Representation Learning at Scale.* Interspeech 2022. arXiv:2111.09296.
4. Radford, A., et al. (2022). *Robust Speech Recognition via Large-Scale Weak Supervision (Whisper).* ICML 2023. arXiv:2212.04356.
5. Fan, R., Zheng, R., & Alwan, A. (2024). *Benchmarking Children's ASR with Supervised and Self-supervised Speech Foundation Models.* Interspeech 2024. arXiv:2406.10507.
6. Lu, Y., Nicolao, L., & Bhatt, N. (2022). *Improving Children's Speech Recognition by Fine-tuning Self-supervised Adult Speech Representations.* arXiv:2211.07769.
7. Pratap, V., et al. (2020). *MLS: A Large-Scale Multilingual Dataset for Speech Research.* Interspeech 2020.
8. Wang, C., et al. (2021). *VoxPopuli: A Large-scale Multilingual Speech Corpus.* ACL 2021.
9. Valk, J., & Alumäe, T. (2021). *VoxLingua107: a Dataset for Spoken Language Recognition.* SLT 2021.
10. Shobaki, K., et al. (2000). *The OGI Kids' Speech Corpus and Recognizers.* ICSLP 2000.

---

## 📊 Progress Tracker

| Week | Dates | Activity | Status |
|------|-------|----------|--------|
| Week 1 | June 2026 | Literature survey: KID-Whisper, XLSR, XLS-R, MyST & CSLU datasets | ✅ Done |

---

*Last updated: Week 1, June 2026*
