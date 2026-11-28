# Lucy

Lucy is a small language for state machines. A `.lucy` file compiles to a module of plain JavaScript machines with no runtime library. The site and docs are at https://lucy.spooky.click/.

This is Lucy's home repo. It holds no product code: the code is in the repos listed below, each with its own history, issues and releases. Work from here so you can see all of them.

## How to work

- Each repo below has a local path under `repos/` (gitignored). If it's there, work in it; if not, clone it from its remote to that path (`scripts/clone <name>`). Run `git fetch` before reading one, since the clone may be behind.
- Read a repo's own `AGENTS.md` or readme before changing it. Its build and test commands live there, not here.
- Make changes on a branch in the child repo and open a pull request there. Use a git worktree when more than one task is running in the same repo. Don't push to a child repo's `main`: for the website that deploys.
- What belongs here: things true of more than one repo (the docs in `docs/`, the skills in `skills/`). What belongs in a child repo: anything about only that repo.
- When a change makes something in `docs/` untrue (a decision reversed, a roadmap item done, a new place the syntax lives), update it in the same piece of work.

## Docs

- [docs/language.md](docs/language.md): the syntax in brief, and every place it's implemented. Read before changing the language.
- [docs/output.md](docs/output.md): what the compiler emits and the rules the output follows.
- [docs/decisions.md](docs/decisions.md): choices already made, and why. Read before proposing to undo one.
- [docs/roadmap.md](docs/roadmap.md): what's in flight, known bugs, and ideas.
- [docs/releasing.md](docs/releasing.md): the order to release things in.

## Skills

- [skills/change-syntax](skills/change-syntax/SKILL.md): adding or changing Lucy syntax across the compiler, grammars, highlighters and docs.

## Other Repos

Active:

- liblucy (the compiler, in C17; builds the `lucyc` CLI and the wasm in the `@lucy/liblucy` npm package): `repos/liblucy`. Clone from `git@github.com:lucydsl/liblucy.git` if missing. Read `src/README.md` (layout and C conventions) before touching the C. Ask the maintainer before pushing or opening a PR.
- website (the site and docs at lucy.spooky.click; Astro, deployed as a Cloudflare Worker on every push to `main`): `repos/website`. Clone from `git@github.com:lucydsl/website.git` if missing. Read its `README.md`; its CSS follows Tideway, with skills in its `.claude/skills/`. Pull requests get a preview URL.
- vite-plugin (the Vite plugin, `@lucy/vite`; the one supported build tool plugin, compiling `.lucy` imports with `@lucy/liblucy`): `repos/vite-plugin`. Clone from `git@github.com:lucydsl/vite-plugin.git` if missing.
- astro (the Astro integration, `@lucy/astro`: machines with views as components, rendered on the server, built on vite-plugin): `repos/astro`. Clone from `git@github.com:lucydsl/astro.git` if missing.

Syntax support (each has its own copy of the grammar; see [docs/language.md](docs/language.md)):

- lezer-lucy (Lezer grammar, `@lucy/lezer-lucy`): `repos/lezer-lucy`. Clone from `git@github.com:lucydsl/lezer-lucy.git` if missing.
- lucy-prism (Prism highlighting, `@lucy/prism`; the website uses it): `repos/lucy-prism`. Clone from `git@github.com:lucydsl/lucy-prism.git` if missing.
- lucy-vscode (VS Code extension, TextMate grammar): `repos/lucy-vscode`. Clone from `git@github.com:lucydsl/lucy-vscode.git` if missing.
- codemirror-lang (CodeMirror 6 language support, built on lezer-lucy): `repos/codemirror-lang`. Clone from `git@github.com:lucydsl/codemirror-lang.git` if missing.
- tree-sitter-lucy (tree-sitter grammar and queries, for Helix, Neovim, Zed and other editors; not published, editors build it from the repo): `repos/tree-sitter-lucy`. Clone from `git@github.com:lucydsl/tree-sitter-lucy.git` if missing. Support for an editor lives here, with setup in its readme; Lucy isn't sent upstream to the editors themselves.

Dormant (last real work in 2021; they target old liblucy versions that emitted XState, so don't treat their code as current, and check with the maintainer before reviving one):

- lucy-repl (browser REPL; liblucy 0.2, CodeMirror 0.18): `repos/lucy-repl`. Clone from `git@github.com:lucydsl/lucy-repl.git` if missing.
- rollup-plugin-lucy (`@lucy/rollup-plugin`; liblucy 0.1; superseded by vite-plugin and no longer in the docs): `repos/rollup-plugin-lucy`. Clone from `git@github.com:lucydsl/rollup-plugin-lucy.git` if missing.
- lucy-snowpack-plugin (`@lucy/snowpack-plugin`; liblucy 0.1, and Snowpack itself is retired; no longer in the docs): `repos/lucy-snowpack-plugin`. Clone from `git@github.com:lucydsl/lucy-snowpack-plugin.git` if missing.
- typescript-plugin (TypeScript language service plugin; a stub, version 0.0.1): `repos/typescript-plugin`. Clone from `git@github.com:lucydsl/typescript-plugin.git` if missing.
- webpack-plugin (never started; the repo is empty): `git@github.com:lucydsl/webpack-plugin.git`.
