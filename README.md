# @react-spring/mock-raf

A simple mock for `requestAnimationFrame` testing with fake timers.

Adapted with gratitude from [`react-motion`](https://github.com/chenglou/react-motion/blob/dafff3f2b00ac11f39d91f3363cc97de664b2406/test/createMockRaf.js).

## Example

To use with `jest`/`jasmine` and `react-spring`:

```js
import createMockRaf from '@react-spring/mock-raf'
import { Globals, FrameLoop, SpringValue } from 'react-spring'

let mockRaf
beforeEach(() => {
  mockRaf = createMockRaf()
  Globals.assign({
    now: mockRaf.now,
    performanceNow: mockRaf.now,
    requestAnimationFrame: mockRaf.raf,
    cancelAnimationFrame: mockRaf.cancel,
    frameLoop: new FrameLoop(),
  })
})

it('animates from 0 to 1', () => {
  const value = new SpringValue(0)
  value.start(1)

  expect(value.get()).toBe(0)
  mockRaf.flush()
  expect(value.get()).toBe(1)
})
```

## API

### `createMockRaf()`

Creates a `mockRaf` instance, exposing the functions you'll use to interact with the mock.

Returns:

- now()
- raf()
- cancel()
- step()

```js
const mockRaf = createMockRaf()
```

&nbsp;

### `now()`

Returns the current `now` value of the mock. Starts at 0 and increases with each `step()` taken. Useful for stubbing out `performance.now()` or a polyfill when using `requestAnimationFrame` with timers.

&nbsp;

### `raf()`

Replacement for `requestAnimationFrame` or a polyfill. Adds a callback to be fired on the next step.

&nbsp;

### `cancel()`

Replacement for `cancelAnimationFrame` or a polyfill. Removes all currently scheduled `requestAnimationFrame` callbacks from the queue.

&nbsp;

### `flush()`

Continuously iterate the `requestAnimationFrame` queue until empty.

Useful for jumping to the end of an animation (or group of animations).

&nbsp;

### `step(options)`

Takes `requestAnimationFrame` steps. Fires currently queued callbacks for each step and increments `now` time for each step. The primary way to interact with a `mockRaf` instance for testing.

#### Options

`step()` takes an optional `options` object:

##### `time`

Type: `Number` Default: `1000 / 60`

The time that should pass during each `requestAnimationFrame` step in milliseconds. Default is roughly equivalent to default browser behavior.

##### `count`

Type: `Number` Default: `1`

The number of steps to take.
