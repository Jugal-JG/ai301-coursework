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
**Pull Request:** https://github.com/aakashsiwach/codeatlas-rag/pull/38

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

**Unit 7: Ship-gate and manifest that run the repo's checks and my pr-precheck on my branch**

My manifest runs the two checks CONTRIBUTING.md names (`ruff check .` and `pytest -q`), and on my branch the ship-gate passed both (155 passed, 7 skipped) and my pr-precheck accepted it, with one preferred check failing: my "Generated files regenerated" box was ticked with no output behind it. I fixed that by adding the script output to my test evidence, and I softened a "does nothing when demote_tests is off" claim in the PR text and changelog, because the outline reorder can still move the top chunk. On Windows the install needed three fixes: the hook used `python3` (a Store stub), the gate's Python could not launch `claude.cmd`, and the manifest needed `.venv\Scripts\python.exe -m ...` instead of `.venv/bin/...`.

**Unit 8: PR monitor and a playbook built from the repo's own rhythm**

I installed the PR monitor (patched, like my other tools, to launch `claude.cmd` on Windows) and its `--state` run read PR #38 correctly: quiet, mergeable, no reviews, no checks reported. Researching my playbook showed the repo has no outside-contributor PRs to measure (all 21 are Dependabot PRs that merge in under a day), so I based my 10-day first-review threshold on the owner's own bursts of activity about 6 days apart, and I found the PR's CI run is `action_required`, waiting for the owner to approve it, which the monitor's report does not show. The full run drafted a wait note, not a nudge, because the PR was about 14 minutes old and my own threshold was nowhere near met, so I posted nothing and kept the CI rule in my playbook to check on the Actions tab myself on the nudge date.

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

When the retriever reranks and also pushes test code below main code, the "how sure am I?" number it reports is read from the top chunk before the test code is pushed down. That chunk may no longer be on top, so the confidence (which the answer step compares to a refusal threshold) describes the wrong chunk.

**Expected Behavior**

The confidence should describe the chunk that is actually first in the final results, at least when a caller opts in. The issue asks for an opt-in option, `confidence_after_demotion`, that is off by default, so nothing changes for anyone who does not turn it on.

**Current Behavior**

With rerank on and test demotion on (both are how the project is normally run), `retrieve()` sets the confidence from the reranked top chunk and only then moves test chunks down. In my offline repro a test chunk scored +5.0 and the main-code chunk that ended up first scored -5.0, but the reported confidence was 0.9933, the test chunk's score.

**Affected Components**

`codeatlas/retrieval/retriever.py` (`RetrievalConfig`, its `OPTIONS` list, and `Retriever.retrieve()`), the refusal logic in `codeatlas/answer.py` that reads `confidence`, the tests in `tests/test_retrieval_and_answers.py`, the `CHANGELOG.md`, and the dev eval set `eval/petclinic-dev.yaml` for the on/off measurement the issue asks for.

**Toolkit**

- Tool: Claude Code for reading the retrieval code and the issue, and my CLAUDE.local.md memory file for the repo's rules.
- What it produced: the location of the bug (`retriever.py` ~line 200 versus ~202-203) and the list of files the change would touch.
- Difficulties: the issue says "rerank confidence" but the vector confidence is also taken before demotion; I had to decide to keep the change to the rerank path, as the issue's wording asks.
- What changed: nothing yet; I wrote that decision into the plan as something I am not changing.

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

- **My findings:** it matches the issue: `retrieve()` sets `confidence = sigmoid(scored[0].score)` right after reranking (`retriever.py` ~line 200) and only then moves test chunks down (~lines 202-203), so the confidence describes a chunk that is no longer on top. Per my reading of `answer.py` (I did not run the answer path), refusal reads that number. The scores are made up, so this shows the order of the two steps, not how often it happens or how big the effect is on real questions; At the time of this repro I had not run the real reranker or the dev split (`eval/petclinic-dev.yaml`), so I claimed nothing about the confidence distribution or refusal counts; I measured that later and report it under Testing Strategy. I only tested the `rerank` path.

**Toolkit**

- Tool: repro-check (live mode, with my voice guide and the wild scope), run on my claim and repro report together; Claude Code to write and run the script.
- What it produced: a reject on the first pass (Environment and Steps followable failed), then an accept after I fixed them.
- Difficulties: it was right that the script was not inlined and that the bug template asks for the Node version and `codeatlas doctor`; it also caught a local path in the script docstring. It had no way to run my script, so it graded the text, not the result.
- What changed: I inlined the script and run command, added Node and the doctor output, marked the answer-path claim as my reading of the code, and removed the local path.

