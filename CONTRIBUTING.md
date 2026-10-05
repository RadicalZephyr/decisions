# Contributing

## Architecture decision records

Decisions whose *reasoning* is the valuable part get a record in
[`docs/decisions/records/`](docs/decisions/records/). Records are living
documents -- edited to stay current, with new information added as a dated
addition marked as arriving after the decision, never as a silent revision of
the original reasoning. Read the directory as the current state of the project's
decisions, and reach for `git log -p` when you want to know how it got there.
[`docs/decisions/README.md`](docs/decisions/README.md) has the conventions;
[`0001-recording-important-decisions.md`](docs/decisions/records/0001-recording-important-decisions.md)
argues for them.

A record whose last row is still `Drafted` is the exception: it has not made a
decision yet, so edit it freely without dating anything. The test is the status
row rather than the pull request -- a record still `Drafted` after its pull
request closes is still a draft.

A record's status is a dated transition log in a collapsed block at the top of
the file, and each row is a transition you log deliberately:

| When | Row you add |
| --- | --- |
| writing the record | `Drafted` |
| a commit before its pull request merges | `Accepted` |
| the pull request that finishes the work | `Implemented` |
| a commit before a superseding record merges | `Superseded by NNNN` |
| when a reversal is written into the record | `Deprecated` |

`Superseded` points outward, to the record that replaced this one.
`Deprecated` points inward: the summary links to the section of this record
explaining the reversal, which is why withdrawing a decision needs no successor
record to stay accountable. A decision abandoned before anything was built on
it is usually `Deprecated`; one that shipped and was later replaced is
`Superseded`.

The current state is the last row, restated in the summary line so a reader who
never expands the block still knows where the record stands. The gap between
`Accepted` and `Implemented` is the number worth watching -- a decision the code
never honoured is a row that never arrived.

Only state transitions belong in the log. A change to what a record *says* is a
dated addition in the body, beside the reasoning it concerns.

## Refer to what is already written

What one document under `docs/decisions/` already carries, another links rather
than repeats -- a rule, a number's provenance line, a brief's question. Quote
only what the argument in front of you needs: the number, or the question in one
sentence. A copy goes stale on its own schedule, and it is read with the same
confidence as the original.

## Notes and research

A record is usually the end of something. What comes before it goes in two
directories beside `records/`, and neither is authoritative:

- [`docs/decisions/notes/`](docs/decisions/notes/README.md) -- where a thought
  landed before there is evidence for it: an idea, what a conversation settled,
  what it would take to find out.
- [`docs/decisions/research/`](docs/decisions/research/README.md) -- documents
  that carry evidence: an experiment's write-up, a brief and the report it
  produced, a review of the literature, requirements gathered from another
  project.

Both are one document per file, named `YYYY-MM-DD-kebab-case-title.md` for the
day it was started, and both open with one italic line giving the date and how
much has run. A research note's line adds the question it answers and where the
code is. If a brief asked the question, the line links the brief and gives its
key question in one sentence instead of restating it. A brief is a research note
too, beside the report it produced, and its own line says whom it is for and
what it asks.

Edit either freely until it merges to the default branch. After that it is kept
as written: later information is a new note, or a dated addition at the end,
under a heading that says when it arrived. The gate is the merge, not a status
row, because a note has no status row.

