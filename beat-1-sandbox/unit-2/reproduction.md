# Unit 2 — Claim and Reproduce

Path: `beat-1-sandbox/unit-2/reproduction.md`

Record of your claim and reproduction on the issue you chose in Unit 1, and of the
evaluation runs that produced `eval-run.txt`. This file is graded at the path above; a copy
kept anywhere else in the repository is not read.

Complete every labelled field below. Each is graded on its own; content placed under the wrong
label is not graded.

---

## Your identity upstream

**GitHub username**

Jugal-JG

---

## Posted upstream

**Claim comment**

https://github.com/codepath/pathreview-ai301-fa26-s1/issues/14#issuecomment-5860428368

Claiming this one for AI301 coursework. I read through the issue body and aishani-muk's comments: scripts/run_evals.py currently just prints two lines and returns, nothing in the codebase calls EvalSuite, and .github/workflows/eval.yml swallows a missing eval_results.json into a placeholder string instead of failing loudly. I haven't reproduced this myself yet; that's my next step, and I'll post a full repro report once I have, but I wanted to post my plan before I start: wire scripts/run_evals.py to actually invoke EvalSuite against the fixtures under tests/fixtures/sample_profiles/, and have it write eval_results.json in the shape that makes eval and the CI workflow expect it.

Two open questions aishani-muk raised are ones I'll have to work through too, since they're upstream of any fix: whether an actionability scorer belongs in this issue (the TODO asks for one, rag/evaluator/ doesn't have one yet), and where the mock-LLM injection seam goes, since ReviewGenerator.__init__ builds its own openai.OpenAI client with no injection point today. I don't have a settled opinion on the actionability-scorer question, yet, but I want to actually run the existing scorer against the fixtures before I guess at scope. If a maintainer weighs in on either question before I get there, I'll follow that.

Not asking anyone to hold the issue for me. If aishani-muk is further along by the time I'm ready to write code, I'm glad to take a narrower slice or a different part of it instead.

**Reproduction comment**

https://github.com/codepath/pathreview-ai301-fa26-s1/issues/14#issuecomment-5860949315

**Environment.** pathreview 0.1.0 at commit `f89c06f` on `main` (same
commit aishani-muk tested against). Windows 11 Home (10.0.26200), Git
Bash (git 2.53.0.windows.2), Python 3.11.9 in a `.venv` built with
`make setup` (repo pins 3.11 in `pyproject.toml` and in every Python CI
job). Docker 29.6.1 / Compose v5.3.0 for the `db` and `redis` backing
services. `.env` copied from `.env.example` with no edits, so
`LLM_PROVIDER=mock` — no API key needed for this issue.

One aside not related to this issue: the `vector-db` (Chroma) container
in `docker-compose.yml` fails to start on a fresh `docker compose up -d`
(`chromadb/chroma:0.4.22` imports `np.float_`, removed in NumPy 2.0).
`db` and `redis` came up healthy and that's all this issue's fix path
touches (`EvalSuite.run` takes profile text directly, no vector store
involved), so I did not chase that further here.

**Steps.**

```
$ git log -1 --format="%H %ci"
f89c06fc3ff292df2a04a39ac51319d32a76b779 2026-09-16 14:48:26 -0700

$ test -f eval_results.json; echo $?
1

$ .venv/Scripts/python.exe scripts/run_evals.py; echo $?
Running RAG evaluation suite...
Evaluation complete. Results written to eval_results.json
0

$ test -f eval_results.json; echo $?
1
```

(`make eval` runs this same script; I called the venv's python directly
since `make` needed a separate install step on this machine, but the
command it runs is identical: `$(PYTHON) scripts/run_evals.py`.)

To see why, I checked whether anything actually calls the scorer:

```
$ grep -rn "EvalSuite" --include="*.py" .
./rag/evaluator/eval_suite.py:22:class EvalSuite:
```

Only its own class definition — no caller anywhere in the codebase.
`rag/evaluator/__init__.py` is 0 bytes, so nothing re-exports it either.
The scorer itself is not broken; calling it directly works:

```
$ .venv/Scripts/python.exe -c "
from rag.evaluator.eval_suite import EvalSuite
print(EvalSuite().run('python fastapi',
    [{'text': 'python expert building fastapi services'}],
    'This developer knows Python and FastAPI.'))
"
EvalResult(relevance_score=1.0, faithfulness_score=1.0, overall_score=1.0)
```

And the input fixture parses fine, so that is not the blocker either:

```
$ .venv/Scripts/python.exe -c "
import json
d = json.load(open('tests/fixtures/sample_profiles/basic_profile.json'))
print(type(d), len(d))
"
<class 'dict'> 4
```

I also read (did not run) `.github/workflows/eval.yml`: it reads
`eval_results.json` inside a try/except with an empty `catch`, so a
missing file resolves to the literal string `'Eval results not found.'`
posted as a PR comment/summary rather than failing the job.

**Expected.** `scripts/run_evals.py` (and the `RAG Evaluation` CI job
that runs it) invokes `EvalSuite` against the benchmark profiles under
`tests/fixtures/sample_profiles/` and writes real scores to
`eval_results.json`.

**Actual.** The script prints the two lines shown above and returns
without calling `EvalSuite`; `eval_results.json` is never created, and
the process still exits 0 — so `make eval` and CI both report success
while nothing was actually measured. This matches the issue exactly:
the scorer (`EvalSuite`) and the fixture (`basic_profile.json`) both
work in isolation, and the only bug is that `run_evals.py` never wires
them together.

**Not covered by this report.** Two design questions the fix depends on
are still open (also raised by aishani-muk, and by me in my claim
comment): whether an actionability scorer belongs in this issue's scope,
and where a mock-LLM injection seam goes for `ReviewGenerator`, which
currently builds its own `openai.OpenAI` client with no seam
(`rag/generator/review_generator.py:35`). I'm not proposing an answer to
either here — this report only establishes that the reported behavior
reproduces and traces where it comes from.

## Eval iterations

Answer all four sections. Quote source text directly; paraphrase does not satisfy these
fields.

**Run history**

1. Warm-up (not scored): hand-graded `calib-02` through the harness with my first-draft
   rubric (`--include-calibration --only calib-02`) before spending on a full run —
   `agreement: 0/0 scored items` (calibration packages are never scored), verdict `reject`,
   matching the worksheet's gold call (pure me-too enthusiasm, zero evidence).
2. First full run (20 packages): `agreement: 18/20 scored items (bar: 18/20: PASS)`,
   category floor met (`clear-accept 6/8 disclosure 1/1 no-evidence 4/4
   unfollowable-comms 3/3 wrong-target 4/4`). Two misses, both gold `accept` that my rubric
   rejected: `pkg-05` (failed `Steps followable` and `Repo conventions and disclosure`) and
   `pkg-12` (failed `Steps followable`).
3. Targeted re-run after revising those two checks: `--only
   pkg-05,pkg-12,pkg-06,pkg-18,pkg-19,pkg-20` (the two misses plus one canary from each
   category the revision could touch — `unfollowable-comms` and the single-package
   `disclosure` category) — `agreement: 6/6 scored items`. Both misses flipped to the
   correct `accept`; all four canaries held their correct `reject`.
4. Confirming full run, saved with `--save-run eval-run.txt`: `agreement: 20/20 scored
   items (bar: 18/20: PASS)`, categories `clear-accept 8/8 disclosure 1/1 no-evidence 4/4
   unfollowable-comms 3/3 wrong-target 4/4`. This is the run in the committed
   `eval-run.txt`.

**Package analysis**

`pkg-05` (source: conda/conda#16543). Gold label: `accept` ("minimal env.yml repro with a
json.tool parse failure as the artifact; conda's stated generative-AI policy is
permissive-with-responsibility, no disclosure requirement"). My rubric's first-draft verdict:
`reject`, failing two checks at once. First, `Steps followable` failed because the report
says "wrote a minimal env.yml containing a valid dependencies list plus an unrecognized
category section" instead of pasting the literal YAML — my original pass condition demanded
the starting state be shown as a literal file dump, and the grader read the absence of a
raw file body as a missing step, even though every value needed to build that file (a valid
dependencies list, plus one unrecognized `category:` key) was stated unambiguously, and the
actual triggering command (`conda env update --quiet --json -f env.yml 2>/dev/null`) was
shown verbatim. Second, `Repo conventions and disclosure` failed because conda's
CONTRIBUTING.md has a "Generative AI" section ("generative AI tools welcome; you are
responsible for all contributions and must review and understand AI-generated content") and
my original pass condition treated any named AI policy as a disclosure requirement, when
this one only makes the human responsible for reviewing AI output — it never asks anyone to
say "I used AI" in a comment. I revised `Steps followable` to accept a precisely-specified
input where nothing is left to guess (not necessarily a literal file dump), and revised
`Repo conventions and disclosure` to distinguish "permissive-with-responsibility" policies
from an actual disclosure requirement. Re-running `--only pkg-05` (bundled with `pkg-12`,
which had the identical `Steps followable` problem, plus four canaries) after that change
gave `pkg-05: accept`, matching gold.

**Check rationale**

The `Steps followable` check currently reads (evidence column): "The repro report's
step-by-step section: starting state, exact commands or UI actions, in order." and (pass
condition): "Pass if a stranger with no other context could reach the same starting state
and re-run the same commands/actions without guessing any value needed to trigger the bug.
A step counts as shown when every value needed to build it is stated unambiguously (e.g. 'a
minimal env.yml with a valid dependencies list plus an unrecognized category section' is
followable; a literal file dump is not required as long as nothing is left to guess). Fail
only when a step is implied but not stated, a needed value is missing (which file, which
flag, which focused element, which parameter), or steps are summarized so loosely that two
different attempts could land in different starting states." I rewrote it from an earlier
version that required the starting-state file's contents to be pasted verbatim, in favour of
this looser wording, because the earlier version failed `pkg-05` and `pkg-12` — both gold
`accept` — purely for describing an unambiguous input precisely instead of dumping it as
raw text, which is a formatting preference, not a followability problem the rubric is
supposed to police.

**Trade-offs**

Loosening `Steps followable` to accept a precisely-specified description (not just a literal
file dump) risks accepting a report that describes its input "precisely" in words but is
actually still missing a value a stranger would have to guess. To check this didn't
happen, I re-ran the three `unfollowable-comms` packages as canaries in the same `--only`
batch that fixed `pkg-05`/`pkg-12`: `pkg-06` (no environment record at all, and the steps
omit which driver was used — still correctly `reject`), `pkg-18` (reproduction lives in an
unshared private monorepo, so no stranger can follow it at all — still correctly `reject`),
and `pkg-19` (steps are fine, but the claim comment is boilerplate — unaffected by a
steps-check change, still correctly `reject`). All three held. The trade-off I accept on
purpose: my looser check will pass a report that names every needed value in prose without
ever showing a raw command's full literal text, on the theory that "unambiguous" is what a
stranger actually needs, not "verbatim." That is a real gap if a package ever describes
something "precisely" while still hiding an assumption in wording I didn't catch — the
canaries above show it hasn't happened yet in this set, not that it never could.

---

Related paths: `eval-run.txt` in this directory; your skill's files in
`tools/repro-check/`.
