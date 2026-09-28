## Testing Basics

### When to use it
Use a model spec to test validation and business-logic methods in
isolation. Use a request spec to test a full controller action,
including routing, params handling, and the response.

### Pattern
```ruby
# spec/models/post_spec.rb
require "rails_helper"

RSpec.describe Post, type: :model do
  it "is invalid without a title" do
    post = Post.new(title: "")
    expect(post).not_to be_valid
    expect(post.errors[:title]).to include("can't be blank")
  end

  it "is valid with a title and body" do
    post = Post.new(title: "Hello", body: "World")
    expect(post).to be_valid
  end
end

# spec/requests/posts_spec.rb
require "rails_helper"

RSpec.describe "Posts", type: :request do
  it "creates a post and redirects" do
    post "/posts", params: { post: { title: "Hello", body: "World" } }
    expect(response).to redirect_to(Post.last)
  end

  it "returns 404 for a missing post" do
    get "/posts/999999"
    expect(response).to have_http_status(:not_found)
  end
end
```

### How it works
`describe` groups related tests around one class or feature, and `it`
defines one specific expectation inside that group. `expect(...).to
eq(...)`, `be_valid`, or `have_http_status(...)` are matchers that check
the actual value against what the test expects, and the test fails with
a readable message if they do not match. A model spec builds objects
directly in Ruby and checks their behavior without going through HTTP. A
request spec sends a real HTTP-style request through the app's router
and controllers, and checks the response, which is closer to how a real
user or client interacts with the app. Test data usually comes from one
of two approaches: fixtures, which are fixed, shared sample records
loaded from a YAML file before the suite runs, or factories, most often
built with the FactoryBot gem, which generate a fresh, customizable
object per test with a Ruby method call such as
`create(:post, title: "Custom title")`. Most current Rails teams prefer
factories, since each test can adjust just the fields it cares about
instead of depending on shared fixed data that every test must not
accidentally break.

### Common mistakes
- Writing a model spec that also hits the database and controller layer
  through `post "/posts"`, when a plain object test would run faster and
  isolate the failure better.
- Sharing one mutable fixture or factory record across many tests
  without resetting it, which lets one test's changes leak into another
  test's expectations.
- Testing only the successful path and skipping cases like invalid
  params or a missing record, which are exactly the cases most likely to
  break in production.

### Interview angle
Q: What is the practical difference between a model spec and a request
spec?
A: A model spec tests a class directly in Ruby, such as validations or
methods, without going through HTTP. A request spec sends an actual
request through the router and controller and checks the real HTTP
response, so it also covers routing and params handling that a model
spec cannot see.
