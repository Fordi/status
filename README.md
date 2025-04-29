Flexible status management object with multiple possible representations.

> Important: this is a work in progress, and does not yet have the documented functionality.  Please do not use it or expect anything from it.

## Installation

```bash
$ npm install @fordi-org/status
```

## Usage

First we create a `StatusManager`, giving it a state management delegate (defaulting to `new StateManager()`), to which we can then attach renderers.

```javascript
import { StatusManager, PercentStateManager, ConsoleLogger } from '@fordi-org/status';

const stateManager = new PercentStateManager({ total: 10 });
const status = new StatusManager({ stateManager });
const renderer = new ConsoleLogger(ConsoleLogger.defaultFormatter);

status.attach(renderer);

var timer = setInterval(function () {
  status.tick();
  if (status.complete) {
    console.log('\ncomplete\n');
    clearInterval(timer);
  }
}, 100);
```

## Interfaces

### `Renderer<T>`

```typescript
interface Renderer<T> {
  // Called by StatusManager to start output
  begin(): Promise<void>;
  // Called by StatusManager to suspend output
  suspend(): Promise<void>;
  // Called by StatusManager to end output
  end(): Promise<void>;
  // Called by StatusManager to set the next render's state
  update(state: T): Promise<void>;
}
```

A `Renderer` is responsible for taking an incoming `state` and rendering it to its render target (e.g., the console, a web page, or a github issue).  It can throttle incoming state updates in various ways, but the primary mechanism is by delaying resolution of the Promises it returns.

### `Formatter<T, R>`

```typescript
type Formatter<T, R> = (state: T) => Promise<R>;
```

A Formatter is a simple transform that accepts a state T and outputs a formatted output, R (usually a string, but depending on the Renderer, it doesn't have to be).

### `StateManager<T>`

```typescript
interface StateManager<T> {
  initialize(state: T): void;
  tick(updates: Partial<T>): void;
  get [key: keyof T](): T[key];
  valueOf(): T;
}
```

A StateManager manages the state.  It's what you pass around to processes that should update status, and is responsible for hydrating state updates.  For example, if you have `curr` and `total` fields in your state, it calculates `complete` from those as needed.

### `StatusManager<T>`

```typescript
interface StatusManager<T> {
  tick(updates: Partial<T>): Promise<void>;
  terminate(): Promise<void>;
}
```

A `StatusManager` is responsible for coordination between a `StateManager` and a `Renderer`.
