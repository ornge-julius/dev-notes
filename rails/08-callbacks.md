## Callbacks

### When to use it
Use a callback only for simple, derived changes to the same record, such
as normalizing a field right before it saves. Reach for a service object
or an explicit method call instead of a callback for anything with a
side effect outside that one record, such as sending an email or
updating a different table.

### Pattern
```ruby
class Post < ApplicationRecord
  before_validation :normalize_title
  before_save :set_published_at, if: :will_save_change_to_published?

  private

  def normalize_title
    self.title = title.strip if title.present?
  end

  def set_published_at
    self.published_at = published? ? Time.current : nil
  end
end

# Prefer an explicit call for anything with an outside effect,
# instead of an after_create callback that emails on every save path.
class Post < ApplicationRecord
  def publish!
    update!(published: true, published_at: Time.current)
    PostMailer.published_notification(self).deliver_later
  end
end
```

### How it works
`before_validation` and `before_save` run automatically as part of the
save process, in a fixed order Rails defines, before the record actually
writes to the database. This makes them convenient for small,
self-contained changes like trimming whitespace, since every save path
gets the same behavior without extra calls. The `publish!` method
instead makes the mailer side effect explicit and visible at the call
site, instead of hidden inside a callback that fires on every save,
including saves that have nothing to do with publishing.

### Common mistakes
- Putting an email send, an API call, or another side effect in an
  `after_save` callback, which fires on every save, including unrelated
  updates, and makes that side effect invisible at the call site.
- Chaining callbacks that each call `save` again on the same or a
  related record, which can trigger an infinite loop of saves calling
  saves.
- Relying on callback order across several callbacks defined in
  different places, such as a callback added by a mixin, which becomes
  hard to trace when something runs in the wrong order.

### Interview angle
Q: Why do many Rails teams avoid callbacks for things like sending a
welcome email after a user signs up?
A: A callback runs on every save that matches its condition, not only
the one code path the developer had in mind, so a later unrelated update
to that record can accidentally trigger the same email again. An
explicit method call, such as `user.complete_signup!`, keeps the side
effect visible and tied to one specific action.