---

## Solution Approach

**Analysis**

In `Retriever.retrieve()` (`codeatlas/retrieval/retriever.py`), when `cfg.rerank` is on the confidence is set right after the cross-encoder sorts the pool: `confidence, source = (sigmoid(scored[0].score), "rerank")` (~line 200). Only afterwards does `_tests_last(scored)` move test chunks below main code (~lines 202-203), and `_outlines_after_methods` may reorder again (~line 206). `results = scored[: cfg.k]` is then cut from the reordered list. So when the best-scoring chunk is test code, the reported confidence is that chunk's score even though a different chunk is first in the final results. `Answerer.is_low_confidence` in `codeatlas/answer.py` compares `rr.confidence` to the refusal threshold for source `rerank`, so the refusal decision can follow a chunk that is no longer on top. My offline repro (fake reranker: test chunk +5.0, main chunk -5.0) shows a final top chunk at -5.0 with a reported confidence of 0.9933.

**Proposed Solution**

Add an opt-in option `confidence_after_demotion` (bool, default False) to `RetrievalConfig` and to `OPTIONS`, so it can be set with `-o confidence_after_demotion=on` and shows in the config name. When it is on and reranking ran, recompute the confidence as `sigmoid(scored[0].score)` from the final top chunk after demotion and the outline reorder, just before `results` is cut, and keep `confidence_source` as `rerank`.

What I am deliberately not changing: the option stays off by default (making it the default needs a new held-out split, per the issue); the frozen 0.868 threshold and the README numbers are untouched; the vector and bm25 confidence paths are unchanged (the issue is about the rerank confidence); the graph expansion still does not affect confidence; nothing in `eval/petclinic-test.yaml`. The option changes the confidence only when test demotion or the outline reorder changes which chunk is first (so not with `demote_tests` off, or for a query about tests, in my tests).

**Implementation Plan**

1. `codeatlas/retrieval/retriever.py`: add the `confidence_after_demotion: bool = False` field, add it to `OPTIONS`, and add the small recompute step before `results = scored[: cfg.k]`.
2. `tests/test_retrieval_and_answers.py`: add tests that fail without the fix, using a fake reranker: option off keeps the old confidence, option on reports the final top chunk's score, the option is a no-op when `demote_tests` is off or the query is about tests, option parsing and the config name include it, and the vector-only confidence is unchanged.
3. `CHANGELOG.md`: an entry under `[Unreleased]`. Check that `python scripts/gen_config_docs.py` and `python scripts/export_openapi.py` produce no diff (`RetrievalConfig` is not part of `Settings`).
4. Run `ruff check .` and `pytest -q`, and rerun my repro script before and after: with the option on, the repro should report about 0.0067 (`sigmoid(-5.0)`) instead of 0.9933. I checked that `answer.py`, `cli.py` and `eval/runner.py` only read `RetrievalResult.confidence`, so the change stays inside `retrieve()`.
5. The issue's Scope says to "choose a threshold on the dev split" while its acceptance criteria freeze 0.868. I read that as: keep 0.868 and only report the on/off confidence ranges and refusal counts on dev (the eval report already prints them), and I will say that reading in the PR. Try the dev-split measurement from the issue (`eval/petclinic-dev.yaml`, option on and off, rerank on): report answerable and unanswerable confidence ranges and refusal counts. If the real reranker cannot run on my machine, say so in the PR instead of reporting numbers I did not measure.

Deviations: none from steps 1 to 4. Step 5 (the dev-split measurement) ran after my first ship-gate pass, on a separate Python 3.11 environment; I then added the numbers to the PR text and test evidence and ran the ship-gate again. I also squashed my three local commits into two before opening the PR, only to remove a co-author line from the messages; the code did not change.

**Toolkit**

- Tool: plan-check (live mode, with my voice guide and the wild scope), run on plan.md and a draft plan comment.
- What it produced: an accept on the first run, with the code line numbers in my plan confirmed against `retriever.py`, and three suggestions.
- Difficulties: it graded text against the repo, not my code; its `procedure.md` had gaps it named itself (no step for live mode without a posted repro, no control-run step, a read order that expects a repo-facts block).
- What changed: I added the expected after-fix number (about 0.0067) to the test plan, wrote down how I read the issue's "choose a threshold on dev" against its frozen 0.868, and checked that only `retrieve()` produces the confidence. I did not post the plan comment, as the assignment says.

