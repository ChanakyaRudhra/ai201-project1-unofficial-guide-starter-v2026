# The Unofficial Guide

<!-- Replace this line with your name and which corpus you picked. -->

> **This file is your submission.** Fill it in as you go — most sections get
> written during the milestone that produces them, not at the end.
>
> How the starter works, and every command you'll need, is in `RUNNING.md`.
> Leave that file alone.
>
> **Paste everything as text.** No screenshots, no video. A typed table gets
> full credit; a picture of the same table gets none.
>
> Delete these instruction blocks as you replace them. The `<!-- -->` comments
> are notes to you and don't show up when the page renders — you can leave them
> or remove them.

---

# Unit 1

## What This Does

This system answers questions about campus life at a university, using student-written posts, admin notices, and course reviews as its source material. It covers topics like housing (room quality, laundry, noise), course workload and exam formats, dining hall wait times, and administrative deadlines (registration, withdrawal, permits). Questions are answered only from the retrieved documents, with sources always cited, and questions outside the corpus's scope are refused rather than guessed at.

## Chunking Strategy

**Chunk size:**
paragraph-aware, with a 150-character floor and 600-character
ceiling (no fixed target size). 

**Overlap:**
none — chunks split on paragraph
breaks rather than character windows, so there's no boundary to overlap.

I chose this because campus_life documents are short, structured posts
(average 317 characters, longest 549) built from a few distinct paragraphs
— a heading, then one to three body paragraphs. A fixed character-count
chunker risks cutting a paragraph mid-sentence; splitting on every paragraph
break risks tiny fragments (a lone heading with no content). My chunker
merges adjacent paragraphs until a chunk reaches at least 150 characters,
and caps growth at 600 so unrelated paragraphs don't get merged together.

Running it on my corpus produced 88 chunks — identical in count and stats
(317 average, 178 shortest, 549 longest) to the starter's fixed-window
chunker. This confirmed rather than changed my chunking: for this corpus,
one post already is the right unit — no document in campus_life is long
enough or paragraph-heavy enough to need splitting further.

---

## Sample Chunks

**Chunk 1** — source: `admin_add_drop_deadline.txt#0` — produced by `chunker.py::split_documents`

On the add/drop deadline

You can add a course through the end of the second week. Dropping is a longer window — through the end of week six — but a drop after week two shows as a W on your transcript. Nothing anywhere on the registrar's site says this plainly, and students find out from each other.

**Chunk 2** — source: `course_biol_160.txt#0` — produced by `chunker.py::split_documents`

BIOL 160 Cell Biology

I lived here my sophomore year. The format is lecture three times a week with a weekly lab. Assessment: four unit tests and a cumulative final. Not curved.

Expect 9 to 11 hours a week; it's the heaviest first-year course by reputation.

The one piece of advice: the unit tests come fast, roughly every three weeks; falling behind once is very hard to recover from.

**Chunk 3** — source: `course_hist_118_workload.txt#0` — produced by `chunker.py::split_documents`

Workload for HIST 118 Modern World History

People keep asking so: a lot of reading, about 120 pages a week, but no problem sets. That's real time, not optimistic time.

It's front-loaded — the first month is heavier than the rest, partly because you're learning the format.

**Chunk 4** — source: `dining_pellew_dining_hall_followup.txt#0` — produced by `chunker.py::split_documents`

Re: Pellew Dining Hall

Adding to what people have said about Pellew Dining Hall. The wait figure of 12 to 18 minutes at peak matches what I've seen. If you're trying to eat between classes, go before 11:45 and it's a different building entirely.

Also worth saying: the furthest hall from anywhere, next to the athletics centre. Nobody tells you this at orientation.

**Chunk 5** — source: `housing_innisfree_hall.txt#0` — produced by `chunker.py::split_documents`

Innisfree Hall — what it's actually like

Transferred in last year, so take this with a grain of salt. Built in 1991, renovated in 2022. Rooms are doubles arranged as pairs sharing one bathroom between two rooms.

The good: the shared-bathroom-between-two-rooms arrangement is the best compromise on campus.

The bad: no air conditioning, which matters for the first three weeks of September.

Laundry costs $1.75 to wash, $1.75 to dry, app-based. On noise: moderate; the building is L-shaped, and the short wing is much quieter.


## Sample Answer

**Question:** Is the housing lottery random?

**Answer:**

```
No, the housing lottery is not entirely random. Rising sophomores have their numbers drawn at random, but juniors and seniors are ordered by accumulated credit hours first, with random selection used only as a tie-breaker.

Source: admin_housing_lottery.txt
```

