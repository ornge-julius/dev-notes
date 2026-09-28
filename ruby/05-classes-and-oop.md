## Classes and OOP

### When to use it
Use a class to bundle related data and behavior together, such as a
`User` that holds a name and knows how to greet someone. Use inheritance
when one class is a more specific version of another and should share
its behavior.

### Pattern
```ruby
class User
  attr_accessor :name
  attr_reader :role

  def initialize(name, role = "member")
    @name = name
    @role = role
  end

  def greet
    "Hello, #{@name}!"
  end

  def self.guest
    new("Guest", "visitor")
  end
end

class Admin < User
  def initialize(name)
    super(name, "admin")
  end

  def greet
    "#{super} You have admin access."
  end
end

user = User.new("Ada")
user.greet
# "Hello, Ada!"
user.name = "Ada Lovelace"

admin = Admin.new("Grace")
admin.greet
# "Hello, Grace! You have admin access."

User.guest.role
# "visitor"
```

### How it works
`initialize` runs automatically when you call `User.new`, and it sets up
the object's instance variables, written with an `@` prefix. `attr_accessor`
generates both a reader and a writer method for an instance variable,
while `attr_reader` generates only the reader, which keeps `role`
read-only from outside the class. `class Admin < User` makes `Admin`
inherit every method from `User`, and `super` inside `Admin#initialize`
and `Admin#greet` calls the version of that method from `User` before
adding more behavior. A method defined with `def self.method_name`,
such as `self.guest`, is a class method, called on the class itself
(`User.guest`) rather than on an instance.

### Common mistakes
- Forgetting `attr_accessor` or `attr_reader` and then trying to read an
  instance variable directly from outside the class, such as
  `user.@name`, which is not valid Ruby syntax.
- Overriding a method in a subclass and forgetting to call `super` when
  the parent's behavior still needs to run.
- Confusing an instance method (`def greet`) with a class method
  (`def self.greet`), then calling it on the wrong receiver.

### Interview angle
Q: What does `super` do inside `Admin#greet`, given that `Admin`
inherits from `User`?
A: `super` calls `User`'s version of `greet` and returns its result,
which lets `Admin#greet` build on top of the parent's behavior instead of
replacing it entirely.
