# Evidence guide: where proof lives in a reproduction package

## Environment

Where it lives: eval bundle — the repro report's opening "Environment"
line(s). Live mode — the same, in the student's draft repro report; the
issue's own environment expectations come from the issue body and any
`bug reports: template asks for ...` line in the repo-facts block.

What good looks like: every runtime detail the issue's environment block
or the repo's template names (tool version, OS/platform, and anything
else the template asks for, e.g. install method or a second tool's
version like git) is stated in the report, not just some of them. If the
report tests a different version than the issue was filed against, the
report says so explicitly in the same sentence or the next one — a
silent version gap is the same as an unrecorded one.

## Steps

Where it lives: eval bundle — the repro report's steps/commands section,
read against the issue's own "Steps" or reproduction command. Live mode —
the student's draft report.

What makes steps followable: a stated starting state (what exists before
you begin — an empty repo, a specific input file, a focused UI element)
followed by commands or actions in order, ending at the point where the
behavior appears, with every value needed to build that starting state
given unambiguously. This does not require a literal file dump: "a
minimal env.yml with a valid dependencies list plus an unrecognized
category section" is followable, because nothing in it is left to guess,
and the actual triggering command is still shown verbatim. What is not
followable is a step that summarizes the action itself ("ran the
reproduction," "did what the issue said") without saying what command was
run, or a starting-state description that omits a value the trigger
depends on (which flag, which parameter, which focused element).

## Behavior shown

Where it lives: eval bundle — the repro report's output/error/trace
excerpt, read against the issue's own description, error text, or
attached trace. Live mode — the same in the student's draft.

What it means to match: the artifact carries the same failure signature
as the issue — the same error message shape, the same panic location, or
the same observably wrong behavior (not just "also a bug," but the *same*
bug). Watch specifically for a report that ran a slightly different
input or command than the one the issue specified (different syntax,
different flag, different value) and got a different error, then narrated
it as confirming the issue anyway — that is a mismatch, not a match, no
matter how confident the write-up sounds. A report that faithfully shows
it could NOT reproduce the exact behavior, and says so, matches this
check's honesty rather than failing it (see Honesty below); what fails is
claiming a match that the shown artifact does not support.

## Honesty

Where it lives: eval bundle — the repro report's and claim comment's own
sentences of conclusion or characterization, read against the artifacts
shown in the same document. Live mode — the same, in the student's
drafts.

What good looks like: a report's own claims never say more than its own
evidence shows. Concretely: no appeals to universal or constant frequency
("everyone I know has this," "happens every single day") offered as proof
in place of an artifact; no claiming "confirmed" or "reproduced exactly"
when the shown output is actually a different error or partial match; an
honest "could not reproduce, here is what happened instead" is evidence
stated faithfully, and passes. The tell for a failing report is emotional
amplification or confident language doing the work that an artifact
should be doing.

## Comms

Where it lives: eval bundle — the repo-facts block's `bug reports:`
template line and `contribution policy` line (including any AI-use
disclosure requirement), read against the claim comment and repro
report's actual content. Live mode — the repo's CONTRIBUTING.md /
issue template (per `references/evidence-guide.md`'s general guidance) or
whatever the scope's house repo publishes, read against the student's
draft comments.

What good looks like: when the repo names a bug-report template, the
report covers what it asks for (version, OS, steps, expected/actual,
etc. — whatever that repo's line names, not a fixed universal list). When
the contribution policy explicitly requires disclosing AI use, the
comment discloses it in plain terms; silence on that specific kind of
policy is a fail here, not an oversight to forgive. Distinguish that from
a policy that merely permits or regulates AI use without requiring
disclosure — "generative AI tools welcome, you are responsible for
reviewing what they produce" is permissive-with-responsibility, not a
disclosure wall, and passes with no disclosure statement needed. A
comment can still pass this check while sounding personal and
non-boilerplate; the check is about coverage and disclosure, not tone
(tone is the voice guide's job, live mode only).
