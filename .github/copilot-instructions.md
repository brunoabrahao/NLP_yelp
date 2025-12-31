<!-- .github/copilot-instructions.md - guidance for AI coding agents -->
# Repo-specific Copilot Instructions

This file gives concise, actionable guidance for AI coding agents working in this repository.

- **Big picture:** This is a teaching repo for an NLP lecture. The primary artifact is the Jupyter notebook `lecture9_word_algebra_sp25.ipynb` which implements a multi-stage NLP pipeline (data filtering, spaCy preprocessing, phrase modeling with `gensim.Phrases`, LDA topic modeling, and Word2Vec embeddings) using the Yelp dataset in `yelp_dataset/`.

- **Primary files/dirs:**
  - `lecture9_word_algebra_sp25.ipynb` — main notebook; most development happens here.
  - `yelp_dataset/` — raw newline-delimited JSON files (`review.json`, `business.json`, etc.).
  - `yelp_dataset/intermediate/` — processed artifacts and saved models (bigram/trigram models, LDA, Word2Vec files).
  - `CLAUDE.md` — contains longer guidance and useful context; use it as a reference.

- **How to run / quick commands:**
  - Activate the Python venv (if present): `source .venv/bin/activate`.
  - Launch the notebook: `jupyter notebook lecture9_word_algebra_sp25.ipynb` or `jupyter lab lecture9_word_algebra_sp25.ipynb`.

- **Key patterns to follow (discoverable in the notebook):**
  - Large/expensive ops are guarded by `if False:` blocks. If you want to re-run preprocessing or model training, toggle those guards to `True` — otherwise prefer loading precomputed artifacts from `yelp_dataset/intermediate/`.
  - Data is newline-delimited JSON: process with streaming iterators (e.g., `LineSentence`, or reading line-by-line) to avoid loading the full 5GB `review.json` into memory.
  - Phrase modeling is built incrementally (bigrams → trigrams) via `gensim.Phrases`; follow the notebook's sequence and naming conventions (e.g., saved bigram models and `_sentences_.txt` files in `intermediate/`).

- **Libraries and idioms:**
  - `spaCy` for tokenization/lemmatization and stopword removal.
  - `gensim` for `Phrases`, `Dictionary`, `LdaMulticore`, and `Word2Vec`. Look for `LineSentence` streaming usage.
  - `pyLDAvis` is used for interactive topic inspection.

- **Data safety & editing rules:**
  - Do not modify the raw files in `yelp_dataset/` (they are newline-delimited JSON). Put generated artifacts in `yelp_dataset/intermediate/` or the notebook's outputs.
  - When adding code to the repo, prefer small helper scripts or separate modules (not large, monolithic notebook edits) so others can reuse processing stages.

- **What agents should do first:**
  1. Open `CLAUDE.md` and `lecture9_word_algebra_sp25.ipynb` to understand the pipeline and existing guards for long ops.
  2. If making changes that require heavy computation, prefer re-using files in `yelp_dataset/intermediate/` and describe required recomputation steps in PR text.
  3. When adding code, include runnable instructions (commands) and mention whether precomputed artifacts are required.

- **Examples (copyable snippets observed in the notebook):**
  - Guard pattern for expensive steps:
    ```python
    # expensive preprocessing step — set to True to run
    if False:
        # compute and save processed files
    else:
        # load precomputed files from disk
    ```
  - Activate venv:
    ```bash
    source .venv/bin/activate
    jupyter lab lecture9_word_algebra_sp25.ipynb
    ```

If anything here is unclear or you'd like more detail about a particular component (LDA training, phrase modeling, or embedding usage), tell me which area and I'll expand this file with examples and file references.
