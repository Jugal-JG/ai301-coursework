# Evidence guide: where evidence lives in a plan package

## Diagnosis and grounding

Where it lives: eval bundle: the cause is the first part of the `## Candidate plan` section (often a line starting "Cause:" or "Diagnosis:"). The facts it must explain are in `## Repro evidence`: the numbered steps, the artifact block, and any "Control" or "Control runs" lines. Live mode: the draft plan.md's diagnosis, against the student's posted repro comment on the issue (or the house repro pack as quoted in the drafts).

What good looks like: the stated cause names a mechanism (a function, a branch, a constant, a pointer) and every control and artifact in the repro agrees with it. A control that changes one thing and flips the outcome is the strongest evidence; a plan that blames something a control shows working, or that blames a step after damage already appears in the artifact, contradicts its own evidence however confident it sounds.

## Scope

Where it lives: eval bundle: the plan's in-scope and not-in-scope statements and its numbered approach steps, read against the issue title and the repro's failure. Live mode: the same lines in plan.md.

What good looks like: one change that fixes the failure the repro shows, with a line naming what stays untouched. A drive-by rewrite shows up as extra approach steps the repro never needed (a migration, a new option, a framework, a harness port, a restructure) sitting beside a correct core fix. A deferral with a stated reason is bounded; extra work with no reason is not.

## Executability

Where it lives: eval bundle: the plan's "Approach" steps, its named files or areas, and any "Change" line. Live mode: the same in plan.md.

What good looks like: a stranger can open the named file, find the named function, and make the first edit without asking the author. The plan picks one approach. Vague verbs ("investigate", "profile and optimize", "somewhere", "whichever is easier") and decisions left to build time mean the plan could not be started.

## Test plan

Where it lives: eval bundle: the plan's "Test" or "Test plan" section, read against the numbered steps and artifact in `## Repro evidence`. Live mode: plan.md's test plan, which should be the unit 2 repro steps re-run with what is expected after the fix.

What good looks like: it names an outcome that differs before and after the fix (the same command now exits 0, the color flips at step 3, a named failing test now passes), tied to the repro's steps. "Run the test suite" or "should feel fast" with no fix-specific outcome is not decisive.

## Honesty

Where it lives: eval bundle: the plan's "Risk", "Risks" or "unknowns" lines and any hedged sentence, plus the plan comment's confidence wording. Live mode: the risks section of plan.md, and the `## Deviations` heading after a build.

What good looks like: the plan names at least one thing it has not verified (a cost not yet measured, a variant it cannot test) where one plainly exists, and no sentence claims more than the repro shows. A mid-build change is recorded under `## Deviations`, not left only in the diff.

## Comms

Where it lives: eval bundle: `## Candidate plan comment`, read against `## Thread highlights` (every maintainer or owner statement, proposed fix, rejected approach, request to test, or prior PR) and `## Repo facts` (the `bug reports:` template line and the `contribution policy` line, including any AI-use disclosure requirement). Live mode: the draft comment.md against the issue's live thread and the repo's CONTRIBUTING and issue template.

What good looks like: the comment names the maintainer's direction and either follows it or says why it diverges; it is specific to this issue and could not be pasted on another. When the contribution policy explicitly requires disclosing AI use, the comment says so plainly (tool and extent). A policy that only permits AI or only makes the human responsible for review is not a disclosure requirement.
