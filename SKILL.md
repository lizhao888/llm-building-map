---
name: llm-building-map
description: "Explain or audit how an LLM is actually built — five components, the pretraining data pipeline, SFT/RLHF/DPO, and why evaluation is the hard part. Use when writing AI explainers, judging vendor claims, or scoping an LLM project."
version: 1.0.0
license: MIT
platforms: [linux, macos, windows]
metadata:
  hermes:
    tags: [llm, training, evaluation, explainer, due-diligence]
    related_skills: [grounded-citations]
---

# LLM Building Map

A compact, verifiable mental model of how a ChatGPT/Claude-style model is made. Distilled from Yann Dubois' Stanford CS229 guest lecture *Building Large Language Models* (Summer 2024); every claim below carries a timestamp into the source transcript.

## When to use this

- Explaining to a non-technical audience how these models are built (and why they behave the way they do).
- Auditing a claim such as "we fine-tuned it, so it knows our domain", or "our model scores 92 on benchmark X".
- Scoping work: deciding whether the next win comes from data, evaluation, or a different model.

## The short version

Five components: **architecture, training objective, data, evaluation, systems.**
Academia optimizes the first two; in practice the last three decide the outcome.

> "What matters in practice is mostly the three other topics — data, evaluation and systems." (00:56–02:48)

Two phases: **pretraining** turns the internet into a next-token model; **post-training** turns that model into an assistant.

---

## 1. Pretraining

### 1.1 Tokenizer (06:51, 12:35)

Text is chunked into sub-word tokens (BPE and relatives). The tokenizer fixes what the model can "see": the same sentence costs a different number of tokens under different tokenizers, which moves both price and quality. Code, math and non-English text are where tokenizers differ most.

### 1.2 The data pipeline (28:51)

"Just train on the internet" hides the actual work. A typical pipeline:

| Step | What happens | Why |
|------|--------------|-----|
| Crawl | Common Crawl: ~250B pages, ~1 PB | The starting corpus |
| Extract | HTML → text | Random pages are menus, footers and truncated sentences |
| Filter | NSFW, harmful, PII | Blacklists plus small classifiers |
| Dedupe | boilerplate, same page under many URLs, repeated paragraphs | Duplicates distort the distribution |
| Heuristics | odd token distribution, word length, too short/long | Cheap quality signal at scale |
| Model-based | Train a classifier on sites *linked from Wikipedia* vs random web | "Referenced by Wikipedia" ≈ high quality |
| Mix | Up-weight code and books, down-weight entertainment | More code measurably helps reasoning |
| Final phase | High-quality data with a decaying learning rate | Deliberately overfit on Wikipedia-grade text |

Team shape, as reported: ~15 people on data in the Llama team — more people than on modelling, more CPUs than GPUs (37:02).

### 1.3 Scaling laws (40:39, 51:37)

Loss falls as a power law in parameters, data and compute, so small runs predict large ones. Chinchilla's practical takeaway: **~20 tokens per parameter** is a better balance than the earlier "bigger model, same data" habit.

---

## 2. Post-training

### 2.1 SFT — supervised fine-tuning (62:03)

Ordinary next-token training on human-written (prompt → ideal answer) pairs.

- **SFT teaches format and persona, not knowledge.** LIMA: scaling from 2,000 to 32,000 examples barely moves quality (65:31).
- Side effect: the model learns to produce a plausible-looking answer instead of admitting ignorance — part of where hallucination comes from (72:06).
- Scale trick: Alpaca took 175 human seeds and generated 52,000 pairs with a strong model (64:03).

### 2.2 RLHF (72:29)

```
same prompt → two candidate answers → a human picks the better one
        ↓
train a reward model (Bradley–Terry: preference → comparable score)
        ↓
optimize the policy with PPO + a KL-style penalty against over-optimizing the reward
```

Binary rewards are too sparse — you learn *which* answer won, not *by how much* (74:11). PPO is notoriously hard to keep stable (79:00).

### 2.3 DPO (79:41)

Skip reinforcement learning: raise the likelihood of the preferred answer, lower the dispreferred one. Under standard assumptions its optimum matches PPO's, with a much simpler implementation — now a default in open-source and common in industry.

### 2.4 The economics of labelling (83:47)

- Humans agree with themselves only ~66% of the time on a binary preference task; the paper's own authors managed 67–68%.
- Human labels cost roughly **$300 per 1,000 examples**; LLM labellers are ~**50× cheaper** and agree with the human majority more often than humans do.
- Bias to watch: labellers prefer longer answers, so models trained on their preferences get verbose — "why ChatGPT rambles" (84:55).

---

## 3. Evaluation — the part people get wrong (87:41)

- **Validation loss is not comparable** across post-training methods (PPO vs DPO optimize different objectives).
- **Perplexity stops being meaningful** after PPO: the model is a policy, no longer a distribution over text (89:10).
- **Chatbot Arena** (blind pairwise human votes) is the most trusted signal — and biased toward tech-savvy users, slow and expensive.
- **LLM-as-judge** (e.g. AlpacaEval): ~**98% correlation** with Arena, runs in **<3 minutes for <$10** (91:46). Biases: prefers longer answers and its own family's style.

Practical rule: before believing any benchmark number, ask **which benchmark, against whom, judged by what**.

---

## 4. Systems (100:05)

- **Mixed precision**: store weights in 32-bit, compute in 16-bit, update in 32-bit. Deep learning is noisy enough that the precision loss is irrelevant.
- **Operator fusion**: every PyTorch op round-trips data through GPU memory; fuse the ops and move data once. `torch.compile` gets roughly **2× faster training** for free (102:37).

---

## 5. What this map does not cover (103:09)

Architecture details, inference/serving, multimodality, product UI, data legality, and the possibility that public internet text runs out.

---

## Common misreadings

| Misreading | Correction |
|------------|------------|
| "Fine-tuning teaches the model our knowledge." | It mostly teaches style. New facts need retrieval or continued pretraining. |
| "More SFT data = better model." | Past a few thousand good examples, gains flatten. |
| "Perplexity/loss proves the model got better." | After preference tuning those numbers stop meaning what they used to. |
| "A new architecture is the main lever." | Data, evaluation and systems dominate in practice. |
| "Benchmark X says we beat Y." | Ask which benchmark, which opponent, which judge. |

## Verification

- Source: Yann Dubois, *Building Large Language Models*, Stanford CS229 guest lecture, Summer 2024 (published by Stanford Online, 27 Aug 2024; 1:44:31).
- Timestamps refer to that recording's captions. Full timestamped transcript is not redistributed here; `references/lecture-notes.md` maps each claim to a timestamp so you can check it against the original.
- This is an unofficial study note, not affiliated with Stanford or Anthropic.

## Files

- `references/lecture-notes.md` — condensed notes with timestamps (English).
- `references/lecture-notes.zh-CN.md` — the same, in Chinese.
