# Roadmap

What's in flight and what's known to be wrong, across repos. Last updated 2026-11-14. Issues in each repo are the detail; this is the overview. Remove items when they're done.

## In flight

- **wasi-sdk build** (liblucy#78): open, CI green, not merged.
- **Typed events** (liblucy#80): `event name(Type)` in a machine block. The grammars and docs follow it, each waiting on the one before: lezer-lucy#2, then codemirror-lang#1 (needs a lezer-lucy release); lucy-prism#2; lucy-vscode#3; website#10 (needs the compiler release, and a `@lucy/prism` release to highlight `event`). See [decisions.md](decisions.md).
- **Release 0.6.0**: `main` emits plain JavaScript, but the latest GitHub release (v0.5.8) and npm (`@lucy/liblucy` 0.5.6) still emit XState, and `package.json` still says 0.5.8. Until a release, the website documents a compiler people can't install. See [releasing.md](releasing.md).

## Known bugs in liblucy

- Named actions and guards bound to symbols (`action log = :log`) don't read `actions`/`guards` from the options, so the machine throws a ReferenceError when they run.
- `send(parent, ...)` compiles but fails at run time: spawned machines get no reference to their parent.
- Spawned and invoked child machines are created without options, so they can't use symbols.
- A top-level `action` or `guard` followed by a `machine { }` block fails to parse ("Unknown top-level identifier" at the machine's first state). Declare them inside the machine instead.
- `lucyc` prints warnings to stdout, where they mix with the compiled output.
- Identifiers from `use` can collide with names the output uses (there's a warning, not a fix).
- Kept on purpose for now, marked `// Known bug:` in the source: multi-line comments, tabs and CRLF handling.

## Behind the compiler

- **Syntax support** (lezer-lucy, codemirror-lang, lucy-prism, lucy-vscode) hasn't been checked against the parser since 2021, apart from `event`. lezer-lucy parses 20 of liblucy's snapshot inputs without errors (with lezer-lucy#2); it lacks chained transitions (`go => guard(x) => target`), `@entry`/`@exit`, `on(name)`, machines nested in states, dotted targets, block transitions and timeframes (`delay(2s)`).
- **Bundler plugins** (rollup, snowpack) target liblucy 0.1 and XState output. A Vite plugin would be the modern replacement.
- **lucy-repl** targets liblucy 0.2. A playground on the website would need the wasm build from liblucy#78.

## Ideas

