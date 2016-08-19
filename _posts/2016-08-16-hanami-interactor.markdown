---
layout: post
title: 'Hanami interactor'
date: 2016-08-16 14:00:00 +0100
author: 'The Crab'
categories: 'Hanami'
---
The Hanami web framework has brought a lot of best practices that are missing in Rails to the table. The organisation of a Hanami app is well designed to allow your app to go from a monolith to small or large services all running and sharing in one business codebase.

In Rails, your app is locked to your database, or simply ActiveRecord. Even your views have a clue what your database tables look like. And this can be very annoying.

Hanami has this little known thing called 'the interactor'. If you are familiar with 'services', this is exactly that. Unfortunaletly its not a well documented feature in the docs, and as its not mentioned in the infamous **'getting started'** section. It is easy to miss it.

This feature solves a lot of the problems that we burden controllers/actions with. In case you're wondering where to find it, your can [find it here](). Lets look at how you can solve some problems.

Take an example of a long running process, say you want to upload and process pictures or talk to an external api. This kind of process can easily take longer than an HTTP request is allowed. Lets see how we can avoid trouble without *monkey-jumping* around.

```rb
require 'hanami/interactor'
require 'hanami/utils/file_util'

class ImageUploader
  include Hanami::Interactor

  expose :image_url

  def initialize(params = {})
    @params = params
    @image_url = ''
  end

  def call
    copy_file
    process_file
    remove_temp_file
    image_url
  end

  private

  def copy_file

  end

  def process_file

  end

  # ...
end
```
