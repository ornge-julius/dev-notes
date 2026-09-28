## Forms and Controlled Inputs

### When to use it
Use a controlled input when the component's state must always match the
value shown in the input field, such as in a login form or a search box.

### Pattern
```jsx
import { useState } from "react";

function LoginForm() {
  const [formData, setFormData] = useState({ email: "", password: "" });

  function handleChange(event) {
    const { name, value } = event.target;
    setFormData((prev) => ({ ...prev, [name]: value }));
  }

  function handleSubmit(event) {
    event.preventDefault();
    console.log(formData);
  }

  return (
    <form onSubmit={handleSubmit}>
      <input
        name="email"
        type="email"
        value={formData.email}
        onChange={handleChange}
      />
      <input
        name="password"
        type="password"
        value={formData.password}
        onChange={handleChange}
      />
      <button type="submit">Log in</button>
    </form>
  );
}
```

### How it works
The input's `value` comes from state, and every keystroke fires
`onChange`, which updates that state through `setFormData`. This makes the
input "controlled" because React, not the DOM, owns the current value. The
`name` attribute on each input lets one shared `handleChange` function
update the correct field by using `[name]: value` as a computed object key.
`event.preventDefault()` stops the browser from reloading the page on
submit.

### Common mistakes
- Setting `value` without an `onChange` handler, which makes the input
  read-only and triggers a React warning.
- Forgetting to spread the previous state (`...prev`) before overwriting
  one field, which erases the other fields.
- Forgetting `event.preventDefault()` in `handleSubmit`, which causes a
  full page reload.

### Interview angle
Q: What makes an input "controlled" versus "uncontrolled" in React?
A: A controlled input gets its value from React state and updates that
state on every change. An uncontrolled input manages its own value
internally in the DOM, and React reads it only when needed, often through
a `ref`.
