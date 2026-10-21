# Acceptance criteria — TakeMeter

Five criteria that say what "working" means for this classifier, written in
unit 5 **before** anything was trained.

**All five are yours this time.** None are given. You've had two projects of
practice.

An acceptance criterion names a number. *"The model is accurate"* is an
opinion. *"Every label has an F1 of at least 0.60 on the held-out set"* is a
criterion.

Under each, write a sentence or two on **why that number**. A reason that says
something about your data or your taxonomy earns credit — *"I picked 0.60 F1
for `reaction` because it's my smallest label and I only have about 50
examples of it"*. A reason that could be attached to any project does not.

> Missing your own targets next unit costs you nothing. Setting a target so
> easy you can't miss it does.

---

## Pick numbers you can defend

Not numbers that sound impressive. Three labels means a coin-flip guesser gets
about 33%, so a target of 0.40 is barely a target. Your number should sit
somewhere you'd honestly call useful.

**Cover at least three of these five areas.** They're here as prompts, not as a
form to fill in — a criterion that fits none of them is fine if it names a
number.

| Area | A question it could answer |
|---|---|
| Overall accuracy | How often does it need to be right to be worth using? |
| Per-label performance | Is one label allowed to be much worse than the others? |
| Balance | How lopsided can your label counts get before it's a problem? |
| Consistency | If someone else labelled the same posts, how often should you agree? |
| Confidence | Should a confident prediction be right more often than an unsure one? |

Two things worth knowing before you pick numbers, because both will affect
whether you hit them:

- **Your smallest label will have the jumpiest score.** If a label has 50
  examples, about 8 land in the test split. An F1 computed on 8 examples moves
  a lot between seeds. A target for that label should be looser than one for
  your biggest label, and saying so is a good reason.
- **Unit 6 tests across three seeds, and the target has to hold across all
  three.** A target of 0.65 against results of 0.71, 0.62, 0.68 is a **miss**.
  Pick with that in mind — it is stricter than it first sounds.

---

## 1.

Overall accuracy is at least 0.70 on the held-out set.

**Why this target:** Lowering this from an initial 0.8 — my own reasoning is that replies aren't strictly one category, some could reasonably fit more than one of my labels, which makes the task harder than a clean single-label problem. 0.70 still clearly beats the 0.67 a model would get by just always guessing `reaction` (my most common label), but I'd rather set something I can actually defend than something that sounds impressive.

---

## 2.

F1 for `hot_take` is at least 0.70 on the held-out set.

**Why this target:** Same reasoning as #1 — hot_takes are also genuinely harder to recognize than the other labels, and this is my thinnest label (only 10 examples total, meaning roughly 1-2 in the test split). A small number of test examples makes F1 swing a lot on a single wrong guess, so I kept this target modest rather than matching overall accuracy.

---

## 3.

No label exceeds 75% of the dataset.

**Why this target:** My real, already-collected data has `reaction` at about 67% of 217 rows. Setting the cap at 75% matches reality without pretending I can rebalance further in the time I have, while still guarding against one label dominating so much that the model just learns to guess it and ignores the rest.

---

## 4.

If I relabeled a sample of these comments myself a second time, I'd agree with my first pass at least 90% of the time.

**Why this target:** Gym/fitness community replies are genuinely easier to categorize into my chosen labels than a lot of domains would be — the distinctions (reporting a result vs. making a backed claim vs. a bare reaction) are usually pretty visible in the text itself, so I expect to be fairly consistent with myself.

---

## 5.

When the model's confidence is above 0.7, it is correct at least 90% of the time.

**Why this target:** Keeping this as written, but flagging a real limitation: my data is heavily skewed (67% reaction, only 5% hot_take), and models trained on skewed data tend to only reach high confidence on the majority label. So this criterion may end up telling me little to nothing about `hot_take` specifically, since the model may rarely be confident enough on it to even be evaluated by this rule.


---

<!-- ─────────────────────────────────────────────────────────────────────────
     UNIT 6 — read this before you change anything above.

     If a criterion turns out to be BROKEN rather than merely unmet, you can
     revise it, and that earns credit. But never delete or edit the original
     line. Add the revision underneath, like this:

         ## 2. Every label performs acceptably

         The model performs well on all labels.

         **Why this target:** ...

         > **Revised in unit 6:** Every label has an F1 of at least 0.60 on
         > the held-out set.
         >
         > **Why revised:** "performs well" gave me nothing to check. I
         > couldn't produce a verdict from it at all.

     That's a revision because the criterion couldn't be MEASURED.

     Lowering a target because you missed it is not a revision, and it costs
     you the point:

         ✗ "Overall accuracy of at least 0.65" → "at least 0.55", because
            0.65 turned out to be optimistic for 200 examples.

     A number you missed stays where it is, gets diagnosed, and gets a fix
     attempted. That's where the points are.
     ───────────────────────────────────────────────────────────────────────── -->
