## useContext, useRef, useMemo, useCallback

### When to use it
Use `useContext` to share a value across many components without passing
props through every level. Use `useRef` to hold a mutable value that must
not trigger a re-render, or to reach into a DOM node. Use `useMemo` and
`useCallback` to skip expensive recalculations or to keep a function
reference stable, but only after a real performance problem shows up.

### Pattern
```jsx
import { createContext, useContext, useRef, useMemo, useCallback } from "react";

const ThemeContext = createContext("light");

function ThemedButton() {
  const theme = useContext(ThemeContext);
  return <button className={theme}>Click me</button>;
}

function SearchBox({ items }) {
  const inputRef = useRef(null);

  const sortedItems = useMemo(() => {
    return [...items].sort((a, b) => a.localeCompare(b));
  }, [items]);

  const handleFocus = useCallback(() => {
    inputRef.current.focus();
  }, []);

  return (
    <div>
      <input ref={inputRef} type="text" />
      <button onClick={handleFocus}>Focus input</button>
      <ul>
        {sortedItems.map((item) => (
          <li key={item}>{item}</li>
        ))}
      </ul>
    </div>
  );
}

function App() {
  return (
    <ThemeContext.Provider value="dark">
      <ThemedButton />
    </ThemeContext.Provider>
  );
}
```

### How it works
`createContext` makes a context object with a default value. A
`Provider` sets the value for every component nested inside it, and
`useContext(ThemeContext)` reads the closest matching value. `useRef`
returns an object with a `current` property that persists across renders
without causing a re-render when it changes, which is why it is used to
hold a DOM node reference in `inputRef`. `useMemo` re-runs its function
only when a value in the dependency array changes, and caches the result
otherwise. `useCallback` does the same thing, but caches a function
definition instead of a computed value.

### Common mistakes
- Reaching for `useMemo` or `useCallback` on every function and value,
  which adds complexity without a measured performance benefit.
- Reading or writing `ref.current` during render instead of inside an
  effect or event handler.
- Wrapping too much of the app in one giant context, which causes every
  consumer to re-render whenever any part of that context value changes.

### Interview angle
Q: Why does updating a `ref` not cause a component to re-render?
A: A `ref` is a plain mutable object that React does not track for
rendering purposes. Only state updates through `useState` or `useReducer`
trigger a re-render.
