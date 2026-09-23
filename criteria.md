# Acceptance criteria — The Unofficial Guide

Five criteria that say what "working" means for this system, written in unit 1
**before** any results existed.

An acceptance criterion names a target: a number, a count, a rate, or something
a person could plainly observe. *"Retrieval works"* is an opinion. *"For at
least 4 of my 5 test questions, the top results include a chunk containing the
answer"* is a criterion.

Under each one, write a sentence or two on **why that target** and not a
stricter or looser one. A reason that says something about your corpus or your
pipeline earns credit; *"80% seemed reasonable"* does not.

> Missing your own targets next unit costs you nothing. Setting a target so
> easy you can't miss it does.

---

## 1. Retrieved chunks contain the answer

For at least 4 of my 5 test questions, the retrieved chunks include one that
contains the answer.

**Why this target:**

My 5 test questions cover distinct, well-documented topics (housing, courses, dining, admin deadlines), so I expect most to retrieve cleanly — my test runs showed distances between 0.179 and 0.382, all correct. I'm leaving room for 1 miss since some corpus topics (like a single dining hall's specific policy) are covered by only one or two documents, which gives retrieval less to work with if a question is phrased differently than the source text.

---

## 2. Every answer names a source

Every answer the system produces names at least one source document.

**Why this target:**

The starter's GROUNDING_INSTRUCTION requires every answer to name its source document, and this held in all 5 of my test runs. I expect this to be near-guaranteed rather than just likely, since it's enforced by the prompt template itself rather than depending on retrieval quality — the only way it fails is if the model ignores an explicit instruction, which is rare.

---

## 3. The relevance gate stops out-of-corpus questions

When I ask a question my documents clearly don't cover, the relevance gate
stops it and the system returns "I don't have enough information about that" —
in at least 4 of 5 tries.

<!-- The five questions are the ones in `OUT_OF_SCOPE` at the bottom of
     `questions.py`, and `run_eval.py` puts them through the gate and writes
     what happened into your run log. Swap them for your own if you'd rather —
     just keep five of them, or the "4 of 5" above has nothing to be 4 of. -->

**Why this target:**
<!-- What did your distances look like when you set the cutoff in Milestone 4?
     Was there a clean gap, or did the two groups overlap? -->

---

## 4. Something about your chunks

After running "python app.py --corpus campus_life chunks -n 5"

At least 5 sampled chunks read as complete thoughts, self-contained thoughts with no cut-off sentences at either edge.

**Why this target:**

After executing the Milestone 1 index, the data that has been observed from running 5 sample chunks is that all these chunks hold up as complete, self-contained thoughts with no cut-off sentences and no fragments, and each one is answerable on its own showed 88 chunks total, with an average of 317 characters; the shortest is 178; the longest is 549.

---

## 5. Your choice

At least 4 of 5 test questions cite the single most specific source file for their topic, not a broader file from the same topic cluster.

**Why this target:**

The corpus splits many topics across multiple related files. For example,
housing topics have a base file and "_laundry" and "_noise" variants
(housing_morrow_house_laundry.txt, housing_morrow_house_noise.txt), and
course topics are split into "_workload" and "_exams" files. When I asked
"Is laundry a problem in Morrow House?" it cited a correct file specifically
(housing_morrow_house_laundry.txt), rather than the broader base file. All 5
picked a correct variant.

---

<!-- ─────────────────────────────────────────────────────────────────────────
     UNIT 2 — read this before you change anything above.

     If a criterion turns out to be BROKEN rather than merely unmet, you can
     revise it, and that earns credit. But never delete or edit the original
     line. Add the revision underneath it, like this:

         ## 1. Retrieved chunks contain the answer

         For at least 4 of my 5 test questions, the retrieved chunks include
         one that contains the answer.

         **Why this target:** ...

         > **Revised in unit 2:** For at least 4 of 5 questions, the top three
         > results contain the answer.
         >
         > **Why revised:** I couldn't judge "the chunks include one that
         > contains the answer" the same way twice — I scored two questions
         > differently on Monday than on Wednesday. The new version is
         > something I can actually check.

     That's a revision because the criterion couldn't be MEASURED.

     Lowering a target because you missed it is not a revision, and it costs
     you the point:

         ✗ "I said 4 of 5 but got 2 of 5, so 2 of 5 is more realistic."

     A number you missed stays where it is, gets diagnosed, and gets a fix
     attempted. That's where the points are.

     The whole reason the originals stay visible is so someone can see what you
     said before you knew the answer.
     ───────────────────────────────────────────────────────────────────────── -->
