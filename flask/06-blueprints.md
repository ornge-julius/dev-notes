## Blueprints

### When to use it
Use a `Blueprint` to group a related set of routes into their own file
once an app grows past a handful of routes defined directly on `app`.

### Pattern
```python
# app/users/routes.py
from flask import Blueprint, jsonify

users_bp = Blueprint("users", __name__, url_prefix="/users")

@users_bp.route("/")
def list_users():
    return jsonify([{"id": 1, "name": "Ada"}])

@users_bp.route("/<int:user_id>")
def show_user(user_id):
    return jsonify({"id": user_id, "name": "Ada"})

# app/__init__.py
from flask import Flask
from app.users.routes import users_bp

def create_app():
    app = Flask(__name__)
    app.register_blueprint(users_bp)
    return app
```

### How it works
A `Blueprint` collects a group of routes, and optionally templates and
static files, without needing the actual `app` object to exist yet.
`url_prefix="/users"` means every route defined on `users_bp` is
automatically nested under `/users`, so `@users_bp.route("/")` actually
serves `/users/` and `@users_bp.route("/<int:user_id>")` serves
`/users/42`. `app.register_blueprint(users_bp)` is the step that
actually attaches those routes to a specific app instance, usually
inside `create_app()`. This separation is what lets a `users` blueprint,
an `orders` blueprint, and an `admin` blueprint each live in their own
file or folder instead of one large `app.py`.

### Common mistakes
- Forgetting `app.register_blueprint(...)` after defining a blueprint,
  which leaves its routes completely inactive with no error.
- Repeating the same `url_prefix` segment inside individual route paths,
  such as `@users_bp.route("/users/<id>")` on a blueprint that already
  has `url_prefix="/users"`, which doubles the prefix.
- Giving two blueprints the same name, which raises an error at
  registration time since Flask uses the blueprint name to build URLs
  with `url_for`.

### Interview angle
Q: What problem do blueprints solve as a Flask app grows?
A: Blueprints let routes, templates, and static files for one feature
area live together in their own module, instead of every route being
defined directly on one growing `app.py` file.
