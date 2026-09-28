## Design Patterns: HOC, Render Props, Compound Components

### When to use it
Use these patterns to share behavior between components without copying
logic. Hooks have replaced most of the need for the first two patterns in
new code, but you will still see them in library code, such as
`react-redux`'s older `connect` function or route guards in some
codebases, so a production engineer must be able to read them.

### Pattern
```jsx
// Higher-order component (HOC): a function that takes a component and
// returns a new component with extra props or behavior.
function withLoading(WrappedComponent) {
  return function WithLoading({ isLoading, ...rest }) {
    if (isLoading) return <p>Loading...</p>;
    return <WrappedComponent {...rest} />;
  };
}

const UserListWithLoading = withLoading(UserList);
// Usage: <UserListWithLoading isLoading={loading} users={users} />

// Render props: a component takes a function as a prop, and calls it
// with data instead of rendering fixed JSX.
function MouseTracker({ render }) {
  const [position, setPosition] = useState({ x: 0, y: 0 });

  function handleMouseMove(event) {
    setPosition({ x: event.clientX, y: event.clientY });
  }

  return <div onMouseMove={handleMouseMove}>{render(position)}</div>;
}
// Usage: <MouseTracker render={(pos) => <p>{pos.x}, {pos.y}</p>} />

// Compound components: a parent component shares state through context,
// and several child components read that shared state.
const TabsContext = createContext(null);

function Tabs({ children }) {
  const [activeIndex, setActiveIndex] = useState(0);
  return (
    <TabsContext.Provider value={{ activeIndex, setActiveIndex }}>
      {children}
    </TabsContext.Provider>
  );
}

function TabList({ children }) {
  return <div className="tab-list">{children}</div>;
}

function Tab({ index, children }) {
  const { activeIndex, setActiveIndex } = useContext(TabsContext);
  return (
    <button
      className={activeIndex === index ? "active" : ""}
      onClick={() => setActiveIndex(index)}
    >
      {children}
    </button>
  );
}
```

### How it works
A higher-order component wraps one component inside another to add shared
behavior, such as a loading check, without changing the wrapped
component's own code. The render-props pattern passes a function as a
prop, so the parent component controls the data, and the caller controls
how that data renders. Compound components split one feature into several
smaller components (`Tabs`, `TabList`, `Tab`) that share hidden state
through context, so the caller can compose the pieces freely while the
components stay in sync.

### Common mistakes
- Naming the HOC's inner variable the same as the outer function, which
  makes React DevTools show a confusing, unlabeled component name. Set
  `WrappedComponent.displayName` for easier debugging.
- Nesting many HOCs around one component ("wrapper hell"), which makes the
  component tree hard to read in DevTools. A custom hook often replaces
  the same logic more clearly.
- Forgetting to spread `...rest` props through a HOC, which silently
  drops props the wrapped component actually needs.

### Interview angle
Q: Why have hooks replaced most uses of HOCs and render props in new
React code?
A: A custom hook shares the same logic across components without adding
extra layers to the component tree, and without the prop-name collisions
or unclear DevTools output that HOCs and render props can cause.
