# Lucy home

The home repo for [Lucy](https://lucy.spooky.click/), a DSL for state machines. Lucy's code lives in about a dozen repos in the [lucydsl](https://github.com/lucydsl) org; this one holds what they share: the map of the repos, the decisions and roadmap that span them, and the agent skills for changes that touch several at once.

It follows the [home repo pattern](https://www.aihero.dev/the-home-repo-pattern). Start your coding agent here, and it will find (or clone) the repo it needs from the pointers in [AGENTS.md](AGENTS.md).

```sh
git clone git@github.com:lucydsl/home.git lucy && cd lucy
scripts/clone liblucy website   # or no arguments, for the active repos
```

- [AGENTS.md](AGENTS.md): what agents read first, with a pointer to every repo.
- [docs/](docs/): the language across repos, the compiler's output, decisions, roadmap, releasing.
- [skills/](skills/): skills for cross-repo work.
- `repos/`: where the other repos are cloned. It's gitignored.
