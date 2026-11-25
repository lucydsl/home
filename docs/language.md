# The language, across repos

The full reference is the website's language guide: `repos/website/src/pages/docs/language.md` (https://lucy.spooky.click/docs/language/). This page is the short version, plus the list of every place the syntax is written down, since a syntax change has to reach all of them.

## In brief

```lucy
use './util.js' { log, getUsers, Order }

machine checkout {
  action logIt = log
  guard canPay = :hasFunds

  event pay(Order)
  event delay

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

- **Keywords:** `use`, `machine`, `state`, `initial`, `final`, `action`, `guard`, `event`, `input`, `assign`, `invoke`, `delay`, `on`, `spawn`, `send`, `append`, `remove`, and the built-in events `@entry` and `@exit`. `event` and `input` are keywords only where a declaration can go, so they still work as an event's name in a state.
- **References:** an identifier names something from `use`, a named `action` or `guard`, or a machine in the file. A `:symbol` names a function supplied when the machine is created, in the option for its kind (`actions`, `guards`, `assigns`, `delays`, `services`).
- **Machines:** `machine name { ... }`, or top-level states for one unnamed machine. A machine inside a state is a nested state; its states are addressed by dotted path (`idle.errored`).
- **Transitions:** `event => guard... => action... => target`. No event name means immediate. `delay(200)`, `delay(2s)`, `delay(:fn)` fire after a time; units are `ms`, `s`, `m`. `on(name)` is the long form, for events named like keywords.
- **Actors:** `assign(ref, spawn(machine))` keeps a child; `send(ref, event)` and `send(parent, event)` pass events, with the data of the event being handled, or with a value as a third argument: `send(parent, removed, self)`. `parent` is whatever created the machine.
- **Lists:** `append(key, spawn(machine))` adds what it spawns to a list in the context, and `remove(key, value)` stops a machine and takes it out, the value being `event.data` or `context.key`. `append` and `remove` are keywords where an action goes, so a state can't be called either and be a target; an event can.
- **Inputs:** a named machine declares what its creator has to supply, `input name` or `input name(Type)`; they are keys of its context. A spawn gives them in a block, one `name = value` to a line: `spawn(machine) { title = event.data }`. A value is a reference, not an expression: `event.data`, `context.key`, `self`, `parent`, or a function (from `use`, or a `:symbol` in `assigns`) called with `(context, event)`. The compiler warns about a same-file spawn that leaves an input out or gives an undeclared one.
- **Symbols at a spawn:** a line in the block that starts with a symbol gives the machine one of its `:symbols`: `:canDelete = :canDeleteTodo` or `:log = log`. The right side is a function from `use` or one of the spawning machine's own symbols, handed over without being called. It is code, and never goes in the context. Same-file machines only.
- **Comments:** `//` to end of line.
- **Events:** a named machine can declare its events, `event name` or `event name(Type)`, where the type is that of `event.data`. A type is a single name, built into TypeScript or imported with `use` (then left out of the JavaScript import). Declarations are optional; with them the compiler warns about undeclared and unhandled events, and the `.d.ts` gets the machine's event types. Not allowed at the top level, in a file's unnamed machine, or in a machine nested in a state. There is no other type syntax.

## Where the syntax lives

| Place | What it is |
| --- | --- |
| `repos/liblucy/src/core/parser/` | The real grammar: a hand-written parser. `keyword.c` lists the keywords; one file per construct. |
| `repos/liblucy/test/snapshots/` | An `input.lucy` and `expected.js` per feature. |
| `repos/lezer-lucy/src/lucy.grammar` | Lezer grammar, used by codemirror-lang. Tests in `test/*.txt` (`npm test`). |
| `repos/codemirror-lang/src/lucy.ts` | Highlighting tags and completion on top of the Lezer grammar. |
| `repos/lucy-prism/lib/lucy.js` | Prism grammar (regex based). The website highlights with it. |
| `repos/lucy-vscode/syntaxes/lucy.tmLanguage.json` | TextMate grammar for VS Code. |
| `repos/website/src/pages/docs/language.md` | The reference people read. |
| `repos/website/src/examples/` | Home page examples; the `.js` files are real `lucyc` output. |

liblucy's parser is the authority. The others were last brought in line with it in 2021, so check each against the parser rather than assuming they agree.
