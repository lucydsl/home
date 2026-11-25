# Releasing

The compiler comes first; everything else follows it.

1. **liblucy.** Bump `version` in `package.json` (the Makefile reads it for `lucyc --version`). Run `make WERROR=1 test` and `make test-sanitize`. Tag `v<version>` and push the tag. The Releases workflow then builds `lucyc` for Linux and macOS and attaches the tarballs to a GitHub release (which the website's `install.sh` downloads), and builds the wasm, runs the tests and publishes `@lucy/liblucy` to npm. The GitHub release is a draft until both tarballs are attached, so `install.sh` gives the previous version until then. If a job fails, run the failed jobs again: every step can be repeated.
2. **Syntax support**, only if the syntax changed: lezer-lucy, then codemirror-lang (it depends on lezer-lucy), lucy-prism, lucy-vscode. See [skills/change-syntax](../skills/change-syntax/SKILL.md). All four release the same way: bump the version, tag `v<version>`, push the tag. lucy-vscode's workflow publishes to the VS Code Marketplace (publisher `lucylang2`) instead of npm.
3. **vite-plugin**, only if it needs a newer `@lucy/liblucy` (it takes a caret range on a 0.x version, such as `^0.7.0`, so patch releases reach people without one, but a new minor does not): bump the dependency and the version, tag `v<version>`, push the tag.
4. **website.** Update the docs for what changed, regenerate `src/examples/*.js` with the released `lucyc`, bump `@lucy/prism` if it changed. Merging to `main` deploys.

The website describes the released compiler, so don't merge docs for a feature before the release that has it.

Every release workflow refuses to publish if the tag doesn't match `package.json`'s version. Publishing uses trusted publishing, on npm and on the VS Code Marketplace, so no tokens are involved (see [decisions.md](decisions.md)).

npm packages are under the `@lucy` scope. Publishing and tagging are the maintainer's call: prepare the change, then ask.
