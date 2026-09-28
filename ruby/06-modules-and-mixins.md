## Modules and Mixins

### When to use it
Use a module to share behavior across classes that are not related
through inheritance, such as giving both a `User` class and an `Order`
class the same logging methods. Ruby only allows a class to inherit from
one parent, so a mixin is the standard way to share behavior with more
than one unrelated class.

### Pattern
```ruby
module Loggable
  def log(message)
    puts "[#{self.class.name}] #{message}"
  end
end

module ClassHelpers
  def self.included(base)
    base.extend(ClassMethods)
  end

  module ClassMethods
    def table_name
      name.downcase + "s"
    end
  end
end

class User
  include Loggable
  include ClassHelpers
end

class Order
  include Loggable
end

user = User.new
user.log("created")
# "[User] created"

User.table_name
# "users"
```

### How it works
`include` mixes a module's methods into a class as instance methods, so
every `User` and every `Order` gains a working `log` method without
duplicating that code or forcing an unrelated inheritance chain between
them. `extend` mixes a module's methods in as methods on the object or
class itself, rather than on its instances, which is how `ClassHelpers`
gives `User` a class-level `table_name` method. The `self.included` hook
runs automatically whenever a class includes that module, which is the
common pattern libraries use to attach class-level methods
automatically the moment a class writes `include ClassHelpers`.

### Common mistakes
- Confusing `include` and `extend`. `include` adds instance methods,
  `extend` adds methods on the object itself, and swapping them causes a
  `NoMethodError`.
- Reaching for a module and `include` when a simple method or a plain
  object would be clearer, adding an unnecessary layer of indirection.
- Naming a module method the same as an existing method in the
  including class, which silently overrides one of them depending on
  the order of inclusion.

### Interview angle
Q: Why does Ruby use mixins instead of allowing a class to inherit from
multiple parent classes?
A: Ruby classes support only single inheritance, to avoid the ambiguity
of multiple inheritance, such as which parent's method wins during a
conflict. Mixins share reusable behavior across unrelated classes
without creating that ambiguity.
