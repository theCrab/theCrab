---
layout: post
title: Hanami authenticate with JSON Web Tokens
date: 2016-08-25 15:00:00 +0000
author: The Crab
categories: Hanami
tags:
  - - JWT
  - - Hanami
---
```rb
# Fumikiri namespace
module Fumikiri
  def self.included(base)
    base.class_eval do
      before :authenticate!
      expose :current_user
    end
  end

  # Configuration namespace
  module Configuration
    attr_accessor :user

    def initialize(user, &blk)
      @user = user
    end
  end

  private

  def authenticate!
    halt 401 unless authenticated?
  end

  def authenticated?
    !current_user
  end

  def current_user
    @current_user ||= UserRepository.find(jwt_token.iss)
    halt 404 if @current_user.nil?
  end

  def jwt_token
    token = env['Authentication'].delete('Bearer ')
    return nil if token.nil?
    JWT.decode(token, true, 'HMAC')
  end
end
```
