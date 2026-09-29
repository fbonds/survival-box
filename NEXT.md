# Where we are

> **Read this before acting on anything below.** Every fact in this file is dated and may
> be stale. It records what was true when it was checked, not what is true now. Anything
> outside this repo moves without a commit here to show it, so re-check it first:
>
> ```sh
> git ls-remote --heads --tags origin | tail -5   # what the remote actually has
> git rev-list --left-right --count origin/main...main   # left = behind, right = unpushed
> git status -sb
> # nothing is published or deployed yet; replace this line with that check when something is
> ```
>
> The rest of the checks, and the procedure for updating this file at the end of a session,
> are in `HOWTOSAVESTATE.md`. Stored memories load before any file and go stale the same
> way: check them against the repo rather than trusting them.

Updated 2026-09-29. Working state for Survival Box. `README.md` is currently the design,
the roadmap and the public documentation in one file: the vision and architecture
sections are the design, "Initial experiments" and "Initial success criteria" are the
roadmap, and the whole file is what the public reads on GitHub. This file is none of
those. It records what is true now and what is decided.

## Standards

How work is done here, and why. Each one carries its reason, or it reads as taste and gets
dropped under time pressure. In this template the reason is the failure that produced it,
except rule 4, which carries two occasions it caught something before it was built, and rule
10, which says plainly that no failure sits behind it.

The examples below come from the project this kit was built in. **Replace each with your own
the first time this project produces one**, since a local failure argues better than a
borrowed one. Do not delete an example and leave a bare rule behind.

**1. No check counts as verified until it has been shown failing on deliberately broken
input, and the report says what was broken.** A check that cannot fail is not evidence, and
it is indistinguishable from a passing one in the report. Three checks were once reported as
verification when none of them could fail, and a guard test written to catch a drifted
dependency tree was invalid because the build regenerated what had been stripped to break
it.

**2. Never verify through a path that can resolve to your own development copy.** The
failure is not that the check was wrong; it is that the check never looked at the thing
being shipped. A linked or globally installed copy, a symlinked directory, a dev server
still running, a mounted volume, a cached build, a tool that falls back to a local default:
each of these will answer as though it were the artifact. Name the path explicitly, resolve
it, and prove the resolved path is the one you meant, inside the location you meant. A
release once shipped a broken binary through four separate checks, every one of which
silently resolved to the working copy rather than to what users would download.

**3. Check claims against the artifact, not the description of it.** The published file, the
fetched page, the built package. Not a commit message, a changelog, a dashboard label, or
this file. A published page told readers that a shipped file had five entries. It had eight.
The page was correct when written and was falsified by a build change nobody re-read it
against.

**4. Proposal before code, for anything with a surface.** A command, a flag, an API, a
schema, a page, a public string. Show the surface and wait for approval before writing the
implementation. It is cheaper to reject a design than a branch, and it has already caught a
feature that should not have existed and a scope that would have overpromised, both before
anything was built.

**5. Stage explicitly.** Never `git commit -am`, `git add -A` or `git add .`. Run `git add`
with the paths, confirm with `git diff --cached --stat`, then commit. Check first whether the
tree already holds approved but uncommitted work from an earlier step. One commit swept an
unrelated file rewrite in under a message that described neither change, and it took a diff
of the wrong file to notice.

**6. Describe contents and order, not counts and pagination.** A count derived from another
file, or a reference to a page number, goes stale silently when something else changes. Three
sentences describing a document's pagination broke at once when a single table row was added
to a page ahead of them, and one of them sent a reviewer backwards past the thing it cited.

**7. Say what was not checked**, rather than leaving it out, and say where a fact came from.
An omission reads as a verified negative. A disclosure was read off a public listing page
rather than off the form that produces it, and because the source went unstated it produced a
full analysis of a conflict that did not exist.

**8. Never point at the conversation.** A cold session can read every file here and none of
the chat. "As discussed", "per the earlier decision", "recover it from the conversation" are
dead ends. A saved note once told a future session that four agreed items "were agreed but
not restated; recover them from the original list", and the original list was a chat log. The
four were nearly lost and had to be rebuilt from the README, the changelog and the git log.
Write it out where it belongs, or record in plain words that it is lost.

**9. Check stored memories against the repo.** They load before any file is read, so a stale
memory outranks a correct `NEXT.md`. Anything a memory asserts about a file, a branch, a flag
or a version is checkable; check it rather than assuming it still holds. Two were wrong within
one week: one described a branch rebase that had never been done, and the other a tool setup
that had been replaced months earlier.

**10. One item at a time: show the diff, wait for approval.** Commit and push only when
asked. **No failure sits behind this one.** It is a working agreement rather than a scar: it
keeps a wrong direction visible while it is still cheap to change, and batching removes that
moment.

<!-- Add project-specific standards here as they are earned, with the reason. -->

## Traps

Things that cost time once and would cost it again: a tool that lies, a check that cannot
fail, an environment that behaves differently here. Add one the same session it bites,
with enough detail to recognise it, not just to remember it happened.

None yet.

## Decided, do not redo

What was settled and why, what was ruled out and why, and anything the owner said not to
do. A resumed session that sees only the remaining tasks will rebuild something already
rejected, or argue for an option already refused.

- 2026-09-29: `README-old.md` deleted. Its one sentence already appears verbatim in the
  Vision section of `README.md`, so it held nothing unique. Recoverable from commit
  `00c67c1` if ever needed.
- 2026-09-29: project-kit (https://github.com/fbonds/project-kit, at commit `a0f13c0`)
  adopted. Its three template files were copied unmodified, confirmed with `diff` against
  `template/`, then filled in. Every `CLAUDE.md` rule was reviewed and kept.
- 2026-09-29: the Jetson Orin Nano 8GB is the first platform to evaluate, confirmed by
  Fletcher. It is the first test target, not the final platform: `README.md` states it
  as a hypothesis to test, and measurements decide whether it stays.

## In flight

What is being worked on now, and what state it is in.

Nothing. The project is in the concept phase. As of 2026-09-29 no hardware has been bought
or tested, per Fletcher.

## Queued, unscheduled, and not to be started unprompted

What is agreed but waiting, with a pointer to wherever the full proposal is written out.

Experiments 1 to 7 under "Initial experiments" in `README.md`, and creating the
directories under "Proposed repository structure". None are started.

## Committed, pushed, released: three separate questions

As of 2026-09-29, when this was written.

**Uncommitted.** Nothing. Everything up to this point is in the commit that added this file.

**Unpushed.** Run `git rev-list --left-right --count origin/main...main`. Snapshot on
2026-09-29 before this work: 0 behind, 0 ahead. Do not trust this snapshot; Fletcher
pushes.

**Unreleased or undeployed.** Nothing is published or deployed. There is no artifact.

## Open, not blocking

Anything known, unresolved, and not in the way.

The "Open questions" section of `README.md` lists the unresolved hardware, AI,
knowledge, UI and field-use questions. None are answered yet.
