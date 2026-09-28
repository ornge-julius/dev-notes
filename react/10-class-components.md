## Class Components and Lifecycle Methods

### When to use it
Modern React code uses function components with hooks for almost
everything. You still need a class component for one production case: an
error boundary. React has no hook equivalent for
`componentDidCatch`, so any app that must catch render errors in part of
the tree needs at least one class component.

### Pattern
```jsx
import React from "react";

class UserProfile extends React.Component {
  constructor(props) {
    super(props);
    this.state = { user: null };
  }

  componentDidMount() {
    fetch(`/api/users/${this.props.userId}`)
      .then((res) => res.json())
      .then((data) => this.setState({ user: data }));
  }

  componentDidUpdate(prevProps) {
    if (prevProps.userId !== this.props.userId) {
      fetch(`/api/users/${this.props.userId}`)
        .then((res) => res.json())
        .then((data) => this.setState({ user: data }));
    }
  }

  componentWillUnmount() {
    console.log("UserProfile is leaving the screen");
  }

  render() {
    const { user } = this.state;
    if (!user) return <p>Loading...</p>;
    return <p>{user.name}</p>;
  }
}

// Error boundary: this pattern has no function-component equivalent.
class ErrorBoundary extends React.Component {
  constructor(props) {
    super(props);
    this.state = { hasError: false };
  }

  static getDerivedStateFromError() {
    return { hasError: true };
  }

  componentDidCatch(error, info) {
    console.error("Caught by boundary:", error, info);
  }

  render() {
    if (this.state.hasError) {
      return <p>Something went wrong.</p>;
    }
    return this.props.children;
  }
}
```

### How it works
A class component stores state on `this.state` and updates it with
`this.setState`, which merges the new fields into the existing state
object instead of replacing it entirely. `componentDidMount` runs once,
right after the first render, similar to `useEffect(fn, [])`.
`componentDidUpdate` runs after every re-render and receives the previous
props and state, so you must compare them yourself to decide whether to
act, similar to `useEffect(fn, [dep])`. `componentWillUnmount` runs right
before React removes the component, similar to the cleanup function
returned from `useEffect`. `getDerivedStateFromError` and
`componentDidCatch` only exist on classes. They let a boundary component
catch a rendering error thrown anywhere in its child tree and show a
fallback UI instead of crashing the whole app.

### Common mistakes
- Calling `this.setState` inside `componentDidUpdate` without a guard
  condition, which causes an infinite update loop.
- Forgetting `super(props)` in the constructor before using `this`.
- Assuming an error boundary catches errors from event handlers. It only
  catches errors thrown during rendering, in lifecycle methods, or in
  constructors of the tree below it.

### Interview angle
Q: Why does React still need class components in 2026, when hooks cover
almost everything else?
A: Error boundaries require `getDerivedStateFromError` and
`componentDidCatch`, and React has not added a hook version of either one,
so any app needing to catch render errors in a subtree still needs at
least one class component.
