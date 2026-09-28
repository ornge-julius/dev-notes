## Classic Redux

### When to use it
Use this pattern to understand what Redux Toolkit does under the hood, or
in a codebase that has not adopted Redux Toolkit yet.

### Pattern
```js
// actionTypes.js
export const INCREMENTED = "counter/incremented";
export const DECREMENTED = "counter/decremented";

// actions.js
import { INCREMENTED, DECREMENTED } from "./actionTypes";

export function increment() {
  return { type: INCREMENTED };
}

export function decrement() {
  return { type: DECREMENTED };
}

// counterReducer.js
import { INCREMENTED, DECREMENTED } from "./actionTypes";

const initialState = { value: 0 };

export function counterReducer(state = initialState, action) {
  switch (action.type) {
    case INCREMENTED:
      return { ...state, value: state.value + 1 };
    case DECREMENTED:
      return { ...state, value: state.value - 1 };
    default:
      return state;
  }
}

// store.js
import { createStore, combineReducers } from "redux";
import { counterReducer } from "./counterReducer";

const rootReducer = combineReducers({
  counter: counterReducer,
});

export const store = createStore(rootReducer);
```

### How it works
Action types are string constants so that a typo shows up as an import
error instead of a silent bug. An action creator is a plain function that
returns an action object, which keeps the shape of every action
consistent. `combineReducers` merges many small reducers into one root
reducer, and each reducer only owns its own slice of the state tree, named
by its key (`counter` in this example). `createStore` builds the actual
store from that root reducer.

### Common mistakes
- Mutating `state` with `state.value++` instead of returning a new object
  with `{ ...state, value: state.value + 1 }`.
- Forgetting the `default: return state` case, which makes the reducer
  return `undefined` for actions it does not handle.
- Writing the action type string directly in both the action creator and
  the reducer, instead of sharing one constant, which risks a mismatch.

### Interview angle
Q: What does `combineReducers` actually do?
A: It takes an object of reducers and returns one root reducer function
that calls each child reducer with its own named slice of state, then
merges the results back into one state object.
