# Decisions

A template for recording the technical decisions a project makes, and the
notes, research and experiments that lead up to them -- language-agnostic,
project-agnostic, and meant to be copied into a repository more or less as it
stands.

It was extracted from
[`sodium-rust`](https://github.com/RadicalZephyr/sodium-rust) at `2acb289`,
where [#43](https://github.com/RadicalZephyr/sodium-rust/pull/43) established
the conventions for a Rust library whose API is mandated from outside.
Everything specific to that library, and to Rust, has been taken out; what is
left is the shape of the decision and the argument for it.

Notes and research came later, from [`rfd`](https://github.com/RadicalZephyr/rfd)
at `4373ef8`, the design repository for the Bough FRP library, where an
experiment had to exist, and be named, before the decision it informed. The
measured provenance line comes from its FRP literature review.

## What is in it

| Path | What it is | On adoption |
| --- | --- | --- |
| [`docs/decisions/README.md`](docs/decisions/README.md) | Owns the rules: naming, the status log, editing versus superseding, referring rather than repeating, the path from note to record, where evidence lives | copy; name the playground |
| [`docs/decisions/records/0001-recording-important-decisions.md`](docs/decisions/records/0001-recording-important-decisions.md) | The argument for the rules, written as the first record in its own format | copy; date it, add your context |
| [`docs/decisions/notes/README.md`](docs/decisions/notes/README.md) | Where a thought lands before there is evidence for it, and how a note is kept | copy |
| [`docs/decisions/research/README.md`](docs/decisions/research/README.md) | Documents that carry evidence, briefs, and what a research note owes its reader | copy |
| [`docs/decisions/experiments/README.md`](docs/decisions/experiments/README.md) | The sub-project for experiments a research note or record cites, and the rule that retires them | copy; wire the sub-project into your build |
| [`CONTRIBUTING.md`](CONTRIBUTING.md) | The rules restated for a human contributor | merge into yours |
| [`CLAUDE.md`](CLAUDE.md) | The rules restated for an agent, plus the rule that keeps restatements honest | merge into yours |

This file is the one thing here that is not part of the template.

## The decision, in short

- **Records are living documents.** Edited to stay current rather than frozen
  on acceptance, because git is the log and the document is the projection.
  New information arrives as a dated addition marked as arriving after the
  decision, never as a silent revision of the original reasoning.
- **Status is a dated transition log, not a single field.** `Drafted` /
  `Accepted` / `Implemented` / `Superseded by NNNN` / `Deprecated`, in a
  collapsed block with the current state restated in the summary line. The gap
  between `Accepted` and `Implemented` becomes visible: a decision the code
  never honoured shows up as a row that never arrived.
- **Edit while the decision is still the decision; supersede when someone
  following the old record would now do the wrong thing.** Diff size measures
  effort and gets this wrong in both directions.
- **Notes and research come before a record.** A note records where a thought
  landed before there is evidence for it; a research note carries the evidence.
  Running the experiment a note proposes opens a research note, and the
  experiment starts under its name -- so an experiment exists before the
  decision it informs. Promotion moves nothing, both can rest indefinitely, and
  both are kept as written once they merge.
- **Evidence must be runnable.** Code producing a number a record or research
  note argues from is committed where a reader can run it, routed by whether a
  reader can run it from a share link: a playground link with the source and a
  toolchain version stamp when the service can run it, the experiments
  sub-project when it cannot -- this project's code, or a dependency the service
  does not carry. Only an experiment too big for the sub-project lives
  elsewhere, pinned at a commit a reader can still reach. A number run outside
  a playground carries a measured provenance line where it is established, and
  is linked, not copied, everywhere else.
- **Research is evidence, not a test.** A record arguing for different
  internals does not bring tests written against the structure it exists to
  replace.
- **Every experiment has a scheduled exit.** It leaves when its record is done,
  or when its research note merges if no record cites it -- deleted when the
  internals it measured are gone, promoted to the benchmark or test suite when
  it still answers a live question. The sub-project is a staging area, not an
  archive.

## Adopting it

For a new project, or one with no `docs/decisions/` yet:

1. Copy `docs/decisions/` in whole.
2. Merge the sections of `CONTRIBUTING.md` and `CLAUDE.md` into the project's
   own, or take the files as they are if it has none.
3. Fill every `{{slot}}` -- `grep -rn '{{' docs CONTRIBUTING.md CLAUDE.md`
   finds them all, and there are few: the playground; how the experiments
   sub-project joins the build and what it is called, where its entry points
   live and what extension they carry, and the command that runs one; and the
   record's date and context.
4. Set the sub-project up in the project's language. It depends on the project
   by path, is never published, and is covered by the same lint and test checks
   as everything else. Leave it empty; it fills when a record needs it. It has
   no tests of its own, so a test runner that fails on an empty collection
   stays off it until it has something to run.
5. Open the pull request with the record in `Drafted`. Log `Accepted` in a
   commit before it merges and `Implemented` in the same pull request, as the
   record's own opening note says to.

For a project that already records decisions some other way, the same steps
apply and the existing records are the first question. Renumbering them is
usually wrong -- a number is an identifier, and anything that already points at
one should keep working -- so move them under `records/` as they are and give
each a status block that logs what actually happened to it. The template's
record then takes the next free number instead of `0001`: nothing points at it
yet, so it is the one renumbering that is right, and `grep -rn 0001-recording`
finds the links to update. An imported record was written under a convention
that never revised it, so open it with a dated addition saying so -- its old
date is its `Drafted` row, and its body is a snapshot until someone revisits it.

For a project that adopted this template before notes and research were part of
it, they arrive as an edit to its `0001`, not as a new record. Its `0001` has
made its decision, so the new reasoning goes in as dated additions beside the
sections it concerns -- by the record's own test that is an edit, since it
widens the scope and deletes nothing anyone acted on. Experiments already named
after records stay valid. Copy `notes/` and `research/`, and bring
`docs/decisions/README.md`, `experiments/README.md`, `CONTRIBUTING.md` and
`CLAUDE.md` up to date with this repository's.

## What was deliberately left out

`sodium-rust` layers three things on top of this that belong to that project
rather than to the decision. Each is worth adding back when it applies:

- **A known-gap test convention.** That library's semantics are mandated by an
  external specification, so a record arguing that the implementation diverges
  from it is a bug report rather than a design preference. That one exception
  *does* get a test -- written against the specification so it stays correct
  after the fix, and marked `#[ignore = "ADR-NNNN: ..."]` so CI stays green
  while the reason prints on every run. Any project implementing a spec, a
  protocol or a port will want the same.
- **A rejected alternative specific to Rust.** Snapshot tests of compiler
  diagnostics (`trybuild`) were considered as evidence and turned down. The
  general form of that argument survives in the record; the specifics did not.
- **The Rust instantiation.** The Rust Playground as the playground, `rustc` as
  the tool that stamps a record, and a Cargo workspace crate named
  `adr-research` as the sub-project. That repository's
  [`docs/decisions/`](https://github.com/RadicalZephyr/sodium-rust/tree/main/docs/decisions)
  is the fully worked Rust instance of this template.

`rfd` layers two more that belong to it rather than to notes and research:

- **A book that carries every note.** `rfd` renders its RFDs, notes and research
  with mdbook, and includes each note into the book, so a link from an RFD to a
  note is checked on every build. A project that renders its docs will want the
  links into `notes/` and `research/` checked the same way; how depends on the
  renderer.
- **Decisions in a repository apart from the code.** `rfd`'s RFDs live in a
  repository of their own and use their own states, so its experiments
  sub-project is a separate repository too, every measured provenance line names
  it, and an experiment retires when its RFD reaches `committed` or `abandoned`
  rather than `Implemented` or `Deprecated`.

## Placeholders

`{{like this}}` marks something the adopting project fills in once. Uppercase
words inside a format line -- `TOOL VERSION (released DATE)` -- are filled in
per record or research note, not per project, and stay as they are.

**Here they stay unfilled, and `0001` stays `Drafted`.** This repository is
where the convention is kept and refined, and it is never adopted here: no
project context fills the record's `Context` slot, and no pull request merges to
log its `Accepted` row. An unfilled date in a status log reads like an
oversight, so it is worth saying outright -- filling these in is the mistake,
not the fix. This is the only file that says so, because it is the only one not
copied into an adopting project, where the opposite instruction is the right
one.

That leaves `0001` permanently `Drafted`, which looks like the failure its
own
[Alternatives considered](docs/decisions/records/0001-recording-important-decisions.md#alternatives-considered)
section rejects: records "permanently marked 'draft' -- a symptom of having no
criterion for when a draft ends." The resemblance stops at the surface. That
failure is a draft nobody can end because nothing says when it ends. Here the
criterion is exact and deliberately never met: acceptance is a merge gate, and
this repository has nothing to gate. The criterion lives in the adopting
repository, and so does the transition.
