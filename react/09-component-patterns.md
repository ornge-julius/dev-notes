## Component Patterns

### When to use it
Use these patterns to decide where state must live and how components must
share it, before the component tree grows too tangled to change safely.

### Pattern
```jsx
// Lifting state up: two children share one piece of state through the parent.
function TemperatureConverter() {
  const [celsius, setCelsius] = useState(0);

  return (
    <div>
      <CelsiusInput value={celsius} onChange={setCelsius} />
      <FahrenheitDisplay celsius={celsius} />
    </div>
  );
}

// Composition instead of prop-drilling: pass a whole component down, not
// ten separate props.
function Layout({ header, children }) {
  return (
    <div>
      <header>{header}</header>
      <main>{children}</main>
    </div>
  );
}

function Page() {
  return (
    <Layout header={<h1>Dashboard</h1>}>
      <p>Page content here.</p>
    </Layout>
  );
}
```

### How it works
Lifting state up means moving a piece of state to the closest common
parent of the components that need it, instead of duplicating that state
in each child. The parent then passes the value down as a prop, and passes
a setter function down as a callback prop, so a child can request a
change. Composition means passing JSX itself as a prop, such as `children`
or `header`, so a wrapper component does not need a long list of specific
props for every possible piece of content. This avoids "prop drilling,"
where a value gets passed through several components that do not use it
themselves, only to reach a component several levels down.

### Common mistakes
- Storing the same value as state in two different components instead of
  lifting it to their shared parent, which causes the two copies to fall
  out of sync.
- Drilling a prop through four or five components instead of using
  composition or context.
- Splitting a component into "container" and "presentational" pieces when
  the component is small enough that the split adds more files than value.

### Interview angle
Q: What does "lifting state up" mean, and when is it needed?
A: It means moving state to the nearest shared ancestor of the components
that need to read or change it. It is needed whenever two sibling
components must stay in sync with the same value.
