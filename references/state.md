# Xstate - State

**Pages:** 15

---

## Observable Actors

**URL:** https://stately.ai/docs/observable-actors

**Contents:**
- Observable Actors
- Observable actor capabilities​
- Observable actor logic​
- Observable actor input​

Observable actors are actors that represent an observable stream of values. This makes it easy to interop with observable libraries like RxJS.

All XState actors are observable.

You can define observable actor logic using the fromObservable(...) actor logic creator, which takes a function that returns an observable and returns actor logic that can be used to create observable actors.

You can pass in input to an observable actor by passing it to the createActor(...) function as the input property of the second argument. In the observable logic (fromObservable(observableFn)), you read the input property of the first argument passed to the observable function:

**Examples:**

Example 1 (ts):
```ts
import { fromObservable, createActor } from 'xstate';import { interval } from 'rxjs';const intervalLogic = fromObservable(() => interval(1000));const intervalActor = createActor(intervalLogic);intervalActor.subscribe((snapshot) => {  console.log(snapshot.context);});intervalActor.start();// logs 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, ...// every second
```

Example 2 (ts):
```ts
import { fromObservable, createActor } from 'xstate';import { interval } from 'rxjs';const intervalLogic = fromObservable(({ input }) => interval(input.interval));const intervalActor = createActor(intervalLogic, {  input: { interval: 10_000 },});intervalActor.subscribe((snapshot) => {  console.log(snapshot.context);});intervalActor.start();// logs 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, ...// every 10 seconds
```

---

## TypeScript

**URL:** https://stately.ai/docs/typescript

**Contents:**
- TypeScript
- Use the latest version of TypeScript​
- Set up your tsconfig.json file​
- Specifying types​
- Dynamic parameters​
- Asserting events​
  - Actions and Guards​
  - Invoked Actor Input​
- Type helpers​
  - ActorRefFrom<T>​

XState v5 and its related libraries are written in TypeScript, and utilize complex types to provide the best type safety and inference possible for you.

XState v5 requires TypeScript version 5.0 or greater.

For best results, use the latest TypeScript version.

Follow these guidelines to ensure that your TypeScript project is ready to use XState v5:

Use the latest version of TypeScript; version 5.0 or greater is required.

The recommended way to strongly type your machine is to use the setup(...) function:

You can also specify TypeScript types inside the machine config using the .types property:

These types will be inferred throughout the machine config and in the created machine and actor so that methods such as machine.transition(...) and actor.send(...) will be type-safe.

It is recommended to use dynamic parameters in actions and guards as they allow you to make reusable functions that are not closely tied to the machine, and are strongly-typed.

It is strongly recommended to use dynamic parameters instead of directly accessing the event object whenever possible for improved type safety and reusability.

If using dynamic parameters is infeasible and you must use the event in an action or guard implementation, you can assert the event type using the assertEvent(...) helper function:

Another case where it helpful to use assertEvent is when specifying input for an invoked actor. The event received could be any one of the events received by that actor. In order for TypeScript to recognize the event type and its properties, you can use assertEvent to narrow down the event type.

XState provides some type helpers to make it easier to work with types in TypeScript.

Results in an ActorRef from the provided T actor logic parameter, which is useful for creating strongly-typed actors. The T parameter can be any ActorLogic, such as the return value of createMachine(…), or any other actor logic, such as fromPromise(…) or fromObservable(…).

Results in a Snapshot from the provided T parameter, which is useful for creating strongly-typed snapshots. The T parameter can be any ActorLogic or ActorRef.

Results in an union of all event types defined in the provided T actor logic parameter. Useful for type-safe event handling.

Typegen does not yet support XState v5. However, with the setup(...) function and/or the .types property explained above, you can provide strong typing for most (if not all) of your machine.

If you were previously using typegen to narrow dow

*[Content truncated]*

**Examples:**

Example 1 (bash):
```bash
npm install typescript@latest --save-dev
```

Example 2 (json5):
```json5
// tsconfig.json{  compilerOptions: {    // ...    strictNullChecks: true,    // or set `strict` to true, which includes `strictNullChecks`    // "strict": true,    skipLibCheck: true,  },}
```

