---
layout: post
title: 'Ruby meta-programming'
date: 2016-08-15 14:00:00 +0100
author: 'The Crab'
categories: 'ruby meta-programming'
---
In most of my Ruby work, I usually tend to focus on writing simple common programming patterns and structures in code design. Simple and expressive style that when a newcomer reads the code they can quickly understand what its doing. Because what I have worked on is usually more practical apps and solutions, even when refactoring, I stick to these simple structures and expressive style.

Recently however, I dived into understanding meta programming. Ruby has a lot of rope if you want to hung yourself. But meta-programming is a deviant for the brain that gives you such a rush.

In the next series of posts I will be exploring more into this world. I will be documenting my finds. For example:

```rb
  class Driver
    def user
      @user ||= UserRepository.new
    end

    def method_missing(some_sym, *_, &block)
      if some_sym.to_s =~ /find_by_(.*)/
        user.send($1, *_, &block)
      else
        super
      end
    end

    def respond_to?(some_sym, include_private = false)
      some_sym.to_s.start_with?('find_by_') || super
    end
  end
```

Now after writing this code. You realise that if you do:

```bash
  driver.method(:find_by_driver_id)
  NameError: undefined method `find_by_driver_id' for class `Driver'
```

So basically its important to actually take care of the `#method` if you want to avoid crushing things. So instead of defining the `respond_to` method, define the `respond_to_missing?` method.

```rb
  def respond_to_missing?(method_name, include_private = false)
    method_name.to_s.start_with?('find_by_') || super
  end
```
