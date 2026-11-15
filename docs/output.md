# What the compiler emits

liblucy turns a `.lucy` file into one JavaScript module, and with `--experimental-dts` a `.d.ts` beside it. The reference is liblucy's readme and the snapshots in `repos/liblucy/test/snapshots/`. Anything that consumes compiled machines (the website's examples, bundler plugins, a REPL) depends on this shape.

## Shape

- One exported factory per machine: `createLight` for `machine light`, and a default export `createMachine` for a file's unnamed machine.
- A factory takes `{ context, actions, guards, assigns, delays, services }` and reads only the options that machine uses. Every supplied function is called as `(context, event)`.
- It returns `{ state, context, send, subscribe, start, stop }`, plus `done` when the machine has final states.
- `state` is a string; nested states are dotted paths (`'red.walk'`). `context` is replaced, never mutated, by each `assign`.
- Events are `{ type, data? }`; `send('name')` is shorthand for `send({ type: 'name' })`. Invokes report with `done` and `error` events carrying `data`.
- The module imports only what the source's `use` lines name. There is no runtime library.
- A machine handles one event at a time. A `send` that arrives during a transition (from an action, a listener, or an actor the machine sent to) is queued, and handled after that transition and its notification, before the outermost `send` returns, so `send` is still synchronous for the caller. Transitions that `send` doesn't start (a timer, an invoke's result, `start()`) go through a `run(step)` helper that does the same. A throw drops the queue; so does `stop()`.

## Rules the output follows

- **Only what the machine needs.** No code for features a machine doesn't use: no timers without delays, no `enterX`/`exitX` functions for states with nothing to do on entry or exit. No queue in a machine that calls no code of its own (no action, guard, assign or delay function, `send`, `spawn` or `invoke`), and no `run` without a timer, invoke or entry work at the start.
- **Readable.** It should look like what a person would write by hand: a `states` table with a method per event.
- **Short stack traces.** From `send` to a user's action is three frames. The queue is a loop inside `send`, so that holds for queued events too.
- **Stable.** Output is covered by snapshots, and a change to it is a deliberate, reviewed change. Refactors of the compiler must leave it byte-identical.
