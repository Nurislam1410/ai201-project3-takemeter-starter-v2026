# TakeMeter

> ### 👋 Start here
>
> **New to this repo? Read [RUNNING.md](RUNNING.md) first** — setup, the
> notebook, the baseline, and what to do when something breaks.
>
> Once `python test.py` passes:
>
> ```bash
> head -5 data/practice_labels.csv     # the shape your labels.csv needs
> ```
>
> Then open `takemeter.ipynb` **in this folder** — in VS Code, or with
> `jupyter notebook` if you prefer. Pick the kernel: the `.venv` inside this
> project. Run section 1, which reports the hardware you'll be training on.
> Everything else waits until you have data.
>
> Nothing to upload, nothing to connect, no accounts and no keys. The notebook
> runs on your machine and writes next to your code.
>
> **The rest of this file is your submission.** Fill it in as you go.

---

<!-- ─────────────────────────────────────────────────────────────────────────
     HOW TO USE THIS FILE

     Unit 5 asks for the first five sections. Unit 6 adds the five below them.

     Everything is pasted as TEXT. No screenshots, no images.

     ⚠️ The confusion matrix especially. The notebook prints one as a markdown
     table, ready to copy. A screenshot of a matrix earns nothing. Paste the
     table.
     ───────────────────────────────────────────────────────────────────────── -->

<!-- ═══════════════════════ UNIT 5 — THE BUILD ═══════════════════════ -->

## What This Does

<!-- Your community, and what your classifier sorts posts into. Three or four
     sentences. -->

I am making use of a public gym or fitness subreddit, for example, r/Fitness, r/gym, or those similar to bodybuilding communities. The material I am relying on comes from my previous experience with them rather than from my current involvement; although I used to check these communities regularly, I am not currently taking part in them.

<!-- TODO once Milestone 2 labels are locked: one sentence on what the classifier sorts comments into. Candidate distinctions noticed so far, to confirm against real comments:
1. A few of the comments mention a particular tracked number or result (such as the weight lifted, the number of repetitions, the measurements, or the time taken); the rest do not.
2. A few of the comments present a confident assertion supported by something verifiable such as a study, a statistic, or a particular example, while others simply state an opinion without any supporting evidence.
3. A few of the comments are merely spontaneous reactions or expressions of pride at the time, with no argument actually being presented. -->

---

## Label Taxonomy

<!-- Each label: a one-sentence definition and two real examples from your
     reading. Then your decision rule for the hardest boundary.

     The decision rule is worth a point on its own and it's the thing most
     people leave out. Every taxonomy has a hardest boundary. Name yours. -->

### `progress_log`

**Definition:** Reports a specific tracked result without arguing a point.

**Example 1:**
> I went to the gym for all five days this week. I've been struggling hard with consistency, but I did it.

**Example 2:**
> Complete beginner here in my 2nd month. Somehow, I killed my calves and it left me with DOMS for 4 days straight. Couldn't really walk for 2 of those days. Finally came back to the gym after a week of rest and set crazy PRs. Increased my bench from 7 to 12 reps.

### `advice_analysis`

**Definition:** Makes a claim backed by something checkable — a mechanism, a number, a specific method.

**Example 1:**
> The most important thing for muscle building is the # of hard reps performed over time. Doing a million bicep curls with no resistance won't build any muscle as 0 of them were what is called an effective rep in the literature.

**Example 2:**
> Stack your joints, regardless of limb length. Then make small adjustments from there for pressing movements.

### `hot_take`

**Definition:** A confident claim about training/diet with no support offered.

**Example 1:**
> You should commit in one direction with body weight loss/gain. Muscle loss during a cut is overblown, you just have less muscle mass than you think.

**Example 2:**
> Still no excuse to skip leg day

### `reaction`

**Definition:** A bare reaction or observation, no argument being made at all.

**Example 1:**
> hell yeah

**Example 2:**
> I picked things up and placed them down.

### The hardest boundary

**Which two labels:** `reaction` vs. `advice_analysis`

**The decision rule I used every time:**
"I joke that pizza is my superpower. On the rare occasion that I smash a pizza, the workout afterwards is always FULL of PRs..." could read as a bare reaction (just bragging) or as advice_analysis (it does name a mechanism — extra calories/energy). Rule: if the post names a specific mechanism or number as the reason behind what happened, it's `advice_analysis`, even if the tone is casual or joking.



