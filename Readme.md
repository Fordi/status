Flexible status management object with multiple possible representations.

> Important: this is a work in progress, and does not yet have the documented functionality.  Please do not use it or expect anything from it.

## Installation

```bash
$ npm install @fordi-org/status
```

## Usage

First we create a `StatusManager`, giving it a state management delegate (defaulting to `new StateManager()`), to which we can then attach renderers.

```javascript
import { StatusManager, StateManager, ConsoleLogger } from '@fordi-org/status';

const stateManager = new StateManager({ total: 10 })
const status = new StatusManager({ stateManager });
const renderer = new ConsoleLogger(ConsoleLogger.defaultFormatter);
renderer.begin();
status.attach(renderer);

var timer = setInterval(function () {
  status.tick();
  if (status.complete) {
    renderer.close();
    console.log('\ncomplete\n');
    clearInterval(timer);
  }
}, 100);
```
