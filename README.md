i# The Unofficial Guide

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

## Run Log — Before

Produced by `run_eval.py::main` (retrieval: `store.py::search`, chunks from `chunker.py::split_documents`). Corpus: campus_life, top-k 5, cutoff 0.6. 3 runs, caching off. Full file: `results/run_2026-09-28_0307_before.md`.

| Criterion | Target | Run 1 | Run 2 | Run 3 | Verdict |
|---|---|---|---|---|---|
| 1. Retrieved chunk contains the answer | 4 of 5 | 5/5 | 5/5 | 5/5 | MET |
| 2. Every answer names a source | 5 of 5 | 5/5 | 5/5 | 5/5 | MET |
| 3. Gate stops out-of-corpus questions | 4 of 5 | 5/5 | 5/5 | 5/5 | MET |
| 4. Chunks read as complete thoughts | 5 of 5 | 5/5 | 5/5 | 5/5 | MET |
| 5. Cites single most specific source file | 4 of 5 | 4/5 | 4/5 | 4/5 | MET |

**Criterion 1 real output** — `Is the housing lottery random?` — run 1 (from `results/run_2026-09-28_0307_before.md`):

```
The housing lottery is not entirely random; rising sophomores have a number drawn at random, but juniors and seniors are ordered by accumulated credit hours first, with random tie-breaks.

Source: admin_housing_lottery.txt
```


**Criterion 2 real output** — every run across all 5 questions included an explicit `Source:` line; example above shows one.

**Criterion 3 real output** — from `run_eval.py::check_out_of_scope`, cutoff 0.6:

```
refused  (best distance 0.825)  What is the capital of Mongolia?
refused  (best distance 0.934)  How do I change the oil in a diesel engine?
refused  (best distance 0.886)  Who won the 1994 World Cup?
refused  (best distance 0.844)  What is the recommended dosage of ibuprofen for a headache?
refused  (best distance 0.896)  How do I write a for loop in Rust?
```


**Criterion 4 real output** — `python app.py --corpus campus_life chunks -n 5`, produced by `chunker.py::split_documents`:

```
On the add/drop deadline

You can add a course through the end of the second week. Dropping is a longer window - through the end of week six - but a drop after week two shows as a W on your transcript. Nothing anywhere on the registrar's site says this plainly, and students find out from each other.
```


**Criterion 5 real output** — `Is laundry a problem in Morrow House?` — run 1 (the pattern, not the exception — all 3 runs cite both files):

```
Yes, dryers back up on Sunday evenings because there are eight washers and six dryers, which is the wrong ratio. Additionally, you will have to wait if you do laundry on Sunday after 6pm.

Sources: `housing_morrow_house_laundry.txt` and `housing_morrow_house.txt`
```

## Verdicts

| # | Criterion | Verdict | How I decided |
|---|---|---|---|
| 1 | Retrieved chunk contains the answer | MET | All 3 runs hit 5/5, exceeding the 4 of 5 target with no borderline cases - every answer's fact matched the expects phrase in questions.py. |
| 2 | Every answer names a source | MET | All 3 runs hit 5/5, matching the target exactly - the Source: line appeared in every single answer with no exceptions. |
| 3 | Gate stops out-of-corpus questions | MET | Measured once since the gate is deterministic - 5 of 5 refused, exceeding the 4 of 5 target. |
| 4 | Chunks read as complete thoughts | MET | All 5 sampled chunks read as complete thoughts in this unit's re-run, matching the previous unit's result exactly since the chunker is deterministic. |
| 5 | Cites single most specific source file | MET | Hit the 4 of 5 target in all 3 runs, but the same question (Morrow House laundry) failed identically every time - the model consistently cites both the specific file and its broader sibling rather than just the specific one. Flagging this as a stable pattern worth diagnosing even though it's a technical MET. |

## Diagnoses

**Criterion 5 (MET, but flagged)** — hit 4 of 5 in every run, on the same failing question every time: "Is laundry a problem in Morrow House?"

**Stage: Generation.** Using `--show-prompt`, both `housing_morrow_house_laundry.txt` and `housing_morrow_house.txt` were retrieved, and both are legitimately relevant — the base file's chunk does mention laundry ("Laundry costs $1.50 wash, $1.25 dry, coin or card"), so retrieval is not at fault. But only `housing_morrow_house_laundry.txt` contains the actual answer (the 8-washer/6-dryer ratio and the Sunday backup). The grounding instruction says "Name the document your answer came from" without telling the model to cite only the source(s) that actually supplied the cited fact, so when two overlapping-but-unequal chunks are present, the model names both rather than isolating the specific one.

