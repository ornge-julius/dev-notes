## React Router

### When to use it
Use React Router to move between pages in a single-page app without a
full page reload, and to read data such as an id from the URL.

### Pattern
```jsx
import {
  BrowserRouter,
  Routes,
  Route,
  Link,
  Outlet,
  useParams,
  useNavigate,
  Navigate,
} from "react-router-dom";

function App() {
  return (
    <BrowserRouter>
      <nav>
        <Link to="/">Home</Link>
        <Link to="/users">Users</Link>
      </nav>
      <Routes>
        <Route path="/" element={<Home />} />
        <Route path="/users" element={<UsersLayout />}>
          <Route index element={<UserList />} />
          <Route path=":userId" element={<UserDetail />} />
        </Route>
        <Route
          path="/admin"
          element={
            <ProtectedRoute>
              <AdminPanel />
            </ProtectedRoute>
          }
        />
        <Route path="*" element={<NotFound />} />
      </Routes>
    </BrowserRouter>
  );
}

function UsersLayout() {
  return (
    <div>
      <h1>Users</h1>
      <Outlet />
    </div>
  );
}

function UserDetail() {
  const { userId } = useParams();
  const navigate = useNavigate();

  return (
    <div>
      <p>Viewing user {userId}</p>
      <button onClick={() => navigate(-1)}>Go back</button>
    </div>
  );
}

function ProtectedRoute({ children }) {
  const isLoggedIn = useIsLoggedIn();
  if (!isLoggedIn) {
    return <Navigate to="/login" replace />;
  }
  return children;
}
```

### How it works
`<BrowserRouter>` wraps the whole app and reads the current URL.
`<Routes>` looks through its `<Route>` children and renders the one
`element` whose `path` matches the current URL. A nested `<Route>` with
no `path` and the `index` flag renders when the parent path matches
exactly, and `<Outlet>` inside the parent's `element` is where React
Router places the matched child route. `useParams` reads dynamic segments
from the URL, such as `:userId`. `useNavigate` returns a function to move
to a new URL from code, such as after a form submits. `<Navigate>`
redirects during render, which is how a protected route sends a logged-out
user to `/login` instead of rendering the page.

### Common mistakes
- Using an `<a href="...">` tag instead of `<Link to="...">` for internal
  navigation, which forces a full page reload and loses all React state.
- Forgetting the catch-all `<Route path="*" element={<NotFound />} />`,
  which leaves the app rendering nothing for an unmatched URL.
- Putting the redirect logic for a protected route inside a `useEffect`
  with `navigate(...)` instead of returning `<Navigate>` directly, which
  causes a visible flash of the protected content before the redirect
  runs.

### Interview angle
Q: What is the difference between `<Link>` and `<Navigate>`?
A: `<Link>` renders a clickable element the user clicks to change routes.
`<Navigate>` is a component that redirects immediately during render,
used for cases like protected routes where the redirect is not a direct
user click.
