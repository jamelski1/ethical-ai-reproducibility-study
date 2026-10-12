# Gender Bias in Word Embeddings: A Reproduction Study

A step-by-step Jupyter notebook that reproduces key results from two papers on gender bias in word embeddings:

1. Bolukbasi, Chang, Zou, Saligrama, and Kalai (2016). *Man is to Computer Programmer as Woman is to Homemaker? Debiasing Word Embeddings.* NeurIPS. [arXiv:1607.06520](https://arxiv.org/abs/1607.06520)
2. Nissim, van Noord, and van der Goot (2020). *Fair Is Better than Sensational: Man Is to Doctor as Woman Is to Doctor.* Computational Linguistics 46(2). [doi:10.1162/coli_a_00379](https://doi.org/10.1162/coli_a_00379)

The notebook is written for learners. Each code cell has a markdown cell before it that explains what the cell does, the math in LaTeX, and an everyday analogy. Each result is followed by an interpretation, and every reproduced number gets an "Expected (paper) vs. Observed" table.

## What is inside

| File | Purpose |
|---|---|
| `embedding_bias_reproduction.ipynb` | The notebook (Parts A, B, C), committed with its outputs |
| `requirements.txt` | Pinned Python dependencies (Python 3.11) |
| `data/` | Downloaded inputs (created on first run, git-ignored) |
| `cache/` | Cached embedding and expensive results (created on first run, git-ignored) |

**Part A** reproduces Bolukbasi et al.: the 26,377-word vocabulary, classic analogies, the gender direction (Figure 6), extreme occupations (Figure 1), DirectBias, indirect bias (Figure 3), Equation 1 analogy generation, hard debiasing with a check of Observation 1, and the optional soft-debiasing SDP.

**Part B** reproduces Nissim et al.: 3CosAdd, 3CosMul, and the Bolukbasi formula, each with and without the input-exclusion constraint (Table 1), Mikolov benchmark macro-accuracy, the vocabulary-size and threshold grid (Table 3), and query direction.

**Part C** puts the two papers side by side and adds the Gonen and Goldberg (2019) clustering test.

## Resource requirements

* **Download:** about **1.6 GB** (the GoogleNews word2vec vectors, fetched once by `gensim.downloader`).
* **Disk:** about 6 GB in total (the compressed download plus a 3.6 GB fast-loading cache).
* **RAM:** the vectors alone need about **4 GB**. The notebook peaks at about 6 to 7 GB, so use a machine with **at least 8 GB**.
* **Time:** about 30 to 40 minutes for the first full run on a 4-core machine (most of it is the Mikolov benchmark and the SDP). Later runs reuse `cache/` and take a few minutes.

## Setup

```bash
# 1. Create a Python 3.11 virtual environment
python3.11 -m venv .venv
source .venv/bin/activate            # Windows: .venv\Scripts\activate

# 2. Install pinned dependencies
pip install --upgrade pip
pip install -r requirements.txt

# 3. Register the environment as a Jupyter kernel
python -m ipykernel install --user --name python3 --display-name "Python 3 (embedding-bias)"
```

The notebook fetches its own data on first run, so you do not need to download anything by hand:

* **Embedding:** `word2vec-google-news-300` via `gensim.downloader`, stored in `data/gensim-data/`.
* **Word lists:** cloned from the authors' repository, [tolga-b/debiaswe](https://github.com/tolga-b/debiaswe), pinned to commit `6bb03ee2`, into `data/debiaswe/` (requires `git`).
* **Analogy test set:** Mikolov's `questions-words.txt`, which ships with gensim's test data.

## Run

Interactively:

```bash
jupyter notebook embedding_bias_reproduction.ipynb
```

Then choose **Kernel > Restart & Run All**.

Headless, top to bottom in a fresh kernel (this is how the committed outputs were produced):

```bash
jupyter nbconvert --to notebook --execute --inplace \
  --ExecutePreprocessor.timeout=-1 embedding_bias_reproduction.ipynb
```

To recompute everything from scratch, delete `cache/` (and `data/` to re-download).

## Reproducibility notes

* Random seeds are fixed (`SEED = 2016`) for Python, NumPy, and scikit-learn.
* When a result does not match the paper, the notebook reports the gap and lists plausible causes. No parameters were tuned to force a match.
* The soft-debiasing SDP uses the open-source SCS solver through `cvxpy`, so no commercial solver is needed.
* The Manzini et al. (2019) race and religion analogies are intentionally **not** reproduced. The notebook summarizes Nissim et al.'s critique of them in prose only.
