# Roadmap

What's in flight and what's known to be wrong, across repos. Last updated 2026-11-15. Issues in each repo are the detail; this is the overview. Remove items when they're done.

## In flight

- **liblucy 0.7: declarations always on, TypeScript output.** Shipped in liblucy 0.7.0 and on the website. `@lucy/vite` 0.2.1 writes `.lucy.d.ts` by default and takes liblucy `^0.7.0`.
- **Actors.** Making a running machine a proper actor, in steps (see [decisions.md](decisions.md)). 1: the event queue, released in liblucy 0.7.1 (liblucy#84). Before the rest, each machine's interface (what its creator has to supply) became one thing in the compiler, which the factory, the declarations and the checks read: released in liblucy 0.7.1 (liblucy#85). A machine has only what its own block names; nothing passes from a parent to a child implicitly. 2: parent references for spawned and invoked machines, and stopping a child whose key is spawned into again: liblucy's `parent` branch. 3: input for spawned machines, as decided (see decisions.md): `input name(Type)` in the machine, and a block of `name = value` lines after `spawn(machine)`. Not started. After it, a data argument for `send`, and the options a child needs. 4: `invoke` re-expressed as spawn on entry and stop on exit, with `done` and `error` as events from the child.

## Known bugs in liblucy

- Spawned and invoked child machines are created without options, so they can't use symbols. The compiler warns about it (the machine and the options it needs); supplying them is part of the actor work.
- A top-level `action` or `guard` followed by a `machine { }` block fails to parse ("Unknown top-level identifier" at the machine's first state). Declare them inside the machine instead.
- `lucyc` prints warnings to stdout, where they mix with the compiled output.
- Identifiers from `use` can collide with names the output uses (there's a warning, not a fix).
- Kept on purpose for now, marked `// Known bug:` in the source: multi-line comments, tabs and CRLF handling.

## Behind the compiler

- **Syntax support:** lucy-prism and lucy-vscode haven't been checked against the parser since 2021, apart from `event`. The Lezer grammar is up to date as of `@lucy/lezer-lucy` 0.2.0: every valid `.lucy` input in liblucy's tests parses with it.
- **Bundler plugins:** `@lucy/vite` (vite-plugin) is the supported one. The rollup and snowpack plugins target liblucy 0.1 and XState output; they're out of the docs and may be updated later.
- **lucy-repl** targets liblucy 0.2. A playground on the website could use the wasm build directly: it is one `lucy.wasm` with no imports.

## Ideas

