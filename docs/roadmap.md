# Roadmap

What's in flight and what's known to be wrong, across repos. Last updated 2026-11-15. Issues in each repo are the detail; this is the overview. Remove items when they're done.

## In flight

- **liblucy 0.7: declarations always on, TypeScript output.** Shipped in liblucy 0.7.0 and on the website. `@lucy/vite` 0.2.1 writes `.lucy.d.ts` by default and takes liblucy `^0.7.0`.
- **Actors.** Making a running machine a proper actor, in steps (see [decisions.md](decisions.md)). 1: the event queue, released in liblucy 0.7.1 (liblucy#84). Before the rest, each machine's interface (what its creator has to supply) became one thing in the compiler, which the factory, the declarations and the checks read: released in liblucy 0.7.1 (liblucy#85). A machine has only what its own block names; nothing passes from a parent to a child implicitly. 2: parent references for spawned and invoked machines, and stopping a child whose key is spawned into again: released in liblucy 0.7.2 (liblucy#88). 3: input for spawned machines, `input name(Type)` and the block after `spawn(machine)`: released in liblucy 0.8.0 (liblucy#90), with `@lucy/lezer-lucy` 0.3.0, `@lucy/codemirror-lang` 0.3.0, `@lucy/prism` 1.7.0, lucy-vscode 0.3.0, `@lucy/vite` 0.2.2 (which takes liblucy `^0.8.0`) and the website. A data argument for `send` (`send(parent, removed, self)`) followed in liblucy 0.8.1, with `@lucy/lezer-lucy` 0.3.1, `@lucy/prism` 1.7.1, lucy-vscode 0.3.1 and the website. Lists of machines, `append(key, spawn(machine))` and `remove(key, value)`, followed in liblucy 0.9.0, with `@lucy/lezer-lucy` 0.4.0, `@lucy/codemirror-lang` 0.4.0, `@lucy/prism` 1.8.0, lucy-vscode 0.4.0, `@lucy/vite` 0.2.3 (which takes liblucy `^0.9.0`) and the website. Giving a spawned machine its symbols, `:name = function` in the spawn's block, is on the `spawn-symbols` branches of liblucy and lezer-lucy (`docs-spawn-symbols` in the website). Still to do: step 4, and giving symbols to a machine from another file. 4: `invoke` re-expressed as spawn on entry and stop on exit, with `done` and `error` as events from the child.

## Known bugs in liblucy

- An invoked machine can't be given inputs or symbols, so one that uses symbols throws when it uses them. The compiler warns. A spawned machine is given both in the spawn's block.
- A top-level `action` or `guard` followed by a `machine { }` block fails to parse ("Unknown top-level identifier" at the machine's first state). Declare them inside the machine instead.
- `lucyc` prints warnings to stdout, where they mix with the compiled output.
- liblucy's release workflow can fail on the GitHub release: its two build jobs each create the release, and the one that loses the race gets "already exists" (it did for 0.8.1). The npm publish is a separate job and isn't affected. Re-running the failed jobs attaches the missing binary.
- Identifiers from `use` can collide with names the output uses (there's a warning, not a fix).
- Kept on purpose for now, marked `// Known bug:` in the source: multi-line comments, tabs and CRLF handling.

## Behind the compiler

- **Syntax support:** lucy-prism and lucy-vscode haven't been checked against the parser since 2021, apart from `event`. The Lezer grammar is up to date as of `@lucy/lezer-lucy` 0.2.0: every valid `.lucy` input in liblucy's tests parses with it. `input` and the spawn block are in all four as of `@lucy/lezer-lucy` 0.3.0, `@lucy/codemirror-lang` 0.3.0, `@lucy/prism` 1.7.0 and lucy-vscode 0.3.0; in lucy-prism and lucy-vscode that is the only part checked.
- **Bundler plugins:** `@lucy/vite` (vite-plugin) is the supported one. The rollup and snowpack plugins target liblucy 0.1 and XState output; they're out of the docs and may be updated later.
- **lucy-repl** targets liblucy 0.2. A playground on the website could use the wasm build directly: it is one `lucy.wasm` with no imports.

## Ideas

