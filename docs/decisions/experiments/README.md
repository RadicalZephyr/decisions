# Experiments

Experiments backing the research notes in
[`docs/decisions/research/`](../research/) and the decision records in
[`docs/decisions/records/`](../records/).

Any code that produces concrete data used to argue a research note or a record
lives here rather than in a scratch file or a gist. A document that cites a
measurement is only as good as a reader's ability to re-run it, and a benchmark
that lived in someone's working tree cannot be re-run at all.

## Layout

This directory is a sub-project of the main build. It depends on the project by
path rather than on a published version, is never published itself, and is
covered by the same checks -- lint, type check, tests -- as anything else in the
repository, because an experiment is still code someone runs, and not publishing
it is no reason to skip that. An experiment routed here because the playground
could not supply a dependency brings that dependency with it, into this
sub-project and so into the repository; [Retirement](#retirement) says when it
leaves. How it joins the build, and what it is called, the project says here:

> **Sub-project:** {{how it joins the build -- a workspace member, a package in
> the monorepo, a directory with its own manifest -- and what it is called}}

Each experiment is an entry point named after the document that quotes its
results -- usually a research note, or a record that argues from it directly:

```text
docs/decisions/experiments/{{path to the entry points}}/2026-10-05-some-question.{{ext}}
docs/decisions/experiments/{{path to the entry points}}/0001-some-decision.{{ext}}
```

and is run from the repository root with one command:

```shell
{{the command that runs the experiment named 0001-some-decision}}
```

The name is the only thing that varies from one experiment to the next. An
experiment for a research note starts the day the note does, under its name,
which is how an experiment exists before any decision rests on it --
[`../README.md`](../README.md#before-a-record-notes-and-research) has the path.
A document with more than one experiment suffixes each after its full name,
`2026-10-05-some-question-cold-start`, because a date alone does not identify a
research note.

Print results in whatever shape the document needs to quote them, and link the
entry point from the section that relies on the numbers. Fixtures shared between
experiments -- a data builder two of them both need, a timing harness -- get a
shared module, and it stays empty until something is actually shared. Resist
putting an experiment's own scaffolding there.

## What does not go here

Research produces *evidence*, not tests. It is not asserting that the project
is correct, and it is not expected to keep passing -- it answers a question
that was open at the time it was written.

An experiment the playground can run does not belong here either. If everything
it needs is within the service's reach -- the toolchain, its standard library,
and any dependency the service supplies -- it goes in a share link recorded in
the research note or record instead, which is also the only route available to
a case that has to *fail* to build. What is out of that reach comes back here:
this project's code, and anything else the service cannot run, most often a
dependency it does not carry. That holds while [`../README.md`](../README.md) names a playground; where
it names none, everything that builds lives here after all, and a case that has
to *fail* to build is recorded in the research note or record with no link.
The same file has the routing rule, what the playground has to provide, and the
version stamps such a document has to carry.

Nor does an experiment that needs more room than this sub-project has -- a spike
that rebuilds the project's core would take it over. That one lives on a branch
or in a repository of its own, and its research note pins the commit it ran at;
[`../README.md`](../README.md#a-measured-number-carries-its-provenance) says
what that note owes its reader. Scope is the only reason to go. An experiment
that fits here goes here.

## Retirement

An experiment is maintained while the question it serves is open, and leaves
the moment its document says the question is answered. Which document that is
depends on whether a record cites it:

- **A record cites it** -- by name, or through its research note. It serves a
  decision, is maintained while that decision is still being argued or built,
  and leaves the moment the decision is done, which the record dates exactly: in
  the `Implemented` row of its status log, or in the `Deprecated` row if the
  decision was withdrawn instead of built.
- **No record cites it.** It serves only its research note, and leaves when that
  note merges. The note is where the result rests -- its provenance lines pin the
  commit that produced every number -- and code has no resting state. A record
  that turns up later rebuilds the experiment from that commit, under the name
  it had, against code that has moved on and wants fresh numbers anyway.

Either way it goes through one of two exits:

- **Deleted** -- it measured internals the record replaced, or answered a
  question that is now closed. An entry point that no longer builds against the
  new code is deleted rather than repaired; the commit that produced its numbers
  is in the provenance lines of the research note or record that established
  them, and history keeps it. Most records here argue for changing the internals
  an experiment was measuring, so breaking is how it ends rather than a
  regression.
- **Promoted** -- it still answers a live question, which means it stopped
  being research. A measurement worth re-running is a benchmark and moves to
  the benchmark suite; something asserting a property we promise is a test and
  moves to the test suite. A project with no benchmark suite starts one the
  first time this happens; the experiment does not stay here as the substitute.

An experiment that brought a dependency with it takes that dependency out
through whichever exit it leaves by. **Deleted** means the manifest entry goes
in the same commit -- otherwise this sub-project trends toward empty while its
dependency list quietly does not, which is accumulation in the one place nobody
thinks to look. **Promoted** means the dependency is promoted too, from
temporary research scaffolding to a permanent dependency of the benchmark or
test suite, and that is a decision somebody makes rather than a side effect of
moving a file. A dependency nobody is willing to keep is an argument against the
promotion, not a footnote to it.

What an experiment never does is linger. This sub-project is a **staging area,
not an archive**: everything in it has a scheduled exit, and a healthy one
trends toward empty. Accumulation is the signal to look for something
miscategorised -- a benchmark that was never promoted, an experiment whose
record quietly landed months ago, one whose research note merged with no record
citing it, or a dependency that outlived the experiment that justified it.
