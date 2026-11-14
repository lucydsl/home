---
name: change-syntax
description: Add or change Lucy language syntax (a keyword, a construct, a new form of an existing one) and carry it through every repo that knows the syntax - the liblucy parser and printer, the Lezer, Prism and TextMate grammars, and the website docs. Use when asked to add a language feature or when a grammar has fallen out of step with the compiler.
---

# Change Lucy's syntax

The syntax is written down in several repos (the table in `docs/language.md`). liblucy's parser is the authority; the rest follow. Work in this order, one branch and pull request per repo.

## 1. Settle the design

Write the new syntax as a few `.lucy` examples, with the JavaScript (and `.d.ts`, if affected) each should compile to. Check it against `docs/decisions.md` and the output rules in `docs/output.md`. Check the new words against the keyword list: a new keyword breaks machines that use it as an event or state name (that's what `on(name)` is for). Agree the examples with the maintainer before writing code.

## 2. liblucy

Read `repos/liblucy/src/README.md` first.

- Parser: `src/core/parser/` (`keyword.c` for a keyword, a file per construct), with new node or expression types in `src/core/ast/`.
- Output: `src/core/js/` (`model.c` builds the model, the other files print from it; `dts.c` for types).
- Tests: a folder in `test/snapshots/` with `input.lucy` and `expected.js` per case, and a behaviour test in `test/behavior/` that runs the compiled machine. Error cases too.
- `make WERROR=1 test-native test-wasm` and `make test-sanitize` must pass. Existing snapshots must not change unless that was the point.

## 3. Grammars

For each, add the syntax and a test or sample that shows it highlighted or parsed:

- `repos/lezer-lucy/src/lucy.grammar`, then `repos/codemirror-lang/src/lucy.ts` if there are new node names to tag.
- `repos/lucy-prism/lib/lucy.js`.
- `repos/lucy-vscode/syntaxes/lucy.tmLanguage.json`.

These may already be behind the parser. Fix what you find in the area you're touching, and note the rest in `docs/roadmap.md`.

## 4. Website

In `repos/website`: document it in `src/pages/docs/language.md` (and its `sections` frontmatter), with examples that are real compiler output. Hold the merge until the compiler release that has the feature (`docs/releasing.md`).

## 5. Here

Update `docs/language.md` (the brief and the keyword list), and `docs/roadmap.md` and `docs/decisions.md` if the change settles something listed there.
