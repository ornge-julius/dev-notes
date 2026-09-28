## The useEffect Hook

### When to use it
Use `useEffect` to run code that must happen after a render, such as
fetching data, subscribing to an event, or setting a timer. Use it when
the code must reach outside of React, into the browser or a network call.

### Pattern
```jsx
import { useState, useEffect } from "react";

function UserProfile({ userId }) {
  const [user, setUser] = useState(null);

  useEffect(() => {
    let isCancelled = false;

    async function fetchUser() {
      const response = await fetch(`/api/users/${userId}`);
      const data = await response.json();
      if (!isCancelled) {
        setUser(data);
      }
    }

    fetchUser();

    return () => {
      isCancelled = true;
    };
  }, [userId]);

  if (!user) return <p>Loading...</p>;
  return <p>{user.name}</p>;
}
```

### How it works
The first argument to `useEffect` is a function that runs after the
render. The second argument, the dependency array, tells React when to run
that function again. React runs the effect after the first render, and
again any time a value inside `[userId]` changes between renders. The
function returned from inside the effect is the cleanup function. React
calls it before the next effect run, and before the component unmounts,
which is why the `isCancelled` flag stops a late network response from
setting state on a component that already moved on to a new `userId`.

### Common mistakes
- Leaving out the dependency array, which makes the effect run after every
  single render.
- Leaving a variable out of the dependency array when the effect actually
  uses it, which causes the effect to use a stale value.
- Forgetting the cleanup function for subscriptions or timers, which
  causes a memory leak or a "set state on an unmounted component" warning.

### Interview angle
Q: What does an empty dependency array, `[]`, mean for `useEffect`?
A: It means the effect runs once, right after the first render, and the
cleanup function runs once, right before the component unmounts.
