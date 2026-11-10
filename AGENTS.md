# AGENTS.md

Rules for AI coding agents working in this repository. People should read
[CONTRIBUTING.md](CONTRIBUTING.md); everything here applies to them too. The
`PR rules` check enforces the rules marked (checked); a pull request that
breaks one cannot be merged.

## What imaji-bim is

A Rust library that will check an openBIM IFC model against Indonesian SNI
requirements and report what does not comply. Today it is only the report
types in `src/lib.rs` (`Outcome`, `Finding`, `Report`) and their tests: there
is no IFC parser and there are no rules yet.

Read before you touch:

- anything: "The rule that shapes the design" in README.md. An element a rule
  could not evaluate reports `Outcome::NotEvaluated` with a reason, never
  `Pass`.
- a rule: "What a good change looks like" and "Things to be careful with" in
  CONTRIBUTING.md, and issue #2.
- the IFC reader: issue #1 and "Untrusted input" in CONTRIBUTING.md.

## Commands

Use the stable Rust toolchain with rustfmt and clippy. Every one of these must
pass before you open or update a pull request. CI runs the same ones:

```bash
cargo fmt --all -- --check
cargo clippy --locked --all-targets -- -D warnings
cargo test --locked
cargo doc --no-deps
```

Run one test while iterating: `cargo test --locked a_failure_makes_a_report_unclean`
(any test name, or part of one).

## Stop and ask the human

Some things must come from the real world. An agent must never invent them.
Stop and ask the person you work for to provide them:

- **The SNI source.** A rule needs the standard number, edition and clause
  (for `Finding::clause`) and the limit it checks, read from the standard
  itself. Never write a clause number, limit or coefficient from memory. If a
  limit cannot be confirmed, make it an input and say so in the pull request.
- **Table values.** A value an engineer reads from an SNI table (exposure
  class, environment category) is an input. Never embed or reconstruct a
  table.
- **IFC schema facts.** Entity and attribute names, attribute order, types,
  property sets and which schema version has them come from the buildingSMART
  schema documentation. Cite it; do not guess.
- **Examples and models.** An expected result comes from the standard, a
  textbook (title, edition, page) or a real model the human supplies. Never
  present an invented model as a real one, and never claim a rule catches
  something it was not run against.
- **An accepted issue.** If the change has no issue labelled `accepted`, stop
  and ask the human to open one and wait for a maintainer.

## Code

- **English only** for code, identifiers, comments, commit messages and docs.
  Answer the person you work for in their language.
- Target Rust 1.75 (`rust-version` in `Cargo.toml`), edition 2021. CI builds on
  stable, so a newer feature passes CI and still breaks 1.75; do not use one.
- Keep `#![forbid(unsafe_code)]`. IFC files come from other people: parsing
  must not panic, loop forever or allocate without bound on malformed input.
- Every public item has a doc comment (`missing_docs`; CI denies warnings).
- Comments are sparse: only where the code is not self-evident. Do not add
  comments or doc comments to code you did not change.

## Tests (checked)

- Every change to `src/` comes with a test: a new `#[test]` (unit tests live
  in the `#[cfg(test)] mod tests` at the end of `src/lib.rs`) or a file under
  `tests/`. The check looks for an added `#[test]` line or a changed `tests/`
  file. A bug fix has a test that fails without the fix; check that it does
  before you open the pull request.
- A new rule has three tests built from literal inputs: an element that
  passes, one that fails, and one that lacks the property the rule needs and
  reports `NotEvaluated`.

## Never

- Never silence rustc, clippy or rustfmt (checked): no `#[allow(`,
  `#![allow(`, `#[expect(`, `#[rustfmt::skip]`, no lint set to `"allow"` in
  `Cargo.toml`, no `clippy.toml` or `rustfmt.toml`. Fix the finding instead.
- Never let any path produce `Outcome::Pass` for an element it did not check.
- Never delete, skip (`#[ignore]`), loosen or rewrite an existing test or
  expectation to make CI pass. If a test looks wrong, say so in the issue.
- Never rename or remove a public item (`Outcome` and its variants, `Finding`,
  `Report` and their fields and methods) or change a rule's `rule_id` once it
  exists. Downstream code and reports refer to them.
- Never copy text or tables out of an SNI standard; they are copyrighted by
  BSN.
- Never bump the version in `Cargo.toml`, publish, tag or release.
- Never add a dependency (the crate has none) or change CI workflows unless
  the issue asks for it.
- Never add unrelated "improvements": no drive-by refactors, renames,
  reformatting of untouched code or dead code.
- Never open pull requests, issues or comments on your own. The human reviews
  your work and posts it.

## Pull requests (checked)

- First an issue, labelled `accepted` by a maintainer. The pull request says
  `Closes #N`.
- One issue per pull request, at most 400 changed lines.
- At most 2 open pull requests per person.
- Fill in the pull request template, including `### AI use` with the tool and
  model.
- The person submitting has read, run and understood every line, and writes
  the description in their own words. Pull requests that are raw, unreviewed
  agent output will be closed.