Example 3 (ts):
```ts
import { setup } from 'xstate';const feedbackMachine = setup({  types: {    context: {} as { feedback: string },    events: {} as { type: 'feedback.good' } | { type: 'feedback.bad' },  },  actions: {    logTelemetry: () => {      // TODO: implement    },  },}).createMachine({  // ...});
```

Example 4 (ts):
```ts
import { createMachine } from 'xstate';const feedbackMachine = createMachine({  types: {} as {    context: { feedback: string };    events: { type: 'feedback.good' } | { type: 'feedback.bad' };    actions: { type: 'logTelemetry' };  },});
```

---

## XState

**URL:** https://stately.ai/docs/xstate

**Contents:**
- XState
- Installation​
- Create a simple machine​
- Create a more complex machine​
- Download the XState VS Code extension​
- Packages​

XState is a state management and orchestration solution for JavaScript and TypeScript apps.

It uses event-driven programming, state machines, statecharts, and the actor model to handle complex logic in predictable, robust, and visual ways. XState provides a powerful and flexible way to manage application and workflow state by allowing developers to model logic as actors and state machines. It integrates well with React, Vue, Svelte, and other frameworks and can be used in the frontend, backend, or wherever JavaScript runs.

Want to find out more about state machines? Read our introduction.

XState is available on npm:

Check out more cheatsheet examples.

The XState VS Code extension does not fully support XState v5 yet.

Read more about our developer tools.

**Examples:**

Example 1 (bash):
```bash
npm install xstate
```

Example 2 (bash):
```bash
pnpm install xstate
```

Example 3 (bash):
```bash
yarn add xstate
```

Example 4 (js):
```js
import { createMachine, assign, createActor } from 'xstate';const countMachine = createMachine({  context: {    count: 0,  },  on: {    INC: {      actions: assign({        count: ({ context }) => context.count + 1,      }),    },    DEC: {      actions: assign({        count: ({ context }) => context.count - 1,      }),    },    SET: {      actions: assign({        count: ({ event }) => event.value,      }),    },  },});const countActor = createActor(countMachine).start();countActor.subscribe((state) => {  console.log(state.context.count);});countActor.send({ type: 'INC' });// logs 1countActo
...
```

---

## Callback Actors

**URL:** https://stately.ai/docs/callback-actors

**Contents:**
- Callback Actors
- Callback actor capabilities​
- Callback actor logic​
- Callback actor input​

Callback actors are actors whose logic is represented by a function that can "callback" to the parent actor by sending events (via sendBack(...)). It can also receive(...) events from other actors.

You can define callback actor logic using the fromCallback(...) actor logic creator, which takes a callback function and returns actor logic that can be used to create callback actors.

You can pass input when creating callback actors, which is passed to the callback actor logic in the input property of the first argument.

**Examples:**

Example 1 (ts):
```ts
import {  createActor,  createMachine,  fromCallback,  sendTo,  setup,} from 'xstate';const resizeLogic = fromCallback(({ sendBack, receive }) => {  const resizeHandler = (event) => {    sendBack(event);  };  window.addEventListener('resize', resizeHandler);  const removeListener = () => {    window.removeEventListener('resize', resizeHandler);  };  receive((event) => {    if (event.type === 'stopListening') {      console.log('Stopping listening');      removeListener();    }  });  // Cleanup function  return () => {    console.log('Cleaning up');    removeListener();  };});const machine = se
...
```

Example 2 (ts):
```ts
import { fromCallback, createActor, setup, type EventObject } from 'xstate';const resizeLogic = fromCallback<EventObject, { defaultSize: number }>(  ({    sendBack,    receive,    input, // Typed as { defaultSize: number }  }) => {    input.defaultSize; // 100    // ...  },);const machine = setup({  actors: {    resizeLogic,  },}).createMachine({  // ...  invoke: {    src: 'resizeLogic',    input: {      defaultSize: 100,    },  },});
```

---

## Cheatsheet

**URL:** https://stately.ai/docs/cheatsheet

