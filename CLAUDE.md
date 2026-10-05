# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with
code in this repository.

## Conventions

### Conventions have an owner

Every convention in this repository is owned by one document, and the rest are
restatements. The ADR rules are owned by
[`docs/decisions/README.md`](docs/decisions/README.md), and each directory
beside `records/` -- `notes/`, `research/`, `experiments/` -- owns its own rules
in its README; what appears here and in [`CONTRIBUTING.md`](CONTRIBUTING.md)
restates them. Change the owner first, then
every restatement. A restatement that has fallen behind is worse than none,
because it is read with the same confidence as the owner.

Anything normative -- a convention, a gate, a process, anything that changes
what someone does -- has to reach `CONTRIBUTING.md`. Write it there for a human
contributor and here for an agent where the register differs, but never
introduce a rule here that a contributor reading only `CONTRIBUTING.md` would
miss. A rule sequestered in an agent's briefing is a rule the people doing the
work never saw.

### Architecture decision records

Decision records live in [`docs/decisions/records/`](docs/decisions/records/),
one per file, `NNNN-kebab-case-title.md`. They are **living documents** --
edited to stay current rather than frozen on acceptance, because git is the log
and the document is the projection. New information goes in as a **dated
addition marked as arriving after the decision**, never as a silent revision of
the original reasoning. A record whose last row is still `Drafted` is exempt --
it has not made a decision yet, so edit it freely without dating anything, and
the test is the status row rather than whether a pull request is open. Read the
directory as the current state of this repository's decisions.
[`0001-recording-important-decisions.md`](docs/decisions/records/0001-recording-important-decisions.md)
argues for all of it; [`README.md`](docs/decisions/README.md) one level up has
the rules, and where a conflict or trade-off came up it belongs in the section
it concerns rather than a changelog at the bottom.

A record's status is a **dated transition log** in a collapsed `<details>` block
at the top of the file, with the current state in the `<summary>` line:

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

Transitions are `Drafted` (replacing a separate `Date` field), `Accepted` (a
commit before the record's PR merges), `Implemented` (the PR that finishes the
work), `Superseded by NNNN`, and `Deprecated`. The last row is the current state
and the summary restates it.

Two things the log is **not**. It is not an edit log -- only state transitions go
in it, and changes to a record's content are dated additions in the body next to
the reasoning they concern. It is not a changelog at the bottom -- it records
state, never reasoning, which is what keeps it consistent with writing conflicts
and trade-offs into the section they belong to. A row that wants a sentence of
explanation means that sentence belongs in the body.

Editing versus superseding: edit while the decision is still the decision; write
a new record when someone following the old one would now do the wrong thing.
Mechanically -- if the change can be a dated addition it is an edit; if it means
deleting a claim someone may have acted on, the old claim earns its own record.

### Refer to what is already written

**Link what another document under `docs/decisions/` already carries, and quote
only what the argument in front of you needs** -- the number, or the question in
one sentence. That covers a record and the rules it follows, a number and its
provenance line, a report and its brief. A copy goes stale on its own schedule
and is read with the same confidence as the original.

### Notes and research

What comes before a record lives beside `records/`: **notes** in
[`docs/decisions/notes/`](docs/decisions/notes/README.md), where a thought landed
before there is evidence for it, and **research notes** in
[`docs/decisions/research/`](docs/decisions/research/README.md), which carry
evidence -- an experiment's write-up, a brief and the report it produced, a
literature review, requirements gathered from another project. Neither is
authoritative; the record is.

- One per file, `YYYY-MM-DD-kebab-case-title.md`, dated the day it was started.
- The first line is italic: the date and how much has run. A research note adds
  the question it answers and where the code is -- or, when a brief asked the
  question, links the brief and gives its key question in one sentence. A brief
  is a research note beside the report it produced; its line says whom it is for
  and what it asks.
- Edit freely until it merges to the default branch. After that it stays as
  written: later information is a new note, or a dated addition at the end
  under a heading that says when it arrived --
  `## Addendum, 2026-10-12: the second run`.
- **Running the experiment a note proposes opens a research note.** Create the
  entry point and the draft research note together, under the same name, and
  link the note. An experiment that already ran -- elsewhere, or before anyone
  wrote a note -- enters as a research note directly.
- A record cites the research note when something costly to undo is about to
  depend on it. Promotion moves nothing: the next document cites the last.

### Evidence

**Any code that produces concrete data used in the argumentation of a record or
a research note must be committed somewhere a reader can run it** -- a number
quoted in either has to be re-derivable from a checkout, or the document is
asserting rather than arguing. Which home depends on two questions -- can a
reader run it from a share link, and does it fit in the sub-project?

- **Needs this project's code** → the sub-project at
  [`docs/decisions/experiments/`](docs/decisions/experiments/), as an entry
  point named after the research note or record that quotes its results
  (`2026-10-05-some-question`, `0001-some-decision`; a second one for the same
  document takes a suffix, `2026-10-05-some-question-cold-start`), run with the
  command its README names. A dependency added there goes through the same
  checks as the rest of the repository; not publishing it is no reason to skip
  that.
