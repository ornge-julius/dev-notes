## React-Redux Hooks

### When to use it
Use `useSelector` to read a value from the Redux store inside a component.
Use `useDispatch` to send an action to the store from an event handler.

### Pattern
```jsx
// main.jsx
import { Provider } from "react-redux";
import { store } from "./store";

function Root() {
  return (
    <Provider store={store}>
      <App />
    </Provider>
  );
}

// Counter.jsx
import { useSelector, useDispatch } from "react-redux";
import { incremented, decremented } from "./counterSlice";

function Counter() {
  const count = useSelector((state) => state.counter.value);
  const dispatch = useDispatch();

  return (
    <div>
      <p>Count: {count}</p>
      <button onClick={() => dispatch(incremented())}>+</button>
      <button onClick={() => dispatch(decremented())}>-</button>
    </div>
  );
}
```

### How it works
`<Provider store={store}>` makes the store available to every component
nested inside it, using React context underneath. `useSelector` takes a
function that receives the whole state tree and returns the one piece of
data the component needs. React-Redux re-renders the component only when
the selected value actually changes. `useDispatch` returns the store's
`dispatch` function, which the component calls with an action to trigger
a state update through the matching reducer.

### Common mistakes
- Selecting the entire state object, such as `useSelector((state) =>
  state)`, instead of one specific slice, which causes the component to
  re-render on every unrelated state change.
- Creating a new object or array inside the selector on every call, such
  as `useSelector((state) => ({ ...state.counter }))`, which breaks the
  equality check and causes extra renders.
- Calling `dispatch` with a plain object that does not match an action the
  reducer understands.

### Interview angle
Q: Why can selecting too much state with `useSelector` hurt performance?
A: React-Redux re-renders the component whenever the selected value
changes by reference. Selecting a large or newly created object means the
reference changes on nearly every store update, even when the specific
data the component cares about did not change.