**Contents:**
- Cheatsheet
- Installing XState​
- Creating a state machine​
- Creating promise logic​
- Creating transition logic​
- Creating observable logic​
- Creating callback logic​
- Parent states​
- Actions​
- Guards​

Use this cheatsheet to quickly look up the syntax for XState v5.

Read more on installing XState.

Read more about the actor model.

Read more about promise actor logic.

A transition function is just like a reducer.

Read more about transition actors.

Read more about observable actors.

Read more about callback actors.

Read more about parent states.

Read more about actions.

Read more about guards.

Read more about invoking actors.

Read more about spawning actors.

Read more about input.

Read more about invoking actors with input.

**Examples:**

Example 1 (bash):
```bash
npm install xstate
```

Example 2 (bash):
```bash
pnpm install xstate
```

Example 3 (bash):
```bash
yarn add xstate
```

Example 4 (ts):
```ts
import { setup, createActor, assign } from 'xstate';const machine = setup({  /* ... */}).createMachine({  id: 'toggle',  initial: 'active',  context: { count: 0 },  states: {    active: {      entry: assign({        count: ({ context }) => context.count + 1,      }),      on: {        toggle: { target: 'inactive' },      },    },    inactive: {      on: {        toggle: { target: 'active' },      },    },  },});const actor = createActor(machine);actor.subscribe((snapshot) => {  console.log(snapshot.value);});actor.start();// logs 'active' with context { count: 1 }actor.send({ type: 'toggle' })
...
```

---

## Inspection

**URL:** https://stately.ai/docs/inspection

**Contents:**
- Inspection
- Inspection events​
- Actor inspection events​
- Event inspection events​
- Snapshot inspection events​
- Microstep inspection events​

The Inspect API is a way to inspect the state transitions of your state machines and every aspect of actors in an actor system. Including:

We’ve recently released Stately Inspector, a universal tool that enables you to visually inspect the state of any application, frontend or backend, with the visualization of Stately’s editor.

Learn more about Stately Inspector

The Inspect API lets you attach an “inspector,” an observer that observes inspection events, to the root of an actor system:

The inspector will receive inspection events for every actor in the system, giving you granular visibility into everything happening, from how an individual actor is changing to how actors communicate with each other.

Inspection events are event objects that have a type property that indicates the type of inspection event. There are four types of inspection events:

The actor inspection event (@xstate.actor) is emitted when an actor in the system is created. It contains the following properties:

Example of an actor inspection event:

The event inspection event (@xstate.event) is emitted when an event is sent to an actor. It contains the following properties:

Example of an event inspection event:

The snapshot inspection event (@xstate.snapshot) is emitted when an actor's snapshot is updated. It contains the following properties:

Example of a snapshot inspection event:

The microstep inspection event (@xstate.microstep) is emitted for each individual state transition, including intermediate "microsteps", that occurs during the processing of an event. This is particularly useful for observing eventless transitions (like always transitions) and understanding the step-by-step progression through multiple states.

It contains the following properties:

Each transition object in the transitions array contains:

Example of a microstep inspection event:

Here's an example of microstep events:

The microstep events will look like this:

**Examples:**

Example 1 (tsx):
```tsx
const actor = createActor(machine, {  inspect: (inspectionEvent) => {    // type: '@xstate.actor' or    // type: '@xstate.snapshot' or    // type: '@xstate.event' or    // type: '@xstate.microstep'    console.log(inspectionEvent);  },});
```

Example 2 (js):
```js
{  type: '@xstate.actor',  actorRef: {/* Actor reference */},  rootId: 'x:0',}
```

Example 3 (js):
```js
{  type: '@xstate.event',  actorRef: {/* Actor reference */},  rootId: 'x:0',  event: {    type: 'someEvent',    message: 'hello'  },  sourceRef: {/* Actor reference */},}
```

Example 4 (js):
```js
{  type: '@xstate.snapshot',  actorRef: {/* Actor reference */},  rootId: 'x:0',  snapshot: {    status: 'active',    context: { count: 31 },    // ...  },  event: {    type: 'increment'  }}
```

---

## State machine actors

**URL:** https://stately.ai/docs/state-machine-actors

