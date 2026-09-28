## Request and Response

### When to use it
Use the `request` object to read data the client sent. Use `jsonify`,
plus an optional status code, to send a JSON response back.

### Pattern
```python
from flask import Flask, request, jsonify

app = Flask(__name__)

@app.route("/search")
def search():
    # Query string: /search?q=flask
    query = request.args.get("q", "")
    return jsonify({"query": query, "results": []})

@app.route("/users", methods=["POST"])
def create_user():
    # JSON body sent by the client, e.g. {"name": "Ada"}
    data = request.json
    name = data.get("name")

    if not name:
        return jsonify({"error": "name is required"}), 400

    user = {"id": 1, "name": name}
    return jsonify(user), 201

@app.route("/upload", methods=["POST"])
def upload():
    # A regular HTML form submission, not JSON.
    email = request.form.get("email")
    return jsonify({"received_email": email})
```

### How it works
`request.args` holds the query string parameters from the URL, such as
`?q=flask`, and `.get("q", "")` reads one with a default if it is
missing. `request.json` parses the request body as JSON and returns a
Python dictionary. `request.form` reads fields from a standard HTML form
submission (`application/x-www-form-urlencoded` or `multipart/form-data`),
which is a different body format than JSON. `jsonify(...)` converts a
Python dictionary or list into a proper JSON HTTP response with the
correct content type header. Returning a tuple, such as
`jsonify({...}), 201`, sets the response's status code alongside the
body.

### Common mistakes
- Reading `request.form` when the client actually sent JSON, which
  returns an empty value instead of raising an error, and quietly breaks
  the endpoint.
- Returning a plain Python dictionary directly from older Flask
  documentation examples without checking that the installed Flask
  version supports it. `jsonify(...)` works across versions and is the
  safer default.
- Forgetting to set an error status code, such as returning
  `jsonify({"error": "..."})` with no `, 400`, which sends the error
  message back with a misleading 200 OK status.

### Interview angle
Q: What is the practical difference between `request.form` and
`request.json`?
A: `request.form` reads data sent as a standard HTML form
(`application/x-www-form-urlencoded` or `multipart/form-data`).
`request.json` parses the request body as JSON. A client that sends one
format will find the other one empty.
