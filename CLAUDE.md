# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Repository Overview

This is a teaching repository for a Spring 2025 NLP course, featuring an end-to-end tutorial on Word Algebra using Word2Vec. The main content is a Jupyter notebook (`lecture9_word_algebra_sp25.ipynb`) that demonstrates natural language processing techniques on the Yelp dataset.

## Notebook Structure

The main notebook (`lecture9_word_algebra_sp25.ipynb`) contains:
- **176 total cells** (88 code cells + 88 markdown cells)
- **Execution status**: All cells have been successfully executed (execution count up to 100)
- **Completeness**: No incomplete sections, TODOs, or errors
- **Status**: All deprecation warnings have been fixed

## Environment Setup

The repository uses a Python virtual environment located at `.venv/`:

```bash
source .venv/bin/activate
```

## Running the Notebook

Launch Jupyter to work with the main tutorial:

```bash
jupyter notebook lecture9_word_algebra_sp25.ipynb
```

Or use JupyterLab:

```bash
jupyter lab lecture9_word_algebra_sp25.ipynb
```

## Notebook Sections

The notebook is organized into the following major sections:

- **Cells 2-14**: The Yelp Dataset - Data loading and exploration
- **Cells 15-37**: spaCy - Industrial-Strength NLP - Text processing fundamentals
- **Cells 38-68**: Phrase Modeling - Bigram and trigram detection
- **Cells 74-108**: Topic Modeling with LDA - Latent Dirichlet Allocation
- **Cells 109-158**: Word Vector Embedding with Word2Vec - Semantic embeddings and word algebra
- **Cells 159-174**: Word Vector Visualization with t-SNE - Dimensionality reduction and interactive plots
- **Cells 174-175**: Conclusion - Summary and wrap-up

## Yelp Dataset Structure

The `yelp_dataset/` directory contains the raw Yelp Dataset Challenge data in JSON format:

- `business.json` - Business records with categories, location, and metadata
- `review.json` - User reviews with text, ratings, and business_id references (5.3GB)
- `checkin.json` - Check-in data
- `tip.json` - User tips
- `photo.json` - Photo metadata
- `intermediate/` - Directory for processed intermediate files (see below)

The dataset uses newline-delimited JSON format (one JSON object per line). The notebook filters for restaurant reviews by matching businesses with "Restaurants" in their categories field, resulting in ~4.2 million restaurant reviews from ~60,000 restaurants.

### Intermediate Files Generated

The notebook creates 12 intermediate files in `yelp_dataset/intermediate/`:

1. `review_text_all.txt` - Extracted restaurant reviews (4.2M reviews, one per line)
2. `unigram_sentences_all.txt` - Tokenized sentences
3. `bigram_sentences_all.txt` - With detected bigrams
4. `trigram_sentences_all.txt` - With detected trigrams
5. `trigram_transformed_reviews_all.txt` - Lemmatized + phrases + stopword-removed
6. `trigram_dictionary.dictionary` - Gensim vocabulary dictionary (91,875 terms)
7. `trigram_bow.mm` - Bag-of-words corpus (Matrix Market format)
8. `lda_model_all.model` - Trained LDA model (50 topics)
9. `ldavis_prepared.pkl` - pyLDAvis visualization data
10. `food2vec_model.model` - Trained Word2Vec model
11. `topic_names.pkl` - Manual topic labels (50 topics)
12. `tsne_vectors.pkl` - 2D t-SNE projection for visualization

## NLP Pipeline Architecture

The notebook implements a multi-stage pipeline:

1. **Data Filtering & Extraction**
   - Filters businesses by "Restaurants" category
   - Extracts review text for restaurant businesses only
   - Creates intermediate text files with one review per line

2. **Text Preprocessing** (using spaCy)
   - Tokenization and normalization
   - Lemmatization
   - Removing stopwords, punctuation, and non-alphabetic tokens
   - Creates processed text files for downstream modeling

3. **Phrase Modeling** (using gensim.Phrases)
   - Detects and joins multi-word expressions (e.g., "happy_hour", "fish_tacos")
   - Builds bigram and trigram models progressively
   - Uses statistical association measures to identify phrases

4. **Topic Modeling** (using LDA via gensim)
   - Creates Dictionary from 91,875-term vocabulary
   - Builds bag-of-words corpus representation in Matrix Market format
   - Trains LdaMulticore model with 50 topics
   - Visualizes topics interactively with pyLDAvis
   - Models discover patterns like food-focused vs. experience-focused reviews
   - Assigns manual topic labels for interpretability

5. **Word Embeddings** (using Word2Vec via gensim)
   - Trains word vector models on preprocessed text (91,875 terms)
   - Enables semantic similarity queries and word algebra operations
   - Demonstrates 7 word algebra examples:
     - `breakfast + lunch` → brunch
     - `taco + chinese - mexican` → dumpling
     - `filet_mignon + seafood - beef` → lobster_tail
     - `coffee + snack - drink` → pastry
     - And 3 more examples
   - Visualizes embeddings using t-SNE dimensionality reduction (2D projection)
   - Creates interactive Bokeh plots with hover functionality

## Helper Functions

The notebook defines 9 helper functions for analysis and exploration:

- `word_algebra(add=[], subtract=[], topn=2)` - Performs vector arithmetic on word embeddings
- `get_related_terms(token, topn=10)` - Finds semantically similar words using Word2Vec
- `get_sample_review(review_number)` - Extracts specific reviews from the dataset
- `lda_description(review_text, min_topic_freq=0.05)` - Assigns LDA topics to text
- `explore_topic(topic_number, topn=25)` - Shows top terms for a given topic
- `punct_space(token)` - Text filtering predicate for cleaning
- `lemmatized_sentence_corpus()` - Generator for text transformation pipeline
- `trigram_bow_generator(filepath)` - Generator for bag-of-words representation
- `line_review()` - Utility for processing reviews line-by-line

These functions enable interactive exploration of the trained models and facilitate understanding of the NLP concepts.

## Key Dependencies

- **spaCy** - Industrial-strength NLP library for text processing (tokenization, lemmatization, NER)
- **gensim** - Topic modeling and word embedding library
  - Components used: `Phrases`, `LdaMulticore`, `Word2Vec`, `Dictionary`, `LineSentence`
- **pyLDAvis** - Interactive topic model visualization (50-topic LDA exploration)
- **Bokeh** - Interactive visualization library for t-SNE plots
- **pandas** - Data manipulation and analysis
- **pickle** - Model and object serialization (for saving trained models)

## Data Processing Pattern

The notebook follows a pattern of conditional execution for time-intensive operations:

```python
# this is a bit time consuming - make the if statement True
# if you want to execute data prep yourself.
if False:
    # expensive preprocessing code
else:
    # load pre-computed results from disk
```

This allows students to either run the full pipeline or skip to pre-computed results. When modifying the notebook, be aware of these conditional blocks and the intermediate files they produce/consume.

## Memory and Performance Considerations

- The review.json file is 5.3GB and contains millions of records
- The notebook uses gensim's streaming iterators (e.g., LineSentence) to process data without loading entire datasets into RAM
- Model training (LDA, Word2Vec) can be time-consuming; the notebook saves trained models to disk for reuse
- Intermediate text files are created to avoid re-parsing JSON repeatedly