**Contents:**
- State machine actors
- State machine actor capabilities​
- State machine actor logic​
- State machine actor input​
- State machine actor output​

State machine actors are actors whose logic is represented by a finite state machine or statechart.

This page is just an overview on using state machines to represent actor logic. Read the documentation on state machines to learn about state machines in depth.

You can define state machine actor logic using the createMachine(...) actor logic creator, which takes a finite state machine or statechart configuration object as its only argument.

You can pass in input to a state machine actor by passing it to the createActor(...) function as the input property of the second argument. In the state machine (setup(…).createMachine(…)), you read the input property of the first argument passed to the context function:

Read state machine output for more information.

**Examples:**

Example 1 (ts):
```ts
import { createMachine, createActor } from 'xstate';const toggleMachine = createMachine({  initial: 'inactive',  states: {    inactive: {      on: {        toggle: {          target: 'active',        },      },    },    active: {      on: {        toggle: {          target: 'inactive',        },      },    },  },});const toggleActor = createActor(toggleMachine);toggleActor.subscribe((snapshot) => {  console.log(snapshot.value); // 'inactive' or 'active'});toggleActor.start();// logs 'inactive'toggleActor.send({ type: 'toggle' });// logs 'active'toggleActor.send({ type: 'toggle' });// logs 'ina
...
```

Example 2 (ts):
```ts
import { setup, createActor } from 'xstate';const feedbackMachine = setup({  // ...}).createMachine({  context: ({ input }) => ({    rating: input.defaultRating,  }),  initial: 'question',  states: {    question: {      /* ... */    },    // ...  },});const feedbackActor = createActor(feedbackMachine, {  input: {    defaultRating: 3,  },});feedbackActor.subscribe((snapshot) => {  console.log(snapshot.context);});feedbackActor.start();// logs { rating: 3 }
```

---

## Promise actors

**URL:** https://stately.ai/docs/promise-actors

**Contents:**
- Promise actors
- Promise actor capabilities​
- Promise actor logic​
- Promise actor input​
- Promise actor output​
- Promise actor error handling​
- Stopping promise actors​

Promise actors are actors that represent a promise that performs some asynchronous task. They may resolve with some output, or reject with an error.

You can define promise actor logic using the fromPromise(...) actor logic creator, which takes a function that returns a promise and returns actor logic that can be used to create promise actors.

You can pass in input to a promise actor by passing it to the createActor(...) function as the input property of the second argument. In the promise logic (fromPromise(promiseFn)), you read the input property of the first argument passed to the promise function:

The input type is inferred from the promise function's first argument type. You can also provide an explicit input type in the second generic parameter:

To get the eventual resolved output of a promise actor, you can subscribe to the promise actor and check the status property of the snapshot. If the status is 'done', you can access the output property of the snapshot to get the resolved value. Otherwise, the output will be undefined.

You can also use toPromise(...) to convert any actor, including promise actors, to a promise. This is useful if you want to await the output of an actor:

If an error occurs in the promise logic, the promise actor will reject with the error. You can subscribe to the promise actor and check the status property of the snapshot in an error observer (the error property of the observer object).

You can stop a promise actor created using createActor(promiseLogic) by calling .stop() on the actor instance. This will discard the resolved or rejected value of the promise and dispose of any subscriptions to the promise actor.

You can also abort a promise actor by passing the signal to it:

**Examples:**

Example 1 (ts):
```ts
import { fromPromise, createActor } from 'xstate';async function getUser(id: string) {  // ...  return { id /* other user data */ };}const promiseLogic = fromPromise(async () => {  const user = await getUser('123');  return user;});const promiseActor = createActor(promiseLogic);promiseActor.subscribe((snapshot) => {  console.log(snapshot.status, snapshot.output);});promiseActor.start();// logs 'active', undefined// ... (after some time)// logs 'done', { id: '123', /* other user data */ }
```

Example 2 (ts):
```ts
import { fromPromise, createActor } from 'xstate';const promiseLogic = fromPromise(async ({ input }) => {  const user = await getUser(input.id);  return user;});const promiseActor = createActor(promiseLogic, {  input: { id: '123' },});
```

