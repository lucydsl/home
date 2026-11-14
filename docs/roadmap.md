# Roadmap

What's in flight and what's known to be wrong, across repos. Last updated 2026-11-14. Issues in each repo are the detail; this is the overview. Remove items when they're done.

## In flight

- **Release 0.6.0**: `main` emits plain JavaScript, but the latest GitHub release (v0.5.8) and npm (`@lucy/liblucy` 0.5.6) still emit XState, and `package.json` still says 0.5.8. Until a release, the website documents a compiler people can't install. Publishing is set up (push a tag); see [releasing.md](releasing.md).

## Known bugs in liblucy

- Named actions and guards bound to symbols (`action log = :log`) don't read `actions`/`guards` from the options, so the machine throws a ReferenceError when they run.
- `send(parent, ...)` compiles but fails at run time: spawned machines get no reference to their parent.
- Spawned and invoked child machines are created without options, so they can't use symbols.
- `lucyc` prints warnings to stdout, where they mix with the compiled output.
- Identifiers from `use` can collide with names the output uses (there's a warning, not a fix).
- Kept on purpose for now, marked `// Known bug:` in the source: multi-line comments, tabs and CRLF handling.

## Behind the compiler

- **Syntax support** (lezer-lucy, codemirror-lang, lucy-prism, lucy-vscode) hasn't been checked against the parser since 2021. Known: the Lezer grammar can't parse states inside a `machine name { ... }` block (`MachineBlock` lists `StateBlock` where it needs `StateDeclaration`).
- **Bundler plugins** (rollup, snowpack) target liblucy 0.1 and XState output. A Vite plugin would be the modern replacement.
- **lucy-repl** targets liblucy 0.2. A playground on the website could use the wasm build directly: it is one `lucy.wasm` with no imports.

## Ideas

- **Typed events.** Declare events once, at the top, with payloads, in the style of P's events and UML signals:

  ```lucy
  use type './types' { User }

  event submit(name: string, age: number)
  event loaded(user: User)
  event cancel
  ```

  Transitions stay as they are. The `.d.ts` would get a concrete event union in place of the open `TEvent` generic, and once a file declares any events the compiler could reject transitions on undeclared ones. Open questions: whether the compiler parses types or passes them through as text for TypeScript to check; whether payload fields sit on the event or stay under `event.data` (a breaking change, so it belongs with 0.6.0 if at all); whether declarations are per file or per machine; and whether context gets the same treatment (`context { count: number }`). Not started.
