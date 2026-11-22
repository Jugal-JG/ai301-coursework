# Contribution: Compute rerank confidence after test-code demotion

Path: `beat-2-contribution/contribution-story.md`

This file is Assignment 5, and it is graded at the path above; a copy kept anywhere else in
the repository is not read. **Keep every section header below under its exact name** — the
format criteria grade exactly that. Your course repo link is the whole submission: no tool
files and no evidence uploads are part of Assignment 5.

**Contribution Number:** [1]
**Student:** Jugal Bhaskar Naga Ganesh Boddu
**GitHub Username:** Jugal-JG
**Issue:** https://github.com/aakashsiwach/codeatlas-rag/issues/25
**Pull Request:** [GitHub PR link, once opened]

One repository, built across units 5 to 8, telling the whole story of your real
contribution. Fill it as you work, not at the end — the sections below ask for evidence that
is far easier to write down as it happens than to reconstruct in the last weekend.

Contribution Number stays `[1]` unless you make more than one contribution this term; it
just numbers them.

---

## Toolkit

Every story section below, from Why I Chose This Issue through Pull
Request, ends with a Toolkit block: which tool did that phase's work
and how you used it, what it produced, what difficulties or
shortcomings it presented, and what you changed or would change
because of them. Together the blocks are the thread the rubric's
Toolkit points read, and they are the raw material for your unit-9
portfolio, so keep them current as you work rather than
reconstructing them later.

A Toolkit block looks like this (copy it into each story section and
fill it there):

```text
**Toolkit**

- Tool: [which tool did this phase's work, and how you used it]
- What it produced: [described in a sentence or a short excerpt]
- Difficulties: [what fought you or fell short, or "none met"]
- What changed: [the patch, revision, or workaround, what you would
  update, or "nothing"]
```

This section also keeps one short dated entry per week of tool work,
starting below: what you built or changed that week, what broke, and
what you changed because of it. The blocks tell the story where it
happened; these entries are the toolkit's own timeline.

All four entries are graded. Keep every heading below, and fill each
one in the week it belongs to.

**Unit 5: Grew my issue-select skill into issue-scout with my own search strategy**

I installed the Unit 5 overlay over my issue-select skill (renamed issue-scout, my rubric kept) and wrote a search strategy: three `gh search issues` queries for Python AI/ML eval, RAG and scoring issues, each traced to a sentence of my fit profile, plus ranking filters (no assignee, updated within 60 days, at most 10 comments, fewest comments first). What broke first was the setup: `gh` wasn't installed on my Windows machine, so I installed it with winget and logged in. Then two of my three queries returned zero results when I ran them by hand, because GitHub requires every word to match and I had used multi-word terms; I changed them to single keywords ("retrieval", "scoring"), which now return 100 results each. My first scan found 233 issues, graded the top five, and accepted four, but it accepted goldener #437 even though a merged PR had already closed it, because my rubric only failed on open linked PRs. I patched the "Not already claimed" check to also fail on a merged PR that closes a still-open issue; I haven't re-run the scan to confirm the patch. Every beginner issue I vetted on the course site also had open PRs, so I tried pytorch/ao #3863, but the scout graded it reject (tracker-style scope, open PRs) and I moved to a different issue.

**Unit 6: Voice guard on my outgoing comments, a repo-memory file, and a patched scope check**

I merged my three voice-guide copies into one guide, added four upstream rules (claims, what I saw on the thread, questions, pings), and installed a hook that checks every `gh issue comment` body against it; it passed my claim on codeatlas-rag #25 and logged a `[hook] pass` line. Three things broke on Windows: symlinks need admin rights (so each skill now holds a one-line pointer to the guide), `python3` is a Store stub (so the hook calls `python`), and the guard's Python could not launch `claude.cmd` (so I patched the installed copy to find it). I also wrote a 29-line `CLAUDE.local.md` for the repo from one scoped read of the retrieval code, excluded from git through `.git/info/exclude`, and replaced my scope check with `scope-feasible`, which changed why pytorch/ao #3863 was rejected but not that it was.

**Unit 7: [one line on the tool work]**

[2-4 sentences: the ship-gate and your gate manifest. What it produced
on your real branch, what fought you, what changed because of it.]

**Unit 8: [one line on the tool work]**

[2-4 sentences: the PR-monitor and your playbook. What it read, what it
drafted, what you did with the draft.]

---

## Why I Chose This Issue

I searched with three `gh search issues` queries built from my fit profile (Python, AI/ML evaluation, RAG and retrieval, scoring logic). The first scan found 233 unique issues, and the scout graded the top five. Its top-ranked issue, aakashsiwach/codeatlas-rag #25 ("Compute rerank confidence after test-code demotion"), was accepted. I then spent a while on the course Issue Selection site, where the AI/ML-looking issues I checked all already had open PRs or claims, and I briefly picked pytorch/ao #3863. My scout rejected it as a tracker-style issue with open PRs (and someone had already said they were working on it), so I dropped it and unclaimed it on the site.

