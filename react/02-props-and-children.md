## Props and Children

### When to use it
Use props to pass data from a parent component to a child component. Use
the `children` prop when a component must wrap other content that the
parent controls.

### Pattern
```jsx
function Button({ label, onClick, variant = "primary" }) {
  return (
    <button className={`btn btn-${variant}`} onClick={onClick}>
      {label}
    </button>
  );
}

function Card({ title, children }) {
  return (
    <div className="card">
      <h2>{title}</h2>
      <div className="card-body">{children}</div>
    </div>
  );
}

// Usage
function App() {
  return (
    <Card title="Profile">
      <Button label="Save" onClick={() => console.log("saved")} />
    </Card>
  );
}
```

### How it works
Props are read-only values passed into a component, similar to function
arguments. Destructure props in the function signature to access them by
name. The `variant = "primary"` syntax sets a default value when the parent
does not pass that prop. `children` is a special prop that holds whatever
JSX the parent puts between the component's opening and closing tags.

### Common mistakes
- Mutating a prop directly instead of treating it as read-only.
- Forgetting to pass `children` through when a wrapper component needs it.
- Passing a large number of unrelated props instead of grouping related
  data into one object.

### Interview angle
Q: Can a child component change the value of a prop it receives?
A: No. Props are read-only. The child must ask the parent to change the
value, usually through a callback prop like `onClick`.
