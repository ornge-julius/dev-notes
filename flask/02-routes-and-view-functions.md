## Routes and View Functions

### When to use it
Use `@app.route` to map a URL to a Python function, called a view
function, that runs when a request comes in for that URL.

### Pattern
```python
from flask import Flask

app = Flask(__name__)

@app.route("/")
def index():
    return "Welcome"

# A path parameter with a type converter.
@app.route("/users/<int:user_id>")
def show_user(user_id):
    return f"User id is {user_id}"

# One route, two allowed HTTP methods.
@app.route("/users", methods=["GET", "POST"])
def users_collection():
    from flask import request
    if request.method == "POST":
        return "Creating a user", 201
    return "Listing users"

# A string converter is the default, so this also works without a type.
@app.route("/articles/<slug>")
def show_article(slug):
    return f"Article slug is {slug}"
```

### How it works
`@app.route("/users/<int:user_id>")` registers the function right below
it as the handler for that URL pattern. The `<int:user_id>` segment is a
type converter: Flask extracts that part of the URL, converts it to an
integer, and passes it into the view function as the `user_id` argument.
If the URL segment cannot convert to an integer, Flask returns a 404
instead of calling the function. By default, a route only responds to
`GET`. Adding `methods=["GET", "POST"]` lets the same URL handle both,
and the view function checks `request.method` to decide what to do.

### Common mistakes
- Forgetting to add `"POST"` to `methods` and then wondering why a form
  submission returns a 405 Method Not Allowed error.
- Using a plain string parameter, such as `<user_id>`, when the value
  must be a number, which means the view function receives a string and
  must convert it manually, and it also matches URLs Flask should reject.
- Defining two routes with URLs that can overlap, such as
  `/users/<user_id>` and `/users/new`, in the wrong order, which can send
  requests to the wrong view function.

### Interview angle
Q: What happens if a request hits `/users/abc` when the route is defined
as `/users/<int:user_id>`?
A: Flask tries to convert `"abc"` to an integer, fails, and returns a 404
Not Found response without ever calling the view function.