I chose codeatlas-rag #25 because it fits what I have already built: it is a bounded change to RAG retrieval and scoring (an opt-in `confidence_after_demotion` option in `Retriever.retrieve()`), the tests run offline with no GPU or API keys, and it has a clear dev-split measurement step like the evaluation harness I wrote before. My re-grade with the patched rubric accepted it with a difficulty note of moderate: the demotion logic is mine to find, and I have to pick a threshold on the dev split only while keeping the frozen 0.868 threshold. I hope to learn how a real RAG project enforces eval discipline (dev-only tuning, frozen thresholds) and how to contribute to a one-maintainer repo. The scout held up on the hard calls, but it missed things a real search needed: it passed an issue a merged PR had already closed, and my old scope check rejected most real issues, so I patched both. The risks I accepted are that the repo has 0 stars and a single owner, so responsiveness is unproven. I did not run the scan again after the patches, so I only know the patched rubric from the two issues I re-graded.

Claim comment: https://github.com/aakashsiwach/codeatlas-rag/issues/25#issuecomment-7074099431

**Toolkit**

- Tool: issue-scout (my search strategy, my rubric), run as a scan and then as direct grades of my picks.
- What it produced: a scan of 233 issues with five graded (four accepted, one rejected), ranking codeatlas-rag #25 first; the re-grade accepted #25 (moderate) and rejected pytorch/ao #3863.
- Difficulties: two of my three queries returned zero results until I used single keywords; it accepted goldener #437 although a merged PR already closed it; my unit-1 scope check rejected most real issues; the search results carry no star count, so solo 0-star repos still rank high.
- What changed: I fixed the queries, added a merged-PR clause to "Not already claimed", and replaced my scope check with `scope-feasible` plus a difficulty note (logged in scan-notes.md).

---

## Understanding the Issue

**Problem Description**

[In your own words, what is broken or missing?]

**Expected Behavior**

[What should happen?]

**Current Behavior**

[What actually happens?]

**Affected Components**

[Which parts of the codebase are involved?]

**Toolkit**

- Tool:
- What it produced:
- Difficulties:
- What changed:

---

## Reproduction Process

**Environment Setup**

- OS: Windows 11 Home (10.0.26300). Python 3.14.4 (the repo requires 3.11+ and CI runs 3.11 and 3.12, so this is outside what CI tests). Node v25.9.0 (not used: the UI is not involved).
- Project: my fork Jugal-JG/codeatlas-rag, cloned at upstream `main`, commit `bbd753e`.
- Setup: `python -m venv .venv`, then `pip install -c requirements-lock.txt -e ".[dev]"`. I skipped the `local`, `openai` and `bedrock` extras because nothing here needs a model or API key.
- `codeatlas doctor` on that setup: `ok python 3.14.4`, `warn index` (no index yet), and `fail` for the embedding model, the reranker (needs `torch`) and the LLM config. Those failures are expected with only `.[dev]`; the repro uses the hashing embedder and a fake reranker.
- Baseline on that commit: `ruff check .` passed; `pytest -q` gave 151 passed, 7 skipped in 59 s.
- Challenges: the first repro run failed because Qdrant needs UUID chunk ids (my test ids were plain strings); I switched to UUIDs. Otherwise none came up.

**Steps to Reproduce**

1. Save the script below as `repro_issue25.py` (it is not part of the repo) and, from the clone root, run `.venv\Scripts\python.exe repro_issue25.py`. It builds a `Retriever` with the offline hashing embedder, an in-memory Qdrant index and two chunks that both match the query: one test file (`src/test/java/PaymentServiceTest.java`) and one main file (`src/main/java/PaymentService.java`).
2. It plugs in a fake reranker that returns fixed scores: +5.0 for the test chunk and -5.0 for the main chunk.
3. It calls `retrieve()` with `RetrievalConfig.parse("hybrid+rerank")`, so `demote_tests` is on (the default). Observed: the final top chunk is the main file (rerank score -5.0), but the reported confidence is 0.9933 from source `rerank`, which is the demoted test chunk's score.

