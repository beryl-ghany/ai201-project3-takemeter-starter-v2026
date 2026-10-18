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

TakeMeter sorts self-posts from r/nba, the public Reddit community for NBA discussion. It puts each post into one of three labels: `analysis` (an argument that cites a checkable fact), `hot_take` (a confident claim with no checkable fact), or `reaction` (a feeling or a box score about a moment, with no argument). The point is the distinction regulars make in a sentence and almost never write down.

---

## Label Taxonomy

### `analysis`

**Definition:** The post makes an argument backed by a specific checkable fact: a rate, a counting stat, a slash line, a dollar figure, or a series result.

**Example 1:**
> [OC] People debate over the bar for making the Hall of Fame for players, but nobody does for coaches. I've analyzed all 22 coaches in the HoF for their NBA careers to set the bar, find the biggest snubs (it's K.C. Jones and it's a joke he's not in), and determine which coaches are on their way.

**Example 2:**
> The Hawks just had a great pre-season; over 5 games they were +56 FGA, +58 3PA, +28 OREBS, +12 STLS, +24 TOV. All of the most important possession stats this team did really good. I think they are the biggest surprise to me this off-season so far.

### `hot_take`

**Definition:** The post states a confident claim and offers no checkable fact to support it.

**Example 1:**
> Nikola Jokic is the greatest passer of all time.

**Example 2:**
> Portland Trail Blazers are so good. I think they are one of the biggest title contenders this year! Team are full of hungry, talented players.

### `reaction`

**Definition:** The post responds to a moment that just happened. It is a feeling or a box score, and it does not argue a claim.

**Example 1:**
> Keep the Blazers in Portland! Tell Dundon to keep the a blazers in a Portland. The city loves basketball and thrive on the great energy that is the Blazers.

**Example 2:**
> Hugo Gonzalez in the win vs the Sixers | 19 MIN | 21 PTS | 4 REB | 3 AST | 6/11 FG | 3/7 3FG | 6/8 FTs | 0 TOV | 72.3% TS

### The hardest boundary

**Which two labels:** `analysis` and `hot_take`. Heated posts do both. Box scores create a second boundary with `reaction`, and the same rule settles it.

**The decision rule I used every time:** If the post names a specific checkable fact and uses it to support a claim, it is `analysis`, even if the tone is heated. If it makes a confident claim and offers no checkable fact, it is `hot_take`. If it is only a feeling about a moment, or a box score with no argument attached, it is `reaction`.

---

## The Dataset

**Where the posts came from:** Public self-posts on r/nba, collected from the Arctic Shift Reddit archive (search API, subreddit `nba`, self-posts only). Game threads, highlight links, and reporter-tagged news posts were left out. Whole posts are in the file, not trimmed excerpts.

**How I labelled them:** No row is a cold label. An assistant applied the definitions and the decision rule above to the collected posts, then the title of every kept row was read and corrected: news copy, polls, quotes with no poster argument, spam, and near-duplicate titles were removed, and posts on the boundaries were moved to the label the rule names. Every note says `pre-labeled`. Before the next unit I need to label 20 of these myself, with no tool, and mark those notes `cold`. The agreement report depends on that.

**Counts per label:**

| Label | Count | Share |
|---|---|---|
| analysis | 77 | 37.2% |
| hot_take | 72 | 34.8% |
| reaction | 58 | 28.0% |
| **Total** | **207** | 100% |

No label is above 45%, which is also acceptance criterion 3. `reaction` is the thin class. About 15% of 58 is 8 or 9 test posts, right on the line where a score gets jumpy.

**Three hard cases**

**1.**
> *The post:* "Pacers will be #1 overall seed in NBA." The body says a lot of people called the 2025 Finals run a fluke, including a Game 7 they led at half, and that the 2025 team already answered the same charge about the 2024 East finals run.
>
> *Could have been:* `hot_take` (the title is a prediction shouted at the sub) or `analysis`.
>
> *I chose, because:* `analysis`. The post names checkable series facts and uses them to argue the run was not a fluke. Heat does not move it.

**2.**
> *The post:* "Ben Simmons tonight: 15 points 8 rebounds 7 assists 5/10 FG." The body repeats the line and says he looks like he's returning to form. "He's back."
>
> *Could have been:* `analysis` (it names a stat line) or `reaction`.
>
> *I chose, because:* `reaction`. The number is the moment. There is no argument about his season beyond the feeling that he's back.

**3.**
> *The post:* "I really respect KG giving props to generations after." The body is about loving his trash talk and the way he praises younger players.
>
> *Could have been:* `hot_take` (it sounds like a strong opinion) or `reaction`.
>
> *I chose, because:* `reaction`. It is appreciation. It never claims someone is better, worse, overrated, or due for anything.

A fourth case changed a label during the review. "SGA is BETTER THAN KOBE" reads like a hot take from the title. The body cites a checkable comparison, two MVPs to one, so the rule puts it in `analysis`.

---

## The Training Run

**Base model:** `distilbert-base-uncased`

**Settings:** 3 epochs, learning rate 2e-5, batch size 16, 128 tokens, seed 42. These are the notebook defaults.

