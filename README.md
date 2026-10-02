# Tunisian Language Identification in Arabizi Texts

## Overview

This project investigates **token-level language identification in Tunisian texts written in Arabizi**.

Tunisian Arabizi is a way of writing Tunisian Arabic using Latin characters and numbers. In online communication, Tunisian Arabic is frequently mixed with foreign words, particularly French. This **code-switching** makes automatic language identification challenging, especially because Arabizi spelling is highly variable and informal.

The project uses the **Tunisian Arabish Corpus (TArC)** and compares two sequence-labeling approaches:

* **Conditional Random Fields (CRF)**
* **XLM-RoBERTa**

The task is formulated as a **binary token classification problem**, distinguishing between:

* `arabizi` — Tunisian Arabic written in Arabizi
* `foreign` — foreign-language tokens

---

## Research Question

The main research question is:

> **How can we automatically identify Tunisian Arabizi and foreign-language tokens in Tunisian texts written in Arabizi?**

The project specifically investigates:

* The performance of CRF and XLM-RoBERTa on token-level language identification.
* The types of tokens that are correctly or incorrectly classified.
* The influence of contextual information on language identification.
* The errors caused by lexical ambiguity, informal writing, and code-switching.

---

## Dataset

The project uses the **Tunisian Arabish Corpus (TArC)**, an annotated corpus of Tunisian Arabic written in Latin characters (Arabizi).

The corpus contains texts collected from several sources:

* **Forums**
* **Social media**
* **Blogs**
* **Tunisian rap lyrics**

The project loads and combines the following four corpus files:

```text
forum.tab
social.tab
blog.tab
rap.tab
```

### Dataset attributes

The original corpus contains linguistic and metadata information such as:

| Column        | Description                      |
| ------------- | -------------------------------- |
| `data`        | Document date or identifier      |
| `arabish`     | Original word form               |
| `class`       | Linguistic category              |
| `CODA`        | Normalized Arabic representation |
| `token`       | Token used for analysis          |
| `pos`         | Part-of-speech tag               |
| `lemma`       | Word lemma                       |
| `governorate` | Speaker's governorate            |
| `age`         | Age group                        |
| `gender`      | Gender                           |
| `sentence_id` | Sentence identifier              |
| `source`      | Corpus source                    |

Examples of tokens include Tunisian Arabizi forms such as `7lib` and French words such as `recette` and `farine`.

### Dataset source

The corpus is available from the official TArC repository:

https://github.com/eligugliotta/tarc

The dataset is **not included in this repository**. It must be obtained from the original TArC repository.

---

## Data Preparation

The notebook performs the following preprocessing steps:

1. Clone the TArC repository.
2. Load the four corpus sources.
3. Combine the sources into a single DataFrame.
4. Add a `sentence_id` to reconstruct sentences.
5. Preserve the original source of each observation.
6. Explore the class distribution.
7. Inspect missing values.
8. Detect and remove duplicate rows.
9. Select the columns required for the classification task.
10. Keep only the `arabizi` and `foreign` classes.
11. Convert the labels into numerical representations.
12. Reconstruct token sequences by sentence.

The data is then divided into **training, validation, and test sets** for model development and evaluation.

---

## Models

### 1. Conditional Random Field

The first approach uses a **Conditional Random Field (CRF)** for sequence labeling.

The CRF relies on manually engineered token-level features and contextual information. The notebook uses character-based and morphological features to capture the irregular spelling patterns found in Arabizi.

The approach is particularly suited to sequential data because predictions are made while considering neighboring tokens.

---

### 2. XLM-RoBERTa

The second approach uses **XLM-RoBERTa** for token classification.

XLM-RoBERTa is a multilingual Transformer model pretrained on multilingual text. In this project, it is fine-tuned for the binary classification of tokens into:

```text
Tunisian
Foreign
```

The notebook also handles the alignment between the original words and the subword tokens produced by the Transformer tokenizer.

---

## Evaluation

The models are evaluated on the test set using token-level metrics:

* Accuracy
* Precision
* Recall
* F1-score
* Macro F1-score
* Confusion matrix

The notebook also provides a direct comparison between the two approaches.

---

## Results

The experiments reported in the notebook obtained the following Macro F1 scores:

| Model       |   Macro F1 |
| ----------- | ---------: |
| CRF         | **0.9624** |
| XLM-RoBERTa | **0.9537** |

The notebook contains the complete evaluation results and detailed error analysis.

### CRF

The CRF achieves a Macro F1 of **0.9624**.