When you run the experiment a note proposes, open the research note at the same
time. The entry point in
[`docs/decisions/experiments/`](docs/decisions/experiments/) takes the research
note's name, so the experiment exists before any decision rests on it, and the
research note links the note that proposed it. If no record cites the
experiment by the time the research note merges, the experiment leaves then --
[`docs/decisions/experiments/README.md`](docs/decisions/experiments/README.md#retirement)
has the exits -- and the research note keeps the result and the commit that
produced it. An experiment that already ran, elsewhere or before anyone wrote a
note, comes in as a research note directly.

Write a record when something costly to undo is about to depend on what a note
or research note found, and cite it. Nothing moves when a thought graduates: the
next document cites the last.

## Evidence

Code that produces concrete data used in the argumentation of a record or a
research note has to be committed somewhere a reader can run it -- a number
quoted in either has to be re-derivable from a checkout, or the document is
asserting rather than arguing. The routing question is whether a reader can run
it from a share link. An experiment the playground can run -- the toolchain, its
standard library, and any dependency the service supplies -- goes in a share
link recorded in the research note or record. Anything out of that reach goes in
the sub-project at [`docs/decisions/experiments/`](docs/decisions/experiments/)
as an entry point named after the research note or record that quotes its
results, with a suffix after that name when one document has several: this
project's code, and anything else the service cannot run, most often a
dependency it does not carry. Take the sub-project because the playground
cannot run the thing, not because adding a dependency locally is quicker -- a
dependency taken on for an experiment is a dependency of this repository, and it
leaves when the experiment does.
[`docs/decisions/README.md`](docs/decisions/README.md#what-the-playground-has-to-be)
names the playground and says what one has to provide. Where it names none,
everything that builds goes in the sub-project, and a case that has to *fail*
to build is recorded in the research note or record with no link -- as is a case
that has to fail to build while needing something the playground cannot supply.

An experiment too big for the sub-project -- a spike that rebuilds the project's
core would take it over -- lives on a branch or in a repository of its own, and
is written up in a research note. Scope is the only reason to go there; an
experiment that fits in the sub-project goes in the sub-project. What it gives
up is the checkout, so the research note pins a commit reachable from a ref
nobody deletes: the default branch's history, or a tag. Tag a spike branch that
is not going to merge before a note cites it, and tag a branch commit too if this
project squash-merges.

The playground is for recording a result, not for finding one. A share link is
minted on someone else's infrastructure, and it publishes a snippet that nobody
can collect afterwards. So an experiment is developed **locally** -- the
toolchain against a file in a scratch directory, as many times as it takes --
and goes to the playground only once it produces the result the document is
going to quote. Nothing is minted to find out what happens; a link is minted to
publish what already happened.

Two consequences, stated outright because the natural working rhythm violates
both. **No iterative development against playground resources**: an experiment
that took four attempts should leave one link behind, not four, and reworking
after minting turns the earlier links into litter that stays live and wrong. And
**at most one link a minute** -- a document needing several is a document that
should mint them at that pace.

A playground experiment is written as four parts in a fixed order: a bolded
label saying what it demonstrates, the source in a code block, its output in a
`text` block, and a provenance line as a blockquote beneath them, reading
`TOOL VERSION (released DATE) - output checked DATE - [PLAYGROUND](URL)`.
The fences stay bare. Both dates matter: the released date belongs to the
toolchain that produced the quoted output, the checked date is when someone last
confirmed the link still produces it.
[`docs/decisions/README.md`](docs/decisions/README.md#playground-experiments-carry-four-things-not-one)
has the skeleton.

A number established by running something outside a playground has no link to
click, so it carries a provenance line too: a blockquote reading
`TOOL VERSION (released DATE) - measured DATE - ENTRY at REPO@COMMIT - MACHINE`,
with the command that produced it in a code block beneath, in the section that
quotes the number. The line goes in the document that establishes the number --
a research note, or a record that argues straight from an experiment -- and a
document quoting that number links the section carrying the line instead of
copying it. A number from outside this project's documents -- a paper, another
project -- cites its source and says it was not reproduced.
[`docs/decisions/README.md`](docs/decisions/README.md#a-measured-number-carries-its-provenance)
says what each part is for.

**Reviewing a record includes checking two things.** First the version stamp: a
playground link re-runs against whatever its channel points at when it is
clicked, so any toolchain output quoted in a record has to say which version
produced it, and a record carrying none cannot be checked later. Second the
status log, which needs its `Accepted` row before the record merges. Neither
should be approved on autopilot.

**Reviewing a research note includes checking the same stamps** -- a version
stamp on any playground output it quotes, and a measured provenance line on every
number it establishes, at a commit a reader can still reach. A record that
establishes a number itself is checked for the same line; one that quotes a
research note's number needs the link, not a copy.

## Tests and research are not the same thing

An ADR arguing for a different internal design should not be accompanied by
tests. Such a test is written against the very structure the ADR exists to
replace, so landing the change means rewriting it -- it never guarded
anything, it just made the diff bigger. Support that argument with an
experiment in [`docs/decisions/experiments/`](docs/decisions/experiments/)
instead, and let the existing suite go on checking that behaviour did not
change while the internals did.

Research is evidence, not a test. It is not expected to keep passing, and it
has a scheduled exit:
[`docs/decisions/experiments/README.md`](docs/decisions/experiments/README.md#retirement)
says when and where it goes.