Example 3 (ts):
```ts
import { fromPromise } from 'xstate';interface User {  name: string;  id: string;}const secondLogic = fromPromise(  async ({ input }: { input: { id: string } }) => {    const user = await getUser(input.id); // User is inferred    return user;  },);const firstLogic = fromPromise<User, { id: string }>(  async ({ input, self /* ... */ }) => {    const user = await getUser(input.id);    return user;  },);
```

Example 4 (ts):
```ts
import { fromPromise } from 'xstate';const promiseLogic = fromPromise(async () => {  const user = await getUser('123');  return user;});const promiseActor = createActor(promiseLogic);promiseActor.subscribe((snapshot) => {  if (snapshot.status === 'done') {    console.log(snapshot.output);    // logs { id: '123', /* other user data */ }  }});
```

---

## The Actor model

**URL:** https://stately.ai/docs/actor-model

**Contents:**
- The Actor model
  - What defines an “actor”?​
- State​
- Communication with events​
- Spawning​
- The actor model in backend development​
- The actor model in frontend development​
- XState​
- Reference​

The Actor model in computer science is a mathematical model of concurrent computation in which an “actor” is the basic building block.

The actor model allows developers to build reliable message-based systems by using actors to communicate. State machines and statecharts can model the logic of actors. These actors can communicate with each other, and with other actors, in the same way.

When you run a state machine in XState, it becomes an actor.

Actors are independent “live” objects that can communicate with each other via asynchronous message passing. In XState, we refer to these messages as events.

You’ll find strong similarities to the actor model in software you may already be familiar with. The concept of objects encapsulating state and passing messages to each other may be familiar from Object-Oriented Programming. And actors are analagous to real-world physical concepts like cell biology, and communication in human relationships.

An actor has its own internal, encapsulated state that only the actor itself can update. An actor may update its internal state in response to a message it receives, but it cannot be updated by any other entity. Actors do not share state. The only way for an actor to share data is by sending events.

Read more about XState actors and state.

Actors communicate with other actors by sending and receiving events asynchronously. Actors use an internal “mailbox” that acts like an event queue, processing events one at a time.

Read more about XState events and transitions.

Actors can spawn new actors, which is useful in situations where an actor needs to delegate work to another actor. Spawning allows for a flexible and dynamic system where actors can be created and destroyed as needed to handle the workload efficiently.

The actor model is often used to coordinate backend systems. There are direct implementations of the Actor model, like Akka for the JVM. In Erlang, processes can be seen as actors, which can send and receive messages and spawn new processes. Erlang is used by massive distributed systems, like Discord and WhatsApp.

In Stately Sky, a state machine actor can be used to manage long-running backend processes like medical patient onboarding flows, inventory management, or multi-player collaborative experiences like whiteboard canvases or games.

The actor model is especially useful for coordinating the many moving parts of a front-end web application.

Your front-end app is always a distributed system, and mana

*[Content truncated]*

---

## XState examples

**URL:** https://stately.ai/docs/examples

**Contents:**
- XState examples
- Simple fetch example​
- 7GUIs counter (React)​
- 7GUIs temperature (React)​
- Simple list (React)​
- Stopwatch​
- Tic-tac-toe game (React)​
- Tiles game (React)​
- TodoMVC (React)​
- Toggle​

XState v5 examples are also available in the /examples directory. Many of the examples have a CodeSandbox link where you can run the example in your browser.

A simple fetch example built with:

Simple fetch example on GitHub

Simple fetch example on CodeSandbox

An implementation of the 7GUIs counter built with:

7GUIs counter (React) on GitHub

7GUIs counter (React) on CodeSandbox

This is an implementation of the 7GUIs temperature converter built with:

7GUIs temperature (React) on GitHub

7GUIs temperature (React) on CodeSandbox

A React list built with:

Simple list (React) on GitHub

Simple list (React) on CodeSandbox

A simple stopwatch built with:

Stopwatch on CodeSandbox

An implementation of tic-tac-toe built with:

Tic-tac-toe game (React) on GitHub

Tic-tac-toe game (React) on CodeSandbox

