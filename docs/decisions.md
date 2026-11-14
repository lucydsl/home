# Decisions

Choices that shape more than one repo, newest first. Add an entry when one is made; don't rewrite old ones, add a new entry that supersedes them.

## 2026-11: The site is at lucy.spooky.click

The project no longer holds lucylang.org, so that domain should appear nowhere: not in readmes, package metadata, docs or install scripts. The site is https://lucy.spooky.click/, and the install script is https://lucy.spooky.click/install.sh.

## 2026-11: The website is Astro, on a Cloudflare Worker, styled with Tideway

The site moved from Eleventy to Astro and is served as a static-assets Worker. Pushing to `main` deploys; pull requests get previews. Its CSS follows [Tideway](https://tidewaystyle.com/): tokens, semantic colour roles, flat BEM. The design ("a statechart, printed at night") is described in the website's readme.

## 2026-11: Build wasm with wasi-sdk, not Emscripten (in progress)

The wasm module is built with a pinned wasi-sdk and loaded by a small hand-written loader, giving one `lucy.wasm` with no imports and no generated glue code. Emscripten's toolchain is large, its output changes between versions, and the compiler needs none of what it provides beyond libc. Open as liblucy#78; until it merges, `main` still builds with Emscripten 7.

## 2026-11: Compile to plain JavaScript, not XState

Up to 0.5.x the compiler emitted XState machine configs. It now emits self-contained JavaScript (see [output.md](output.md)): no dependency, only the code a machine needs, readable output, short stack traces. This is a breaking change for users and is why the next release is 0.6.0. The API was renamed with it: `compileXstate` became `compile`.

## 2026-11: One arena per compile; the library never prints

In liblucy, everything a compile allocates comes from one arena freed at the end, there are no mutable globals, and the library reports errors and warnings as diagnostics on the result instead of printing. The public C API is `src/core/lucy.h`. Details are in liblucy's `src/README.md`.

## Standing

- **liblucy's parser is the authority on syntax.** Grammars elsewhere follow it.
- **Compiler output is covered by snapshots.** Changing it is always deliberate.
- **License:** BSD-2-Clause.
