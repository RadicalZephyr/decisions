# Research

Dated documents that carry evidence: the write-up of an experiment, a brief and
the report it produced, a review of the literature, requirements gathered from
another project. A record cites a research note when it argues from the note's
evidence. The note is not authoritative; the record is.

A research note is where an experiment's result rests. The experiment itself
does not stay -- [`../experiments/README.md`](../experiments/README.md#retirement)
says when it leaves -- but the note keeps what it found and pins the commit that
found it, so a record written months later still has something to argue from.
[`../README.md`](../README.md#before-a-record-notes-and-research) has the path
from a note to here and from here to a record.

## Files

One research note per file, `YYYY-MM-DD-kebab-case-title.md`, dated the day it
was started. Running the experiment a [note](../notes/README.md) proposes is
what opens one: the entry point and the draft research note start together,
under the same name, and the research note links the note that proposed it. An
experiment that already ran -- elsewhere, or before anyone wrote a note -- comes
in here directly.

A research note opens with one italic line: its date, the question it answers,
what ran, and where the code is.

```markdown
_2026-10-05. Does a first-in first-out free list spread slot reuse evenly under
churn? The experiment is `2026-10-05-free-list-churn` in the experiments
sub-project; the bounded case has not run._
```

It is kept as written once it merges, the way a note is:
[`../notes/README.md`](../notes/README.md#kept-as-written) has the rule.

## Briefs

A brief is the question as it was put to whoever runs the research -- a person,
another session, an agent -- with what they were told to take as settled. It is
a research note too, named the same way, and it sits beside the report it
produced: a report's answer is only legible against what it was asked and what
it was told not to reopen.

A brief's opening line says whom it is for and what it asks. A report with a
brief links it from its own opening line and gives the key question in one
sentence, rather than restating the brief:

```markdown
_2026-10-07. Answers [the brief](./2026-10-05-free-list-churn-brief.md): does a
first-in first-out free list spread slot reuse evenly under churn? Everything
asked has run._
```

## What ran

A number a research note establishes has to be re-derivable: the note says what
ran, with which toolchain, on what, and where the code is. Each such number
carries a provenance line, and the command that produced it, in the section
that quotes it; [`../README.md`](../README.md#a-measured-number-carries-its-provenance)
has the format. A number the note did not establish -- a paper's, another
project's -- cites its source and says it was not reproduced here.

The experiment's code lives in [`../experiments/`](../experiments/README.md)
unless it needs more room than the sub-project has.
[`../README.md`](../README.md#evidence) has the routing, and what a research
note owes its reader when the code lives somewhere else.