**Anything I changed from the defaults, and why:** Nothing. The labels are the part that was in doubt, not the hyperparameters.

**Device:** cpu. `results.json` records `"device": "cpu"`, `"device_name": "CPU"`, and `"torch_version": "2.14.1+cpu"`. Two runs of this same file on different hardware can land on different numbers, because the arithmetic is not in the same order. That belongs next to the three seeds in the next unit.

**Split sizes:** Seed 42, 70/15/15. Train 144, validation 31, test 32. In the test split: analysis 12, hot_take 11, reaction 9. Every label has at least 8 test posts. `reaction` is the smallest, so its F1 will move the most between seeds.

---

## How I Used AI

**Moment 1**

- *What I asked for:* The strongest case for `analysis` and the strongest case for `hot_take` on posts that sit between them, without rewriting the definitions. That is the check the assignment describes for a boundary.
- *What came back:* Both cases were easy to make whenever a post was angry and also contained a number. "SGA is BETTER THAN KOBE" is the clearest one: the title is a shout, and the body counts MVPs.
- *What I changed:* The decision rule, before the 200-row file was treated as final. A checkable fact used in the argument wins, even when the title is heated. That post is `analysis`.

**Moment 2**

- *What I asked for:* Whether each acceptance criterion could be checked by someone who could not ask what I meant, and one reason each target might be too easy and one reason it might be too hard. I did not ask for different numbers.
- *What came back:* Criterion 3 (the 45% cap) can be satisfied by how the file is built, so it is the easy one. Criterion 2 (every label at 0.50 F1) is the hard one, because `reaction` will have about 8 test posts and unit 6 requires the target on all three seeds.
- *What I changed:* Nothing about the numbers. 0.60 accuracy, 0.50 F1, 45% balance, 0.70 agreement, and 0.70 accuracy on the confident slice all stayed. They were written before the training run.

**Pre-labelling disclosure:** Every row in `labels.csv` was pre-labeled. A script applied the definitions and the decision rule, then every kept title was read and corrected as described in The Dataset. Notes say `pre-labeled`. None say `cold`. The five criteria were drafted with that same assistant, before any training metric existed, and the targets above are the ones to grade against. They should be re-read before they are defended out loud.

<!-- ═══════════════════════ UNIT 6 — THE TEST ═══════════════════════

     Don't fill these in during unit 5.
     ═══════════════════════════════════════════════════════════════════ -->

---

## Baseline vs. Trained

<!-- Both models on the same posts. `python baseline.py --trained results.json`
     prints this table for you. -->

| Measure | Baseline | Trained | Difference |
|---|---|---|---|
| Overall accuracy |  |  |  |
| Macro F1 |  |  |  |
| F1 — `label_one` |  |  |  |
| F1 — `label_two` |  |  |  |

**What I predicted before I looked:**
<!-- Milestone 1 asks you to write this BEFORE seeing the trained numbers. A
     prediction made afterwards isn't one. -->

**What the gap actually means:**
<!-- If the baseline matched your trained model, your fine-tuning added
     nothing — and that is a real finding, not a failure. Say it plainly. -->



---

## Run Log — Before

<!-- Five criteria across three seeds. The notebook's section 6 prints the
     spread table; the Target and Verdict columns are yours. -->

| Criterion | Target | Seed 42 | Seed 7 | Seed 2024 | Verdict |
|---|---|---|---|---|---|
| 1.  |  |  |  |  |  |
| 2.  |  |  |  |  |  |
| 3.  |  |  |  |  |  |
| 4.  |  |  |  |  |  |
| 5.  |  |  |  |  |  |

### Confusion matrix

<!-- ⚠️ TYPED AS A MARKDOWN TABLE. The notebook prints one ready to paste.
     An image of a matrix earns nothing. -->

| true \ predicted |  |  |  |
|---|---|---|---|
| **** |  |  |  |
| **** |  |  |  |
| **** |  |  |  |

**My biggest off-diagonal number, and what it means:**
<!-- Not "the model made mistakes" — WHICH boundary it didn't learn, and which
     direction. "7 real analysis posts were called hot_take and only 3 went the
     other way" is a direction, not just an error rate. -->



---

## Verdicts and Diagnoses

<!-- MET or MISSED against LAST UNIT's target. The target has to hold across
     all three seeds, not turn up sometimes. -->

| # | Criterion | Target | Verdict | How I decided |
|---|---|---|---|---|
| 1 |  |  |  |  |
| 2 |  |  |  |  |
| 3 |  |  |  |  |
| 4 |  |  |  |  |
| 5 |  |  |  |  |

**Diagnoses**

<!-- For each miss: the cause, and how you know. The four common causes are:
     too few examples for a label, a boundary you applied inconsistently, a
     genuinely hard label pair, and a task the model can't reach from this
     much data.

     ⚠️ Use your agreement report as evidence. It is the only instrument you
     have that can tell a LABELLING problem from a MODEL problem, and this
     section is graded on whether you used it that way. -->



---

## Agreement Report

<!-- Your rate against the staff set, and every disagreement adjudicated.

     Remember you labelled these 30 under the STAFF taxonomy in
     data/staff_taxonomy.md, not your own — so every argument below is made
     from those definitions and those decision rules. -->

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
