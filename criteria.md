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

## 1. Held-out accuracy is at least 0.60

On the notebook's held-out test split, accuracy is at least 0.60.

**Why this target:** Three labels means a guesser who ignores the text lands near 0.33. 0.60 is high enough that sorting a feed with it would beat guessing, and low enough that it can still hold on all three seeds in the next unit. A 0.75 target would be a guess about a model that has not been trained.

---

## 2. Every label has an F1 of at least 0.50

On the held-out test split, `analysis`, `hot_take`, and `reaction` each have an F1 of at least 0.50.

**Why this target:** `reaction` is the smallest label, about 58 posts, so only about 8 or 9 of them land in the test split. An F1 on that many posts jumps between seeds. 0.50 is above a one-in-three guess without demanding 0.70 from a handful of examples. One label is not allowed to collapse while the overall accuracy still looks fine.

---

## 3. No label is above 45% of labels.csv

In `labels.csv`, no label's share is above 0.45.

**Why this target:** The course cap is 70%. A file can sit under that cap and still be a majority class the model learns to guess. 45% means there is no majority label. The file I am about to train on is roughly 37% analysis, 35% hot_take, and 28% reaction, so this is a property of the data, checked before any score exists.

---

## 4. A second labeler agrees on at least 0.70 of a 30-post sample

On a shared sample of at least 30 posts, a second person applying these same definitions agrees with the label in `labels.csv` at least 70% of the time.

**Why this target:** The hard boundary is analysis versus hot_take, and box scores sit on reaction versus analysis, so perfect agreement is not realistic. Chance agreement for three labels is about 0.33. 0.70 says two people would apply the taxonomy the same way on most posts and still leaves room for the posts the decision rule exists to settle.

---

## 5. Predictions at confidence 0.80 or higher are at least 0.70 accurate, and more accurate than the rest

On the held-out set, accuracy among predictions whose top-label probability is at least 0.80 is at least 0.70, and that accuracy is higher than accuracy on predictions below 0.80.

**Why this target:** A score I would use as a filter has to be right more often when it is sure. 0.90 would be brittle: the confident slice of a test set of about 30 posts is small, and one or two misses swing it. 0.70 is the line where I would trust the sure calls more than the unsure ones.



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