---

## Testing Strategy

1. **Reproduction before and after.** My repro script (fake reranker: test chunk +5.0, main chunk -5.0) on the branch, with the option off, still prints `reported confidence: 0.9933 from source 'rerank'`, as on main. With `confidence_after_demotion=True` it prints `0.0067`, which is `sigmoid(-5.0)`, the score of the chunk that is first. The final top chunk is the same file in both runs, so the ranking is unchanged and only the confidence differs.
2. **New tests that fail without the fix.** Four tests in `tests/test_retrieval_and_answers.py`: option off keeps `sigmoid(5.0)`, option on gives `sigmoid(-5.0)` with the same chunk order; no change when `demote_tests` is off or the query is about tests; the vector-only confidence is untouched; the option parses, defaults off and appears in the config name. With `retriever.py` stashed they give `4 failed`; with the fix, `4 passed`. After the owner's review I added two more tests for the outline reorder (a class outline scored 5.0 above one of its methods scored 3.0: default off keeps `sigmoid(5.0)`, the option gives `sigmoid(3.0)`; and the same data with `outlines_after_methods` off, where nothing changes), and confirmed the first fails if the recompute is moved before the outline reorder.
3. **The repo's own checks** (CONTRIBUTING.md): `ruff check .` printed `All checks passed!` and `pytest -q` gave `155 passed, 7 skipped` (151 passed, 7 skipped on main before my change; 157 passed, 7 skipped after the two review tests). I also regenerated `docs/configuration.md` and `docs/openapi.json`; the only difference was Windows line endings, so I left those files unchanged.
4. **Dev-split measurement with the real reranker** (the issue's acceptance criterion): on Spring PetClinic 500158f, CodeAtlas 6cacc64, Python 3.11 with CPU torch, `eval/petclinic-dev.yaml` (40 questions: 33 answerable, 7 unanswerable) with `hybrid+rerank`, option off versus `-o confidence_after_demotion=on`. Retrieval is identical (recall@5 0.889, MRR 0.806, the numbers docs/evaluation.md documents, same retrieved lists for all 40). The confidence ranges are identical too (answerable 0.8336 to 0.9609, median 0.9238; unanswerable 0.4890 to 0.8661, median 0.8094) and so are the refusal counts at 0.868 (4 of 33 answerable and 7 of 7 unanswerable below it, both runs). The option did change the confidence on 8 of 40 questions, all answerable and all slightly lower (largest: 0.9590 to 0.9305), and none crossed 0.868. I did not run the test split.

**Toolkit**

- Tool: ruff and pytest from the repo's own checks, my repro script, and the ship-gate manifest that runs the two checks in a fresh terminal.
- What it produced: `test_evidence.md` with the before and after repro output and the check results, and a manifest that parses and runs both checks.
- Difficulties: the manifest's `.venv/bin/pytest` form does not exist on Windows, so I used `.venv\Scripts\python.exe -m pytest -q`; testing it through Git Bash and `cmd.exe` hung, and it worked from PowerShell.
- What changed: the manifest uses the Windows form of each command, and I restored the regenerated docs files after checking they had no real changes.

---

## Implementation Notes

**2026-11-21**

Reproduced the issue offline with a fake reranker. My first run failed because Qdrant needs UUID chunk ids; switched to `uuid5` ids. The repro showed a final top chunk at -5.0 with a reported confidence of 0.9933.

**2026-11-21**

Wrote the plan and checked it with plan-check (accept). Decided to put the new confidence right before `results = scored[: cfg.k]`, after both the test demotion and the outline reorder, because the issue is that the confidence should describe the chunk that is actually first, and the outline reorder can also move the top chunk. I did not post the plan comment on the issue.

**2026-11-21**

Built the fix on branch `fix/25-confidence-after-demotion`: a `confidence_after_demotion` field (default off) and an `OPTIONS` entry, plus a three-line recompute step in `retrieve()`. Wrote four tests with a fake reranker, confirmed all four fail with `retriever.py` stashed and pass with it, added the `CHANGELOG.md` entry, and made two commits locally (nothing pushed yet). `scripts/gen_config_docs.py` and `scripts/export_openapi.py` only changed line endings, so I restored those files.

**2026-11-21**

Wrong turn: I first tested my manifest commands through Git Bash with `cmd.exe /c`, and it hung; the same commands ran fine from PowerShell.

**2026-11-22**

Ran the dev-split measurement the issue asks for. Python 3.14 has no torch build I could use, so I made a separate 3.11 venv with CPU torch, cloned PetClinic at 500158f (git-ignored `repos/`), indexed it and ran `eval/petclinic-dev.yaml` with `hybrid+rerank`, option off and on. Result: identical retrieval, identical ranges and refusal counts, and 8 of 40 answerable questions with a slightly lower confidence, none crossing 0.868. Plain finding, not the dramatic one I might have hoped for: on dev the option does not change any refusal decision. I wrote it into the PR as it is.

**2026-11-22**

My voice guard stopped my PR description for ticking "No secrets in the diff" with no output behind it. I added the diff stat, a count of touched `eval/` files (0) and a grep for secret-like patterns (0) to my test evidence and to the checklist lines.

**2026-11-22**

Squashed my local commits into two to remove a `Co-Authored-By` line that had been added to the messages, force-pushed the branch to my own fork (no PR existed yet), and opened PR #38 against `aakashsiwach:main` from GitHub's website. It shows 2 commits, 3 files. Its CI run is `action_required`, so nothing has run on it yet.

**2026-11-22**

Installed the PR monitor, wrote my playbook, and ran it: `--state` showed a quiet, mergeable PR with no reviews, and the full run drafted a wait note. I sent nothing, because my own rule says to nudge only after 10 quiet days with no reply.

**2026-11-22**

The owner reviewed PR #38 and asked for a regression test for the outline reorder, since my recompute runs after both test demotion and `_outlines_after_methods` but my tests only covered demotion. I added two tests (a class outline scored 5.0 above one of its methods scored 3.0), shared my fake-reranker helper between the tests, and used a deliberate mutation (recompute moved before the outline reorder) to confirm the new test really fails when the behavior is wrong. All checks pass locally; the new CI run is waiting on the owner's approval.

**Toolkit**

- Tool: Claude Code to read the repo and write the change and tests, with my plan.md and the repo's own checks as the guard rails.
- What it produced: a 17-line source change, four tests, a changelog entry and the evidence file.
- Difficulties: Windows (line endings in regenerated files, Git Bash versus PowerShell for commands) and a Python 3.14 venv that CI does not cover.
- What changed: I kept every change inside the plan and restored the unrelated regenerated files, so the diff holds only the option, its tests and the changelog.

---

## Pull Request

**PR Link:** https://github.com/aakashsiwach/codeatlas-rag/pull/38

**PR Description:** The PR (title on GitHub: "Fix/25 confidence after demotion"; it is `Fixes #25`) adds an opt-in `confidence_after_demotion` ranking option, off by default, so that with rerank on the reported confidence comes from the chunk that is first after test demotion instead of the chunk demotion moved down. It lists what I left alone (default off, the frozen 0.868 threshold, the README numbers, the vector and bm25 confidence, the test split) and flags one reading of the issue's wording about choosing a threshold on dev. Its testing section has the offline repro before and after (0.9933 to 0.0067), `ruff check .` clean, `pytest -q` at 155 passed and 7 skipped, the four new tests failing without the change, and the real dev-split run on PetClinic: identical retrieval, ranges and refusal counts, with the confidence slightly lower on 8 of 40 answerable questions and no refusal flipped. It ends with an AI disclosure in my own words and a checklist where every ticked box has output behind it. The diff is 3 files: `retriever.py`, the tests and `CHANGELOG.md`. After the owner's review I added two more tests in a new commit.

**Maintainer Feedback:**

- 2026-11-22: none yet. The PR is under a day old with no comments or reviews, and its CI run is `action_required` (waiting for the owner to approve it for a first-time contributor). I ran my PR monitor; it drafted a wait note, not a nudge, because my playbook's first-review threshold is 10 quiet days. I posted nothing.
- 2026-11-22 (later that day): the owner commented. He said the scope looks aligned with the issue (opt-in and off by default, only the rerank confidence, the frozen 0.868 threshold and the README numbers kept). Before he can recommend a merge he asked for two things: a focused regression test for the `outlines_after_methods` reorder, showing that when the reorder changes the final first chunk the option reports the sigmoid of that chunk's rerank score while the default-off behavior stays unchanged, and for the PR's CI run, which showed `action_required`, to be authorized and green. He asked me to keep the 0.868 threshold, the README numbers and the held-out test split unchanged.
- 2026-11-22: I added two tests: a class outline the fake reranker scores 5.0 above one of its own methods scored 3.0, where the reorder puts the method first, so default-off keeps `sigmoid(5.0)` and the option gives `sigmoid(3.0)` with the same chunk order; and the same data with `outlines_after_methods` off, where the option changes nothing. I checked the first test fails when I temporarily move the recompute before the outline reorder, ran `ruff check .` (clean) and `pytest -q` (157 passed, 7 skipped), and pushed a new commit (754c26b). The new CI run is again `action_required`: only the owner can approve it, so no automated check has run yet. I replied on the PR with that summary and asked him to approve the workflow run (posted 2026-11-22 17:30 UTC).

**Status:** Iterating

**Toolkit**

- Tool: the ship-gate (my manifest's `ruff check .` and `pytest -q`, then my pr-precheck on the branch, plan, evidence and description), run by hand twice, plus my voice guard on the description.
- What it produced: two PASS runs. The first accepted the branch with one preferred check failing (a ticked "generated files" box with no output); the second, after I added the evidence and fixed the claims, passed every check.
- Difficulties: it was right about an overclaim ("does nothing when demote_tests is off"), and my voice guard stopped a ticked "no secrets" box with nothing behind it. I also opened the PR from GitHub's website, so neither hook ran on the opening step itself (they had run by hand on the same text), and the title on GitHub differs from the one I prepared.
- What changed: I backed each checklist box with output, softened the wording, and corrected the AI disclosure to say Claude Code ran the full set while I re-ran the two repo checks and reviewed the diff.

---

## Learnings and Reflections

**Technical Skills Gained**

I learned how this project's refusal logic works end to end: a cross-encoder rerank score goes through `sigmoid` into a confidence, which `Answerer.is_low_confidence` compares to a frozen threshold (0.868), and test-code demotion can change which chunk is first after that number is taken. I learned to test that kind of ordering bug offline, with a fake reranker and fixed scores, and to prove a test is real by stashing the fix and watching all four fail. I also learned to read an eval report's per-question JSON to get confidence ranges and refusal counts, and that a clean result (identical refusals on dev) is a legitimate thing to report.

**Challenges Overcome**

Finding an issue was the hard part: my scout's top pick was fine, but every AI/ML issue I checked on the course site already had open PRs or claims, and the one I briefly chose (pytorch/ao #3863) was rejected by my own scout, so I switched to a cleaner issue and unclaimed it. Setting up the tools on Windows took several fixes (symlinks, `python3` as a Store stub, launching `claude.cmd`, venv paths in the manifest). The dev-split measurement needed a separate Python 3.11 environment with CPU torch and about 700 MB of models. My own voice guard and ship-gate pushed back on real mistakes: a ticked checklist box with no output, and an overclaim about when the option changes nothing.

**What I'd Do Differently Next Time**

I would ask before committing and before adding anything to a commit message: a Co-Authored-By line I had not noticed showed up on GitHub and I had to rewrite my branch. I would run the real measurement earlier, before writing the PR text, instead of first writing "not measured" and then redoing the text. I would open the PR through Claude Code, not the website, so both hooks run on the opening step itself. And I would check for repos with outside-contributor PRs before relying on a review-wait number, since this repo had none and I had to use the owner's activity pattern instead.

---

## Resources Used

- The repo's CONTRIBUTING.md, which gave the exact checks (`ruff check .`, `pytest -q`), the evaluation rules (dev only, frozen thresholds) and the PR steps: https://github.com/aakashsiwach/codeatlas-rag/blob/main/CONTRIBUTING.md
- The repo's docs/evaluation.md, which gave the PetClinic setup commit (500158f), the eval commands and the dev numbers I checked mine against: https://github.com/aakashsiwach/codeatlas-rag/blob/main/docs/evaluation.md
- The issue itself, whose acceptance criteria defined the work: https://github.com/aakashsiwach/codeatlas-rag/issues/25
- The repo's PR template (`.github/PULL_REQUEST_TEMPLATE.md`), which I answered item by item in the PR description.