A simple tiles game built with:

Tiles game (React) on GitHub

Tiles game (React) on CodeSandbox

An implementation of TodoMVC built with:

TodoMVC (React) on GitHub

TodoMVC (React) on CodeSandbox

A simple toggle built with:

Toggle on CodeSandbox

Serverless hello world workflow from the CNCF Serverless Workflow examples built with:

Hello world workflow on GitHub

Serverless greeting workflow from the CNCF Serverless Workflow examples built with:

Greeting workflow on GitHub

Serverless event-based greeting workflow from the CNCF Serverless Workflow examples built with:

Event-based greeting workflow on GitHub

Serverless math solving problem workflow from the CNCF Serverless Workflow examples built with:

Solving math problems on GitHub

Serverless parallel execution workflow from the CNCF Serverless Workflow examples built with:

Parallel execution workflow on GitHub

Serverless async function invocation workflow from the CNCF Serverless Workflow examples built with:

Async function invocation workflow on GitHub

Serverless async subflow invocation workflow from the CNCF Serverless Workflow examples built with:

Async subflow invocation workflow on GitHub

Serverless event-based transitions workflow from the CNCF Serverless Workflow examples built with:

Event-based transitions workflow on GitHub

Serverless applicant request decision workflow from the CNCF Serverless Workflow examples built with:

Applicant request decision workflow on GitHub

Serverless provision orders (error handling) workflow from the CNCF Serverless Workflow examples built with:

Provision orders (error handling) workflow on GitHub

Serverless monitor job for completion (polling)

*[Content truncated]*

---

## Templates

**URL:** https://stately.ai/docs/templates

**Contents:**
- Templates

XState runs anywhere you can run JavaScript and is framework-agnostic.

You can get started with or without your framework of choice using the templates below.

🤖 XState Template Open in CodeSandbox Open in StackBlitz

⚛️ XState + React Template Open in CodeSandbox Open in StackBlitz

💚 XState + Vue Template Open in CodeSandbox Open in StackBlitz

---

## Systems

**URL:** https://stately.ai/docs/system

**Contents:**
- Systems
- Actor registration​
- Actor communication​
- Stopping a system​
- Systems and TypeScript​
- System cheatsheet​
  - Cheatsheet: actor system​
  - Cheatsheet: explicitly assign a systemId​
  - Cheatsheet: register an invoked actor with the system​
  - Cheatsheet: register a spawned actor with the system​

An actor system is a collection of actors that can communicate with each other. Actors can invoke/spawn other actors, which forms a natural hierarchy of actors that belong to the same system.

In XState, a system is implicitly created from the root actor, which is the actor that is returned from createActor(machine).start(). The system can be accessed from the actor.system property of actors, and from the destructured { system } property from state machine actions:

The root of a system can also be explicitly assigned a systemId in the createActor(...) function:

This is useful for actors in the system to be able send events to the root actor.

Coming soon… systems in Stately Studio’s editor 🤫

Actors can be registered with the system so that any other actor in the system can obtain a reference to it.

Invoked actors are registered with a system-wide systemId in the invoke object:

Spawned actors are registered with a system-wide systemId in the 2nd argument of the spawn function:

You can also reference a specific actor from the system using system.get('actorId'):

XState v5 requires TypeScript version 5.0 or greater.

For best results, use the latest TypeScript version. Read more about XState and TypeScript

**Examples:**

Example 1 (ts):
```ts
import { createMachine, createActor } from 'xstate';const machine = createMachine({  entry: ({ system }) => {    // ...  },});const actor = createActor(machine).start();actor.system;
```

Example 2 (ts):
```ts
import { createActor } from 'xstate';const actor = createActor(machine, {  systemId: 'root-id',});actor.start();
```

Example 3 (ts):
```ts
import { createMachine, createActor, sendTo } from 'xstate';const formMachine = createMachine({  // ...  on: {    submit: {      actions: sendTo(({ system }) => system.get('notifier'), {        type: 'notify',        message: 'Form submitted!',      }),    },  },});const feedbackMachine = createMachine({  invoke: {    systemId: 'formMachine',    src: formMachine,  },  // ...  states: {    // ...    form: {      invoke: formMachine,    },  },});const feedbackActor = createActor(feedbackMachine).start();
```

