---
layout: post
title: "Cross Origin Resource Sharing in Sinatra (ruby) and Ring (clojure) - CORS"
date: 2015-03-10 13:00:00 -0700
comments: true
author: Fellipe Brito
categories: [javascript, sinatra, ruby, clojure, ring, ajax]
---

_by [@fellipebrito](https://github.com/fellipebrito)_

Few weeks ago I started thinking about the idea of bigger layers of separation between the frontend and the backend. In my perfect world, I would create a project that would rely 100% of its dynamic content in an API that would have only one goal: **talk to the database and return a JSON**.

In this perfect world, specifications would be the first thing to write. You sit down with the tech team, review the mockups, define the JSON the frontend needs, write it down and done! **Frontend team will have its content mocked and ready to go, and backend team would have a perfect spec to return.**

Then the time to code came. I invested few hours coding two simple applications. One to handle the backend and another one to provide a simple HTML entry for my localhost. The backend server is both made in **Sinatra and Clojure** I finished this task in few minutes. The next task was to make them talk to each other. BOOM!

***“No 'Access-Control-Allow-Origin' header is present on the requested resource”.***

> if you want to step out all the explanation and jump direct to the code, here is the commit that saved my life: [Enables cross origin access](https://github.com/fellipebrito/front-back-case/commit/5dede83ab8ae5ab320fe2041160c07e18e7f4b7e)

The error above is the one I found on my chrome web development tool, and then after few clicks here and there, CORS came up to me.

**Why  is CORS a problem?**
Regular web pages can use the XMLHttpRequest object to send and receive data from remote servers, but they're limited by the same origin policy.

**So here is my problem:** I have two different sources, and I want them to talk through a SPA.

Thankfully both Sinatra and Ring (for Clojure) offer us a solution for that: [Sinatra cross-origin](https://github.com/britg/sinatra-cross_origin) - a Cross Origin Request Sharing extension for Sinatra and [Ring middleware for Cross-Origin Resource Sharing](https://github.com/r0man/ring-cors)

### Sinatra
Three steps:

1) add the gem to your Gemfile
```ruby
'sinatra-cross_origin'
```

2) require it in your app
```ruby
require "sinatra/cross_origin"
```

3) enable it inside your app
```ruby
register Sinatra::CrossOrigin
configure do
      enable :cross_origin
end
```

### Ring/Clojure
Three steps:

1) Add the dependence in project.clj
```clojure
[ring-cors "0.1.6"]
```

2) require it in the handler
```clojure
[ring.middleware.cors :refer [wrap-cors]]
```

3) wrap the routes with it
```clojure
;; from:
(def app
  (wrap-defaults app-routes site-defaults))


;; to:
(def app 
  (-> (wrap-defaults app-routes site-defaults)
      (wrap-cors :access-control-allow-origin #"http://localhost:3000"
                 :access-control-allow-methods [:get :put :post]
                 :access-control-allow-headers ["Content-Type"])))
```

Done!
