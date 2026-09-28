## Templates with Jinja

### When to use it
Use `render_template` and Jinja when a route must return an HTML page
built from data, instead of a plain string or a JSON response.

### Pattern
```python
# app.py
from flask import Flask, render_template

app = Flask(__name__)

@app.route("/users")
def user_list():
    users = [{"name": "Ada", "active": True}, {"name": "Alan", "active": False}]
    return render_template("users.html", users=users, title="All Users")
```

```html
<!-- templates/base.html -->
<html>
<head><title>{% block title %}My App{% endblock %}</title></head>
<body>
  {% block content %}{% endblock %}
</body>
</html>
```

```html
<!-- templates/users.html -->
{% extends "base.html" %}

{% block title %}{{ title }}{% endblock %}

{% block content %}
  <h1>{{ title }}</h1>
  <ul>
    {% for user in users %}
      <li>
        {{ user.name }}
        {% if user.active %}(active){% endif %}
      </li>
    {% endfor %}
  </ul>
{% endblock %}
```

### How it works
`render_template("users.html", users=users, title="All Users")` looks
for `users.html` in the `templates/` folder and passes `users` and
`title` into it as variables. `{{ ... }}` inside the template outputs a
value, and Flask escapes it automatically to prevent injecting raw HTML
from user data. `{% for %}` and `{% if %}` are Jinja control-flow tags,
closed with `{% endfor %}` and `{% endif %}`. `{% extends "base.html" %}`
makes a template inherit the structure of a parent template, and
`{% block content %}...{% endblock %}` marks the section the child
template is allowed to override.

### Common mistakes
- Putting templates outside the `templates/` folder Flask expects by
  default, which causes a "template not found" error.
- Using `{{ }}` for a Jinja tag or `{% %}` for outputting a value, since
  the two syntaxes are not interchangeable.
- Forgetting `{% endfor %}` or `{% endif %}`, which throws a template
  syntax error at render time instead of at import time, since Jinja
  templates are not checked until they run.

### Interview angle
Q: Why does `{{ user_input }}` in a Jinja template not create an HTML
injection risk by default?
A: Jinja auto-escapes variable output in `.html` templates, converting
characters like `<` and `>` into their safe HTML entities, so injected
markup renders as plain text instead of running as HTML.
