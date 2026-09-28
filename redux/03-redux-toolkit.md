## Redux Toolkit

### When to use it
Use Redux Toolkit for any new Redux code. It is the standard, recommended
way to write Redux today, and it replaces the classic pattern from
[Classic Redux](02-classic-redux.md) with far less code.

### Pattern
```js
// counterSlice.js
import { createSlice } from "@reduxjs/toolkit";

const counterSlice = createSlice({
  name: "counter",
  initialState: { value: 0 },
  reducers: {
    incremented: (state) => {
      state.value += 1;
    },
    decremented: (state) => {
      state.value -= 1;
    },
    incrementedByAmount: (state, action) => {
      state.value += action.payload;
    },
  },
});

export const { incremented, decremented, incrementedByAmount } =
  counterSlice.actions;
export default counterSlice.reducer;

// store.js
import { configureStore } from "@reduxjs/toolkit";
import counterReducer from "./counterSlice";

export const store = configureStore({
  reducer: {
    counter: counterReducer,
  },
});
```

### How it works
`createSlice` takes a name, an initial state, and an object of reducer
functions, and generates the action types, action creators, and the
reducer for you. Inside a slice reducer, you can write `state.value += 1`
as if you were mutating the state directly. Redux Toolkit uses a library
called Immer underneath, which tracks that "mutation" and produces a
proper new immutable state object for you, so the direct-mutation rule
from classic Redux is not broken, it is just handled automatically.
`configureStore` replaces `createStore` and `combineReducers`, and it also
turns on the Redux DevTools and a default set of middleware.

### Common mistakes
- Returning a new state object AND mutating `state` in the same reducer
  function, which confuses Immer. Either mutate `state` directly, or
  return a brand new value, not both.
- Trying to reassign the whole `state` parameter, such as `state = {}`,
  which does not work with Immer's mutation tracking. Return the new value
  instead.
- Forgetting that `action.payload` is the standard field name Redux
  Toolkit expects for the action's data.

### Interview angle
Q: How can you "mutate" state directly inside a Redux Toolkit reducer when
classic Redux strictly forbids mutation?
A: Redux Toolkit uses Immer, which lets you write mutating-looking code
against a draft state, then produces a new immutable state object behind
the scenes, so the actual store update is still immutable.
