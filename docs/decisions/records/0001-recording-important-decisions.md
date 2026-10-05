# 0001 -- Recording important decisions

<details>
<summary><strong>Status:</strong> Drafted {{YYYY-MM-DD}}</summary>

| Date | Transition |
| --- | --- |
| {{YYYY-MM-DD}} | Drafted |

</details>

*This record is its own first worked example. Because the rules it describes are
implemented by the same pull request that adopts them, the pre-merge commit logs
`Accepted` and `Implemented` on the same date -- the one case where those two
transitions collapse, and a demonstration that the log can say so plainly where
a single status field could not.*

*The argument was made once, elsewhere, and is adopted here as it stood; "we"
in the sections below is whoever made it, not a claim about this project's
past.*

## Context

This repository had nowhere to put reasoning. The code carries some of it in
comments, and a changelog records what changed without saying why. Neither
survives the question "we already settled this a year ago -- what did the first
answer cost?"

Three things constrain where it can go. The first is that most of the decisions
a project makes are about things it intends to change -- its internal
implementation, or how a requirement it does not control should be spelled in
this language. Reasoning about a structure that is about to be replaced turns
out to constrain how the reasoning can be recorded.

The second is what a decision leaves behind. Prototypes, measurements and probes
with nowhere to live end up in the test suite, where they fail for reasons
unrelated to the project ever after. That is the concrete failure this record
exists to prevent.

The third is what comes before a decision. Experiments are run to inform one,
so they exist before the record that will cite them, and so do the ideas that
prompted them. A convention that hangs everything off a record has nowhere to
put either until the decision is already made -- which is to say, nowhere to
put it while it is doing its job.

{{What forced the question in this project, if anything.}}

## Decision

Decisions live in `docs/decisions/records/`, one record per file, and they are
**living documents**: a record is edited to stay current rather than frozen at
the moment it was written.

What comes before a decision lives beside the records: **notes**, in
`docs/decisions/notes/`, for where a thought landed before there is evidence for
it, and **research notes**, in `docs/decisions/research/`, for the evidence.
Both are dated and kept as written once they merge. An experiment is named after
the research note that writes it up, so it can exist before any decision rests
on it.

The rules themselves live in [`README.md`](../README.md) one level up, beside
`records/`, and in [`CONTRIBUTING.md`](../../../CONTRIBUTING.md) for
contributors. This record holds the argument, what we turned down, and what we
are still unsure about. Keeping the two apart is deliberate: a record that
restates its own rulebook goes stale the first time the rulebook changes. The
same holds between any two documents here -- what one carries, another refers
to, quoting only what its own argument needs.

## Why living documents rather than immutable records

The usual ADR practice freezes a record on acceptance and supersedes it with a
new one. We are not doing that, and the reason is sharper than "immutability is
a computer-science habit applied to a human process."

An append-only log is half of a pattern. Event-sourced systems work because
they keep the log *and* materialise a view from it. Immutable ADR practice
keeps the log and then tells you to read the log -- so reconstructing the
current state means walking a supersession chain, which in practice nobody
will ever do.

The synthesis was already available: **git is the log, the document is the
projection.** Making records mutable does not discard history, it finally
builds the view that makes the history usable. Git keeps every revision, dated,
and `git log -p` on a record is there for anyone who wants the diff.

The strongest argument for immutability is not preservation but cost --
write-once records need no maintenance. That is an accounting trick. A frozen
record that has gone stale still costs; the cost just moves to read time and is
charged to every reader instead of once to an author. For a directory we want
to function as documentation, read cost is the one that matters.

## Dated strata are what make mutability safe

The real hazard of editable records is not lost history, it is **hindsight
contamination**: once a record can be revised, it drifts toward "what we now
think we thought," and stops being evidence of a commitment made under specific
information. Git does not protect against this, because nobody reads git.

