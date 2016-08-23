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

#### Setup
Depending on how you load your interactor. The directory structure I use looks like this:

**For all apps in the `apps/` directory** `lib/<APPNAME>/interactors/upload/upload.rb`. This way its required and included automatically project-wide.

**For a specific app in the `apps/` directory** `lib/interactors/upload/upload.rb`. To load it in each app we need some more setup steps in each app:

```rb
  # apps/web/application.rb
  # ...
  load_paths << [
    'controllers',
    'views',
    '../../lib/interactors'
  ]
  # ...
```

#### The Interactor
Say we want to build an uploader that we can use all around. Lets get the base out of the way

```rb
# lib/interactors/upload/upload.rb
require 'hanami/interactor'
require 'hanami/utils/path_prefix'

module Uploader
  include Hanami::Interactor

  def initialize(tempfile, filename)
    @tempfile, @filename = tempfile, dest_dir.join(filename).to_s
    @uploaded_file_url = ''
  end

  def storage_location
    FileUtils.mkdir_p(root.join('public/uploads'))
  end

  # Root path
  def root
    ::Application.configuration.root
  end

  def upload
    begin
      FileUtils.cp(@tempfile.path, @filename.to_s)
    rescue => e
      e.backtrace.each { |msg| error(msg) }
      return
    ensure
      @tempfile.close
      @tempfile.unlink
    end

    @uploaded_file_url = Hanami::Utils::PathPrefix.new('uploads').join(@filename).to_s
  end
end

# lib/interactors/upload/image.rb
class ImageUploader
  include Uploader

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
