## Core Concepts: Store, Action, Reducer

### When to use it
Use Redux when several unrelated components across the app need to read
and update the same piece of state, and passing that state through props
would require drilling it through many layers.

### Pattern
```
Data flow in one direction:

  UI event
    -> dispatch(action)
      -> reducer(state, action)
        -> new state
          -> store updates
            -> UI re-renders with new state
```

```js
// An action is a plain object with a "type" field.
const action = { type: "counter/incremented" };

// A reducer is a pure function: (state, action) => newState.
function counterReducer(state = { value: 0 }, action) {
  switch (action.type) {
    case "counter/incremented":
      return { value: state.value + 1 };
    default:
      return state;
  }
}
```

### How it works
The store holds the entire application state in one JavaScript object.
The only way to change that state is to dispatch an action, which is a
plain object describing what happened. The reducer is a pure function that
takes the current state and an action, and returns a brand new state
object. It never mutates the existing state and never causes side effects.
The store notifies every subscribed UI component after a reducer produces
a new state, so the screen can re-render with fresh data.

### Common mistakes
- Mutating the existing state object inside a reducer instead of returning
  a new object.
- Putting side effects, such as a network call, directly inside a reducer.
- Treating the action `type` string as optional or inconsistent between
  the action creator and the reducer's `switch` statement.

### Interview angle
Q: Why must a reducer be a pure function?
A: A pure reducer makes state changes predictable and testable, and it
lets Redux tools like time-travel debugging and the DevTools work, because
the same state and action always produce the same result.