So new information enters a record as a **dated addition, marked as arriving
after the decision** -- the practice [the ADR community calls
timestamps](https://github.com/architecture-decision-record/architecture-decision-record).
That single constraint converts "living document" from overwriting into
accretion, which preserves exactly what immutability was protecting while
leaving the file readable as current state.

The same discipline applies to any claim that will not age well: benchmark
numbers, toolchain behaviour, quoted diagnostics. The version stamp on quoted
toolchain output, in *Evidence* below, is this rule's first and strictest
instance.

## The status field

A record's status is a **dated transition log**, collapsed at the top of the
file, with the current state in the summary line. [`README.md`](../README.md)
has the shape and the set of transitions.

The reasoning that got us here is worth keeping, because we tried a simpler thing
first. The original design was a single `Status` line that recorded *deviation
from the expected lifecycle*: made-and-built was the terminal state and carried
no line at all, on the argument that a field answering "what is unusual about
this record?" does not need to say "nothing".

That argument holds, and it is still why we do not want a bare `Implemented`
line on forty records. But it defended an asymmetry rather than removing one, and
it left a real cost: absence cannot distinguish "done" from "the author forgot."

The log dissolves both problems instead of trading between them. `Implemented`
becomes a row rather than an absence, so the special case stops existing; and
because every row is dated, the line is never noise -- `Accepted 2026-09-10` then
`Implemented 2026-11-03` is a fact about how this project actually moves.

It is also the more consistent application of our own principle. We adopted
living documents because forcing a reader to reconstruct state from git is the
failure we were trying to escape, and then put state transitions in exactly that
place. The gap between `Accepted` and `Implemented` is the hot-air metric a
decisions directory most needs: a decision the code never honoured shows up as a
row that never arrived, visible in the file rather than derivable from
`git log`.

## Editing versus superseding

The rule we needed was when to revise a record and when to write a new one that
supersedes it. Our first instinct was to ground it in diff size -- substantially
rewriting a record means writing a new one instead.

That does not survive contact. Changing "we will use X" to "we will not use X"
is four characters and unambiguously a new decision; rewriting an entire record
for clarity without touching a conclusion is a total diff and unambiguously not.
Diff size measures effort, and correlates with the thing we care about in
neither direction. It is also, ironically, the *more* rigid option: counting
lines looks precise while being wrong.

The rule is grounded in consequence to a reader instead:

> Edit the record when the decision it documents is still the decision. Write a
> new one when someone following the old record would now be doing the wrong
> thing.

With a mechanical companion that makes this rule and the dated-strata rule
enforce each other:

> Can the change be written as a dated addition? It is an edit. Does it require
> deleting a claim someone might have acted on? The old claim deserves to
> survive as its own record.

`Superseded` and `Deprecated` differ in where the reasoning lives, not whether
it exists. **Superseded points outward** -- the argument is in the successor
record. **Deprecated points inward** -- the argument is a dated addendum in the
record itself, and the status line links to it.

We looked for a case where a record would be deprecated with no reasoning worth
capturing anywhere, and could not construct one. Even the degenerate case --
accepting a decision and then simply not doing it -- has a reason attached, and
that reason is a dated addendum rather than a whole new record, because nothing
was built for anyone to have acted on. A useful intuition, though not a rule:
`Deprecated` is mostly what happens to a record that died in the not-yet-
implemented state, and `Superseded` is what happens to one that shipped.

## Notes and research come before the record

We started with no place for anything before a record. An experiment was an
entry point named after the record it served, and retired with it -- which
assumed the record came first. It does not. An experiment is run to inform a
decision, so it exists before the decision is written, and a convention that
cannot name it until then has nowhere sanctioned to put it while it is doing its
job. That is the scratch-file failure again, one step earlier.

So two kinds of dated document come before a record. A **note** records where a
thought landed before there is evidence for it. A **research note** carries the
evidence: an experiment's write-up, a brief and the report it produced, a review
of the literature, requirements gathered from another project. Running the
experiment a note proposes is what opens a research note, and the entry point
starts with it, under its name. A record cites the research note when something
costly to undo is about to depend on it. An experiment that already ran --
elsewhere, or before anyone wrote a note -- enters as a research note directly.

**Promotion moves nothing.** The next document cites the last, and the last
stays as it was written. That is the dated-strata rule applied before a
decision: a note is evidence of where a thought stood on its date, and a note
revised to match where the thought went next is evidence of nothing.

**Notes and research notes can rest; experiments cannot.** Most notes never
reach a record, and that is not a failure -- a dated record of a thought costs
nothing to keep and nothing to read past. An experiment is code under the
project's checks, and code has no resting state. So an experiment no record
cites leaves when its research note merges. The research note keeps the result
and pins the commit that produced it; a record that turns up months later
rebuilds the experiment from that commit, against code that has moved on and
wants fresh numbers anyway. An experiment a record does cite leaves with the
record, as before.

**Drafting ends at the merge.** A record's drafting ends at a status row, and a
note has none -- adding one to every idea would cost more than most ideas are
worth. The merge is the gate a note does have. After it, later information is a
new note, or a dated addition at the end of the old one: at the end rather than
beside the reasoning it concerns, unlike a record's, because a record is a
projection kept current and a note is a snapshot, which appending keeps whole.

**A brief belongs beside its report.** Research handed to someone else -- a
person, another session, an agent -- starts as a brief: the question as it was
put, and what was to be taken as settled. A report's answer is only legible
against that, so the brief is a research note too, and the report's opening line
links it, with the key question in one sentence rather than a restatement.

**Every note says what has run.** Each opens with one italic line: its date and
how much of it has run, and for a research note the question it answers and
where the code is. It does for these documents what the status summary does for
a record -- a reader knows at a glance whether they are reading an idea or
evidence, which is the one distinction between the two directories.

## Evidence

A number quoted in a record or a research note has to be re-derivable by a
reader, or the document is asserting rather than arguing. Experiments are routed
by whether a reader can run one from a share link -- anything the playground can
run goes in a share link recorded in the document, and anything out of its reach
goes in the [`experiments/`](../experiments/) sub-project beside `records/`: this
project's code, and anything else the service cannot run, most often a
dependency it does not carry. One more route is for scope: an experiment that
would take the sub-project over lives on a branch or in a repository of its own.
[`README.md`](../README.md) has the routing table, what a playground has to
provide to qualify, and what a project with none does instead;
[`experiments/README.md`](../experiments/README.md) has the retirement rule.

Four things about that arrangement are decisions rather than mechanics.

**The playground constraint is a feature.** A playground cannot depend on this
project, so anything that fits in one is necessarily a minimal reproduction. It
is also the only route open to a build-time experiment, because a sub-project
that does not build breaks the project's build.

The alternative for a build-time result is a snapshot test: capture the
toolchain's diagnostic once and assert it on every run. We turned that down for
ADR evidence. Such a test is written against diagnostic wording the toolchain
does not promise to keep stable, so it asserts something the project neither
controls nor set out to promise. Pinning the wording is not incidental to the
technique, it *is* the technique: a snapshot with nothing to compare against is
not a snapshot, and a test that only asserts the build failed demonstrates
nothing a reader can quote. And the cost lands on contributors rather than on
CI: anyone whose toolchain is a release ahead of or behind the one the wording
was captured from gets a red build out of a diff they did not write. Evidence
near-certain to break buys nothing a playground link does not.

**A share link is live, not frozen**, which is why a playground record is
written as four parts rather than one. A link that names a channel rather than
a version re-runs against whatever that channel points at on the day it is
clicked. The link is convenience, the code block is the frozen record, and the
toolchain version that produced the quoted output is what makes the record
falsifiable later. [`README.md`](../README.md) states the shape as the rule.

That last requirement is there because the failure it prevents is quiet. A
diagnostic quoted from one toolchain release can differ from the next by a
single word, and whoever re-records it on the older release commits that
wording in a diff nobody looks at twice. Without a version stamp, a reader
cannot tell whether the toolchain moved or the record was always wrong.

**Out of the repository is a matter of scope, never convenience.** The
sub-project is the default because it holds an experiment to the same checks as
everything else. A spike that rebuilds the project's core would take it over, so
that one lives elsewhere -- and what it gives up is the checkout. A spike branch
not meant to merge is exactly the kind that gets pruned, and the evidence goes
with it. So the research note pins a commit reachable from a ref nobody deletes,
and an unmerged spike is tagged before anything cites it.

**A measured number carries a version stamp too.** A number from a run outside
a playground has no link to click; it is re-derived by checking out a commit and
running a command. So it carries a provenance line -- the toolchain version, the
date it was measured, the entry point and commit, the machine -- with the
command beneath. It guards against the playground stamp's failure in a
different place: a reader who re-runs the command and gets a different number
cannot otherwise tell whether the toolchain moved, the machine did, or the
document was always wrong. The line goes with whichever document establishes
the number, a research note or a record, and a document that quotes the number
links there instead: a copied provenance line is a second place for it to be
wrong, and the copy is the one nobody re-checks.

## Tests are not evidence

A decision arguing for different internals does **not** bring tests with it. Such
a test is written against the structure the record exists to replace, so landing
the change means rewriting it: it guarded nothing and only enlarged the diff. The
existing suite is what checks that behaviour did not change while the internals
did; the argument for changing them belongs in an experiment.

## Alternatives considered

**Immutable records with a supersession chain.** The conventional practice, and
what we have used on other repositories. Rejected because it optimises for the
writer at the reader's expense, and because in practice it produced records
permanently marked "draft" -- a symptom of having no criterion for when a draft
ends. Making acceptance a merge gate dissolves that.

**Diff size as the edit-versus-supersede criterion.** Rejected above. It
measures effort rather than consequence and fails in both directions.

**Collapsing the status field entirely**, with implementation state inferred
from the code. Rejected: it reproduces the failure it was meant to fix, making a
reader reconstruct current state from somewhere else. It also assumed a reader
looking at a pull request on GitHub, where the surrounding interface supplies
the context. A record is frequently read from a local checkout at a commit --
especially in a repository where the reason to check out is to run the
experiments a record cites -- and there it must speak for itself.

**A single `Status` line with no marker for the implemented state.** Our own
first design, replaced by the transition log before any record used it. The
argument for it is in *The status field* above; what it could not do was
distinguish a finished record from a forgotten one, and it kept state
transitions in git after we had just finished arguing that git is where state
goes to be ignored.

**Experiments named only after records.** Our own first design, replaced by
research notes. It put an experiment after the decision it was meant to inform,
which left the experiment that matters most -- the one a decision has not been
made on yet -- with nowhere sanctioned to live.

**Keeping the sub-project for records, and running earlier experiments wherever
they ran.** It kept the retirement rule exactly as it was, at the cost of
putting every experiment that informs a decision outside the checks the
sub-project exists to apply. Leaving the repository is for scope, not timing.

**An experiment that stays until a record takes it over.** Kinder to a long gap
between research and decision, which is common when a record is written only as
something costly to undo comes to depend on it. Rejected because the sub-project
stops trending toward empty: an experiment nobody decided on that measures
something stable would stay forever, and accumulation would stop meaning that
something was miscategorised.

**Ending a note's drafting at its first citation.** It protects exactly the
right thing -- whoever cited it -- but a citation from another repository is
invisible to whoever is editing, and a rule nobody can check is not one.

**Notes outside `docs/decisions/`.** Some notes are not about a decision at all,
and may never be. Rejected because here a note is a way into a decision, and one
that never becomes one can rest where it is.

**A plain `## History` section at the bottom of the record** rather than a
collapsed block at the top. Rejected because it puts the current state at the
opposite end of the document from where a reader starts, and duplicates it
between a top-line status and a bottom-row log. The collapsed block keeps one
source of truth and puts it first.

## Consequences

Living records converge on **explanation**. A record that is continuously
updated becomes a description of why the architecture is the way it is, which is
[Diataxis](https://diataxis.fr/) explanation in all but name. That is the
mechanism by which this directory becomes useful documentation rather than an
archive, not drift to be corrected.

The shape of the directory is a design signal. Few records edited often means a
small number of load-bearing decisions being refined -- healthy. Many records
edited rarely means the decisions are independent, and the directory is an
archive that will want an index. **Many records edited often is the warning**:
decisions are entangled, so the code's seams do not match the decisions' seams.
It looks like health from inside, because everything is current and active. The
first place to look is whichever boundary nearly every decision touches on both
sides.

`experiments/` should trend toward empty. Every experiment in it has a scheduled
exit -- when its record is done, or when its research note merges if no record
cites it -- and leaves by deletion once the internals it measured are gone, or
by promotion to the benchmark suite or the test suite once it turns out to
answer a live question. Accumulation means something was miscategorised.

`notes/` and `research/` grow, and that is not accumulation. A directory of
dated notes is a history of what was thought and found, and it costs nothing to
read past. The signal to watch there runs the other way: a record quoting a
number that nothing pins.

## Open questions

**How explanation and decisions divide.** In a codebase that already exists
when this convention arrives, the reasoning behind its current shape may not be
in the repository's history at all. An explanation document would often have to
say "this is what the code does, and we do not know why it came to be this way."
Deriving that history from the code alone would be fabrication.

So explanation and decisions will overlap, from opposite directions: explanation
describes a present nobody can account for, while decisions accumulate the
richer history from here forward. When a decision is later made about something
an explanation document covers, some of that document is context for the record
-- but probably not all of it, and the record should not swallow the whole
thing.

We are deliberately not settling this in advance. The case in miniature is
settled: a file of undigested observations with no decision attached is a note.
It may graduate into research or a record, or legitimately stay where it is, and
that last option is still the disanalogy with `experiments/` -- an experiment
has no valid resting state, a note does. Whether a note can graduate into
explanation too, and where a research note ends and an explanation document
begins, are still open, and we will have a better feel for both with a real
case in front of us.
