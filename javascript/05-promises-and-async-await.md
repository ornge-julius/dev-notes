## Promises and async/await

### When to use it
Use a promise, or `async`/`await` on top of one, any time code must wait
for something outside of JavaScript to finish, such as a network request.
This is the exact pattern behind every API call inside `useEffect`, a
Redux thunk, or an event handler.

### Pattern
```js
// A promise-based version, using .then and .catch
function fetchUser(id) {
  return fetch(`/api/users/${id}`)
    .then((response) => response.json())
    .then((data) => data)
    .catch((error) => {
      console.error("Failed to load user:", error);
      throw error;
    });
}

// The same logic written with async/await
async function fetchUser(id) {
  try {
    const response = await fetch(`/api/users/${id}`);
    const data = await response.json();
    return data;
  } catch (error) {
    console.error("Failed to load user:", error);
    throw error;
  }
}

// Common React usage inside an event handler
async function handleSave() {
  setStatus("saving");
  try {
    await saveUser(formData);
    setStatus("saved");
  } catch (error) {
    setStatus("error");
  }
}

// Running two requests at the same time instead of one after another
async function fetchDashboardData(userId) {
  const [user, orders] = await Promise.all([
    fetchUser(userId),
    fetchOrders(userId),
  ]);
  return { user, orders };
}
```

### How it works
A promise represents a value that is not ready yet, but will resolve
successfully or reject with an error at some point. `.then()` runs when
the promise resolves, and `.catch()` runs when it rejects. `async`/`await`
is syntax built on top of promises: marking a function `async` lets you
use `await` inside it, which pauses that function until the awaited
promise settles, without blocking the rest of the app. A `try`/`catch`
block around `await` catches a rejected promise the same way it catches a
thrown error. `Promise.all` runs several promises at the same time and
waits for all of them, which is faster than awaiting each one in a row
when the requests do not depend on each other.

### Common mistakes
- Forgetting `await` in front of an async call, which leaves you holding
  a pending promise object instead of the actual resolved value.
- Forgetting the `try`/`catch` around `await`, which lets a rejected
  promise crash the surrounding function or leave the UI stuck.
- Awaiting several independent requests one after another instead of
  using `Promise.all`, which makes the page wait far longer than needed.
- Marking a function `async` but never actually awaiting anything inside
  it, which adds unnecessary complexity for no benefit.

### Interview angle
Q: What is the practical difference between awaiting three requests one
after another versus using `Promise.all`?
A: Awaiting them one after another runs them in sequence, so the total
wait time is the sum of all three. `Promise.all` starts all three at the
same time, so the total wait time is roughly the length of the slowest
one.
