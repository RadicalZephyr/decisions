# Architecture Decision Records

An ADR records a decision that shaped the project: what we chose, what we
turned down, and why. The point is to save the next person -- usually us, a
year later -- from re-litigating a question that was already settled, or from
re-settling it without knowing what the first answer cost.

Not every change needs one. Write an ADR when the reasoning is the valuable
part: a trade-off between two workable designs, a constraint that is not
obvious from the code, a decision whose alternatives will look tempting again
later. Write it when something costly to undo is about to depend on it; until
then the thinking is a note, and the evidence is research.

## Files

One record per file in [`records/`](records/), `NNNN-kebab-case-title.md`,
numbered from `0001` in the order they are written. Numbers are never reused. A
number is an identifier, not a chronology -- it is what supersession references
point at, which is why it survives a retitle. This directory holds the rules;
what comes before a record, in [`notes/`](notes/) and [`research/`](research/);
and, in [`experiments/`](experiments/), the code that backs either. The records
themselves are everything under `records/` and nothing else.

Suggested sections, though the record should follow the argument rather than the
template:

- **Status** -- the transition log, described below. It replaces a separate
  `Date` field, because when the record was written is its first transition.
- **Context** -- the forces in play, including anything measured.
- **Decision** -- what we are doing.
- **Consequences** -- what this buys and what it costs, including the parts we
  are not happy about.
- **Alternatives considered** -- and why each was turned down.

Where discussion produced a conflict or a trade-off, write it into the section it
belongs to rather than into a changelog at the bottom. The disagreement is
usually the most useful thing in the document.

## Records are living documents

A record is **edited to stay current**, not frozen on acceptance. Read
`docs/decisions/records/` as the current state of the decisions made in this
repository.

Git is the log; the document is the projection. Every revision is kept and dated
already, and `git log -p` on a record is there for anyone who wants the diff, so
nothing is lost by keeping the file readable as it stands today.

What makes that safe is that new information arrives as a **dated addition,
marked as arriving after the decision** -- never as a silent revision of the
original reasoning:

> **2027-02-14 (after the decision):** re-running the benchmark on the next
> toolchain release closes the gap to 4%, which weakens but does not reverse
> the argument below.

Accretion, not overwriting. Without that discipline a mutable record drifts
toward what we now think we thought, and stops being evidence of a commitment
made under specific information.

A record whose last row is still `Drafted` is exempt: it is being written, not
revised, so edit it freely without dating anything. The dated-addition rule
protects a decision that has already been made from being quietly reworded in
hindsight, and a draft has not made one yet. The test is the status row, not the
pull request. The two usually coincide, because a record is drafted in the pull
request that accepts it -- but a record still `Drafted` after its pull request
closes is still a draft, and still edits freely.

[`records/0001-recording-important-decisions.md`](records/0001-recording-important-decisions.md)
argues for all of this.

## Status is a transition log

State changes are exactly the information that would otherwise live only in git,
so they go in the record. Each record opens with a collapsed log: the summary
line carries the current state, expanding it gives the history.

```markdown
<details>
<summary><strong>Status:</strong> Implemented 2026-11-03</summary>

| Date | Transition |
| --- | --- |
| 2026-09-10 | Drafted |
| 2026-09-24 | Accepted |
| 2026-11-03 | Implemented |

</details>
```

The last row is the current state and the summary restates it, so there is one
source of truth and a reader who never expands the block still knows where the
record stands. The gap between `Accepted` and `Implemented` is the interesting
number in there: a decision the code never honoured shows up as a row that never
arrived.

| Transition | Logged when |
| --- | --- |
| `Drafted` | the record is written -- this is what a `Date` field used to say |
| `Accepted` | a commit before the record's pull request merges |
| `Implemented` | the pull request that finishes the work |
| `Superseded by NNNN` | a commit before the superseding record merges |
| `Deprecated` | when the reversal is written into the record |

`Superseded` points outward, to the record that replaced this one.
`Deprecated` points inward: the summary links to the section of this record that
explains the reversal, which is why a withdrawn decision needs no successor to
stay accountable.