- **Needs anything else the playground cannot run** → the same sub-project, on
  the same terms. Most often a dependency the service does not carry: what a
  playground can run is a property of the named service, not of the language, so
  judge it per experiment. Take this route because the playground cannot run the
  thing, not because adding a dependency locally is quicker. The dependency
  leaves when the experiment does -- struck from the manifest in the same commit
  on the delete exit, deliberately adopted as a permanent dependency of the
  benchmark or test suite on the promote exit.
- **Needs only what the playground can run** (the toolchain, its standard
  library, and any dependency the service supplies) → a share link on the
  playground named in `docs/decisions/README.md`, recorded in the research note
  or record. This is
  also the only route for a build-time experiment: a case that must *fail* to
  compile or type-check cannot be an experiment entry point, because a
  sub-project that does not build breaks the project's build. If the README
  names no playground, everything that builds goes in `experiments/`, and a
  must-fail case is recorded in the research note or record as the four parts
  below with the provenance line cut to `TOOL VERSION (released DATE)` and no
  link -- as is a must-fail case needing something the playground cannot supply.
- **Needs more room than the sub-project has** -- a spike that rebuilds the
  project's core would take it over → a branch or repository of its own, written
  up in a research note. Scope is the only reason: an experiment that fits the
  sub-project goes there. The note pins a commit reachable from a ref nobody
  deletes -- the default branch's history, or a tag. Tag an unmerged spike
  branch before a note cites it, and a branch commit too if the project
  squash-merges.

**Develop the experiment locally and mint the link last.** Run it with the
local toolchain against a file in your scratch directory until it produces the
result the document will quote, and only then create the share link. Never mint
one to find out what happens, and never rework an experiment after minting --
the superseded links stay live and nobody can collect them. **At most one share
link a minute**; if a document needs more than that, ask the user to mint them
rather than minting them faster.

A playground record is four parts in a fixed order: a bolded label saying what
it demonstrates, the source in a code block, its output in a `text` block, and a
provenance line as a blockquote beneath them --

```text
> TOOL VERSION (released DATE) - output checked DATE - [PLAYGROUND](URL)
```

Keep the fences bare; the blockquote is only the provenance line, which renders
muted. Both dates are load-bearing: the released date belongs to the toolchain
that produced the quoted output, the checked date is when the link was last
confirmed to still produce it. **The version stamp is required and is checked in
review** -- a link that names a channel rather than a version drifts, and
without the stamp a reader cannot tell whether the toolchain moved or the record
was wrong. `docs/decisions/README.md` has the skeleton.

**Every number established by running something outside a playground carries a
measured provenance line in the document that establishes it** -- a research
note, or a record that argues straight from an experiment -- as a blockquote with
the command that produced it in a code block beneath, in the section that quotes
the number:

```text
> TOOL VERSION (released DATE) - measured DATE - ENTRY at REPO@COMMIT - MACHINE
```

It is checked in review like the playground stamp. A document quoting a number
another one established links the section carrying its line instead of copying
it. A number quoted from a paper or another project cites its source and says it
was not reproduced.

An experiment a record cites -- by name, or through its research note -- is
maintained while that decision is still being argued or built, and leaves the
moment the decision is done, which the record dates exactly: in the
`Implemented` row of its status log, or in the `Deprecated` row if the decision
was withdrawn rather than built. An experiment no record cites leaves when its
research note merges; the note's provenance lines keep the commit. Either way it
goes by one of two exits -- **deleted** (it measured internals the record
replaced, or answered a question now closed; an entry point that no longer
builds is deleted, not repaired, and the research note or record cites the
commit that produced its numbers) or **promoted** (it still answers a live
question, so it stopped being research: a measurement worth re-running moves to
the benchmark suite, a property we promise moves to the test suite). It never
lingers.

So do not fix up an experiment that the project's checks break on without first
checking whether the record it serves has logged `Implemented` or `Deprecated`
-- or, if no record cites it, whether its research note has merged; most
records argue for changing the internals the experiment was measuring, and
breaking is the expected end of its life. The sub-project is a staging area,
not an archive, and should trend toward empty.

### Research is evidence, not a test

**Do not write tests to motivate an ADR.** A test written against the structure
an ADR exists to replace has to be rewritten when the change lands: it guarded
nothing and only enlarged the diff. Support the argument with an experiment in
`docs/decisions/experiments/`, and let the existing suite keep checking that
behaviour did not change while the internals did.

[`CONTRIBUTING.md`](CONTRIBUTING.md) states this for human contributors, and
carries process that never appears here. Read it before changing how anything
in this repository is done -- it is not a restatement of this file, and treating
it as one is how a convention ends up in only one of the two.
