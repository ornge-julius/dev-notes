## Controllers and Strong Parameters

### When to use it
Use strong parameters any time a controller action creates or updates a
record from user-submitted data. This is the standard, required way to
protect a Rails app from mass-assignment attacks.

### Pattern
```ruby
class PostsController < ApplicationController
  def create
    @post = Post.new(post_params)
    if @post.save
      redirect_to @post
    else
      render :new, status: :unprocessable_entity
    end
  end

  private

  def post_params
    params.require(:post).permit(:title, :body)
  end
end
```

### How it works
`params` holds every value submitted with the request, including fields
the form never showed, since a request can be crafted by hand outside
the browser. `params.require(:post)` pulls out the nested `post` hash and
raises an error if it is missing. `.permit(:title, :body)` then filters
that hash down to only the named fields, dropping anything else, such as
an `admin: true` field a malicious user might add to the request body.
Passing the filtered `post_params`, not raw `params`, into `Post.new` is
what makes the create action safe.

### Common mistakes
- Calling `Post.new(params[:post])` directly instead of going through a
  `permit`-filtered method, which lets an attacker set any column on the
  model, including ones like `admin` or `role` that the form never
  exposed.
- Forgetting `params.require(:post)` and calling `.permit` on the whole
  `params` hash, which mixes in unrelated top-level parameters like
  `:controller` and `:action`.
- Permitting a field that should never be user-editable, such as
  `:user_id` on a comment, which lets a user attach content to someone
  else's account.

### Interview angle
Q: Why is `params.require(:post).permit(:title, :body)` safer than
`params[:post]`?
A: `permit` creates an explicit allowlist of fields, so any extra field a
request includes, expected or not, gets dropped instead of passed
straight into the model, which prevents mass-assignment attacks.