### Two things the log is not

**It is not an edit log.** Only state transitions go in it. Changes to the
*content* of a record are dated additions in the body, where the reasoning they
belong to is. A log that grows a row every time someone fixes a sentence is a bad
reimplementation of `git log`, and it will be skipped for the same reason the
real one is.

**It is not a changelog at the bottom.** It records state, never reasoning --
which is what keeps it consistent with writing conflicts and trade-offs into the
section they concern. If a row starts wanting a sentence of explanation, that
sentence is a dated addition in the body and the row just records that the
transition happened.

## Editing versus superseding

> Edit the record when the decision it documents is still the decision. Write a
> new one when someone following the old record would now be doing the wrong
> thing.

The mechanical form of the same test, which is usually quicker to apply:

> Can the change be written as a dated addition? It is an edit. Does it require
> deleting a claim someone might have acted on? The old claim deserves to
> survive as its own record.

New data supporting the existing choice, a widened scope, a clarity rewrite: all
edits. A reversal, or a decision that stands while its mechanism changes out from
under it: a new record. Deliberately a judgement about a reader rather than
something countable -- diff size measures effort and gets this wrong in both
directions.

## Dating claims that will not age well

Anything in a record that is true *as of* rather than true: benchmark numbers,
costs, quoted toolchain output, toolchain behaviour, third-party capabilities.
Say when it was measured, and against what.

This is not a separate convention from the version stamps on quoted output
below -- the playground's and the measured line's are this rule's strictest
instances.

## Refer to what is already written

What one document here already carries, another refers to rather than repeats.
A second copy goes stale on its own schedule and is read with the same
confidence as the first, so a reader is better served by a link and as much as
the argument in front of them needs -- the number being quoted, or the question
in one sentence.

It comes up in three places:

- **A record and its rules.** A record argues; the rules it follows live in this
  file, and it links them.
- **A number and its provenance.** The document that establishes a number
  carries its provenance line. One that quotes the number links the section
  carrying the line and does not copy it.
