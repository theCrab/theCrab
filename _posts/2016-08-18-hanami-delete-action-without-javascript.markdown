---
layout: post
title: "DELETE action without Javascript"
date: 2016-08-17 14:00:00 +0100
author: 'The Crab'
categories: 'Hanami'
---
I have been seeing this question many times on the [hanami chat](https://gitter.im/hanami/chat). The hanami-controller offers as much as Rails in routing. So you get `resources :posts` with all accompanying routes.

```ruby
# apps/web/config/routes.rb
resources :posts

# $ bundle exec hanami routes

GET     '/posts',          to: Web::Controllers::Posts::Index
GET     '/posts/new',      to: Web::Controllers::Posts::New
POST    '/posts',          to: Web::Controllers::Posts::Create
GET     '/posts/:id',      to: Web::Controllers::Posts::Show
GET     '/posts/:id/edit', to: Web::Controllers::Posts::Edit
PATCH   '/posts/:id',      to: Web::Controllers::Posts::Update
DELETE  '/posts/:id',      to: Web::Controllers::Posts::Destroy
```

#### DELETE'ing a resource
The problem I have seen mostly is because a lot of people expect it to work like in Rails.
Rails is a very mature framework and a lot of nuances have been taken care of. Hanami is still getting its feet on the road and its ready for production if you are willing to write a little more code than in Rails.

The way [Rails does it is by simulating](http://guides.rubyonrails.org/form_helpers.html#how-do-forms-with-patch-put-or-delete-methods-work-questionmark) a `form` using the `POST` HTTP verb. Since most browser vendors only support HTTP protocols **GET, POST and PUT** verbs. The DELETE verb needs a workaround. When you click a link that implements a delete action. JQueryUJS intercepts that click and presents an alert, after you confirm. The action is sent as a form post. So in the form below, your submit action is sent as a `POST`, the **param[_method]** is used to initiate the `#destroy` action on your `controller` :

```html
<form action="/posts/1234" method="post">
  <input type="hidden" name="_method" value="delete">
  <!-- ... -->
</form>
```
**In Hanami** however, unless you include `JQueryUJS`. You will have to do this manually. So lets take one of my favourite approach.

```ruby
# action code
module Web::Controllers::Posts
  class Destroy
    include Web::Action

    params do
      required(:id).filled
    end

    def initialize(post: PostRepository)
      @post = post.new.find(params[:id])
      # @comments = CommentRepository.new.for_post(@post.id)
      # @user = UserRepository.new.find(@post.user_id)
    end

    def call(params)
      if params.valid?
        # authorize!(@user, @post)
        @post.destroy
        redirect_to '/'
      else
        # handle error
      end
    end
  end
end
```

Its relatively straightforward. Now we just need to setup our route to:

`POST '/posts/:id', to: Web::Controllers::Posts::Destroy`

And this should work with or without Javascript. Now you just need to use a form to post to this action.

```ruby
  form_for post, routes.post_path(post.id), method: :delete do
    submit 'Destroy', class: 'uk-button uk-button-danger'
  end
```
This will produce a form similar to the following:

```html
<form id="post-form" action="/posts/1234" method="post">
  <input type="hidden" name="_method" value="delete">
  <input type="hidden"name="authenticity_token"  value="f755bb0ed134b76c432144748a6d4b7a7ddf2b71" />

  <button type="submit" name="commit">Destroy</button>
</form>
```
