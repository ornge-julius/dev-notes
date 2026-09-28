## JSX and Function Components

### When to use it
Use JSX to describe what the user interface must show. Use a function
component to package that JSX into a reusable piece.

### Pattern
```jsx
function Greeting({ name }) {
  return (
    <div className="greeting">
      <h1>Hello, {name}!</h1>
      <p>Welcome back.</p>
    </div>
  );
}

export default Greeting;
```

### How it works
JSX looks like HTML, but it compiles to JavaScript function calls. Every
JSX block must return one root element, or a fragment (`<>...</>`) if you do
not want an extra `<div>`. A function component is a plain JavaScript
function that accepts one `props` object and returns JSX. React calls this
function each time the component must render.

### Common mistakes
- Returning two sibling elements without wrapping them in one parent or a
  fragment.
- Using `class` instead of `className` for CSS classes.
- Forgetting that a JSX expression inside `{}` must be a valid JavaScript
  expression, not a statement.

### Interview angle
Q: What does JSX compile to?
A: JSX compiles to calls to `React.createElement`, which builds a
JavaScript object that describes the UI.