- **A report and its brief.** A report links its brief and gives the key
  question in one sentence; [`research/README.md`](research/README.md#briefs)
  has the shape.

## Before a record: notes and research

A record is usually the end of something. Two kinds of dated document come
before it, in directories beside `records/`:

- A **note**, in [`notes/`](notes/README.md), records where a thought landed
  before there is evidence for it -- an idea, what a conversation settled, what
  it would take to find out.
- A **research note**, in [`research/`](research/README.md), carries evidence --
  an experiment's write-up, a brief and the report it produced, a review of the
  literature, requirements gathered from another project.

A thought moves from one to the next in two steps:

1. **Note to research note**, when someone runs the experiment a note proposes.
   The entry point in [`experiments/`](experiments/README.md) and the draft
   research note start together, under one name, so an experiment exists and is
   named before any decision rests on it.
2. **To a record**, when something costly to undo is about to depend on it. The
   record cites the research note and quotes the numbers it needs -- or cites
   the note, when what it needs is the reasoning rather than a number.

An experiment that already ran -- elsewhere, or before anyone wrote a note --
enters as a research note directly. It has nothing to graduate from.

A step moves nothing. The next document cites the last, and the last stays as it
was written. Most notes and research notes never reach a record, and both can
rest where they are indefinitely, which is the difference from an experiment:
an experiment has a scheduled exit, and they do not. Neither is authoritative.
A note or research note says what was thought or found on its date; the record
says what was decided.

Both are edited freely until they merge to the default branch, and kept as
written after that. [`notes/README.md`](notes/README.md#kept-as-written) has the
rule, and why its gate is the merge where a record's is a status row.

## Evidence

Code that produces concrete data used in the argumentation of a record or a
research note -- benchmarks, memory measurements, probes into toolchain
behaviour -- **must** be committed somewhere a reader can run it. A number
quoted in either should be re-derivable by anyone with a checkout; if the
experiment only ever existed in a scratch buffer, the document is asserting
rather than arguing.

A record usually argues from a research note rather than from an experiment
directly. The research note says what ran and pins it; the record quotes the
number it needs and links the note.

Which home an experiment gets is decided by two questions -- can a reader run it
from a share link, and does it fit in the sub-project?

| The experiment needs | It lives in |
| --- | --- |
| this project's code | the sub-project in [`experiments/`](experiments/), as an entry point named after the research note or record it serves |
| anything else the playground cannot run -- most often a dependency the service does not carry | the same sub-project, on the same terms |
| only what the playground can run: the toolchain, its standard library, and any dependency the service supplies | a share link on the playground, with the source in the research note or record |
| more room than the sub-project has -- a spike that rebuilds the project's core, say | a branch or repository of its own, written up in a research note and pinned at a commit a reader can reach |

The second row is not an exception to the first and third, it is the gap between
them. "Needs the project" and "needs only the standard library" answer different
questions and never did partition the space, so an experiment wanting one
third-party package and nothing of ours fell between them with nowhere to go --
and an experiment with nowhere sanctioned to go stays in the scratch file it was
written in, which is the failure this section exists to prevent. The table is
meant to be total.

The last row is a matter of scope, and only scope. The sub-project is the
default because it holds an experiment to the same checks as everything else,
and an experiment leaves it only when it would take the sub-project over -- a
spike that rebuilds the project's core is a project of its own -- never because
somewhere else is quicker. What it gives up is the checkout: a reader can re-run
it only while the commit it ran at is still there, so the research note pins one
reachable from a ref nobody deletes. [A measured number carries its
provenance](#a-measured-number-carries-its-provenance) says how.

What a playground can run is a property of the named service rather than of the
language -- some carry a fixed set of popular packages, some carry none -- so it
is judged per experiment, and "the service does not carry X" is a third-party
capability, dated like anything else that will not age well. A service that adds
the package later does not send a live experiment back: it is on its way out
already.

Reach past the playground because it cannot run the thing, not because adding a
dependency locally is quicker. The constraint earns its keep -- a playground
cannot depend on this project, so anything that fits in one is necessarily a
minimal reproduction -- and a route taken for convenience spends that minimality
without buying anything. It is also the only route open to a build-time
experiment: a case that must *fail* to compile or type-check cannot live in the
sub-project, because a sub-project that does not build breaks the project's
build. A project with no playground loses that route entirely, and so does any
one experiment that must fail to build while needing something the playground
cannot supply; the next section says what both do instead.

A dependency taken on for an experiment is a dependency of this repository. It
lands in a manifest, goes through the same checks as everything else, and is
somebody's to audit. It leaves when the experiment does.

See [`experiments/README.md`](experiments/README.md) for the sub-project,
including how an experiment and anything it brought with it are retired.

### What the playground has to be

"The playground" is a hosted service that gives back a **durable share link**
to a snippet, **runs the snippet on demand against the language's real
toolchain** -- so a reader is one click from a running compiler or interpreter
rather than from a listing -- and **says which toolchain version ran**, so the
provenance line below can be honest. Most languages have a service that meets
all three, and this project's is:

> **Playground:** {{name and URL of the service, or "none"}}

A language with nothing that meets all three closes the playground route.
Everything that builds then goes in `experiments/`, and a case that has to
*fail* to build is recorded in the research note or record as the four parts
below with the provenance line cut to `TOOL VERSION (released DATE)` -- there is
no link to re-check, so no checked date. The same holds for a single experiment
that has to *fail* to build while needing something the playground cannot
supply: both routes are shut, and the four parts with no link are what is left.

### The playground is for recording, not for iterating

A share link is minted on someone else's infrastructure, and it publishes a
snippet that nobody can collect afterwards. So an experiment is developed
**locally** -- the toolchain against a file in a scratch directory, as many
times as it takes -- and goes to the playground only once it produces the
result the document is going to quote. Nothing is minted to find out what
happens. A link is minted to publish what already happened.

Two consequences, stated outright because the natural working rhythm violates
both:

- **No iterative development against playground resources.** An experiment
  that took four attempts should leave one link behind, not four. Reworking
  after minting turns the earlier links into litter that stays live and wrong.
- **At most one link a minute.** A document needing several is a document that
  should mint them at that pace.

### Playground experiments carry four things, not one

A share link is live, not frozen. A link that names a channel -- `stable`,
`latest`, whatever the service calls it -- rather than a version re-runs against
whatever that channel points at on the day someone clicks it: the wording of a
diagnostic will drift out from under the record, and the same code may
eventually build clean. So a playground experiment is recorded as four parts, in
this order, each doing one job:

1. **A label**, bolded, saying what the experiment demonstrates -- so a reader
   skimming knows whether to stop.
2. **The source, in a code block** -- the frozen record. Not a backup against
   the snippet being deleted; it is what the document actually argued from.
3. **Its output, in a `text` code block** -- what the quoted claim rests on.
4. **A provenance line**, as a blockquote directly beneath, carrying the
   toolchain version that produced the output, the date it was last checked,
   and the share link -- live convenience, one click to a running toolchain.

````markdown
**Experiment -- what it shows**

```LANGUAGE
...
```

```text
...
```

> TOOL VERSION (released DATE) - output checked DATE - [PLAYGROUND](URL)
````

`LANGUAGE` is the fence for whichever language the snippet is in. `TOOL` is the
command that produced the output -- the compiler, the interpreter, the type
checker -- `VERSION` is what its `--version` reports, and `PLAYGROUND` is the
service named above. The blockquote renders muted, which is the point:
provenance is needed but is not what the reader came for. Keep the fences bare
-- wrapping the whole block in a blockquote would offset it more strongly, at
the cost of `> ` on every line and source no longer copyable out of a checkout.

Both dates earn their place by answering different questions. The **released**
date belongs to the toolchain that produced the output quoted here. The
**checked** date is when someone last confirmed the link still produces it, and
is what tells a later reader how stale the live half has gone.

The version stamp is **required, and reviewers check for it**. Without it a
reader who clicks through to different output cannot tell whether the toolchain
moved or the record was always wrong. With it, that disagreement is itself the
signal that a superseding record is due.

### A measured number carries its provenance

A number established by running something anywhere but a playground -- the
sub-project, a spike branch, a repository of its own -- has no link to click. It
is re-derived by checking out a commit and running a command, so those are what
it carries: a provenance line as a blockquote, and the command beneath it, in
the section that quotes the number.

````markdown
> TOOL VERSION (released DATE) - measured DATE - ENTRY at REPO@COMMIT - MACHINE

```shell
...
```
````

`TOOL VERSION (released DATE)` is the playground line's stamp, for the same
reason: a reader who re-runs the command and gets a different number needs to
know whether the toolchain moved. `ENTRY` is the entry point or target the
command runs, and `MACHINE` is what it ran on -- wall-clock numbers depend on it,
and for counts it costs a few words. The **measured** date takes the place of
the playground's checked date: a link is re-checked by clicking it, and a
measurement re-run is a new measurement, with a line of its own.

`REPO@COMMIT` names the repository and a commit **reachable from a ref nobody
deletes** -- the default branch's history, or a tag. A spike branch that is not
going to merge gets a tag before a research note cites it. So does a branch
commit in a project that squash-merges, because an experiment that leaves when
its research note merges may never reach the default branch at all.

The line goes with the document that establishes the number -- usually a
research note, or a record that argues straight from an experiment -- and it is
**required there, and reviewers check for it**, as they check the playground's
version stamp. A document quoting a number another one established links the
section carrying its line instead of repeating it; [Refer to what is already
written](#refer-to-what-is-already-written) says why. A number from outside
this project's documents -- a paper, another project's records -- is that
source's claim: cite the source, and say it was not reproduced.

Research is evidence, not a test. An ADR arguing for different internals should
not bring tests with it -- they would be written against the structure the
record exists to replace. See
[`CONTRIBUTING.md`](../../CONTRIBUTING.md#tests-and-research-are-not-the-same-thing).
