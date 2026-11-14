# Releasing

The compiler comes first; everything else follows it.

1. **liblucy.** Bump `version` in `package.json` (the Makefile reads it for `lucyc --version`). Run `make WERROR=1 test` and `make test-sanitize`. Tag the version and push the tag: the Releases workflow builds `lucyc` for Linux and macOS and attaches the tarballs to a GitHub release, which is what the website's `install.sh` downloads. No workflow publishes `@lucy/liblucy` to npm: it is published by hand, after building the wasm (`make all`), which the package ships in `dist/`.
2. **Syntax support**, only if the syntax changed: lezer-lucy, then codemirror-lang (it depends on lezer-lucy), lucy-prism, lucy-vscode. See [skills/change-syntax](../skills/change-syntax/SKILL.md).
3. **website.** Update the docs for what changed, regenerate `src/examples/*.js` with the released `lucyc`, bump `@lucy/prism` if it changed. Merging to `main` deploys.

The website describes the released compiler, so don't merge docs for a feature before the release that has it.

npm packages are under the `@lucy` scope. Publishing and tagging are the maintainer's call: prepare the change, then ask.
