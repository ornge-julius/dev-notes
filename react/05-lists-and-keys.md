## Lists and Keys

### When to use it
Use `.map()` to turn an array of data into an array of JSX elements, such
as a list of items fetched from an API.

### Pattern
```jsx
function TodoList({ todos }) {
  return (
    <ul>
      {todos.map((todo) => (
        <li key={todo.id}>
          {todo.text} {todo.done ? "(done)" : ""}
        </li>
      ))}
    </ul>
  );
}
```

### How it works
`.map()` creates a new array of `<li>` elements, one for each item in
`todos`. Each element in the list needs a `key` prop so that React can
track which item is which across re-renders. React uses the key to decide
whether to reuse, move, or recreate a DOM node when the list changes.

### Common mistakes
- Using the array index as the key when the list can be reordered, filtered,
  or have items removed from the middle. This can cause React to reuse the
  wrong DOM node and show stale input values or animations.
- Forgetting the key altogether, which triggers a React warning in the
  console.
- Putting the key on the wrong element, such as on a child inside the
  returned JSX instead of on the outermost element of the mapped item.

### Interview angle
Q: Why does React need a stable key for list items?
A: The key lets React match items between renders by identity instead of
by position, so it can correctly add, remove, or reorder DOM nodes without
losing component state tied to the wrong item.
