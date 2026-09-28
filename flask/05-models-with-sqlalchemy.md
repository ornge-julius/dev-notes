## Models with Flask-SQLAlchemy

### When to use it
Use Flask-SQLAlchemy to define database tables as Python classes, and to
query them without writing raw SQL for common cases.

### Pattern
```python
# app/extensions.py
from flask_sqlalchemy import SQLAlchemy

db = SQLAlchemy()

# app/models.py
from app.extensions import db

class User(db.Model):
    id = db.Column(db.Integer, primary_key=True)
    name = db.Column(db.String(80), nullable=False)
    email = db.Column(db.String(120), unique=True, nullable=False)

    orders = db.relationship("Order", back_populates="user")

class Order(db.Model):
    id = db.Column(db.Integer, primary_key=True)
    total = db.Column(db.Numeric(10, 2), nullable=False)
    user_id = db.Column(db.Integer, db.ForeignKey("user.id"), nullable=False)

    user = db.relationship("User", back_populates="orders")

# Usage inside a route
user = User.query.filter_by(email="ada@example.com").first()
active_users = User.query.filter(User.name.isnot(None)).all()

new_user = User(name="Ada", email="ada@example.com")
db.session.add(new_user)
db.session.commit()
```

### How it works
Each class attribute defined with `db.Column` becomes one column in the
table, and the class itself maps to one table, named `user` for a `User`
class by default. `db.relationship` on both sides links `User` and
`Order` in Python, backed by the `user_id` foreign key column that
actually stores the relationship in the database. `User.query.filter_by`
and `.filter` build a SQL query, and calling `.first()` or `.all()` is
what actually runs it against the database. Nothing is written to the
database until `db.session.commit()` runs. Schema changes, such as
adding a new column, are handled by Flask-Migrate, which generates a
migration script from the difference between the models and the current
database, similar in spirit to Rails migrations.

### Common mistakes
- Calling `db.session.add(new_user)` without a following
  `db.session.commit()`, which leaves the change pending and never
  actually saved.
- Forgetting `nullable=False` or `unique=True` on columns that must
  always have a value or must stay unique, which pushes that
  validation entirely onto application code instead of the database.
- Editing a model's columns directly without generating and running a
  Flask-Migrate migration, which leaves the actual database schema out
  of sync with the Python model.

### Interview angle
Q: If you add a new column to a `User` model, does the database table
update automatically?
A: No. Flask-SQLAlchemy only describes the shape of the table in Python.
A separate migration, generated and applied through Flask-Migrate, must
run before the actual database table gains the new column.
