## Performance: memo, useMemo, useCallback, and Code Splitting

### When to use it
Use `React.memo`, `useMemo`, and `useCallback` only after you find a real,
measured re-render problem, such as with the React DevTools Profiler. Use
code splitting to shrink the initial bundle a user must download, mainly
for routes or heavy components that are not needed on first load.

### Pattern
```jsx
import { memo, useState, useMemo, useCallback, lazy, Suspense } from "react";

// React.memo skips a re-render when props have not changed (shallow check).
const UserRow = memo(function UserRow({ user, onSelect }) {
  console.log("rendering", user.name);
  return <li onClick={() => onSelect(user.id)}>{user.name}</li>;
});

function UserList({ users }) {
  const [query, setQuery] = useState("");

  // useMemo skips recalculating a value unless its dependencies change.
  const filteredUsers = useMemo(() => {
    return users.filter((u) =>
      u.name.toLowerCase().includes(query.toLowerCase())
    );
  }, [users, query]);

  // useCallback keeps the same function reference between renders, so
  // memo(UserRow) does not see a "new" onSelect prop on every render.
  const handleSelect = useCallback((id) => {
    console.log("selected", id);
  }, []);

  return (
    <div>
      <input value={query} onChange={(e) => setQuery(e.target.value)} />
      <ul>
        {filteredUsers.map((user) => (
          <UserRow key={user.id} user={user} onSelect={handleSelect} />
        ))}
      </ul>
    </div>
  );
}

// Code splitting: load a component's code only when it is actually needed.
const AdminPanel = lazy(() => import("./AdminPanel"));

function App() {
  return (
    <Suspense fallback={<p>Loading admin panel...</p>}>
      <AdminPanel />
    </Suspense>
  );
}
```

### How it works
`React.memo` wraps a component and compares its new props to the previous
props using `Object.is` on each field. If every prop is equal, React
skips re-rendering that component. `useMemo` caches the result of a
calculation between renders and only redoes the work when a value in the
dependency array changes, which matters for expensive operations such as
filtering or sorting a large list. `useCallback` caches a function
definition itself, which matters mainly when that function is passed as a
prop into a component wrapped in `React.memo`, since a brand new function
on every render would defeat the memo check. `React.lazy` combined with
`import()` tells the build tool to put that component's code into a
separate file, downloaded only when the component is first rendered.
`<Suspense>` shows a fallback UI while that file loads.

### Common mistakes
- Wrapping every component in `React.memo` by default. The comparison
  itself costs time, and it only helps when re-renders are actually
  expensive and props actually stay the same often.
- Using `useCallback` or `useMemo` without a matching `React.memo` or
  expensive calculation downstream, which adds complexity with no
  measured benefit.
- Forgetting that an inline object or array prop, such as
  `style={{ color: "red" }}`, creates a new reference every render, which
  breaks `React.memo` even if the visible values never change.
- Forgetting the `<Suspense>` boundary around a `lazy` component, which
  throws an error instead of showing a loading state.

### Interview angle
Q: Why can adding `React.memo` sometimes make an app slower, not faster?
A: `React.memo` still runs a prop comparison on every render. If the
component was cheap to render anyway, or if its props change most of the
time, the comparison itself adds overhead without preventing enough
re-renders to make up for it.
