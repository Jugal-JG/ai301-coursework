# Making a code-search tool report confidence about the right result: an opt-in fix, its tests, and the checks that kept my claims honest

## Overview

CodeAtlas is a small open-source tool that answers questions about a codebase and refuses to answer when its confidence is too low. Issue [#25](https://github.com/aakashsiwach/codeatlas-rag/issues/25) reports that, with reranking on, the confidence is read from the top result before test files are pushed down the ranking, so it can describe a result that is no longer first. I found the issue with a scout tool I built in this course, reproduced the bug offline, and opened [PR #38](https://github.com/aakashsiwach/codeatlas-rag/pull/38): an opt-in `confidence_after_demotion` option (off by default), four tests, and a measurement on the project's own 40 dev questions. The PR is open: the owner reviewed it and asked for one more test, which I added, and its automated checks are still waiting for his approval. The parts I wrote myself are the scout's rubric and search strategy, my voice guide, the ship manifest that runs the repo's checks, and a monitor playbook. I worked with Claude Code, which ran most commands on my machine; the PR description says so.

## What happened

**Finding an issue (2026-11-21).** My scout runs `gh search issues` queries from my own strategy, ranks the results, and grades the top five against my rubric. The first scan covered 233 issues and accepted four of five. One accepted issue, goldener #437, was already done: a merged PR closed it, but my rubric only failed on *open* linked PRs. I changed the check, which is a change to my own tool:

Before: `Fail if assignees is non-empty, or if any linked PR is open.`

After: `Fail if assignees is non-empty, or if any linked PR is open, or if any linked PR is already merged and says it closes/fixes this issue while the issue is still open (the work is probably already done).`

The scout then ranked codeatlas-rag #25 first. I briefly picked pytorch/ao #3863 from the course site instead, but my own scout rejected it (a tracker issue with several open PRs), so I switched to #25.

**Reproducing it.** I built a two-chunk case with a fake reranker that scores a test file +5.0 and a main file -5.0. With reranking and test demotion on, the main file ends up first, but the confidence still comes from the test file:

```
final top chunk: src/main/java/PaymentService.java (rerank score -5.0)
reported confidence: 0.9933 from source 'rerank'
```

**The fix and its proof.** The fix is a small recompute step before the results are cut, behind an option that defaults to off:

```python
if cfg.confidence_after_demotion and cfg.rerank and scored:
    confidence = sigmoid(scored[0].score)
```

With the option on, the same script prints `0.0067`. I added four tests with the same fake reranker. With the fix stashed they failed (`4 failed, 21 deselected`); with it, `4 passed`. The repo's own checks gave `ruff` clean and `pytest -q` at 155 passed, 7 skipped (151 before my tests).

**A change I made after a check caught me.** My ship-gate runs the repo's checks and then my own PR checker on the branch. It passed on the first run but flagged two things; one quote:

`Repo checks were run and reported [fail]: ruff and pytest shown with real output, but the checklist box 'Generated files regenerated' is ticked and test_evidence.md has no output for gen_config_docs.py or export_openapi.py.`

It also noted an overclaim: my PR text and changelog said the option changes nothing when test demotion is off, but the class-outline reorder can also move the top chunk. I added the missing output and rewrote the sentence:

Before: `It changes nothing when demote_tests is off, when the query is about tests, or for the vector and bm25 confidence.`

After: `It does not touch the vector and bm25 confidence, and the value is the same as before whenever neither test demotion nor the class-outline reorder changes which chunk is first.`

My voice guard then stopped another ticked box with nothing behind it, logging `stop: rule broken: "A ticked checklist box I have not backed with output."`, so I added the diff stat and a secret-pattern search. The second ship-gate run passed every check.

**A real measurement, with a plain result.** The issue asks for numbers from the real reranker, so I built a separate Python 3.11 environment, indexed Spring PetClinic, and ran the 40 dev questions with the option off and on:

|              | n  | confidence range | refused (< 0.868), off / on |
| ------------ | -- | ---------------- | --------------------------- |
| answerable   | 33 | 0.8336 to 0.9609 | 4 / 4                       |
| unanswerable | 7  | 0.4890 to 0.8661 | 7 / 7                       |

Retrieval was identical (recall@5 0.889, matching the repo's own docs). The option lowered the confidence slightly on 8 of 40 answerable questions (largest 0.9590 to 0.9305), and none crossed the threshold, so no refusal decision changed on dev. I put exactly that in the PR instead of a stronger claim.

**The owner's review changed my work again (2026-11-22).** About seven and a half hours after I opened the PR, the owner commented that the scope looked aligned with the issue (opt-in, default off, only the rerank confidence, the 0.868 threshold and README numbers kept). Before recommending a merge he asked for two things: a regression test for the outline reorder, because my recompute runs after that reorder too but my tests covered only test-code demotion, and for the PR's CI run, shown as `action_required`, to be authorized and green. I added two tests. The key assertions, with a class outline the fake reranker scores 5.0 above one of its own methods scored 3.0:

```python
legacy = retriever.retrieve(query, RetrievalConfig.parse("hybrid+rerank"))
assert legacy.confidence == pytest.approx(sigmoid(5.0))  # default off: still the outline's score
fixed = retriever.retrieve(query, RetrievalConfig.parse("hybrid+rerank", confidence_after_demotion=True))
assert fixed.confidence == pytest.approx(sigmoid(3.0))  # the final top chunk is the method
```

A second test turns `outlines_after_methods` off and shows the option changes nothing. To check the test was real, I temporarily moved the recompute to before the outline reorder, and the first test failed; with the real code `ruff check .` was clean and `pytest -q` gave 157 passed, 7 skipped. I pushed it as a new commit and replied on the PR with that summary, asking him to approve the workflow run.

**Where it stands.** What landed: the scout surfaced a usable issue in its first scan (and re-graded it as accept after my rubric patches), my gate caught two things before a maintainer saw them, and a maintainer read the PR, confirmed the scope, and gave me a concrete path to merge. What has not landed: no merge yet. The new CI run is `action_required` again (GitHub holds a first-time contributor's run for approval), which my PR monitor's report does not show. Only the owner can approve it, so nothing automated has run, and he said he will do a final pass once the test is in and CI is green.

## Limitations and what's next

My first tests did not cover the outline reorder, which my own PR text admitted; the owner asked for that test and I added it, but I still only tested the `rerank` path. The evidence is one repo and 40 dev questions, where the option fixed the number without changing any refusal decision, so I can't claim it improves answers. I tested on Python 3.14, which the project's CI doesn't cover, and ran the eval on 3.11. I have one review comment from the owner, who is positive about the scope, but he has not approved the CI run or said it is ready to merge, so whether he wants the option at all is not settled. The scout has a limit too: search results carry no star count, so solo, zero-star repos still rank high.

Next, I need the owner to approve the CI run so the project's checks can pass on my new commit, and I can attach the two eval reports if he wants them in the PR. If I hear nothing for 10 quiet days after his last comment, my playbook allows one polite nudge that asks only for the CI approval or a final pass, and if there is still no reply 14 days after that, I would close the PR with a short thank-you note. The scout would also get a ranking signal for repo activity from a rubric check, since the search itself can't see it.
