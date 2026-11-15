# Releasing

The compiler comes first; everything else follows it.

1. **liblucy.** Bump `version` in `package.json` (the Makefile reads it for `lucyc --version`). Run `make WERROR=1 test` and `make test-sanitize`. Tag `v<version>` and push the tag. The Releases workflow then builds `lucyc` for Linux and macOS and attaches the tarballs to a GitHub release (which the website's `install.sh` downloads), and builds the wasm, runs the tests and publishes `@lucy/liblucy` to npm.
2. **Syntax support**, only if the syntax changed: lezer-lucy, then codemirror-lang (it depends on lezer-lucy), lucy-prism, lucy-vscode. See [skills/change-syntax](../skills/change-syntax/SKILL.md). The three npm packages release the same way: bump the version, tag `v<version>`, push the tag.
3. **vite-plugin**, only if it needs a newer `@lucy/liblucy` (it takes `^0.6.0`, so a compatible compiler release reaches people without one): bump the dependency and the version, tag `v<version>`, push the tag.
4. **website.** Update the docs for what changed, regenerate `src/examples/*.js` with the released `lucyc`, bump `@lucy/prism` if it changed. Merging to `main` deploys.

The website describes the released compiler, so don't merge docs for a feature before the release that has it.

Every release workflow refuses to publish if the tag doesn't match `package.json`'s version. Publishing uses npm trusted publishing, so no tokens are involved (see [decisions.md](decisions.md)).

npm packages are under the `@lucy` scope. Publishing and tagging are the maintainer's call: prepare the change, then ask.
