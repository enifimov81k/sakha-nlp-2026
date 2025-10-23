# Sakha NLP: Bootstrapping NER and Sentiment Analysis for a Low-Resource Language

This repository contains code and data for our LREC 2026 submission on bootstrapping NLP for Sakha (Yakut), a low-resource Turkic language spoken by approximately 450,000 people in northeastern Siberia.

## Resources

### Datasets
- **Sakha NER Corpus**: 690 sentences, 921 entities (PER, LOC, ORG), annotated using BIO tagging
  - Inter-annotator agreement: F1=0.897, Cohen's κ=0.96
- **SAAS Corpus** (Sentiment Analysis and Sakha): 798 sentences with 3-class labels (positive, negative, neutral)
  - Dual domains: formal journalism (Kyym, 497 sentences) + informal forum (Forum YKT, 301 sentences)
  - Inter-annotator agreement: Cohen's κ=0.54, weighted κ=0.55

### Models
- Vocabulary-extended BERT variants (mBERT and RuBERT) optimized for Sakha
- Baseline NER models: F1=0.535 (mBERT base), F1=0.429 (RuBERT extended)
- Baseline sentiment models: 54% accuracy (mBERT), 49% accuracy (RuBERT)

## Repository Structure
```
.
├── sakha-ner/                                          # Named Entity Recognition
│   ├── sakha-ner-merged_with_negatives.json           # NER corpus (690 sentences)
│   ├── sakha_ner_docsplit_FAIR_v1.json                # Train/dev/test split
│   ├── sakha-wordpiece-tokenizer-cased.json           # Sakha tokenizer vocabulary
│   ├── Tokenizer_extension.ipynb                      # Vocabulary extension (mBERT + RuBERT)
│   ├── train_mbert_ner.ipynb                          # NER training with mBERT
│   ├── train_rubert_ner.ipynb                         # NER training with RuBERT
│   └── NER_annotation_guidelines.md                   # Annotation guidelines
│
└── sakha-sa/                                           # Sentiment Analysis
    ├── SAAS-corpus/                                    # Sentiment corpus (798 sentences)
    ├── Scripts/
    │   ├── MBERT-sa.ipynb                             # mBERT sentiment training
    │   ├── RUBERT-sa.ipynb                            # RuBERT sentiment training
    │   └── Cross_dataset_experiment-sa.ipynb          # Cross-domain evaluation
    └── English_annotation_guidelines.md               # Sentiment annotation guidelines
```

## Setup

### Requirements
- Python 3.8+
- CUDA-capable GPU (recommended: 16GB+ VRAM)
- Google Colab or Jupyter Notebook

### Installation
```bash
pip install transformers>=4.30.0 datasets>=2.14.0 torch>=2.0.0 seqeval>=1.2.2 evaluate>=0.4.0 scikit-learn>=1.3.0
```

## Usage

### Named Entity Recognition

#### 1. Vocabulary Extension
Run `sakha-ner/Tokenizer_extension.ipynb` to extend mBERT and RuBERT vocabularies with Sakha-specific tokens.

- Extends mBERT: 119,547 → 130,659 tokens
- Extends RuBERT: 120,138 → 131,502 tokens
- Uses length-weighted subword averaging for embedding initialization

**Runtime:** ~10-15 minutes per model on GPU

#### 2. Train NER Models

**mBERT:**
```bash
jupyter notebook sakha-ner/train_mbert_ner.ipynb
```

**RuBERT:**
```bash
jupyter notebook sakha-ner/train_rubert_ner.ipynb
```

**Expected results:**
- mBERT base: Overall F1 ≈ 0.535 (PER: 0.590, LOC: 0.560, ORG: 0.423)
- RuBERT extended: Overall F1 ≈ 0.429 (PER: 0.474, LOC: 0.434, ORG: 0.361)

**Training time:** ~2-3 hours per model on Tesla T4 GPU

### Sentiment Analysis

#### 1. Train Sentiment Models

**mBERT:**
```bash
jupyter notebook sakha-sa/Scripts/MBERT-sa.ipynb
```

**RuBERT:**
```bash
jupyter notebook sakha-sa/Scripts/RUBERT-sa.ipynb
```

**Expected results (pooled 5-fold CV):**
- mBERT: 54% accuracy, weighted F1=0.52
- RuBERT: 49% accuracy, weighted F1=0.49

#### 2. Cross-Domain Evaluation

Run `sakha-sa/Scripts/Cross_dataset_experiment-sa.ipynb` to evaluate domain transfer:
- Kyym (formal) → Forum (informal): 47% accuracy
- Forum (informal) → Kyym (formal): 26% accuracy