---

## The Dataset

<!-- Where you collected from, how you labelled, your counts, and three hard
     cases. -->

**Where the posts came from:** A public gym/fitness subreddit community (r/Fitness, r/gym, r/bodybuilding-type sources) — comments/replies specifically, not just original post bodies, since that's where most of the real opinion/argument/reaction content lives in this community.

**How I labelled them:** 20 posts labelled cold first — no AI, no help, just my own definitions, as the honesty check. The remaining ~197 were pre-labelled by AI using simple keyword rules against my own label definitions, then I read through and corrected them myself before finalizing.

**Counts per label:**

| Label | Count | Share |
|---|---|---|
| reaction | 143 | 66% |
| advice_analysis | 40 | 18% |
| progress_log | 22 | 10% |
| hot_take | 12 | 6% |
| **Total** | 217 | 100% |

**Three hard cases**

<!-- Any post that made you pause: what it was, which two labels it could have
     been, and what you chose. These are worth more than the easy 190. -->

**1.**
> *The post:* "It's by far women 30+ who interrupt the most, followed by unfit women in their 20s, and men in their 60s+."
>
> *Could have been:* `reaction` (a bare observation/complaint)
>
> *I chose `hot_take`, because:* Wasn't sure — went with my gut, since it reads as a confident generalization with no real support behind it.

**2.**
> *The post:* "However you want to define it. There's no universal standard"
>
> *Could have been:* `reaction` (a short, low-content reply)
>
> *I chose `hot_take`, because:* Wasn't sure — went with my gut.

**3.**
> *The post:* "Pennies have been useless for decades. The Simpsons even has a joke back in the 90s about government keeping them in circulation..."
>
> *Could have been:* `reaction` (riffing on a running joke in the thread, not really arguing a point)
>
> *I chose `hot_take`, because:* Wasn't sure — went with my gut.

---

## The Training Run

<!-- Your starting model, your settings, and anything you changed and why. -->

**Base model:** distilbert-base-uncased (project default, not changed).

**Settings:** 3 epochs, learning rate 2e-5, batch size 16, max length 128, seed 42 (all defaults, not changed).

**Environment note:** training initially failed in the local project venv — iCloud Desktop sync had corrupted installed package files (confirmed: `numpy/__init__.py` was missing entirely, then a different file inside `torch` was missing on the next attempt). Fixed by building a fresh virtual environment outside of iCloud-synced storage and reinstalling dependencies there. Training then ran cleanly.

**Split sizes:** 151 train / 33 val / 33 test (stratified 70/15/15 on 217 rows). `hot_take` has only 2 test examples and `progress_log` 3 — both flagged by the notebook's own split checker as too thin for a stable F1.

**Results (seed 42):**

| Metric | Value |
|---|---|
| Overall accuracy | 0.667 |
| Macro F1 | 0.200 |

| Label | F1 | Support |
|---|---|---|
| `reaction` | 0.800 | 22 |
| `advice_analysis` | 0.000 | 6 |
| `progress_log` | 0.000 | 3 |
| `hot_take` | 0.000 | 2 |

Confidence: accuracy on the most-confident third of predictions was 0.727, versus 0.636 on the least-confident third.

**What actually happened:** the confusion matrix shows the model predicted `reaction` for every single test example, including all the `advice_analysis`, `progress_log`, and `hot_take` ones. It collapsed to the majority class — the 0.667 accuracy is exactly what you'd get by always guessing `reaction`, since that's 66% of the data. This matches what criteria.md #5 already flagged as a risk before training: a model trained on this imbalance would rarely get confident enough on the minority labels to be evaluated on them at all, and here it didn't learn to distinguish them from `reaction` whatsoever.

---

## How I Used AI

<!-- Two specific moments — what you asked, what came back, what you changed.

     ⚠️ Plus disclosure of any pre-labelling. If you had a model pre-label a
     batch and then read and corrected every one, say that. It's an allowed
     workflow and disclosing it costs you nothing. Not disclosing it is the
     problem. -->

**Moment 1**

