## Error Handling and Testing

### When to use it
Use `@app.errorhandler` to return a consistent response when a specific
error happens, instead of Flask's default error page. Use the built-in
test client to test routes without running a real server.

### Pattern
```python
# Custom error handlers
from flask import Flask, jsonify

app = Flask(__name__)

@app.errorhandler(404)
def not_found(error):
    return jsonify({"error": "Resource not found"}), 404

@app.errorhandler(500)
def server_error(error):
    return jsonify({"error": "Something went wrong"}), 500

@app.route("/users/<int:user_id>")
def show_user(user_id):
    if user_id != 1:
        from flask import abort
        abort(404)
    return jsonify({"id": 1, "name": "Ada"})
```

```python
# tests/test_users.py
from app import create_app

def test_show_existing_user():
    app = create_app("app.config.TestingConfig")
    client = app.test_client()

    response = client.get("/users/1")

    assert response.status_code == 200
    assert response.json["name"] == "Ada"

def test_show_missing_user():
    app = create_app("app.config.TestingConfig")
    client = app.test_client()

    response = client.get("/users/999")

    assert response.status_code == 404
```

### How it works
`abort(404)` immediately stops the current view function and raises the
matching HTTP error. `@app.errorhandler(404)` registers a function that
runs whenever that error happens anywhere in the app, so every 404
response gets the same JSON shape instead of Flask's default HTML error
page. `app.test_client()` returns an object that can send fake requests,
such as `client.get("/users/1")`, directly to the app in memory, with no
real network call and no running server. The returned `response` object
exposes `status_code` and `json`, which the test asserts against
directly.

### Common mistakes
- Testing against the same app and database used for development,
  instead of a separate `TestingConfig` with its own test database,
  which can leave test data mixed into real data.
- Catching every exception broadly inside a view function and returning
  a generic error, which hides the real cause and makes debugging harder
  than letting `@app.errorhandler(500)` catch it once, centrally.
- Forgetting that `abort(404)` stops execution immediately, and writing
  code after it in the same function that assumes it still runs.

### Interview angle
Q: What is the advantage of `app.test_client()` over starting a real
Flask server and sending HTTP requests to it in tests?
A: The test client calls the app directly in memory, with no real
network or socket involved, which makes tests run faster and avoids
port conflicts or leftover running servers between test runs.
