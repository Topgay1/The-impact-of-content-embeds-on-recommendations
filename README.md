# The-impact-of-content-embeds-on-recommendations
# Hybrid Recommendation Systems: Impact of Content Embeddings

## Overview

Modern recommender systems often combine collaborative filtering (CF) with content-based signals to improve recommendation quality. While content embeddings are widely used to address cold-start and sparsity issues, their impact on recommendation quality can vary significantly depending on user and item characteristics.

This project investigates how different types of content embeddings (textual, visual, and multimodal) affect recommendation quality across multiple realistic scenarios, including cold-start items, sparse users, and active users. In addition to offline performance, we analyze model robustness to noise in content representations.

The goal of this project is not to optimize a single metric, but to **understand trade-offs, risks, and failure modes** of hybrid recommendation systems.

---

## Dataset

We use the **VK-LSVD** open dataset, which contains:
- user–item interaction logs,
- content features for items,
- precomputed content embeddings.

Dataset source:  
https://huggingface.co/datasets/deepvk/VK-LSVD

---

## Problem Setting

Given a set of users \( U \), items \( I \), and interaction history \( R \), the task is to generate a ranked list of items for each user.

We compare:
- **CF-only models** that rely solely on interaction data,
- **Hybrid models** that incorporate content embeddings.

The analysis is performed **not only on the full dataset**, but also across specific user and item segments that commonly cause failures in recommender systems.

---

## User and Item Segmentation

To reflect real-world conditions, we evaluate models under the following scenarios:

### User Groups
- **Sparse users**: users with fewer than \( K \) interactions in the training set.
- **Active users**: users with at least \( K \) interactions in the training set.

### Item Groups
- **Cold-start items**: items with fewer than \( N \) interactions in the training set.
- **Popular items**: items with interaction counts above the cold-start threshold.

Values of \( K \) and \( N \) are fixed per experiment and varied to test robustness.

---

## Models

We evaluate a minimal but representative set of models to isolate the effect of content signals:

| ID | Model |
|----|------|
| M0 | CF-only baseline |
| M1 | CF + text embeddings |
| M2 | CF + image embeddings |
| M3 | CF + multimodal embeddings |

All models share the same training and evaluation protocol. The only difference is the type of content signal used.

---

## Evaluation Protocol

### Data Splitting
We use a user-level **leave-last-out** strategy:
- the most recent interaction(s) per user are held out for testing,
- the remaining interactions are used for training.

This setup approximates a realistic recommendation scenario.

### Metrics
We report the following metrics:
- **NDCG@10** — ranking quality,
- **Recall@10** — recommendation coverage,
- **Coverage@10** (optional) — diversity of recommended items.

---

## Research Hypotheses

### H1 — Active Users (Risk of Content Noise)
For active users with rich interaction histories, adding content embeddings does not improve recommendation quality compared to CF-only models and may degrade ranking performance due to noisy or conflicting signals.

**Motivation:**  
Collaborative filtering already provides a strong signal for active users. Introducing content features may interfere with fine-grained preference modeling.

---

### H2 — Sparse Users (Benefit with Fragility)
For sparse users, content embeddings improve recommendation quality compared to CF-only models; however, hybrid models are more sensitive to noise in content representations.

**Motivation:**  
When collaborative signals are weak, content features provide valuable information, but errors in embeddings can significantly affect recommendations.

---

### H3 — Cold-Start Items (Necessity of Content)
For cold-start items, content embeddings significantly improve recommendation quality compared to CF-only models, as collaborative signals for such items are absent or extremely limited.

**Motivation:**  
Content-based representations are the primary mechanism for introducing new items into the recommendation pool.

---

## Experimental Design

Each hypothesis is evaluated using the same experimental pipeline but on different data subsets.

### Scenario-Based Evaluation

| Scenario | Users | Items | Purpose |
|--------|------|------|--------|
| Active users | Active | All | Test risk of content interference |
| Sparse users | Sparse | All | Test benefit and sensitivity |
| Cold-start items | All | Cold-start | Test necessity of content |

---

### Robustness Experiments

To evaluate model stability, we introduce artificial noise into content embeddings:
- Gaussian noise with varying variance,
- Random feature dropout.

We measure how recommendation quality degrades under increasing noise levels.

---

## Expected Outcomes

This project aims to produce:
- scenario-specific insights into when content embeddings help or hurt,
- comparative analysis of text, image, and multimodal representations,
- empirical evidence of robustness and failure modes in hybrid recommenders.

Importantly, hypotheses may be **confirmed or disproved**, and both outcomes are considered valuable.

---

## Limitations and Future Work

- Offline metrics may not fully reflect online user behavior.
- Exploration–exploitation strategies are not explicitly modeled.
- A/B testing and user-level feedback loops are outside the scope of this study.

Future work may include online evaluation, adaptive weighting of content signals, and exploration-aware recommendation strategies.

---

## Repository Structure (Planned)