Example 4 (ts):
```ts
import { createMachine, createActor, assign } from 'xstate';const todoMachine = createMachine({  // ...});const todosMachine = createMachine({  // ...  on: {    'todo.add': {      actions: assign({        todos: ({ context, spawn }) => {          const newTodo = spawn(todoMachine, {            systemId: `todo-${context.todos.length}`,          });          return context.todos.concat(newTodo);        },      }),    },  },});
```

---

## Spawn

**URL:** https://stately.ai/docs/spawn

**Contents:**
- Spawn
- API​
- Source​
- Lifecycle​
- Stopping an actor​
- Spawn and TypeScript​
- Spawn cheatsheet​
  - Basic Spawning​
  - Spawning with Context​
  - Spawning Different Actor Types​

Sometimes invoking actors may not be flexible enough for your needs. For example, you might want to:

You can do this by spawning an actor instead of invoking. Actors created by spawning are spawning actors, and actors created with invoke are invoking actors.

Read about the difference between spawning and invoking actors.

There are two ways to spawn: the spawnChild action creator, or the spawn helper function for assign.

In most cases, prefer spawnChild, which causes an actor to be spawned, and can accept a configurable ID for the actor to reference it later:

You can use spawnChild for multiple spawned actors:

You can also use the spawn helper function provided by the assign action creator, which allows you to store a reference to the spawned actor (an ActorRef) in the machine's context:

However, if you use spawn, make sure you remove the ActorRef from context to prevent memory leaks when the spawned actor is no longer needed:

You can spawn as many actors as you need:

If you don't need to keep track of a reference to the spawned actor (e.g.: for anonymous spawned actors), you can use the spawnChild action creator. It does not return a reference, but indicates to the XState interpreter that a new actor should be spawned:

You can stop a child actor via the "stop child" action. This action is created from the stopChild(id) action creator.

Stopping a child actor does not remove it from context. To remove it from context, use the assign(...) action creator:

XState v5 requires TypeScript version 5.0 or greater.

For best results, use the latest TypeScript version. Read more about XState and TypeScript

**Examples:**

Example 1 (ts):
```ts
createMachine({  entry: spawnChild(childMachine, {    id: 'child',  }),});
```

Example 2 (ts):
```ts
createMachine({  entry: [    spawnChild(childMachine, { id: 'child-1' }),    spawnChild(childMachine, { id: 'child-2' }),    spawnChild(childMachine, { id: 'child-3' }),  ],});
```

Example 3 (ts):
```ts
const parentMachine = createMachine({  entry: [    assign({      childMachineRef: ({ spawn }) => spawn(childMachine, { id: 'child' }),    }),  ],});
```

Example 4 (ts):
```ts
actions: [stopChild('child'), assign({ childMachineRef: undefined })];
```

---

## Transition Actors

**URL:** https://stately.ai/docs/transition-actors

**Contents:**
- Transition Actors
- Transition actor capabilities​
- Transition actor logic​
- Transition actor input​

Transition actors are actors whose logic is represented by a state-transition function, which is a function that returns the next state of an actor given:

This is very similar to a reducer function in libraries like Redux.

You can define transition actor logic using the fromTransition(...) actor logic creator, which takes two arguments:

The actor logic creator returns actor logic that can be used to create transition actors.

You can pass in input to a transition actor, which will be passed to the function that resolves the initial state.

**Examples:**

Example 1 (ts):
```ts
import { fromTransition, createActor } from 'xstate';const countLogic = fromTransition(  (state, event) => {    switch (event.type) {      case 'increment': {        return { count: state.count + 1 };      }      case 'decrement': {        return { count: state.count - 1 };      }      default: {        return state;      }    }  },  { count: 0 },); // Initial stateconst countActor = createActor(countLogic);countActor.subscribe((snapshot) => {  console.log(snapshot.context);});countActor.start();// logs { count: 0 }countActor.send({ type: 'increment' });// logs { count: 1 }countActor.send({ ty
...
```

