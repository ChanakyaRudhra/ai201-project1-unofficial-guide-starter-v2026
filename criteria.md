# Acceptance criteria - The Unofficial Guide

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

My 5 test questions cover distinct, well-documented topics (housing, courses, dining, admin deadlines), so I expect most to retrieve cleanly - my test runs showed distances between 0.179 and 0.382, all correct. I'm leaving room for 1 miss since some corpus topics (like a single dining hall's specific policy) are covered by only one or two documents, which gives retrieval less to work with if a question is phrased differently than the source text.

---

## 2. Every answer names a source

Every answer the system produces names at least one source document.

**Why this target:**

The starter's GROUNDING_INSTRUCTION requires every answer to name its source document, and this held in all 5 of my test runs. I expect this to be near-guaranteed rather than just likely, since it's enforced by the prompt template itself rather than depending on retrieval quality - the only way it fails is if the model ignores an explicit instruction, which is rare.

---

## 3. The relevance gate stops out-of-corpus questions

When I ask a question my documents clearly don't cover, the relevance gate
stops it and the system returns "I don't have enough information about that" -
in at least 4 of 5 tries.

**Why this target:**

I ran 5 in-scope test questions and 5 out-of-scope questions to find the
cutoff. In-scope best distances ranged from 0.179 to 0.382; out-of-scope
best distances ranged from 0.825 to 0.934 — a clean gap of about 0.44 with
no overlap. The starter's default cutoff of 0.6 sits comfortably in that
gap, so I kept it rather than changing it. At this cutoff, all 5 in-scope
questions passed the gate and all 5 out-of-scope questions were correctly
refused (5 of 5 both ways), so I'm confident 4 of 5 is an achievable
standard going forward, even on questions I haven't tested yet.

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

The corpus splits many topics across multiple related files. For example, housing topics have a base file and "_laundry" and "_noise" variants (housing_morrow_house_laundry.txt, housing_morrow_house_noise.txt), and course topics are split into "_workload" and "_exams" files. When I asked "Is laundry a problem in Morrow House?" it cited a correct file specifically (housing_morrow_house_laundry.txt), rather than the broader base file. All 5 picked a correct variant.

---
