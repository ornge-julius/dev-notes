## State and Events

### When to use it
Use `useState` when a component must remember a value between renders, and
that value can change over time, such as a counter or a toggle.

### Pattern
```jsx
import { useState } from "react";

function Counter() {
  const [count, setCount] = useState(0);

  function handleIncrement() {
    setCount((c) => c + 1);
  }

  return (
    <div>
      <p>Count: {count}</p>
      <button onClick={handleIncrement}>Add one</button>
    </div>
  );
}
```

### How it works
`useState(0)` returns an array with the current value and a setter
function. React re-renders the component every time the setter runs with a
new value. The updater form, `setCount((c) => c + 1)`, receives the latest
state value and is the safe way to update state that depends on the
previous value, especially inside event handlers that may run more than
once before a render happens.

### Common mistakes
- Writing `setCount(count + 1)` twice in a row and expecting `count` to go
  up by two. It will not, because `count` is stale until the next render.
- Calling the state setter during render instead of inside an event
  handler or effect, which causes an infinite render loop.
- Storing a value in state that can be calculated from existing props or
  state, instead of just calculating it during render.

### Interview angle
Q: Why does `setCount((c) => c + 1)` behave differently than
`setCount(count + 1)` when called multiple times in a row?
A: The updater function always receives the most current state value, even
across multiple queued updates, while `count` in the surrounding code is a
snapshot from the last render.