The analysis in the notebook shows that character-based features are useful for handling the spelling variability of Tunisian Arabizi.

### XLM-RoBERTa

XLM-RoBERTa achieves a Macro F1 of **0.9537**.

Its contextual multilingual representations allow it to capture information from the surrounding context, while its subword tokenization can create difficulties for highly irregular Arabizi forms.

---

## Error Analysis

The notebook includes a detailed analysis of classification errors for both models.

### Lexical ambiguity

Short words such as:

```text
ou
la
et
un
```

can be difficult to classify because identical forms may occur in French and in Tunisian transliteration.

### Code-switching

Both models can encounter difficulties when the language changes abruptly within a sentence.

### Informal and noisy data

The corpus contains informal elements such as:

* URLs
* Usernames
* Numbers
* Unusual spellings

Examples include forms such as:

```text
https
facebook
m5abbi1226
12
15j
```

The notebook shows that these elements can introduce classification errors because of inconsistent annotation or unusual linguistic patterns.

### Model-specific observations

The notebook identifies different behaviors between the two approaches:

* **CRF:** benefits from character n-gram and local morphological features, which are useful for irregular Arabizi spelling.
* **XLM-RoBERTa:** benefits from contextual multilingual representations but can be affected by subword tokenization of unusual Arabizi forms.

---

## Project Workflow

The complete workflow implemented in the notebook is:

```text
TArC Corpus
     │
     ▼
Data Acquisition
     │
     ▼
Data Loading & Exploration
     │
     ▼
Cleaning & Preprocessing
     │
     ▼
Sentence Reconstruction
     │
     ▼
Train / Validation / Test Split
     │
     ├───────────────┐
     ▼               ▼
    CRF          XLM-RoBERTa
     │               │
     └───────┬───────┘
             ▼
       Model Evaluation
             │
             ▼
       Error Analysis
             │
             ▼
        Model Comparison
```

---

## Repository Structure

```text
tunisian-language-identification/
├── README.md
├── requirements.txt
├── .gitignore
├── data/
│   └── README.md
├── notebooks/
│   └── tunisian-language-identification.ipynb
└── results/
    └── figures/
```
The main component of the repository is the Jupyter Notebook, which contains the complete experiment from data preparation to model evaluation and error analysis.

---

## Requirements

The notebook uses Python and the following main libraries:

```text
numpy
pandas
matplotlib
seaborn
scikit-learn
sklearn-crfsuite
datasets
transformers
evaluate
seqeval
torch
accelerate
```

The required dependencies can be installed with:

```bash
pip install -r requirements.txt
```

---

## Running the Project

Clone the repository:

```bash
git clone https://github.com/<YOUR_GITHUB_USERNAME>/tunisian-language-identification.git
cd tunisian-language-identification
```

Install the dependencies:

```bash
pip install -r requirements.txt
```

Launch Jupyter:

```bash
jupyter notebook
```

Open:

```text
notebooks/tunisian_language_identification.ipynb
```

The notebook downloads the TArC corpus directly from its GitHub repository before processing the data.

---

## Limitations and Future Work

The notebook identifies several possible directions for improving the system:

* Combining character-level and contextual word representations.
* Using language-specific lexicons to resolve ambiguous short tokens.
* Applying deterministic linguistic rules as a post-processing step.
* Cleaning noisy annotations before model training.
* Improving the handling of URLs, usernames, numbers, and other non-standard tokens.

---

## References

* Conneau, A. et al. (2020). *Unsupervised Cross-lingual Representation Learning at Scale*. Proceedings of ACL 2020.
  https://aclanthology.org/2020.acl-main.747/

* Gugliotta, E. & Dinarelli, M. (2020). *TArC: Incrementally and Semi-Automatically Collecting a Tunisian Arabish Corpus*. Proceedings of LREC 2020, pp. 6279–6286.
  https://aclanthology.org/2020.lrec-1.770/

* Gugliotta, E. & Dinarelli, M. (2022). *TArC: Tunisian Arabish Corpus, First Complete Release*. Proceedings of LREC 2022.
  https://aclanthology.org/2022.lrec-1.121/

* Lafferty, J., McCallum, A. & Pereira, F. (2001). *Conditional Random Fields: Probabilistic Models for Segmenting and Labeling Sequence Data*. Proceedings of ICML 2001, pp. 282–289.
  https://www.cs.columbia.edu/~jebara/6772/papers/crf.pdf

---

## Author

**Siwar Ben Younes**
École Supérieure de la Statistique et de l’Analyse de l’Information (ESSAI)
Academic Year: **2026–2027**
