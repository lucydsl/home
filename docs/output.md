# What the compiler emits

liblucy turns a `.lucy` file into one JavaScript module and the `.d.ts` that types it, or (`lucyc --ts`, `format: 'ts'`) into one TypeScript module holding both. The reference is liblucy's readme and the snapshots in `repos/liblucy/test/snapshots/`. Anything that consumes compiled machines (the website's examples, bundler plugins, a REPL) depends on this shape.

## Shape

- One exported factory per machine: `createLight` for `machine light`, and a default export `createMachine` for a file's unnamed machine.
- A factory takes `{ context, actions, guards, assigns, delays, services }` and reads only the options that machine uses. Every supplied function is called as `(context, event)`.
- A machine that uses `send(parent, event)` also takes `parent`: anything with a `send`. A machine gives itself as the parent to what it spawns or invokes (a machine of the same file only when it sends to its parent; an imported factory or a `services` entry always, as `{ parent }`). Without a parent the send does nothing.
- A send's data is `event.data` unless it has a third argument, a value: `parent?.send({ type: 'removed', data: machine })`. A `:symbol` value comes from `assigns`.
- Spawning into a context key stops what the key held first.
- `append(key, spawn(x))` makes a new array, `[...(machine.context.key ?? []), x().start()]`, so the list need not exist. `remove(key, value)` stops the machine and filters it out, only if the list includes it. `stop()` stops each machine in the list.
- A spawn with a block of inputs creates the machine with them as its `context`: `createTodoItem({ context: { title: event.data }, parent: machine })`, printed over several lines. A `:symbol` value comes from `assigns`. An imported factory or a `services` entry is called with `{ context, parent }`.
- A symbol a spawn gives is beside the context, in the option the spawned machine uses it in: `createTodoItem({ context: {...}, guards: { canDelete: guards.canDeleteTodo }, actions: { log } })`. A symbol of its own that a machine gives away is in the same option of its factory, and typed `(context: any, event: any)` in the `.d.ts` when the machine only passes it on.
- An invoke of a machine creates it with the same call a spawn does, without the `.start()`: `const child = createCountdown({ context: {...}, guards: {...}, parent: machine });`, started once the state is listening for its `done`.
- Inputs change nothing in the machine that declares them. In the `.d.ts` they are `TodoItemInput`, the context has to hold them, and the options are required.
- It returns `{ state, context, send, subscribe, start, stop }`, plus `done` when the machine has final states.
- `state` is a string; nested states are dotted paths (`'red.walk'`). `context` is replaced, never mutated, by each `assign`.
- Events are `{ type, data? }`; `send('name')` is shorthand for `send({ type: 'name' })`. Invokes report with `done` and `error` events carrying `data`.
- The module imports only what the source's `use` lines name. There is no runtime library.
- A machine handles one event at a time. A `send` that arrives during a transition (from an action, a listener, or an actor the machine sent to) is queued, and handled after that transition and its notification, before the outermost `send` returns, so `send` is still synchronous for the caller. Transitions that `send` doesn't start (a timer, an invoke's result, `start()`) go through a `run(step)` helper that does the same. A throw drops the queue; so does `stop()`.

## Rules the output follows

- **Only what the machine needs.** No code for features a machine doesn't use: no timers without delays, no `enterX`/`exitX` functions for states with nothing to do on entry or exit. No queue in a machine that calls no code of its own (no action, guard, assign or delay function, `send`, `spawn` or `invoke`), and no `run` without a timer, invoke or entry work at the start.
- **Readable.** It should look like what a person would write by hand: a `states` table with a method per event.
- **Short stack traces.** From `send` to a user's action is three frames. The queue is a loop inside `send`, so that holds for queued events too.
- **Typed at the edges.** The declarations are always produced; there is no option. The TypeScript format is the same code with the same types: each factory's declaration is its overload signature, and the body uses `any`. It starts with `// @ts-nocheck`, because the output can hold functions and type parameters nothing uses (an unreachable state's `enterX`), which a project's `noUnusedLocals` would reject. Importers are still fully checked.
- **Stable.** Output is covered by snapshots, and a change to it is a deliberate, reviewed change. Refactors of the compiler must leave it byte-identical.
