## Conditional Rendering

### When to use it
Use conditional rendering when the UI must show different content based on
a condition, such as a loading state, an error state, or a user role.

### Pattern
```jsx
function UserStatus({ isLoading, error, user }) {
  if (isLoading) {
    return <p>Loading...</p>;
  }

  if (error) {
    return <p className="error">{error}</p>;
  }

  return (
    <div>
      <p>Welcome, {user.name}</p>
      {user.isAdmin && <span className="badge">Admin</span>}
      {user.isAdmin ? <AdminPanel /> : <UserPanel />}
    </div>
  );
}
```

### How it works
An early `return` inside the function body handles a whole branch of the
UI, such as a loading or an error screen. The `&&` operator renders the
right-hand side only when the left-hand side is truthy, and renders
nothing when it is falsy. The ternary operator (`condition ? a : b`) picks
between two pieces of JSX inline, inside the returned markup.

### Common mistakes
- Using `count && <p>{count}</p>` when `count` can be `0`. React renders
  the literal `0` on the screen, because `0` is falsy but is still a
  value, not `false` or `null`.
- Nesting many ternaries inside one JSX block, which becomes hard to read.
  Use an early return or a helper function instead.

### Interview angle
Q: Why might `{count && <Badge count={count} />}` show a stray `0` on the
page?
A: When `count` is `0`, the expression evaluates to `0`, and React renders
that number instead of rendering nothing.