- *What I asked for:* Stress-test my label boundaries once I'd picked `progress_log` / `advice_analysis` / `hot_take` / `reaction`, and find a real ambiguous case from my own collected comments.
- *What came back:* A boundary case between `reaction` and `advice_analysis` (a comment joking about pizza before a workout) and a proposed decision rule: if the post names a specific mechanism or number as the reason, it's `advice_analysis`, even if the tone is casual.
- *What I changed:* Nothing — I confirmed this matched what I was seeing in my own reading and kept it as the decision rule.

**Moment 2**

- *What I asked for:* Critique on my 5 acceptance criteria numbers (overall accuracy, hot_take F1, balance, consistency, confidence) for being too easy or too hard, without being told different numbers.
- *What came back:* A flag that two of my numbers didn't match my own stated reasoning (reasoning argued the task was harder, but the number didn't reflect that), and a factual catch that one target (no label exceeds 50%) was already false against my own collected data (reaction was at 67%).
- *What I changed:* Lowered the mismatched numbers to 0.7, and corrected the balance target to 75% to match reality.

**Pre-labelling disclosure:** I collected 217 real comments (manually copy-pasted from gym/fitness subreddits, plus some extracted from screenshots I'd taken). I had the AI pre-label all of them against my 4 definitions using simple keyword-based rules, with 20 rows deliberately left blank in the note column for me to label completely on my own, without help, as the cold/honesty check. I read through and corrected the AI's pre-labels myself before finalizing `labels.csv`.

<!-- ═══════════════════════ UNIT 6 — THE TEST ═══════════════════════

     Don't fill these in during unit 5.
     ═══════════════════════════════════════════════════════════════════ -->

---

## Baseline vs. Trained

<!-- Both models on the same posts. `python baseline.py --trained results.json`
     prints this table for you. -->

| Measure | Baseline | Trained | Difference |
|---|---|---|---|
| Overall accuracy | 0.182 | 0.667 | +0.485 |
| Macro F1 | 0.077 | 0.200 | +0.123 |
| F1 — `advice_analysis` | 0.308 | 0.000 | −0.308 |
| F1 — `hot_take` | 0.000 | 0.000 | 0.000 |
| F1 — `progress_log` | 0.000 | 0.000 | 0.000 |
| F1 — `reaction` | 0.000 | 0.800 | +0.800 |

**What I predicted before I looked:**
<!-- Milestone 1 asks you to write this BEFORE seeing the trained numbers. A
     prediction made afterwards isn't one. -->
*(Skipped — I already saw both result files by the time I filled this table in, so anything written here now wouldn't actually be a prediction. Flagging the gap honestly rather than backfilling a fake one.)*

**What the gap actually means:** Looking at `baseline_results.json`'s raw predictions, the zero-shot baseline predicted `advice_analysis` for **every single one of the 33 test posts** — its own kind of majority-class collapse, just to a different label than the trained model picked. The trained model collapsed to `reaction` (66% of training data) instead. So fine-tuning did measurably move the numbers — accuracy and macro F1 both went up — but neither model actually learned to tell the four categories apart. They each just settled on one label to always say, for different underlying reasons: the baseline's zero-shot entailment prompt apparently treats `advice_analysis` as the closest match to most text regardless of content, while the trained model took the standard loss-minimizing shortcut on an imbalanced training set.


---

## Run Log — Before

<!-- Five criteria across three seeds. The notebook's section 6 prints the
     spread table; the Target and Verdict columns are yours. -->

| Criterion | Target | Seed 42 | Seed 7 | Seed 2024 | Verdict |
|---|---|---|---|---|---|
| 1. Overall accuracy | ≥ 0.70 | 0.667 | 0.667 | 0.667 | **MISS** (all three seeds under target, and identical) |
| 2. `hot_take` F1 | ≥ 0.70 | 0.000 | 0.000 | 0.000 | **MISS** |
| 3. No label > 75% of dataset | ≤ 75% | 66% | 66% | 66% | **PASS** (dataset-level, doesn't change across seeds) |
| 4. Self-relabel consistency | ≥ 90% | — | — | — | **NOT MEASURED YET** (needs a manual second-pass relabel, not a model run) |
| 5. Correct ≥ 90% when confidence > 0.7 | ≥ 90% | — | — | — | **NOT MEASURABLE** — no prediction in any seed reached 0.7 confidence (mean confidence when right ranged 0.551–0.592, when wrong 0.568–0.597; both are below the 0.7 gate, so the criterion has zero qualifying examples to check) |

### Confusion matrix

<!-- ⚠️ TYPED AS A MARKDOWN TABLE. The notebook prints one ready to paste.
     An image of a matrix earns nothing. -->

Identical across all three seeds (seed 42 shown; seeds 7 and 2024 produced the exact same matrix):

| true \ predicted | `progress_log` | `advice_analysis` | `hot_take` | `reaction` |
|---|---|---|---|---|
| **`progress_log`** | 0 | 0 | 0 | 3 |
| **`advice_analysis`** | 0 | 0 | 0 | 6 |
| **`hot_take`** | 0 | 0 | 0 | 2 |
| **`reaction`** | 0 | 0 | 0 | 22 |

**My biggest off-diagonal number, and what it means:**
<!-- Not "the model made mistakes" — WHICH boundary it didn't learn, and which
     direction. "7 real analysis posts were called hot_take and only 3 went the
     other way" is a direction, not just an error rate. -->

The biggest single off-diagonal cell is `advice_analysis` → `reaction`: 6 out of 6 `advice_analysis` test posts were called `reaction`. But the real pattern isn't one boundary — it's every boundary at once, in one direction: all 11 non-`reaction` test posts (3 `progress_log`, 6 `advice_analysis`, 2 `hot_take`) were predicted `reaction`, and zero `reaction` posts were ever predicted as anything else. The model didn't fail to learn a specific pair like `reaction` vs. `advice_analysis` — it never learned any of the three minority labels at all. It just learned "say `reaction`," which is the loss-minimizing shortcut on a training set that's 66% `reaction`.

---

## Verdicts and Diagnoses

<!-- MET or MISSED against LAST UNIT's target. The target has to hold across
     all three seeds, not turn up sometimes. -->

| # | Criterion | Target | Verdict | How I decided |
|---|---|---|---|---|
| 1 | Overall accuracy | ≥ 0.70 | **MISS** | 0.667 on all three seeds, identical every time — not close to the line, not a fluke of one seed |
| 2 | `hot_take` F1 | ≥ 0.70 | **MISS** | 0.000 on all three seeds — the model never once predicted `hot_take`, let alone correctly |
| 3 | No label > 75% of dataset | ≤ 75% | **PASS** | `reaction` sits at 66% of the 217 collected rows — a dataset-level fact, so it doesn't vary by seed |
| 4 | Self-relabel consistency | ≥ 90% | **NOT MEASURED** | this requires me to manually relabel a sample a second time, which I haven't done yet given the time I had this unit |
| 5 | Correct ≥ 90% when confidence > 0.7 | ≥ 90% | **NOT MEASURABLE AS-IS** | across all three seeds, no single prediction reached 0.7 confidence (mean confidence when right/wrong both sat in the 0.55–0.60 range) — there's no example that qualifies for this criterion to even apply to |

**Diagnoses**

<!-- For each miss: the cause, and how you know. The four common causes are:
     too few examples for a label, a boundary you applied inconsistently, a
     genuinely hard label pair, and a task the model can't reach from this
     much data.

     ⚠️ Use your agreement report as evidence. It is the only instrument you
     have that can tell a LABELLING problem from a MODEL problem, and this
     section is graded on whether you used it that way. -->

**Important caveat on this diagnosis:** my agreement report is blocked (see the Agreement Report section below — `data/staff_labels.csv` never arrived), so I can't use it here to separate a labelling problem from a model problem the way this section asks. Everything below is reasoned from the confusion matrix alone, which can only point at the model side. If the staff labels show up later, this diagnosis should be redone properly with that evidence.

From the confusion matrix: every one of the 11 non-`reaction` test posts (`progress_log`, `advice_analysis`, `hot_take` combined) was predicted `reaction`, and no `reaction` post was ever misclassified. That's the single cause behind misses #1 and #2 — it isn't a hard boundary between two specific labels (like `reaction` vs. `advice_analysis`), it's a flat majority-class collapse: `reaction` is 66% of the training data, so always guessing it is the loss-minimizing shortcut, and the model took it. `hot_take` additionally has only 12 total examples (2 in the test split) — too few for the model to learn a pattern for it even if the imbalance weren't there. Both causes point the same direction: this looks like a data problem (imbalance + a genuinely thin minority label), not a boundary-definition problem — but I can't confirm that against the agreement report until it's unblocked.

---

## Agreement Report

<!-- Your rate against the staff set, and every disagreement adjudicated.

     Remember you labelled these 30 under the STAFF taxonomy in
     data/staff_taxonomy.md, not your own — so every argument below is made
     from those definitions and those decision rules. -->

**BLOCKED:** `data/staff_labels.csv` (the staff answer key) never arrived in this repo, so `agreement.py` has nothing to compare against and can't run. My own 30/30 labels under the staff taxonomy are saved in `my_staff_labels.csv`, ready to go the moment the staff file shows up — I'm not filling in the agreement rate or disagreements below with fabricated numbers in the meantime.

**Agreement rate:** ___ / 30 = ___%

<!-- Nobody grades this number. A 60% who argues every disagreement from the
     stated rules beats a 95% who wrote "staff was right" nine times. Several
     of the 30 were chosen because they're genuinely ambiguous — you should be
     winning some of these. -->

**Disagreements**

<!-- Three lines each: the post, both labels, and who you think is right and
     why — grounded in the staff definitions you were both applying.

     Then sort each into one of three piles:
       (a) the rule covered it and I applied it loosely → a consistency problem
       (b) the rule genuinely doesn't say               → a gap in the definitions
       (c) the rule is ambiguous here and my reading is defensible → argue it.
           This is a legitimate win.

     Pile (a) is the one that matters most for your diagnosis: if you applied a
     written rule two different ways on 30 posts, that is direct evidence about
     what you did across your own 200. -->

**1.**
> *The post:*
>
> *Staff said / I said:*
>
> *My call, and why:*
>
> *Which pile:*

**2.**
> *The post:*
>
> *Staff said / I said:*
>
> *My call, and why:*
>
> *Which pile:*

**What the pattern in my disagreements tells me:**



---

## The Improvement

**What I changed:**

**Which diagnosis pointed at it:**

### Run Log — After

| Criterion | Target | Seed 42 | Seed 7 | Seed 2024 | Verdict |
|---|---|---|---|---|---|
| 1.  |  |  |  |  |  |
| 2.  |  |  |  |  |  |
| 3.  |  |  |  |  |  |
| 4.  |  |  |  |  |  |
| 5.  |  |  |  |  |  |

**Did it help, and how do I know:**

<!-- If it didn't, say so. Relabelling that didn't help is a genuinely
     interesting result and earns full credit. -->



---

## What's Still Broken

<!-- For each criterion still missed: what you'd do, and why you stopped. -->



**The gap between what I meant my labels to capture and what the model
learned:**
<!-- Two sentences. Your confusion matrix is the evidence. -->



<!-- ═════════════════════════════════════════════════════════════════════

     SUBMISSION CHECKLIST — unit 5

       [ ] criteria.md has five numbered criteria, each naming a NUMBER
       [ ] Each has a reason underneath tied to your data or taxonomy
       [ ] labels.csv: at least 150 rows, text/label/note, ONE file not split
       [ ] No label above 70%
       [ ] All five unit 5 sections have real content
       [ ] Label Taxonomy includes the decision rule for your hardest boundary
       [ ] The Dataset includes three hard cases
       [ ] results.json and test_split.csv committed (the notebook does this)
       [ ] At least four new commits
       [ ] Repository URL submitted — WRITE IT DOWN

     SUBMISSION CHECKLIST — unit 6

       [ ] Baseline vs. Trained table, with your prediction written beforehand
       [ ] Run Log — Before, five criteria across three seeds
       [ ] Confusion matrix TYPED AS A MARKDOWN TABLE
       [ ] A verdict on every criterion
       [ ] A diagnosis for every miss, using the agreement report as evidence
       [ ] Agreement Report with every disagreement adjudicated
       [ ] One improvement, with Run Log — After
       [ ] What's Still Broken
       [ ] results_three_seeds_before.json, results_three_seeds_after.json,
           baseline_results.json and
           agreement_results.json committed
       [ ] At least four new commits
       [ ] The SAME repository URL as last unit

     Do not delete and recreate this repository.
     ═════════════════════════════════════════════════════════════════════ -->

---

📖 **How to run this project: [RUNNING.md](RUNNING.md)**
