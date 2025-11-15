---
title: "Multimedia Information Processing – Assignment #1"
author: "Fiaz Zafar (SAP-ID 70189246)"
date: "15 November 2025"
---

# Cover Page

**Department of Computer Science**  \
**Subject:** Multimedia Information Processing  \
**Assignment # 1 – Cosine Similarity using Category Discriminating Terms**  \
**Due Date:** 15/11/2025 &nbsp;&nbsp;&nbsp;&nbsp; **Total Marks:** 15

**Submitted By:**  \
Name: **Fiaz Zafar**  \
SAP-ID / Roll No: **70189246**  \
Program: **MS Computer Science**  \
Submitted To: **Dr. Mehtab Afzal**  \
Institution: **The University of Lahore**  \
Submission Date: **15 November 2025**

> **Design Note:** Insert the University of Lahore crest centered near the top of the cover page and place a low-opacity Minar-e-Pakistan watermark in the lower-right corner. Keep the color palette green (#0C5C2C) and gray, mirroring your friend’s layout while remaining original.

# Table of Contents

1. Introduction  
2. Dataset Overview  
3. Experimental Objectives  
4. Methodology and Parameterization  
5. Detailed Implementation (Python Workflow)  
6. Category Discriminating Terms (CDTs)  
7. Cosine Similarity Computation  
8. Evaluation Metrics and Results  
9. Visualizations  
10. Observations and Discussion  
11. Conclusion  
12. References  
Appendix A – PDF Conversion Checklist  
Appendix B – Suggested Attachments

# 1. Introduction

The Multimedia Information Processing (MIP) Assignment #1 builds a content-based retrieval pipeline for classifying YouTube-style videos into predefined categories. Each video description bundles the original title and user-assigned tags. The training corpus (4,610 videos) is used exclusively to learn Category Discriminating Terms (CDTs), while the testing corpus (4,657 videos) is evaluated by computing cosine similarity between each test vector and category profile vectors. Performance is reported via Mean Average Precision (MAP) in line with standard information-retrieval practice.

# 2. Dataset Overview

- **Training File:** `Training data (Core) 4610.xlsx`
- **Testing File:** `Testing data (Core) 4657.xlsx`
- **Fields Consumed:** `core-video-video-title`, `core-video-video-tag`, and `category` (train only)
- **Auxiliary Resource:** `english.stop` (custom stop-word list)

Both datasets contain multilingual, noisy text, demanding uniform preprocessing to ensure consistent token statistics.

# 3. Experimental Objectives

1. Preprocess textual attributes (title + tags) for both training and testing sets.
2. Generate Category Discriminating Terms with a documented frequency threshold of **60**.
3. Build category profile vectors restricted to the learned CDT vocabulary.
4. Compute cosine similarity between each test vector and every category profile.
5. Rank categories per test sample, compute Average Precision (AP), and report MAP.
6. Visualize AP distribution and CDT coverage to highlight strengths and weaknesses.

# 4. Methodology and Parameterization

| Parameter | Chosen Value | Rationale |
| --- | --- | --- |
| Text Combination | `title + tags` | Captures descriptive + contextual information |
| Normalization Steps | Lowercasing, punctuation stripping | Minimizes token sparsity |
| Stop-word List | `english.stop` (custom) | Aligns with provided resources |
| Minimum Token Length | 3 characters | Filters noise tokens |
| CDT Source | Training data only | Prevents data leakage |
| CDT Frequency Threshold | **60** | Balances discriminativeness and coverage |
| Vectorizer | `CountVectorizer` (raw TF) | Offers interpretability |
| Similarity Metric | Cosine similarity (L2-normalized vectors) | Standard for sparse text |
| Evaluation Metric | Mean Average Precision (MAP) | Required by assignment |

# 5. Detailed Implementation (Python Workflow)

1. **Library Imports** – `pandas`, `numpy`, `re`, `CountVectorizer`, `cosine_similarity`, `normalize`, `matplotlib.pyplot`.
2. **Data Loading** – Read both Excel files with `pd.read_excel`.
3. **Stop-word Acquisition** – Load `english.stop` into a Python `set` for O(1) lookup.
4. **Preprocessing Routine** – Lowercase, strip punctuation via regex, split tokens, remove stop-words and tokens shorter than three characters, then rejoin.
5. **CDT Extraction per Category** – Fit a `CountVectorizer` on each category subset, sum column counts, and keep terms with frequency ≥ 60.
6. **Vocabulary Consolidation** – Merge all per-category CDT lists into a unique, sorted list (20 terms total).
7. **Category Profile Construction** – Reuse a shared vectorizer constrained to the CDT vocabulary, transform each category subset, sum TF rows, and L2-normalize the resulting profile vectors.
8. **Test Vector Encoding** – Apply the same vectorizer to cleaned test text and normalize row-wise.
9. **Cosine Similarity & Ranking** – Use `cosine_similarity(test_matrix, profile_matrix)` to score each video against all categories, then rank scores descending.
10. **Average Precision & MAP** – For every category, compute Average Precision across its relevant test items. MAP is the macro-average of these AP scores.
11. **Artifact Export** – Persist CDT list (`Category_Discriminating_Terms.txt`), similarity matrix (`Cosine_Similarity_Matrix.csv`), AP table (`Average_Precision_Scores.csv`), and visualization files (`AP_Bar_Chart.png`).

# 6. Category Discriminating Terms (CDTs)

- **Threshold:** 60 occurrences within a category.
- **Total CDT Count:** 20 unique terms.

| Category | CDT Count | Sample Terms |
| --- | --- | --- |
| Pets & Animals | 6 | dog, puppy, cat, pet, animal, rescue |
| Gaming | 5 | game, gameplay, tutorial, mission, level |
| Music | 3 | music, song, official |
| Comedy | 2 | comedy, funny |
| How-to & Style | 2 | makeup, tutorial |
| Autos & Vehicles | 2 | car, auto |
| News & Politics | 2 | news, political |
| Film & Animation | 1 | animation |
| Remaining Categories | 0 | — |

> **Visualization Tip:** Insert a horizontal bar chart titled “Number of CDTs per Category (Threshold = 60)” showing Pets & Animals as the tallest bar.

# 7. Cosine Similarity Computation

Cosine similarity between two L2-normalized TF vectors \(A\) and \(B\) is:

$$\text{cosine\_similarity}(A, B) = \frac{A \cdot B}{\lVert A \rVert \times \lVert B \rVert}$$

Because both vectors are already normalized, the denominator is 1, leaving the dot product as the similarity score. This metric is scale-invariant and well-suited for sparse, high-dimensional term frequencies.

# 8. Evaluation Metrics and Results

Average Precision (AP) per category (sorted):

| Category | AP Score |
| --- | --- |
| How-to & Style | 0.5624 |
| Comedy | 0.4319 |
| Gaming | 0.4095 |
| Pets & Animals | 0.3829 |
| Autos & Vehicles | 0.3212 |
| Music | 0.2925 |
| Film & Animation | 0.2186 |
| News & Politics | 0.1626 |
| Nonprofits & Activism | 0.0857 |
| Science & Technology | 0.0744 |
| Entertainment | 0.0739 |
| Education | 0.0678 |
| Sports | 0.0652 |
| People & Blogs | 0.0650 |
| Travel & Events | 0.0548 |

**Mean Average Precision (MAP): 0.2179**

# 9. Visualizations

1. **Average Precision Bar Chart** – Horizontal layout, categories sorted ascending, color gradient from #A7E4C2 to #0C5C2C, value labels at bar ends.
2. **Pipeline Diagram** – Training Data → Preprocessing → CDT Extraction → Category Profiles → Cosine Similarity → Ranked Output → MAP Evaluation.
3. **Code Screenshots** – Include 5–6 crisp snippets covering preprocessing, CDT selection, similarity calculations, and plotting. Place captions beneath each image.

# 10. Observations and Discussion

- Categories with highly specific vocabulary (How-to & Style, Gaming, Pets & Animals) dominate AP because their CDTs recur frequently in both training and testing corpora.
- Generic buckets (Travel & Events, People & Blogs, Education) gain few or zero CDTs at threshold 60, producing weak similarity signals.
- Only 20 total terms passed the threshold, highlighting a deliberately selective feature space; loosening the threshold or switching to TF-IDF could enhance coverage but was avoided to match assignment rules.
- Cosine similarity with normalized vectors keeps comparisons fair even when test clips reference fewer CDT tokens.

# 11. Conclusion

An end-to-end multimedia retrieval pipeline was successfully executed for MIP Assignment #1. Using the documented CDT threshold of 60, the system produced 20 discriminating terms and achieved a MAP of 0.2179 on the testing corpus. All intermediate artifacts (CDT listings, similarity matrix, AP tables, visualizations) are prepared for submission, ensuring transparency and reproducibility while remaining original relative to peer submissions.

# 12. References

1. Scikit-learn Developers. *CountVectorizer* and *cosine_similarity* documentation.  
2. McKinney, W. “Data Structures for Statistical Computing in Python,” *pandas*.  
3. Hunter, J. D. “Matplotlib: A 2D Graphics Environment.”  
4. Manning, C. D., Raghavan, P., Schütze, H. *Introduction to Information Retrieval* (Vector Space Models & Evaluation).

## Appendix A – PDF Conversion Checklist

1. Copy this document into Microsoft Word or Google Docs.  
2. Insert the University crest and Minar-e-Pakistan watermark as described.  
3. Place code screenshots and generated plots in their respective sections.  
4. Use built-in heading styles to regenerate the Table of Contents.  
5. Export as `MIP_Assignment1_Fiaz_Zafar_70189246.pdf`.

## Appendix B – Suggested Attachments

- Assignment_Report_Fiaz_Zafar_70189246.pdf  
- Category_Discriminating_Terms.txt  
- Cosine_Similarity_Matrix.csv  
- Average_Precision_Scores.csv  
- AP_Bar_Chart.png  
- Code_Screenshots.zip (optional)
