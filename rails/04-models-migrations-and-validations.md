## Models, Migrations, and Validations

### When to use it
Use a migration any time the database schema must change. Use
validations any time a model must reject bad data before it reaches the
database.

### Pattern
```
$ rails generate migration CreatePosts title:string body:text published:boolean
$ rails db:migrate
```

```ruby
# db/migrate/20240101000000_create_posts.rb
class CreatePosts < ActiveRecord::Migration[7.1]
  def change
    create_table :posts do |t|
      t.string :title, null: false
      t.text :body
      t.boolean :published, default: false
      t.timestamps
    end
  end
end
```

```ruby
# app/models/post.rb
class Post < ApplicationRecord
  validates :title, presence: true, length: { maximum: 200 }
  validates :title, uniqueness: true
end

post = Post.new(title: "")
post.valid?        # false
post.errors[:title] # ["can't be blank"]
```

### How it works
A migration is a versioned, ordered change to the database schema,
written in Ruby instead of raw SQL. `rails db:migrate` runs any
migration that has not run yet and records that it ran, so the same
migration never applies twice. A model class such as `Post` inherits
from `ApplicationRecord`, which gives it a full set of database methods
(`find`, `create`, `save`, `update`) without you writing any SQL, based
on the columns in the `posts` table. `validates` adds rules that
`save` checks before writing to the database. If a rule fails, `save`
returns `false` and the model exposes readable failure messages through
`errors`, instead of the record being written.

### Common mistakes
- Editing an already-migrated file instead of writing a new migration,
  which leaves other environments and teammates with a schema that does
  not match the file's current content.
- Adding a validation without also adding a matching database constraint
  (such as `null: false` or a unique index) for anything that truly must
  never be invalid, since validations only run through Rails, not
  through direct SQL or a race condition between two requests.
- Calling `.save` and ignoring its `false` return value instead of
  checking it or checking `.errors`, which lets an invalid save fail
  silently.

### Interview angle
Q: Why is a `uniqueness` validation not enough to guarantee a column is
truly unique?
A: A `uniqueness` validation runs a query to check for a duplicate before
saving, but two requests can run that check at nearly the same time and
both pass, then both save. A unique index at the database level is
required to fully prevent duplicates.