Example 2 (ts):
```ts
import { fromTransition, createActor } from 'xstate';const countLogic = fromTransition(  (state, event) => {    // ...  },  ({ input }: { input: number }) => ({    count: input, // Initial state  }),);const countActor = createActor(countLogic, {  input: 42,});countActor.subscribe((snapshot) => {  console.log(snapshot.context);  // logs { count: 42 }});countActor.start();
```

---

## Invoke

**URL:** https://stately.ai/docs/invoke

**Contents:**
- Invoke
- How are actors different from actions?​
- Lifecycle​
  - Re-entering​
- The invoke property API​
  - Source​
    - Inline src​
    - Provided src​
  - onDone​
  - onError​

State machines can “invoke” one or many actors within a given state. The invoked actor will start when the state is entered, and stop when the state is exited. Any XState actor can be invoked, including simple Promise-based actors, or even complex machine-based actors.

Invoking an actor is useful for managing synchronous or asynchronous work that the state machine needs to orchestrate and communicate with at a high level, but doesn't need to know about in detail.

Read about the difference between invoking and spawning actors.

You can visualize your state machines and easily invoke actors in our drag-and-drop Stately editor. Read more about actors in Stately’s editor.

Actors can be invoked within any state except for the top-level final state. In the following example, the loading state invokes a Promise-based actor:

Actors can also be invoked on the root of the machine, and they will be active for the lifetime of their parent machine actor:

And invoke can be an array, to invoke multiple actors:

For further examples, see:

Actions are “fire-and-forget”; as soon as their execution starts, the state machine running the actions forgets about them. If you specify an action as async, the action won’t be awaited before moving to the next state. Remember: transitions are always zero-time (states transition synchronously).

Invoked actors can do asynchronous work and communicate with their parent machine actor. They can send and receive events. Invoked machine actors can even invoke or spawn their own child actors.

Unlike actions, errors thrown by invoked actors can be handled directly:

Whereas errors thrown by actions can only be handled globally by a subscriber of their parent state machine:

Invoked actors have a lifecycle that is managed by the state they are invoked in. They are created and started when the state is entered, and stopped when the state is exited.

If a state is entered and then immediately exited, e.g. due to an eventless ("always") transition, then no actors will be invoked on that state.

By default, when a state machine transitions from a parent state to the same parent state or a descendent (child or deeper), it will not re-enter the parent state. Because the transition is not re-entering, the parent state's existing invoked actors will not be stopped and new invoked actors will not be started.

However, if you want a transition to re-enter the parent state, set the transition's reenter property to true. Transitions that re-enter t

*[Content truncated]*

**Examples:**

Example 1 (ts):
```ts
import { setup, createActor, fromPromise, assign } from 'xstate';const fetchUser = (userId: string) =>  fetch(`https://example.com/${userId}`).then((response) => response.text());const userMachine = setup({  types: {    context: {} as {      userId: string;      user: object | undefined;      error: unknown;    },  },  actors: {    fetchUser: fromPromise(async ({ input }: { input: { userId: string } }) => {      const user = await fetchUser(input.userId);      return user;    }),  },}).createMachine({  id: 'user',  initial: 'idle',  context: {    userId: '42',    user: undefined,    error: und
...
```

Example 2 (ts):
```ts
import { fromEvent } from 'rxjs';import { fromEventObservable } from 'xstate';const interactiveMachine = createMachine({  invoke: {    src: fromEventObservable(      () => fromEvent(document.body, 'click') as Subscribable<EventObject>,    ),  },  on: {    click: {      actions: ({ event }) => console.log(event),    },  },});
```

Example 3 (ts):
```ts
const vitalsWorkflow = createMachine({  states: {    CheckVitals: {      invoke: [        { src: 'checkTirePressure' },        { src: 'checkOilPressure' },        { src: 'checkCoolantLevel' },        { src: 'checkBattery' },      ],    },  },});
```

Example 4 (ts):
```ts
invoke: {  src: 'fetchUser',  onError: {    target: 'failure',    actions: assign({ error: ({ event }) => event.error })  }}
```

---
