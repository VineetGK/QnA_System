# QnA_System
# Q\&A System

## Overview

This project builds a biomedical question-answering (QA) pipeline using retrieval-augmented generation (RAG). It has two steps:

1. **Retriever**: Imagine you have a huge library of books and you need to find the right pages with the answers. The retriever searches through about 40,000 biomedical passages and picks out the most relevant ones. It is `sentence-transformers/all-mpnet-base-v2`, fine-tuned on question/passage pairs.
2. **Generator**: Once you have the right passages, the generator reads them and writes an answer. It is GPT-2 (124M), fine-tuned on `Question / Context / Answer` prompts.

Splitting the problem this way means each part can be trained and improved on its own.

---

## How It Works (for a 12‑year‑old)

1. **Library Helper (Retriever)**: Think of your computer as a helper that quickly flips through pages of many books and collects only the pages that might have your answer.
2. **Storyteller (Generator)**: Your computer then reads those pages and tells you the answer in a way that’s easy to understand—like turning facts into a short, clear story.
3. **Why Two Parts?**

   * The helper avoids showing you hundreds of pages you don’t need.
   * The storyteller makes sure you get a neat, friendly answer instead of a big jumble of text.

---

## Files in This Repo

* `QnA_System.ipynb`: The main Jupyter Notebook (written for Google Colab with a GPU). It covers dataset download, retriever training and evaluation, generator training and evaluation, and writing the prediction CSVs.
* `requirements.txt`: The Python libraries the notebook imports (`sentence-transformers`, `transformers`, `datasets`, `torch`, `nltk`, and others).
* `download (3).png`: A diagram of the RAG pipeline.
* `download (2).png`: A slide explaining the contrastive (InfoNCE) loss, which is the idea behind the retriever's loss. The slide is from A. Maier et al., FAU Erlangen-Nürnberg.

The dataset (`kaggle_3_documents.csv`, `kaggle_3_train.csv`, `kaggle_3_test.csv`) is not in the repo. The first cells of the notebook download it.

---

## Installation

1. **Clone the repo**

   ```bash
   git clone https://github.com/VineetGK/QnA_System.git
   cd QnA_System
   ```
2. **Set up a virtual environment** (optional but recommended)

   ```bash
   python -m venv venv
   source venv/bin/activate  # macOS/Linux
   venv\Scripts\activate  # Windows
   ```
3. **Install dependencies**

   ```bash
   pip install -r requirements.txt
   ```

---

## Usage

1. **Open the notebook**

   ```bash
   jupyter notebook QnA_System.ipynb
   ```

2. **Run each cell** in order. A GPU is strongly recommended. The notebook:

   * Downloads and loads the documents and the train/test questions.
   * Splits the training questions into a training split and a held-out validation split (90/10).
   * Fine-tunes the retriever on the training split and reports recall@10, precision@10 and MRR on the validation split.
   * Fine-tunes the GPT-2 generator on the training split. Long contexts are truncated so the answer always fits in 1024 tokens. It reports word-level BLEU on the validation split.
   * Retrieves passages for the test questions, generates answers, and writes `stage_1_prediction.csv` and `simple_baseline1.csv`.

3. **Ask your own questions** by modifying the notebook’s test section.

---

## Evaluation Metrics

* **Precision (Retriever)**: Out of all the pages the system found, how many actually had the right answer?
  *Formula: TP / (TP + FP)*

* **Recall (Retriever)**: Out of all the pages that should have been found, how many did the system actually find?
  *Formula: TP / (TP + FN)*

* **BLEU (Generator)**: Word-level corpus BLEU between generated and reference answers, from 0 to 1.

* **Overall Score**: The average of Precision, Recall, and BLEU. This gives one number to compare different versions.

---

## Results

> **Note:** The outputs saved in the notebook predate several bug fixes, so its numbers should not be quoted. Before the fixes:
> * retriever metrics were computed on rows it had been trained on;
> * BLEU was character-level and computed on 5 training rows;
> * training answers were cut off at 1024 tokens;
> * test-time prediction received an empty context.
>
> Re-run the notebook to get valid held-out metrics.

---

## Next Steps

* Try different retriever models (e.g., dense vs. sparse embeddings).
* Use an instruction-tuned or larger generator (with LoRA/QLoRA fine-tuning).
* Batch generation on the GPU to speed up predictions.

---

## Contributing

Contributions are welcome! Feel free to open an issue or submit a pull request with improvements.

---
