# imaji-bim — Rust crate for checking openBIM IFC models against Indonesian SNI requirements

Check an openBIM IFC model against Indonesian SNI requirements, and report
what does not comply.

## Status: early

Read this before trying to use it.

What exists today is the scaffolding: the finding and report types, the
three-state outcome, and the tests around them. **There is no IFC parser and
there are no rules yet.** The open issues set out what is planned and in what
order.

For now this is a research project: no rules are being shipped, and the
browser tool in the open issues is on hold.

This is stated plainly rather than buried because a compliance tool that
overstates what it does is worse than no tool.

## The idea

A BIM model carries geometry and properties. Whether that model satisfies a
building standard is a separate question, and today it is answered by a person
reading a drawing set and a spreadsheet.

Part of that question is mechanical: is the cover at least the minimum for
this exposure, is the reinforcement ratio inside its bounds, is the clear
spacing sufficient, does the beam depth meet the span over depth limit. Those
are checks a machine can run over every element in a model in seconds, on
every revision, instead of on a sample at review time.

The rest of the question is judgement, and no tool of this kind can answer it.

## The rule that shapes the design

**An unevaluated rule reports `NotEvaluated`, never a pass.**

This is the whole reason for the three-state outcome. If a model does not
carry the property a rule needs, the honest answer is "I could not check
this", and it must be visible in the report. A tool that silently passes what
it could not read is worse than useless, because it produces confidence
without checking anything.

So `Report::is_clean()` only tells you nothing failed. `Report::not_evaluated()`
tells you how much was actually looked at, and both belong in any summary a
person reads.

## Not a substitute for an engineer

A clean report means the implemented rules found nothing wrong. It does not
mean the design complies with SNI, and it does not mean the building is safe.

Every rule cites the clause it comes from so the source can be checked. The
clause is the authority; this crate is a way of applying it consistently.

## Relationship to the other crates

- [imaji-structural-sni](https://github.com/saimskywalker/imaji-structural-sni)
  does the structural arithmetic: capacities, load combinations, limit states
- [imaji-tirta](https://github.com/saimskywalker/imaji-tirta)
  does irrigation hydraulics

This crate is the third piece: reading a model and deciding which questions to
ask of it. Where a rule needs a capacity, it should call `imaji-structural-sni`
rather than reimplement the check, so a requirement is only ever encoded once.

## Standards and copyright

This crate implements **requirements and procedures**, which are methods and
facts, and cites the clause each rule comes from. It does not reproduce the
text of any standard and does not embed lookup tables out of one.

SNI documents are copyrighted by BSN and sold. Where a requirement depends on
a table an engineer reads (exposure class, environment category), the value is
an input rather than an embedded copy.

IFC is an open standard published by buildingSMART.

## Development

```
cargo test
cargo clippy --all-targets -- -D warnings
cargo fmt --all -- --check
cargo doc --no-deps --open
```

Contributions are welcome, particularly from engineers who review models for a
living and know which checks actually catch things. A rule proposal is more
useful with a real example of a model that should fail it.

## Licence

MIT. See [LICENSE-MIT](LICENSE-MIT).
