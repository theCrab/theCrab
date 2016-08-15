---
layout: post
title: 'RubyMotion: step-by-step to AppStore'
date: 2016-07-10 15:00:00 +0100
author: 'The Crab'
categories: 'rubymotion ios'
---
It takes a long time to develop an app. In RubyMotion, that time is kinda reduced in many ways.

There are many things that Xcode does behind the scenes that RubyMotion doesn't. I guess all those who use RM tend to have a fetish for control and writing everything in plain text. I share in that kink too.

One of the things you must do is configure your app manually with the right configs for the AppStore. So lets get down on it:

**This setup is ideal for an indie developer distributing their own apps**. Follow this steps in the order from 1 going down.

1. Create an [App ID](https://developer.apple.com/account/ios/identifier/bundle/) _(Avoid using the WildCard*)_, this can be the greatest source of headaches. It looks like **com.paxilabs.spota**
2. Create a [Development &amp; Distribution Certificate](https://developer.apple.com/account/ios/certificate/create). Ideally you only need 1 of each of these certificates **for ALL your apps**, keep this files safe from wicked eyes.
3. Create 2 [Provisioning Profiles](https://developer.apple.com/account/ios/profile/create). 1 Development and 1 Distribution profile **for each individual app.** Every app must have 2 of these profiles.
4. [Register your devices](https://developer.apple.com/account/ios/device/create) for testing your apps. Just makes life easy.

So thats the first side done. We now need to do some [iTunes Connect configurations](https://itunesconnect.apple.com/). After loging into iTunes Connect:

1. Create a new app record. In the **Bundle ID** select your ID from the list.
2. Follow the rest of the requirements there. Its very straight forward and it wont submit until its all done properly.

> **Download Code-signing Dev &amp; Dist Certificates and Provisioning Profiles**. This is how you could setup your env.

```sh
Developer/Apple/ios/CertificateSigningRequest.certSigningRequest
Developer/Apple/ios/ios_development.cer
Developer/Apple/ios/ios_distribution.cer
```
I use Xcode to download the Provisioning Profiles. But I recommend you download yours directly and place them in an appropriate location away from your RubyMotion app directory, please.

### RubyMotion Rakefile
Here is a sample of what your file would resemble.

```rb
# -*- coding: utf-8 -*-
$:.unshift("/Library/RubyMotion/lib")
require 'motion/project/template/ios'
require "rubygems"
require 'bundler'
Bundler.require

env_vars = Dotenv.load

Motion::Project::App.setup do |app|
  app.name          = "Spõta"
  app.frameworks   += %w(CoreLocation CoreMotion)
  app.short_version = ENV["SHORT_VERSION"]
  app.identifier    = ENV["APP_IDENTIFIER"]

  # app.pods do
    # pod "Firebase"
    # pod "Raven"
  # end

  app.info_plist["NSLocationAlwaysUsageDescription"]    = "We utilize location data to enhance your app experience."
  app.info_plist["NSLocationWhenInUseUsageDescription"] = "We utilize location data to enhance your app experience."
  app.info_plist["NSMotionUsageDescription"]            = "We would like to use your motion data."
  app.info_plist["UIBackgroundModes"]                   = ["location"]

  app.development do
    app.codesign_certificate = ENV["DEV_CODESIGN_CERTIFICATE"]
    app.provisioning_profile = ENV["DEV_PROVISIONING_PROFILE"]
  end

  app.release do
    app.codesign_certificate = ENV["CODESIGN_CERTIFICATE"]
    app.provisioning_profile = ENV["PROVISIONING_PROFILE"]
  end
end
```

Now you can `rake device`. Make sure your connected device is one of the ones added earlier in step 4 above. If the app launches on the device successfully, then you are ready to distribute.

### Finally!
I recommend you use [motion-appstore](https://github.com/HipByte/motion-appstore) from here on. Follow their instructions.