**This is a pattern, not a one-off.** Every housing hall's base file duplicates a line from its own `_laundry` file (confirmed in `housing_old_brewhouse_laundry.txt`, `housing_innisfree_hall_laundry.txt`, and `housing_aldridge_hall_laundry.txt` too), so any hall-laundry question is likely to trigger the same over-citation, not just this one.

No criterion was outright missed. But criterion 5 sitting at exactly 4/5 in all three runs, on an identifiable and repeatable mechanism, suggests the target wasn't set too loose — the system has a real, specific weakness that the criterion correctly caught.


## The Improvement

**What I changed:** Tightened the grounding instruction's citation rule in `generate.py`. Old: "Name the document your answer came from, using the filename given in each excerpt." New: "Name only the single document that directly supports the specific fact you state. If two documents mention the same topic but only one contains the detail you are citing, name only that one."

**Why I picked it:** My Milestone 3 diagnosis found the Morrow House laundry over-citation was a generation-stage issue, not retrieval — both files were correctly retrieved, but the old instruction never told the model to prefer the one that actually supported the specific fact. This directly targets that mechanism.

### Run Log — After

Produced by `run_eval.py::main`. Same corpus, top-k, and cutoff as before. Full file: `results/run_2026-09-28_0511_after.md`.

| Criterion | Target | Run 1 | Run 2 | Run 3 | Verdict |
|---|---|---|---|---|---|
| 1. Retrieved chunk contains the answer | 4 of 5 | 5/5 | 5/5 | 5/5 | MET |
| 2. Every answer names a source | 5 of 5 | 5/5 | 5/5 | 5/5 | MET |
| 3. Gate stops out-of-corpus questions | 4 of 5 | 5/5 | 5/5 | 5/5 | MET |
| 4. Chunks read as complete thoughts | 5 of 5 | 5/5 | 5/5 | 5/5 | MET |
| 5. Cites single most specific source file | 4 of 5 | 4/5 | 5/5 | 5/5 | MET |

**Criterion 5 real output (after)** — `Is laundry a problem in Morrow House?` — run 2, where the fix worked:

```
Yes, laundry can be a problem because there are eight washers and six dryers, which is the wrong ratio and causes the dryers to back up on Sunday evenings.

Source: housing_morrow_house_laundry.txt
```

Compare to run 1 (after), where the old behavior still appeared:

```
Laundry can be an issue on Sunday evenings because the building has eight washers and six dryers, which is the wrong ratio and causes the dryers to back up.

Sources: `housing_morrow_house_laundry.txt` and `housing_morrow_house.txt`
```

**Did it help?** 
Partially. Before the fix, criterion 5 landed at exactly 4/5 in all three runs — the same question failed the same way every time. After the fix, it improved to 4/5, 5/5, 5/5 — the targeted mechanism (citing an overlapping broader file alongside the specific one) was resolved in 2 of 3 runs, but resurfaced once, since generation still has run-to-run variance the prompt change doesn't fully control. Criteria 1-4 held at their prior levels with no regression, so the change was net-positive but not a complete fix.


## What's Still Broken

Criterion 5 still slipped once (run 1 of 3) after the fix — the model reverted to citing both `housing_morrow_house_laundry.txt` and `housing_morrow_house.txt` even with the tightened instruction. The prompt change reduced the frequency of the failure but didn't eliminate it, because generation has inherent run-to-run variance that a static instruction can only partially constrain. A more reliable fix would likely require changing what gets retrieved in the first place — e.g. de-duplicating near-identical sentences across a hall's base file and its `_laundry` file at chunking or indexing time, so the overlapping chunk never reaches the model as a second option to cite. I stopped here because the assignment scopes this unit to one measured change, and the prompt-level fix was the more targeted, lower-risk option pointed at directly by my diagnosis — a chunking-level fix would be a second, larger change I haven't measured in isolation.

## What I'd Do Differently

Looking back, criterion 5 is the one I'd rewrite. "Cites the single most specific source file" turned out to depend partly on corpus structure I didn't fully anticipate — several housing halls duplicate a sentence across their base file and their topic-specific file (laundry, noise), which makes "most specific" ambiguous exactly when both files genuinely contain the cited fact. I'd tighten the criterion itself to something like "for questions about a hall's laundry or noise, cites only the dedicated _laundry or _noise file, not the base hall file" — naming the actual structural pattern in my corpus rather than a general rule that turned out to have a predictable exception built into the data itself.
