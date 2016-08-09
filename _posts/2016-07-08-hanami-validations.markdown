---
layout: post
title:  "Hanami version 0.8 validations"
date:   2016-08-07 18:00:00 +0100
categories: Ruby
author: The Crab
tags: hanami validations
---
Hanami `version 0.8` has just been released. This version is major due to a number of reasons and features. I will be talking about just one, but the others are mostly bug fixes and improvements.

The biggest change is that the validations have been completely revamped. The new validations are built on top of **[dry-validations](http://dry-rb.org/gems/dry-validations)** which is a much stricter and better validation approach. In a different post I will write about [Dry-RB](http://www.dry-rb.org)

> **dry-rb** is a collection of next-generation Ruby libraries,
each intended to encapsulate a common task

#### New validation interface
In the past versions, to validate form inputs in the controller/action. We used to do something like below:

**Before:**

```rb
module Web::Controllers::Users
  class Create
    include Hanami::Action

    params do
      param :user do
        param :username, presence: true
        param :password, presence: true
        param :email,    presence: true
      end
    end

    def call(params)
      if params.valid?
        @user = UserRepository.create(User.new(params[:user]))

        redirect_to('/users/' + @user.id)
      else
        status = 422
      end
    end

  end
end
```

**Now you just do:**

```rb
# ... snip
params do
  required(:user).schema do
    required(:username).filled(:str?)
    required(:password).filled(:str?)
    required(:email).filled(:str?)
  end
end
# ... snip
```

This i find very beautiful and it sticks to the old validation style, but more powerful.

If you are like me, I like to move my validations to a central place where I can reuse them for other apps in the Hanami stack, for example if you decide to have `apps/api` or `apps/admin`. Lets move the validations out, I will put mine in the `lib/bookshelf/validations/` directory:-

```rb
# lib/bookshelf/validations/user_validations.rb
class UserParams < Hanami::Action::Params
  params do
    required(:user).schema do
      required(:username).filled(:str?)
      required(:password).filled(:str?)
      required(:email).filled(:str?)
    end
  end
end

# Then you can use it
# ...
def call(params)
  user_params = UserParams.new(params[:user])

  if user_params.valid?
    user = UserRepository.create(User.new(user_params))

    redirect_to("/users/#{user.id}")
  else
    status = 422
  end
end
# ...
```

Is there a better way? Lets chat about this on the [Hanami chat](https://gitter.im/hanami/chat) and help improve this post.
