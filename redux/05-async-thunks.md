## Async Logic with Thunks

### When to use it
Use a thunk when a Redux action must run a side effect first, such as an
API call, before the store's state actually changes.

### Pattern
```js
// usersSlice.js
import { createSlice, createAsyncThunk } from "@reduxjs/toolkit";

export const fetchUser = createAsyncThunk(
  "users/fetchUser",
  async (userId) => {
    const response = await fetch(`/api/users/${userId}`);
    return response.json();
  }
);

const usersSlice = createSlice({
  name: "users",
  initialState: { data: null, status: "idle", error: null },
  reducers: {},
  extraReducers: (builder) => {
    builder
      .addCase(fetchUser.pending, (state) => {
        state.status = "loading";
      })
      .addCase(fetchUser.fulfilled, (state, action) => {
        state.status = "succeeded";
        state.data = action.payload;
      })
      .addCase(fetchUser.rejected, (state, action) => {
        state.status = "failed";
        state.error = action.error.message;
      });
  },
});

export default usersSlice.reducer;

// Component.jsx
dispatch(fetchUser(userId));
```

### How it works
A plain reducer must stay pure and cannot make a network call. A reducer
cannot wait for a promise to finish. `createAsyncThunk` wraps an async
function and automatically dispatches three actions around it: `pending`
when the call starts, `fulfilled` when it succeeds with the returned
value as `action.payload`, and `rejected` when it throws. The
`extraReducers` field on `createSlice` lets that same slice respond to
those three action types, even though they were not defined inside its
own `reducers` object.

### Common mistakes
- Calling `fetch` or another async operation directly inside a
  `createSlice` reducer, instead of moving it into a thunk.
- Forgetting to handle the `rejected` case, which leaves the UI stuck on a
  loading state after a failed request.
- Reading `action.payload` on the `rejected` case, when the error details
  are actually on `action.error`.

### Interview angle
Q: Why can Redux not run an API call directly inside a reducer?
A: A reducer must be a pure, synchronous function so that its output only
depends on its inputs. An API call is asynchronous and has side effects,
so it must run outside the reducer, in a thunk, which then dispatches
plain actions once the result is ready.