**My relevance cutoff:** 0.6 (the starter's default — I tested it against my own data rather than changing it blindly; see the table below)

| Question | In corpus? | Best distance |
|---|---|---|
| Is the housing lottery random? | Yes | 0.284 |
| How many hours a week does CS 210 take outside class? | Yes | 0.300 |
| What's the deadline to withdraw from a course? | Yes | 0.382 |
| Is laundry a problem in Morrow House? | Yes | 0.259 |
| How long is the wait at Verrill Street Grill on Friday? | Yes | 0.179 |
| What is the capital of Mongolia? | No | 0.825 |
| How do I change the oil in a diesel engine? | No | 0.934 |
| Who won the 1994 World Cup? | No | 0.886 |
| What is the recommended dosage of ibuprofen for a headache? | No | 0.844 |
| How do I write a for loop in Rust? | No | 0.896 |

There's a clean gap of about 0.44 between the in-corpus and out-of-corpus groups, with no overlap. The starter's default cutoff of 0.6 sits comfortably in that gap, so I kept it rather than changing it.

## How I Used AI



**1.** Chunker moment: I used AI to write a custom chunker for my corpus. From that I got the pragraph aware strategy with a size floor and ceiling. I ran it and compared the output against the starter's chunker both produced identical chunks counts and stats. Which proves my corpus didn't need a differentchunking approach after all. 

**2.** For setup errors: I asked Claude to help fix a Python environment error (chroma-hnswlib failing to compile on Windows due to missing SDK headers). After several failed fixes with VS Build Tools and manual environment variables, I changed approach and moved to Google Colab instead — where Claude then helped me work around Colab's broken venv (no ensurepip) by manually bootstrapping pip.



---

# Unit 2

<!-- These sections get ADDED to what's already above. Don't delete or rewrite
     unit 1 — the point is that someone can see what you said before you knew
     how it went. -->

## Run Log — Before

<!-- Your five criteria, three runs each. `python run_eval.py --label before`
     runs the questions, puts the OUT_OF_SCOPE ones through the gate, and
     writes it all into results/ for you. Targets come from criteria.md; the
     verdict column is your call.

     Criterion 3 is measured in one deterministic pass rather than three, so
     the same number goes in all three run columns. That's correct, not lazy.

     Milestone 1. -->

| Criterion | Target | Run 1 | Run 2 | Run 3 | Verdict |
|---|---|---|---|---|---|
| 1. Retrieved chunk contains the answer | 4 of 5 |  |  |  |  |
| 2. Every answer names a source | 5 of 5 |  |  |  |  |
| 3. Gate stops out-of-corpus questions | 4 of 5 |  |  |  |  |
| 4. | | | | | |
| 5. | | | | | |

<!-- Underneath, paste the REAL output for each criterion from one of your
     runs — the actual text your system produced, not a description of it.
     Name the file and function that produced it. -->

## Verdicts

<!-- MET or MISSED for each of the five, against the target you wrote last
     unit — not a new one. Plus a sentence on how you decided. That sentence
     matters most where it was close.

     If your target said 4 of 5 and your runs came out 4, 3, 4, that's a MISS.
     The target has to hold, not show up occasionally.

     Milestone 2. -->

| # | Criterion | Verdict | How I decided |
|---|---|---|---|
| 1 |  |  |  |
| 2 |  |  |  |
| 3 |  |  |  |
| 4 |  |  |  |
| 5 |  |  |  |

## Diagnoses

<!-- For each miss: which stage caused it, and how. The stage alone isn't
     enough — you need the mechanism.

     Not a diagnosis: "Question 3 didn't work."
     A diagnosis:     "Question 3 asks about laundry costs. The answer is in
                       one sentence that got split across two chunks, so
                       neither chunk on its own contains it."

     The five stages: loading → chunking → embedding → retrieval → generation.

     Look for a pattern. If three misses all ask about numbers, that's one
     problem, not three.

     Missed nothing? Say so, then say honestly whether your targets were set
     low, and which one you'd tighten and to what.

     Milestone 3. -->

## The Improvement

**What I changed:**

**Why I picked it:**

<!-- Connect it to a specific diagnosis above in one sentence. If you can't,
     you picked a fix because it sounded impressive. -->

### Run Log — After

<!-- Same format, same five criteria, three runs each.
     `python run_eval.py --label after` -->

| Criterion | Target | Run 1 | Run 2 | Run 3 | Verdict |
|---|---|---|---|---|---|
| 1. Retrieved chunk contains the answer | 4 of 5 |  |  |  |  |
| 2. Every answer names a source | 5 of 5 |  |  |  |  |
| 3. Gate stops out-of-corpus questions | 4 of 5 |  |  |  |  |
| 4. | | | | | |
| 5. | | | | | |

**Did it help?**

<!-- Say plainly whether it did, and how you know. If it made things worse,
     say that — a change that backfired, honestly reported, earns full credit
     and is more interesting than one that worked. What matters is that you can
     tell.

     Milestone 4. -->

## What's Still Broken

<!-- For each criterion still missed after your fix: what you'd do about it,
     and why you stopped where you did.

     "I ran out of time" is fine if it's true. Pretending nothing is left is
     not.

     Milestone 5. -->

## What I'd Do Differently

<!-- Knowing what you know now — which of your five criteria would you write
     differently, and why?

     Milestone 5. -->
