# Contributing

Contributions are welcome. A few things worth knowing before you spend time.

## How changes land

Every change reaches `main` through a pull request that the maintainer reviews
and merges. That includes changes from forks, which is the normal path: you
cannot push to this repository directly, and nothing merges without a review.
Fork the repository, branch from the latest `main`, and open the PR against
`main`. Keep one topic per PR. If a PR sits without a response, a nudge is
fine.

Commit messages follow the short conventional style already in the log:
`fix: ...`, `docs: ...`, `chore: ...`, with the PR number added on merge.

## Before opening a PR

```bash
cargo fmt --all -- --check
cargo clippy --locked --all-targets -- -D warnings
cargo test --locked
cargo doc --no-deps
```

CI runs exactly these four on `ubuntu-latest` with the stable toolchain.
Warnings are denied, and that includes `missing_docs`: a new public item
without a doc comment fails the build.

## Pull requests

Every CI check must pass before a pull request can be merged. The `PR rules`
check enforces these:

- **Issue first.** Open an issue and wait until a maintainer labels it
  `accepted`, then write `Closes #N` in the pull request.
- **Tests.** Every change to `src/` needs a test: a new `#[test]` or a
  change under `tests/`. A bug fix needs a test that fails without the fix.
- **Small.** At most 400 changed lines (`Cargo.lock` not counted), one issue
  per pull request.
- **No suppressions.** No `#[allow(`, `#[expect(` or `#[rustfmt::skip]`, no
  lint set to `"allow"` in `Cargo.toml`, no `clippy.toml` or `rustfmt.toml`.
  Fix the finding.
- **At most 2 open pull requests** per person.

If a rule really does not fit your change, say why in the description. A
maintainer can waive it with a label (`no-tests-needed`, `large-change` or
`suppression-ok`).

## Legal notice

By submitting a contribution you confirm that you wrote it, that you have the
right to submit it, and that it may be distributed under the project licence
(see [LICENSE-MIT](LICENSE-MIT)).

## AI-assisted contributions

Using AI tools is fine; please just be open about it.

- Say in the PR description which AI tools you used, if any: none, some or most, plus the tool or agent and the model (for example "Claude Code, Opus 5.5" or "Copilot, GPT-5").
- Please write the PR description in your own words; short and plain is fine. It shows us what you understood about the change. Please don't paste generated text.
- Read, run and test every line you submit, and be ready to explain it in review. If you don't understand what a line does, don't submit it.
- Keep the change focused. Agents like to add unrelated "improvements", long comments and dead code; remove them before you open the pull request.
- Pull requests that are raw, unreviewed agent output will be closed.
- If you use an agent, point it at [AGENTS.md](AGENTS.md).
- If the disclosure is missing, a check adds a reminder to the PR. It does not fail the PR, but a maintainer will ask about it before merging.

## What a good change looks like

A test that shows the behaviour, built from literal inputs. For a new rule:
one element that passes, one that fails, and one that lacks the property the
rule needs and so reports `NotEvaluated`. A rule proposal is much more useful
with a real example of a model element that should fail it.

For a bug fix, the test fails without the fix.

## Things to be careful with

**`NotEvaluated` is never a pass.** If a rule cannot read what it needs, it
reports `Outcome::NotEvaluated` with a reason. No path may produce `Pass`
without having checked the element. This is the rule the crate is built
around, and a change that blurs it will not be merged.

**Cite the clause.** Every rule carries the SNI standard, edition and clause
it comes from in `Finding::clause`. If you cannot confirm a limit, do not
guess one: leave it as an input and say so in the PR.

**Do not reproduce the standard.** SNI documents are copyrighted by BSN. A
value an engineer reads from a table (exposure class, environment category)
is an input, not an embedded copy.

**Do not reimplement capacities.** Where a rule needs a structural capacity,
call [imaji-structural-sni](https://github.com/saimskywalker/imaji-structural-sni)
so each requirement is encoded once.

**Untrusted input.** IFC files come from other people. Parsing must not panic,
loop forever, or allocate without bound on malformed input.

## Scope

Reading an IFC model and reporting which SNI requirements it does not meet.
Structural arithmetic and irrigation hydraulics live in the sibling crates.
The crate has no dependencies yet, and a PR that adds one needs a strong
reason.

## Stay in touch

Don't forget to follow [@saimskywalker](https://github.com/saimskywalker) on
GitHub to see new releases and other open-source projects as they come out.
