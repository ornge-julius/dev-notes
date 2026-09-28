## Associations

### When to use it
Use associations to describe how two models relate, such as a post
having many comments, so Rails generates the methods to read and manage
that relationship without hand-written queries.

### Pattern
```ruby
class Post < ApplicationRecord
  has_many :comments, dependent: :destroy
  has_many :taggings
  has_many :tags, through: :taggings
end

class Comment < ApplicationRecord
  belongs_to :post
end

post = Post.find(1)
post.comments          # every comment for this post
post.comments.create(body: "Nice post!")
post.tags               # every tag through the taggings join table

comment = Comment.find(1)
comment.post            # the post this comment belongs to
```

```ruby
# db/migrate/..._create_comments.rb
create_table :comments do |t|
  t.text :body
  t.references :post, null: false, foreign_key: true
  t.timestamps
end
```

### How it works
`belongs_to :post` on `Comment` declares that a comments row stores a
`post_id` foreign key pointing back to its post. `has_many :comments` on
`Post` is the other side of that same relationship, and it lets you call
`post.comments` to get every comment whose `post_id` matches. A
`has_many :through` association, like `tags` through `taggings`, models
a many-to-many relationship using a join model in the middle.
`dependent: :destroy` tells Rails to delete every associated comment
automatically when its post is deleted, so no orphaned rows are left
behind.

### Common mistakes
- Adding `has_many :comments` without `dependent: :destroy` or a
  database-level cascade, then deleting a post and leaving orphaned
  comment rows pointing at a post that no longer exists.
- Forgetting the foreign key column, `post_id` on `comments`, since
  `belongs_to`/`has_many` only works because that column exists.
- Calling `post.comments.build(...)` and then never saving it, when the
  intent was `post.comments.create(...)`, which builds an unsaved record
  and saves it in one step.

### Interview angle
Q: What is the difference between `has_many :tags` and
`has_many :tags, through: :taggings`?
A: A plain `has_many :tags` assumes `tags` has a direct foreign key back
to the parent model. `has_many :tags, through: :taggings` models a
many-to-many relationship by going through a separate join model, which
also lets you store extra data on the relationship itself, such as when
a tag was added.
