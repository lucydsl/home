# The language, across repos

The full reference is the website's language guide: `repos/website/src/pages/docs/language.md` (https://lucy.spooky.click/docs/language/). This page is the short version, plus the list of every place the syntax is written down, since a syntax change has to reach all of them.

## In brief

```lucy
use './util.js' { log, getUsers }

action logIt = log
guard canPay = :hasFunds

machine checkout {
  initial state idle {
    pay => canPay => logIt => assign(total, :sum) => paying
    on(delay) => idle
  }

  state paying {
    @entry => action(:showSpinner)
    @exit => action(:hideSpinner)
    invoke(getUsers) {
      done => assign(users) => paid
      error => idle
    }
    delay(30s) => idle
  }

  final state paid {}
}
```

- **Keywords:** `use`, `machine`, `state`, `initial`, `final`, `action`, `guard`, `assign`, `invoke`, `delay`, `on`, `spawn`, `send`, and the built-in events `@entry` and `@exit`.
- **References:** an identifier names something from `use`, a named `action` or `guard`, or a machine in the file. A `:symbol` names a function supplied when the machine is created, in the option for its kind (`actions`, `guards`, `assigns`, `delays`, `services`).
- **Machines:** `machine name { ... }`, or top-level states for one unnamed machine. A machine inside a state is a nested state; its states are addressed by dotted path (`idle.errored`).
- **Transitions:** `event => guard... => action... => target`. No event name means immediate. `delay(200)`, `delay(2s)`, `delay(:fn)` fire after a time; units are `ms`, `s`, `m`. `on(name)` is the long form, for events named like keywords.
- **Actors:** `assign(ref, spawn(machine))` keeps a child; `send(ref, event)` and `send(parent, event)` pass events.
- **Comments:** `//` to end of line.
- There is no type syntax. Event payloads travel as `event.data`.

## Where the syntax lives

| Place | What it is |
| --- | --- |
| `repos/liblucy/src/core/parser/` | The real grammar: a hand-written parser. `keyword.c` lists the keywords; one file per construct. |
| `repos/liblucy/test/snapshots/` | An `input.lucy` and `expected.js` per feature. |
| `repos/lezer-lucy/src/lucy.grammar` | Lezer grammar, used by codemirror-lang. |
| `repos/codemirror-lang/src/lucy.ts` | Highlighting tags and completion on top of the Lezer grammar. |
| `repos/lucy-prism/lib/lucy.js` | Prism grammar (regex based). The website highlights with it. |
| `repos/lucy-vscode/syntaxes/lucy.tmLanguage.json` | TextMate grammar for VS Code. |
| `repos/website/src/pages/docs/language.md` | The reference people read. |
| `repos/website/src/examples/` | Home page examples; the `.js` files are real `lucyc` output. |

liblucy's parser is the authority. The others were last brought in line with it in 2021, so check each against the parser rather than assuming they agree.
