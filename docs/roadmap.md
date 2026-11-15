# Roadmap

What's in flight and what's known to be wrong, across repos. Last updated 2026-11-15. Issues in each repo are the detail; this is the overview. Remove items when they're done.

## In flight

- **Typed events, the last release.** Shipped in liblucy 0.6.0, `@lucy/lezer-lucy` 0.2.0, `@lucy/codemirror-lang` 0.2.0, `@lucy/prism` 1.6.0 and the website. Left: lucy-vscode#3 (merged) still has to be published to the Marketplace by hand. See [decisions.md](decisions.md).

## Known bugs in liblucy

- Named actions and guards bound to symbols (`action log = :log`) don't read `actions`/`guards` from the options, so the machine throws a ReferenceError when they run.
- `send(parent, ...)` compiles but fails at run time: spawned machines get no reference to their parent.
- Spawned and invoked child machines are created without options, so they can't use symbols.
- A top-level `action` or `guard` followed by a `machine { }` block fails to parse ("Unknown top-level identifier" at the machine's first state). Declare them inside the machine instead.
- `lucyc` prints warnings to stdout, where they mix with the compiled output.
- Identifiers from `use` can collide with names the output uses (there's a warning, not a fix).
- Kept on purpose for now, marked `// Known bug:` in the source: multi-line comments, tabs and CRLF handling.

## Behind the compiler

- **Syntax support:** lucy-prism and lucy-vscode haven't been checked against the parser since 2021, apart from `event`. The Lezer grammar is up to date as of `@lucy/lezer-lucy` 0.2.0: every valid `.lucy` input in liblucy's tests parses with it.
- **Bundler plugins** (rollup, snowpack) target liblucy 0.1 and XState output. A Vite plugin would be the modern replacement.
- **lucy-repl** targets liblucy 0.2. A playground on the website could use the wasm build directly: it is one `lucy.wasm` with no imports.

## Ideas

