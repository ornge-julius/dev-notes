## App Factory and Project Structure

### When to use it
Use a plain `Flask(__name__)` app for a quick script or a tiny demo. Use
the application factory pattern for any app that needs tests, multiple
environments, or extensions such as a database.

### Pattern
```python
# Minimal app, fine for a quick script.
from flask import Flask

app = Flask(__name__)

@app.route("/")
def index():
    return "Hello, Flask"

if __name__ == "__main__":
    app.run(debug=True)
```

```python
# app/__init__.py — the application factory pattern.
from flask import Flask
from app.extensions import db
from app.main.routes import main_bp

def create_app(config_object="app.config.DevelopmentConfig"):
    app = Flask(__name__)
    app.config.from_object(config_object)

    db.init_app(app)
    app.register_blueprint(main_bp)

    return app
```

```
# Typical project layout for the factory pattern.
myapp/
  app/
    __init__.py          (create_app lives here)
    extensions.py         (db = SQLAlchemy(), etc.)
    config.py             (DevelopmentConfig, TestingConfig, ...)
    main/
      routes.py           (main_bp Blueprint)
      models.py
  run.py                  (calls create_app() and app.run())
  tests/
```

### How it works
`Flask(__name__)` at module level creates one global app object the
moment the module loads. `create_app()` instead builds and returns a new
app object each time it is called, which lets a test suite create a
fresh app with a `TestingConfig` (for example, an in-memory test
database) without touching the app used in development or production.
Extensions such as `db` are created once, outside the factory, then
attached to the specific app instance inside `create_app` through
`db.init_app(app)`. This two-step setup is what lets the same extension
object work with more than one app instance.

### Common mistakes
- Creating the `Flask` app at import time in a large project, which
  makes it hard to configure differently for tests versus production.
- Initializing an extension with `db.init_app(app)` before
  `app.config.from_object(...)` has set the database URL, which points
  the extension at the wrong (or missing) configuration.
- Registering blueprints outside of `create_app`, at module level, which
  ties the blueprint to one specific app instance instead of whichever
  app the factory currently builds.

### Interview angle
Q: Why does a production Flask app usually use an application factory
instead of a single global `app = Flask(__name__)`?
A: A factory function returns a new, independently configured app on
each call, which lets tests build an app with test settings and a test
database, separate from the app instance used in development or
production.
