I opened a fix for a bug in an open-source tool that answers questions about code and is supposed to say "I don't know" when it is unsure. The bug made it measure its own certainty on the wrong result.

I found the issue with a search tool I built this term, reproduced the problem, and wrote an optional setting that corrects it, plus four tests. Then I ran the project's own 40 test questions both ways. The honest result was modest: nothing changed about which questions it answers or refuses, only the confidence numbers moved slightly. I reported that as it was instead of claiming more.

What I took from it: my own checks caught two things before a maintainer ever saw them, a box I had ticked with no proof behind it and a sentence that claimed more than my tests showed. Writing down only what I can back up is a habit I want to keep.

The fix is open for review now. It hasn't been reviewed or merged yet, and the project's automated checks are waiting for the owner's approval.

https://github.com/aakashsiwach/codeatlas-rag/pull/38

#OpenSource #Python #SoftwareTesting
