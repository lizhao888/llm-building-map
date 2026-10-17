# Evidence map — timestamps for every claim

Source: Yann Dubois, *Building Large Language Models*, Stanford CS229 guest lecture, Summer 2024.
Recording: Stanford Online, published 2024-08-27, 1:44:31. Timestamps below refer to that recording's captions.

The table exists so any claim in `SKILL.md` can be checked in about a minute. Paraphrases, not verbatim quotes, except where marked.

## Framing

| Time | What is said | What it implies |
|------|--------------|-----------------|
| 00:56 | Five components matter: architecture, loss/algorithm, data, evaluation, systems | The map this skill is built on |
| 02:17 | Academia focuses on architecture and training; in practice data, evaluation and systems matter most | Where effort actually pays off |
| 02:56 | Two phases: pretraining (language modelling) and post-training (making an assistant) | The structure of the rest of the talk |
| 12:35 | The tokenizer determines how much text costs and how well it is modelled | Tokenizer choices are product decisions |

## Pretraining

| Time | What is said | What it implies |
|------|--------------|-----------------|
| 28:51–29:20 | "All of the clean internet": random pages are dirty and unrepresentative | "Train on the internet" is not a plan |
| 30:00 | Common Crawl: ~250B pages, ~1 PB | Scale of the raw corpus |
| 30:46–35:20 | Extract text → filter undesirable → dedupe → heuristic filters → model-based filter trained on Wikipedia-linked sites → domain weighting | The eight-step pipeline |
| 35:01 | Up-weighting code is reported to help general reasoning | Why code appears in the mix |
| 37:02 | ~15 people work on data in the Llama team; data needs CPUs more than GPUs | Data is a team, not a script |
| 40:39 | Scaling laws: loss falls predictably with parameters/data/compute | Small runs predict large ones |
| 51:37 | Chinchilla: ~20 tokens per parameter | The data/parameter balance |

## Post-training

| Time | What is said | What it implies |
|------|--------------|-----------------|
| 62:03 | SFT is next-token training on human-written ideal answers | Same loss as pretraining, different data |
| 64:03 | Alpaca: 175 human seeds → 52,000 generated examples | Synthetic data as a scaling trick |
| 65:31 | LIMA: 2,000 → 32,000 SFT examples barely helps | SFT teaches format, not knowledge |
| 72:06 | Teaching the model to produce an ideal answer encourages plausible invention | Part of hallucination has a training cause |
| 72:29 | RLHF: two answers, human picks, optimize toward the preferred one | Preference data instead of demonstrations |
| 74:11 | Binary rewards are sparse; a reward model gives a continuous score | Why Bradley–Terry models appear |
| 77:19 | PPO with a regularizer to avoid over-optimizing the reward | The standard RLHF loop |
| 79:00 | RL "is a mess" in practice — clipping, rollouts, undocumented edge cases | Why DPO was welcome |
| 79:41 | DPO: raise preferred, lower dispreferred, no RL loop | Simpler, widely adopted |
| 80:50 | Under assumptions, DPO and PPO share the same optimum | Not a hack, a reparameterization |
| 85:28 | Humans agree with themselves ~66% of the time on binary preferences; authors managed 67–68% | Ground truth is noisy |
| 86:10 | Human labels ≈ $300 per 1,000 examples | Cost of preference data |
| 87:32 | LLM labellers ≈ 50× cheaper, higher agreement with the human majority | Why synthetic labelling won |
| 84:55 | More RLHF → longer answers; labellers prefer length | "ChatGPT rambles" is a labelling artifact |

## Evaluation and systems

| Time | What is said | What it implies |
|------|--------------|-----------------|
| 88:05 | Validation loss is not comparable across PPO/DPO | Loss is not a leaderboard |
| 89:10 | After PPO the model is a policy, not a distribution; perplexity loses meaning | Classic LM metrics stop working |
| 90:05 | Chatbot Arena: blind pairwise human votes; skewed to technical users, slow, costly | Most trusted, least scalable |
| 91:46 | LLM-as-judge: ~98% correlation with Arena, <3 min, <$10; biased to length and own family | Cheap proxy, with known bias |
| 100:05 | Mixed precision: 32-bit storage, 16-bit compute, 32-bit updates | Standard training setup |
| 102:37 | Operator fusion; `torch.compile` ≈ 2× faster training | Free speedup most people skip |
| 103:09 | Not covered: architecture details, inference, multimodality, UI, data legality, data scarcity | Scope of this map |

## Reading the lecture yourself

- The full recording is on YouTube (`9vM4p9NN0Ts`, Stanford Online). Captions are auto-generated, so product names get mangled — check unfamiliar terms against the slides.
- The lecture is conversational and includes live Q&A; timestamps above point at the substantive passages.
