# Decisions

Choices that shape more than one repo, newest first. Add an entry when one is made; don't rewrite old ones, add a new entry that supersedes them.

## 2026-11: A machine is an actor: one event at a time, with a queue

A running machine is treated as an actor, and the work on actors (parent references, input for spawned machines, invoke) builds on that. The first piece is the mailbox:

- **Run to completion.** An event sent to a machine during a transition is queued and handled afterwards, in order.
- **Synchronous, not a microtask.** The outermost `send` drains the queue before it returns, so the API is unchanged: `machine.send('x'); machine.state` still works.
- **Only where it can matter.** The queue is emitted for machines that call code they don't own during a transition. A machine without one keeps the old behaviour when a listener sends during a notification: the nested send runs at once.
- **Not decided here:** dropping events sent after `stop()`, which would change every machine's output.
- **Actor references stay opaque in the language.** Lucy code only does `send(ref, event)`, so a reference could later be an id resolved by a system instead of an object.

## 2026-11: Declarations are always on, and TypeScript is a second output format

Making the `.d.ts` opt-in (`--experimental-dts`, `dts: true`) was a mistake: types are the reason to declare events, and nobody is worse off for having the file. From liblucy 0.7:

- **No option.** `lucyc` writes the `.d.ts` beside whatever it writes, and `compile()` always returns `{ js, dts }`. `--experimental-dts` and `dts: true` are accepted and ignored. The C API's `lucy_result_set_options` is gone.
- **JavaScript stays the default.** It runs anywhere with no build step, and the snapshots keep it readable.
- **TypeScript is a format, not a replacement.** `lucyc --ts` and `compile(source, name, { format: 'ts' })` give one `.ts` module with the types inline, for projects that want a single file to check in or run directly. It is typed at its exports and uses `any` inside, under `// @ts-nocheck`; see [output.md](output.md).
- **The Vite plugin writes `toggle.lucy.d.ts`**, on by default (`@lucy/vite` 0.2.0). TypeScript finds that name for `import './toggle.lucy'` with no configuration; `toggle.d.lucy.ts` needs `allowArbitraryExtensions`.

## 2026-11: Vite is the one supported build tool

`@lucy/vite` (vite-plugin) is the only build tool plugin that is documented and kept up with the compiler. It is written for Vite 8's Rolldown: a `transform` hook with a hook filter for `.lucy` ids, so other modules never reach the plugin, and it still works back to Vite 6.3. The Rollup and Snowpack plugins are left as they are, out of the docs; they may be updated later.

## 2026-11: Typed events are declared per machine, with a type name for their data

A machine declares its events with `event name` or `event name(Type)` (liblucy#80). This settles the open questions in the roadmap's typed-events idea:

- **Per machine, not per file.** Declarations go in a `machine { }` block and cover that machine and the machines nested in its states. They aren't allowed at the top level, so nothing depends on a file's unnamed machine, which may be removed.
- **The payload stays under `event.data`.** An event's type is the type of its `data`, so the JavaScript and `send({ type, data })` are unchanged. Sending a DOM event straight in as the event isn't a goal.
- **Types are passed through, not parsed.** A type is one name: built into TypeScript, or imported with `use`, in which case the JavaScript leaves it out of the import and the `.d.ts` imports it with `import type`. Anything richer is named in a `.ts` file. There is no `use type`.
- **One type per event name** within a machine, since whoever sends an event doesn't know what state the machine is in.
- **Warnings, not errors**, for transitions on undeclared events and declared events no state handles, so a machine can be annotated gradually. A machine with no declarations isn't checked.
- **Context gets no declaration.** The `.d.ts` instead checks that the `context` passed in can hold what `assign(key)` stores from an event's data.

## 2026-11: The site is at lucy.spooky.click

The project no longer holds lucylang.org, so that domain should appear nowhere: not in readmes, package metadata, docs or install scripts. The site is https://lucy.spooky.click/, and the install script is https://lucy.spooky.click/install.sh.

## 2026-11: The website is Astro, on a Cloudflare Worker, styled with Tideway

The site moved from Eleventy to Astro and is served as a static-assets Worker. Pushing to `main` deploys; pull requests get previews. Its CSS follows [Tideway](https://tidewaystyle.com/): tokens, semantic colour roles, flat BEM. The design ("a statechart, printed at night") is described in the website's readme.

## 2026-11: Publish to npm from CI, with trusted publishing

The four published packages (`@lucy/liblucy`, `@lucy/lezer-lucy`, `@lucy/codemirror-lang`, `@lucy/prism`) publish from a `release.yml` workflow in their repo when a `v*` tag is pushed. npm trusts that workflow file in that repo (OIDC), so there are no tokens. The workflow's filename and the repo are fixed on npmjs.com: renaming either breaks publishing until the trusted publisher is deleted and added again there.

## 2026-11: Build wasm with wasi-sdk, not Emscripten

The wasm module is built with a pinned wasi-sdk and loaded by a small hand-written loader, giving one `lucy.wasm` with no imports and no generated glue code. Emscripten's toolchain is large, its output changes between versions, and the compiler needs none of what it provides beyond libc. Merged as liblucy#78.

## 2026-11: Compile to plain JavaScript, not XState

Up to 0.5.x the compiler emitted XState machine configs. It now emits self-contained JavaScript (see [output.md](output.md)): no dependency, only the code a machine needs, readable output, short stack traces. This is a breaking change for users and is why the next release is 0.6.0. The API was renamed with it: `compileXstate` became `compile`.

## 2026-11: One arena per compile; the library never prints

In liblucy, everything a compile allocates comes from one arena freed at the end, there are no mutable globals, and the library reports errors and warnings as diagnostics on the result instead of printing. The public C API is `src/core/lucy.h`. Details are in liblucy's `src/README.md`.

## Standing

- **liblucy's parser is the authority on syntax.** Grammars elsewhere follow it.
- **Compiler output is covered by snapshots.** Changing it is always deliberate.
- **License:** BSD-2-Clause.
