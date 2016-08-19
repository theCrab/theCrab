---
layout: post
title: "iOS provisioning simplified"
date: 2016-08-18 14:00:00 +0000
author: 'The Crab'
categories: 'RubyMotion'
---
This has been a tricky one to accept. Somehow I like the idea of simplified processes. There is this quote by Richard Branson:

> Through the right people focusing on the right things, we can, in time, get on top of a lot if not most of the problems of this world. And that's what a number of us are trying to do.
<small>-- Richard Branson</small>

I think this quote capture very well what the guys at HypBite are trying to do with RubyMotion. They have just released version 4.13 and it looks great.

Along with it they have released a very helpful gem that fixes what I would regard a **BIG PROBLEM** and that is _getting your app to the AppStore_. [I have written](/rubymotion/ios/2016/07/10/prepare-rubymotion-app-for-appstore.html) about this process before.

Apple has not made tis process any easier. Tonnes of well documented process which means tonnes of reading and getting truly confused.

So yes I like this release. It means RubyMotion is a solution that with time, will be as mature and reliable as any framework out there. This direction is promising. Developers can invest in the project and we should be looking forward to better tools for Apple based app development.

The **motion-provisioning** toolchain manages the certificates and profiles automatically without using Xcode. Just sign in with your Apple ID and password and the rest is taken care of. Hopefully this will lead to developer productivity.

**Warning!** Beware of too much Ruby Magic.

Here is what the new gem will do for you. Read the fantastic [**Code Signing Guide**](http://www.rubymotion.com/developers/guides/manuals/cocoa/codesign/). The new Rakefile will look like below:

```rb
Motion::Project::App.setup do |app|
  app.name = 'VanMan'
  app.identifier = 'com.example.vanman'

  app.development do
    app.codesign_certificate = MotionProvisioning.certificate(
      type: :development,
      platform: :ios)

    app.provisioning_profile = MotionProvisioning.profile(
      bundle_identifier: app.identifier,
      app_name: app.name,
      platform: :ios,
      type: :development)
  end

  app.release do
    app.codesign_certificate = MotionProvisioning.certificate(
      type: :distribution,
      platform: :ios)

    app.provisioning_profile = MotionProvisioning.profile(
      bundle_identifier: app.identifier,
      app_name: app.name,
      platform: :ios,
      type: :distribution)
  end
end
```
This is designed to work for both paid and free Apple Developer accounts. Lets see how you roll new apps. Checkout **VanMan** on the UK Store.
