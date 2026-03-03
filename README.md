# Minor Safety Content Detection

### LLM-Augmented Harmful Content Classification for Online Minor Protection

---

## Why This Project Exists

Online platforms serve hundreds of millions of users — including teenagers and children — yet detecting harmful content at scale remains a hard problem. Rule-based systems fail to generalize; black-box classifiers lack explainability. This open source project explores how **fine-tuned transformers + LLM reasoning** can improve both detection accuracy and interpretability for minor safety use cases.

**Our goal:** Provide a reusable, well-documented pipeline that others can adapt, extend, or learn from — whether for research, platform moderation, or Trust & Safety tooling.

---

## Who This Helps

| Audience | How It Helps |
|----------|--------------|
| **Platform builders** | Reference implementation for content moderation pipelines; error analysis patterns to audit your own models |
| **Trust & Safety teams** | Systematic vulnerability analysis methodology; LLM-augmented review workflow design |
| **Researchers** | Reproducible baseline for minor safety classification; grooming/implicit-harm detection gaps to study |
| **Students & practitioners** | End-to-end example from data prep → fine-tuning → evaluation → LLM integration; clear documentation of design choices |

---

## Overview

This project builds an end-to-end pipeline for harmful content detection targeting minors:

- **Data:** Jigsaw Toxic Comment dataset, binary labels, balanced sampling
- **Model:** Fine-tuned DistilBERT for sequence classification
- **Evaluation:** F1 / precision / recall + systematic error analysis
- **LLM layer:** Claude API as secondary reviewer on uncertain/high-risk cases — mirroring human-in-the-loop moderation

---

## Pipeline

```
Raw Data (Jigsaw 150K+)
       ↓
Dataset Standards & Balanced Sampling
       ↓
DistilBERT Fine-Tuning
       ↓
Model Evaluation (F1 / Precision / Recall)
       ↓
Systematic Error Analysis
       ↓
LLM Secondary Review Layer (Claude API)
       ↓
Vulnerability Report & Improvement Proposals
```

---

## Key Results

| Metric | Score |
|--------|-------|
| Accuracy | 93% |
| F1 Score | 0.932 |
| Precision | 0.925 |
| Recall | 0.939 |

---

## Error Analysis & Vulnerability Findings

A core focus is **systematic model vulnerability analysis** — identifying not just overall accuracy, but *where and why* the model fails.

### False Positives (148 cases — clean content flagged as harmful)
- Model over-triggers on **informal or aggressive-sounding language** that is not actually harmful
- Example: *"Ya dude thats not cool"* → incorrectly flagged as toxic
- **Root cause:** Training data conflates assertive tone with toxicity

### False Negatives (120 cases — harmful content missed)
- Model struggles with **implicit and indirect harm**, including:
  - Subtle racial bias embedded in factual-sounding statements
  - Veiled threats framed as warnings
  - **Grooming-style language** — e.g., *"Let's meet up after school, don't tell your parents"* scored only 0.7% harmful
- **Root cause:** Model lacks exposure to context-dependent minor safety patterns

### Vulnerability Pattern: Text Length vs Error Rate

| Text Length | Error Rate |
|-------------|------------|
| < 50 chars  | 6.3% |
| 50–150 chars | 5.3% |
| 150–300 chars | 7.1% |
| 300–500 chars | 7.1% |
| 500+ chars  | 8.9% |

Longer texts show higher error rates, suggesting the model loses contextual focus over longer sequences.

---

## Proposed Improvements

Based on error analysis:

1. **Expand training data** for grooming, implicit threats, and context-dependent minor safety patterns
2. **Data augmentation** for underrepresented harmful categories (subtle bias, indirect threats)
3. **Refine labeling standards** to distinguish aggressive tone from genuine harm
4. **LLM-assisted re-labeling** of ambiguous training samples to reduce annotation inconsistency

---

## LLM Secondary Evaluation Layer

Claude (Anthropic) is used as a secondary reviewer on high-risk and false-negative cases:

- **Input:** Text flagged as uncertain or missed by the classifier
- **Output:** Structured JSON with `is_harmful`, `severity`, `category`, and `reason`
- **Categories:** `hate_speech` | `harassment` | `explicit` | `grooming` | `safe`

This mirrors production Trust & Safety pipelines where LLMs augment and explain automated decisions.

---

## Tech Stack

- **Model:** DistilBERT (fine-tuned for sequence classification)
- **Framework:** HuggingFace Transformers, PyTorch
- **Dataset:** [Jigsaw Toxic Comment Classification Challenge](https://www.kaggle.com/c/jigsaw-toxic-comment-classification-challenge) (150K+ samples)
- **LLM Layer:** Claude API (Anthropic)
- **Environment:** Google Colab (GPU)

---

## Quick Start

1. Install dependencies: `pip install -r requirements.txt`
2. Download Jigsaw dataset from [Kaggle](https://www.kaggle.com/c/jigsaw-toxic-comment-classification-challenge) and place `train.csv` in the project root
3. Open `minor_safety_content_detection.ipynb` and run all cells (Colab or local with GPU)
4. Optional: Set `ANTHROPIC_API_KEY` to enable LLM secondary review

---

## Relevance to Trust & Safety

- **Proactive detection** of harmful content at scale
- **Vulnerability identification** across content categories
- **Dataset quality improvement** through systematic error analysis
- **LLM integration** into content moderation workflows
- **Minor-specific safety patterns** including grooming detection gaps

---

## Contributing

Contributions are welcome — whether improving the model, expanding the dataset, or refining the error analysis methodology. Open an issue or PR to discuss.
