# Rubric: is this reproduction package ready to post?

## Checks

| Check | Evidence | Pass condition | Weight |
|---|---|---|---|
| Environment recorded | The repro report's environment line(s): tool/library version, OS/platform, and any other runtime detail the issue's own environment block names (git version, install method, shell, etc.). | Pass if the report states a version and platform for every runtime detail the issue's environment block names. If the report's version differs from the issue's, pass only if the report says so explicitly (e.g. "issue filed against X, I tested Y"); a silent mismatch is a fail, not unclear. | required |
| Steps followable | The repro report's step-by-step section: starting state, exact commands or UI actions, in order. | Pass if a stranger with no other context could reach the same starting state and re-run the same commands/actions without guessing any value needed to trigger the bug. A step counts as shown when every value needed to build it is stated unambiguously (e.g. "a minimal env.yml with a valid dependencies list plus an unrecognized category section" is followable; a literal file dump is not required as long as nothing is left to guess). Fail only when a step is implied but not stated, a needed value is missing (which file, which flag, which focused element, which parameter), or steps are summarized so loosely that two different attempts could land in different starting states. | required |
| Behavior matches issue | The artifact shown in the report (output, error text, stack trace, or described screen state), read against the issue's own description/error/steps. | Pass only if the artifact demonstrates the *same* symptom the issue describes: same error class/message shape, same panic site, or same observable misbehavior. Fail if the report ran a different input/command than the issue specifies (even a small syntax change) and got a different error or symptom, even when the report calls it equivalent or "the same class of failure." An honest, evidenced "cannot reproduce, behavior differs as follows" is a pass on this check — it is reporting the actual behavior faithfully. | required |
| Honesty of claims | The report's stated conclusions (e.g. "confirms the bug," "definitely," "everyone has this") compared to what the artifacts in that same report actually show. | Pass if every claim is backed by an artifact shown in the report — no claims of frequency, universality, or certainty ("happens constantly," "everyone I know has this") that are not themselves evidence, and no claiming a match when the shown artifact is actually a different error. Fail on unsupported amplification even if the rest of the report is otherwise well-formed. | required |
| Repo conventions and disclosure | The repo-facts block's bug-report template and contribution/AI-disclosure policy, read against the claim comment and repro report's actual content and any AI-use statement in them. | Pass if the report/comment covers what the repo's own template asks for (when one is named), and, when the contribution policy explicitly requires disclosing that AI assistance was used, the comment discloses it. Fail only on that explicit case: the policy states you must disclose AI use, and the draft is silent. A policy that merely permits or regulates AI use without requiring disclosure (e.g. "AI tools are welcome; you are responsible for reviewing what they produce") is not a disclosure requirement — pass in that case, and pass by default when the repo states no template and no AI policy at all. | required |
| Claim comment does not overpromise | The candidate claim comment's own wording. | Pass if the claim names the issue's specifics and states an investigation/next-step plan without promising a fix, a timeline, or a merge. Fail if the claim asserts the bug is already fixed, promises a delivery date, or otherwise commits to an outcome the poster does not yet control. | preferred |

## Verdict rule

Accept only if every `required` check grades `pass`. Any `required` check
graded `fail` or `unclear` makes the verdict `reject`. `preferred` checks
are reported but never change the verdict. `unclear` is treated as `fail`:
evidence I could not verify is evidence that is not ready to post.

In live claim-only mode, per SKILL.md only the checks whose evidence is
the claim comment or repo conventions apply here — "Repo conventions and
disclosure" and "Claim comment does not overpromise" — the rest are
marked `unclear: not yet applicable: claim-only draft` and excluded from
this rule. Accept the claim-only draft if both applicable checks pass
(a `preferred`-weighted check still gates in claim-only mode, since it is
one of only two checks left to judge readiness by).
