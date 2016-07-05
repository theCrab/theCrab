---
layout: post
title:  "Swara backend and api with Hanami"
date:   2016-06-24 15:34:02 +0100
categories: swara hanamirb api
---
[Swara](https://itunes.apple.com/) is our **Mobile Card Payment** app. Currently its used mostly by **taxi drivers** taking payments for taxi fairs.

It is growing steadily and we are looking to make a few features available once the Android version is released.

Meanwhile, on launch day. I realised that running everything of the mobile app was not a sustainable idea. This was fine as long as it was running on my phone. But when it was released on the App Store, I quickly realised how that was a bad idea.

So I built quickly a Sinatra app and deployed on a small AWS server. During the night the app was downloaded 150 times. The AWS deploy started looking inadequate. I quickly rolled out Hanami and started doing things properly.

This is the first experience with Hanami in a somewhat serious app. I like that I can extend and grow the app using Hanami. This is great framework.

Thanks Luca Guidi.