```python
r"""Repro for codeatlas-rag #25: with rerank on and demote_tests on (the default),
the refusal confidence comes from the top chunk BEFORE test code is demoted.

Offline: hashing embedder, in-memory Qdrant, a fake reranker with fixed scores.
Run from the clone root:  .venv\Scripts\python.exe repro_issue25.py
"""

import uuid

from qdrant_client import QdrantClient

from codeatlas.embeddings import HashingEmbedder
from codeatlas.models import Chunk
from codeatlas.retrieval.reranker import sigmoid
from codeatlas.retrieval.retriever import RetrievalConfig, Retriever
from codeatlas.storage.vector_store import VectorStore

TEXT = "retry the payment charge with exponential backoff"
test_chunk = Chunk(str(uuid.uuid5(uuid.NAMESPACE_DNS, "t1")), "src/test/java/PaymentServiceTest.java", "method", TEXT, TEXT, 1, 5, symbol="T#retry")
main_chunk = Chunk(str(uuid.uuid5(uuid.NAMESPACE_DNS, "m1")), "src/main/java/PaymentService.java", "method", TEXT + " main", TEXT + " main", 1, 5,
                   symbol="P#charge")


class FakeStore:
    def all_chunks(self):
        return [test_chunk, main_chunk]


class FakeReranker:
    """The cross-encoder loves the test chunk (+5.0) and dislikes the main chunk (-5.0)."""

    def score(self, query, chunks):
        return [5.0 if "/test/" in c.path else -5.0 for c in chunks]


emb = HashingEmbedder()
vectors = VectorStore(QdrantClient(location=":memory:"), "repro", emb.dim)
vectors.upsert([test_chunk, main_chunk], emb.embed_documents([test_chunk.embed_text, main_chunk.embed_text]))
r = Retriever(FakeStore(), vectors, emb, reranker=FakeReranker())
r.refresh()

cfg = RetrievalConfig.parse("hybrid+rerank")  # demote_tests defaults to True
res = r.retrieve("how does the payment retry work", cfg)
top = res.chunks[0]
print("config:", cfg.name, "| demote_tests =", cfg.demote_tests)
print("final top chunk:", top.chunk.path, "(rerank score %.1f)" % top.signals.get("rerank"))
print("reported confidence: %.4f from source %r" % (res.confidence, res.confidence_source))
print("sigmoid(rerank score of the final top chunk) would be: %.4f" % sigmoid(top.signals["rerank"]))
```

**Reproduction Evidence**

- **Commit showing reproduction:** none (the script is inlined above, not committed to my fork).
- **Screenshots/logs:**

```
config: hybrid+rerank | demote_tests = True
final top chunk: src/main/java/PaymentService.java (rerank score -5.0)
reported confidence: 0.9933 from source 'rerank'
sigmoid(rerank score of the final top chunk) would be: 0.0067
```

- **My findings:** it matches the issue: `retrieve()` sets `confidence = sigmoid(scored[0].score)` right after reranking (`retriever.py` ~line 200) and only then moves test chunks down (~lines 202-203), so the confidence describes a chunk that is no longer on top. Per my reading of `answer.py` (I did not run the answer path), refusal reads that number. The scores are made up, so this shows the order of the two steps, not how often it happens or how big the effect is on real questions; I have not run the real reranker or the dev split (`eval/petclinic-dev.yaml`), so I claim nothing yet about the confidence distribution or refusal counts. I only tested the `rerank` path.

**Toolkit**

- Tool: repro-check (live mode, with my voice guide and the wild scope), run on my claim and repro report together; Claude Code to write and run the script.
- What it produced: a reject on the first pass (Environment and Steps followable failed), then an accept after I fixed them.
- Difficulties: it was right that the script was not inlined and that the bug template asks for the Node version and `codeatlas doctor`; it also caught a local path in the script docstring. It had no way to run my script, so it graded the text, not the result.
- What changed: I inlined the script and run command, added Node and the doctor output, marked the answer-path claim as my reading of the code, and removed the local path.

---

## Solution Approach

**Analysis**

[Your analysis of the root cause: what is causing the issue, traced
to the files and functions involved.]

**Proposed Solution**

[High-level description of your fix approach, and what you are
deliberately NOT changing.]

**Implementation Plan**

1. [Modify file X to do Y]
2. [Add or change Z]
3. [Update tests]

[Record deviations here as they happen: what changed mid-build and
why. A recorded deviation is honest work; silent drift is not.]

**Toolkit**

- Tool:
- What it produced:
- Difficulties:
- What changed:

---

## Testing Strategy

[What observable checks prove the fix: your reproduction steps re-run
against the built change (before and after), and the repo's own test
suite or checks with the outcome shown. "Tests pass" with nothing
observable named is not a strategy.]

**Toolkit**

- Tool:
- What it produced:
- Difficulties:
- What changed:

---

## Implementation Notes

[A running log, added to as you work. Date each entry. What you
built, decisions you made when you made them, wrong turns included.]

**[Date]**

[Entry]

**[Date]**

[Entry]

**Toolkit**

- Tool:
- What it produced:
- Difficulties:
- What changed:

---

## Pull Request

**PR Link:** [GitHub PR URL when opened]

**PR Description:** [what the PR says; much of the content above
adapts into it]

**Maintainer Feedback:**

- [Date]: [feedback received, or "none yet"; silence is a normal
  state and costs you nothing]
- [Date]: [how you addressed it]

**Status:** [Awaiting review / Iterating / Merged / Closed. The
status is a fact you record, never a grade: the PR is graded as
opened.]

**Toolkit**

- Tool:
- What it produced:
- Difficulties:
- What changed:

---

## Learnings and Reflections

**Technical Skills Gained**

[What you learned technically, named concretely.]

**Challenges Overcome**

[What was hard and how you solved it; point at the real events, the
Toolkit blocks above are full of them.]

**What I'd Do Differently Next Time**

[Reflection on your process.]

---

## Resources Used

[What actually helped, not a reading list. List things this story
already shows you using: the doc you followed to set the environment
up, the thread that explained the bug, the person who answered you.
One real entry beats ten plausible ones.]

- [Link to documentation that helped]
- [Discussion, issue, or post that helped]
