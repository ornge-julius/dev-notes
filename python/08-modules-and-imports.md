## Modules and Imports

### When to use it
Use imports to split code across multiple files and reuse functions and
classes between them. Use the `if __name__ == "__main__":` guard when a
file should behave differently depending on whether it is run directly or
imported by another file.

### Pattern
```python
# math_utils.py
def add(a, b):
    return a + b

def subtract(a, b):
    return a - b

if __name__ == "__main__":
    print(add(2, 3))  # only runs when this file is executed directly


# main.py
import math_utils
from math_utils import subtract
from math_utils import add as plus

math_utils.add(1, 2)
subtract(5, 2)
plus(1, 2)


# A package layout
# project/
#   calculator/
#     __init__.py
#     basic.py
#     advanced.py
#   main.py

# calculator/__init__.py
from .basic import add, subtract

# main.py
from calculator import add
```

### How it works
`import math_utils` runs `math_utils.py` once, top to bottom, and makes
its names available through `math_utils.name`. `from math_utils import
subtract` pulls one name directly into the current file's namespace.
`as` renames an import, useful for avoiding a name clash or shortening a
long module name. A folder becomes a package once it contains an
`__init__.py` file, and that file's own imports decide what is exposed
directly from the package, which is why `from calculator import add`
works even though `add` actually lives in `calculator/basic.py`. The
`if __name__ == "__main__":` guard checks whether the file is the one
Python started running, which is `"__main__"`, versus being imported by
another file, in which case its own name is used instead.

### Common mistakes
- Writing top-level code that runs immediately, such as a function call
  with real side effects, outside of the `__main__` guard, which then
  runs unexpectedly every time another file imports that module.
- Creating circular imports, where module A imports from module B and
  module B imports from module A, which raises an `ImportError` in many
  cases.
- Using `from module import *`, which pulls in every public name and
  makes it hard to tell where a given name actually came from.

### Interview angle
Q: Why does `print(add(2, 3))` inside `math_utils.py` not run when
another file does `import math_utils`?
A: That line sits inside `if __name__ == "__main__":`, which is only
true when Python runs `math_utils.py` directly. When another file
imports it, `__name__` is set to `"math_utils"` instead, so the guard's
condition is false and the line is skipped.