## Key Findings

### Vocabulary Extension (NER)
- **Model-dependent effects:** Extension improved RuBERT (+9.4 F1) but degraded mBERT (−6.1 F1)
- **Decision criterion:** Extension helps when base UNK rate >10% (RuBERT: 12.25%), harms when coverage is adequate (mBERT: 3.52%)
- **Tokenization improvement:** mBERT reduced fragmentation from 3.21 to 1.46 pieces/word; RuBERT from 2.37 to 1.39

### NER Performance
- **Best model:** mBERT base (F1=0.535) despite worse tokenization than extended variants
- **Per-class:** PER outperforms (0.590), LOC moderate (0.560), ORG bottleneck (0.423)
- **ORG challenge:** Only 160 training instances + high lexical diversity + ORG↔LOC semantic overlap (18% of disagreements)

### Sentiment Analysis
- **mBERT outperforms RuBERT** despite Russian-Sakha bilingualism (54% vs 49% accuracy)
- **Neutral-boundary detection is the bottleneck:** 89% of annotator disagreements involve neutral classification
- **Severe cross-domain asymmetry:** 21 percentage point gap between transfer directions
- **Domain composition > model architecture:** 21pp transfer gap exceeds 4.5pp model difference

## Annotation Guidelines

### NER
See `sakha-ner/NER_annotation_guidelines.md` for detailed instructions:
- Label definitions (PER, LOC, ORG)
- Morphological suffix handling (suffixes preserved within entity spans)
- Span boundary rules and overlap resolution
- Institutional permanence criterion for ORG entities

### Sentiment Analysis
See `sakha-sa/English_annotation_guidelines.md` for detailed instructions:
- 3-class scheme (positive, negative, neutral)
- Handling code-switching (Sakha-Russian bilingualism)
- Orthographic variation in informal text (62% of forum sentences)
- Cultural semantics and cross-linguistic non-equivalence

## Experimental Design

### NER
- **Data split:** 483 train / 103 dev / 104 test sentences (70/15/15, stratified by entity presence)
- **Training:** 8 epochs, early stopping (patience=3), AdamW optimizer (lr=3e-5)
- **Windowing:** Max length 256 tokens, stride 32 (train), stride 0 (eval)
- **ORG oversampling:** 2× duplication of ORG-bearing windows
- **Evaluation:** Entity-level F1 via seqeval (strict span matching)

### Sentiment Analysis
- **Pooled CV:** 5-fold stratified cross-validation (preserving label + domain proportions)
- **Cross-domain:** Bidirectional transfer (Kyym ↔ Forum YKT)
- **Training:** 5 epochs, early stopping (patience=2), AdamW optimizer (lr=1e-5)
- **Class weighting:** Inverse frequency weighting for 54% neutral dominance
- **Evaluation:** Weighted F1 (primary), macro F1, per-class precision/recall

## Citation

If you use these resources, please cite our LREC 2026 paper:
```bibtex
[Citation will be added after publication]
```

## License

This work is licensed under [CC-BY-NC 4.0](https://creativecommons.org/licenses/by-nc/4.0/).

**Attribution required:** Cite our paper when using these resources.

**Non-commercial use only:** Commercial use requires separate permission.

## Limitations

### Data Scale
- Small corpora: 690 NER sentences, 798 sentiment sentences
- Limited training instances for ORG entities (160 vs 495 LOC, 266 PER)
- Single annotator for primary annotation (targeted IAA on 15-19% of corpus)

### Domain Coverage
- NER: Wikipedia only (encyclopedic register)
- Sentiment: News + forum only (formal + informal, but excludes speech, literature, social media, administrative documents)

### Generalization
- Geographic: Primarily central Yakutsk dialect (northern/rural dialects untested)
- Temporal: 2015-2024 snapshot (language evolution may degrade future performance)
- Typological: Findings most applicable to other agglutinative Turkic languages

## Reproducibility

All experiments use:
- Fixed random seed (42)
- Deterministic algorithms enabled
- Identical hyperparameters across model variants (for fair comparison)
- Sentence-level data splits (SHA1-hashed for reproducibility)

**Hardware:** Google Colab with Tesla T4 GPU (16GB VRAM)

## Contact

This repository contains the exact code and data used for our LREC 2026 submission. For questions or collaborations, please contact us after the review period.

## Acknowledgments

We thank the Sakha-speaking community for their support. All experiments were conducted using Google Colab infrastructure.
