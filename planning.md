# Project 3 Planning — TakeMeter
**Community:** r/summonerschool  
**Classifier goal:** Categorize posts by discourse type to surface how people engage with League of Legends improvement content.

---

## Community Choice

**r/summonerschool** is a League of Legends subreddit focused entirely on player improvement. Unlike r/leagueoflegends, which mixes esports news, memes, cosplay, and meta discussion, r/summonerschool has a narrow, consistent purpose: people trying to get better at the game. This makes it well-suited for a discourse quality classifier because the post types are structurally distinct and the community norms are clear — posts are expected to be earnest, improvement-oriented, and substantive.

I chose this community because I'm personally familiar with League of Legends and can make judgment calls about post quality without needing external context.

---

## Label Taxonomy

### Labels

| Label | Definition |
|---|---|
| `advice_request` | The poster is asking for help improving their own gameplay. Posts ask how to play a champion, what to do in a specific scenario, how to climb, or whether a build/strategy is good. The poster is the one who needs help. |
| `guide` | The poster is teaching something. Posts explain a concept, share a tip or strategy, break down a matchup, or offer a structured walkthrough. The poster has knowledge to give, not a question to ask. |
| `discussion` | The poster is opening a debate or inviting opinions about the meta, game design, balance, or how the game works — without a clear right answer and without asking for personal help. |

### Examples per label

**`advice_request`**
- "How do I climb as Wukong/Yi in this meta? I keep hitting the same wall at Gold 2."
- "Is Doran's Shield worth buying into poke matchups or should I just start corrupting potion?"

**`guide`**
- "A complete guide to wave management for low-elo players — covers freezing, slow pushing, and when to crash."
- "Here's what worked for me to finally hit Diamond: track enemy summoner spell timers every single game."

**`discussion`**
- "Why does Yasuo vs Irelia feel completely unplayable for Yasuo? Is it a kit problem or a stat problem?"
- "What is causing support Camille's pick rate to slowly increase? Is it a meta shift or a bug in the system?"

---

## Edge Case Rules

**Rule 1 — Personal experience shared as a tip → `guide`**  
If a post says "here's what worked for me" and proceeds to explain a technique or strategy, label it `guide` even though it's anecdotal. The structural intent is to teach, not to ask.

**Rule 2 — Meta curiosity vs. personal improvement → `discussion` vs. `advice_request`**  
Ask: is the poster trying to improve their own gameplay, or trying to understand/debate something about the game?  
- "How do I play Camille support?" → `advice_request`  
- "Why is Camille support pick rate rising?" → `discussion`  
The signal is who benefits from the answer: the poster themselves (advice_request) or the community in general (discussion).

**Rule 3 — Stat-supported hot take → `discussion`**  
A post that cites a win rate or stat but uses it to make a debatable point ("Yuumi has 52% winrate which proves Riot can't balance") is `discussion`, not `guide`. The reasoning is assertive/opinionated, not instructional.

**Rule 4 — Vague or unanswerable posts**  
If a post is too short or vague to reliably apply any label (e.g., a two-word rant), mark it as `skip` during annotation and exclude it from the dataset. Do not force a label.

---

## Data Collection Plan

- **Source:** r/summonerschool on Reddit
- **Method:** Manual collection by browsing Hot`(~75 posts)`, Top (past month)`(~75 posts)`, and New feeds`(~50 posts)`
- **Target:** 200+ labeled examples
- **Format:** CSV with columns: `post_id`, `title`, `body` (if present), `label`
- **Collection note:** Collect all 200+ posts before labeling. Read 30–40 first to verify labels apply cleanly, then revise definitions if needed before committing to full annotation.

### Target label distribution
Aim for at least 20% per label (~40+ examples each). Expected natural distribution leans `advice_request`-heavy, so actively seek out `guide` and `discussion` posts to balance.

| Label | Target count |
|---|---|
| `advice_request` | ~80 (40%) |
| `guide` | ~60 (30%) |
| `discussion` | ~60 (30%) |

### Train/validation/test split
- Train: 70% (~140 examples)
- Validation: 15% (~30 examples)
- Test: 15% (~30 examples)

---

## Evaluation Metrics Plan

**Primary metric:** Overall accuracy on the test set (fine-tuned model vs. zero-shot baseline).

**Per-class metrics:** Precision, recall, and F1 for each label. This matters because class imbalance could inflate accuracy — a model that always predicts `advice_request` might still hit 40% accuracy.

**Confusion matrix:** Will identify which label pairs are most often confused (likely `advice_request` ↔ `discussion`, since both involve questions).

**Baseline:** Zero-shot classification using Groq llama-3.3-70b-versatile with a prompt that provides the label definitions and asks it to classify each test example.

---

## Model and Training Plan

- **Base model:** `distilbert-base-uncased` (HuggingFace)
- **Fine-tuning environment:** Google Colab (T4 GPU)
- **Libraries:** `transformers`, `datasets`, `scikit-learn`
- **Default hyperparameters:** 3 epochs, learning rate 2e-5, batch size 16
- **Hyperparameter note:** Will document any changes from defaults and reasoning in README.

---

## AI Tool Plan

I plan to use Claude to:
1. Review and stress-test label definitions and edge case rules before annotation begins
2. Help identify patterns in wrong predictions after evaluation (paste misclassified examples, ask for common themes — then verify manually)

I will not use AI to pre-label posts. All 200 annotations will be done by hand.

---

## Hard Annotation Decisions Log

*(To be filled in during annotation — document at least 3 genuinely difficult cases here and in the README)*

| Post summary | Options considered | Final label | Reasoning |
|---|---|---|---|
|New jungler asking how to identify why they win/lose|advice_request vs discussion|advice_request|Poster is seeking personal improvement guidance; "how do you" framing is rhetorical, not an invitation to debate|
|Dia4 player explains how joining fights over farming CS helped them climb from Emerald|guide vs discussion vs advice_request|guide|Post teaches a concrete macro concept; "can anyone explain" is rhetorical — reader walks away with actionable insight|
|Master player returning after 10yr break, frustrated by MMR/matchmaking situation, asking if others relate|advice_request vs discussion|discussion|Poster isn't seeking gameplay improvement advice; inviting shared experiences and commiseration about a structural account problem|
|Player shares free Hwei spell execution trainer with leaderboard|guide vs discussion|guide|Post shares an actionable learning resource; reader walks away with a tool to improve, no debate opened|
|Poster asking community what mindset shift helped them climb most as jungler, with structured response template|advice_request vs discussion|discussion|Collecting community experiences/stories, not seeking personal improvement guidance; "I'm curious about" signals debate/sharing intent|

---

## Stretch Features (tentative)

- [ ] **Error pattern analysis** — identify systematic patterns in model failures beyond individual examples
- [ ] **Deployed interface** — Gradio app that takes a post and returns label + confidence
- [ ] **Inter-annotator reliability** — have one other person label 30+ examples and compute agreement

*Will update planning.md before starting any stretch feature.*
