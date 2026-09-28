## Closures

### When to use it
You do not choose to "use" a closure the way you choose a method. A
closure forms automatically any time a function is defined inside
another function. Understanding closures explains why event handlers,
`useEffect`, and `useState` updater functions sometimes see an "old"
value.

### Pattern
```js
function makeCounter() {
  let count = 0;
  return function increment() {
    count += 1;
    return count;
  };
}

const counter = makeCounter();
counter(); // 1
counter(); // 2

// The same idea, explaining a common React bug
function Timer() {
  const [count, setCount] = useState(0);

  useEffect(() => {
    const id = setInterval(() => {
      // This closure "closes over" count from the render that
      // created this effect, and never sees it change.
      console.log("count is still", count);
      setCount(count + 1);
    }, 1000);

    return () => clearInterval(id);
  }, []); // count is missing from the dependency array

  return <p>{count}</p>;
}
```

### How it works
A closure is a function bundled together with the variables that were in
scope when it was created. `increment` keeps access to `count` from
`makeCounter` even after `makeCounter` has already finished running,
because the returned function still holds onto that variable. In the
`Timer` example, the arrow function passed to `setInterval` closes over
the `count` variable from the exact render when the effect first ran.
Because the dependency array is `[]`, the effect never re-runs, so the
interval callback keeps using that original `count` value forever, even
though `count` changes on screen. This is often called a "stale closure."

### Common mistakes
- Leaving a value out of `useEffect`'s dependency array when the effect's
  callback actually reads that value, which creates a stale closure bug.
- Assuming a variable's value inside a callback is always current, when
  it is really frozen at the moment the closure was created.
- Fixing a stale closure by adding the value to the dependency array
  without also fixing the effect's cleanup, which can cause repeated
  setup and teardown of timers or subscriptions.

### Interview angle
Q: In the `Timer` example, why does `console.log` always print `0` even
though the counter visibly increases on screen?
A: The `setInterval` callback closed over `count` from the render when
the effect first ran, and the empty dependency array means the effect
never re-runs to capture a fresh value, so the callback keeps using the
original `0` forever.
