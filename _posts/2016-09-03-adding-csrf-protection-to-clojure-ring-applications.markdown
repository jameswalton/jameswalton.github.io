---
layout: post
title: "Adding CSRF protection to Clojure ring applications"
date: 2016-09-03 18:34
comments: true
categories:
- clojure
- ring
- security
- csrf
---
This post was originally going to specifically target [CSRF](https://en.wikipedia.org/wiki/Cross-site_request_forgery) protection using the wrap-anti-forgery middleware, but during the writeup I started having a look at the ring-defaults library which has some sensible security defaults out of the box. There are two flavours for configuring an HTTP API (api-defaults & secure-api-defaults) and two for configuring a browser-accessible website. ring-defaults is straightforward to add to any new or existing ring application but it’s worth understanding the default middleware that gets applied since duplicate middleware could cause unwanted behaviour.

First include the library in your project.clj. The current version can be found [here](https://github.com/ring-clojure/ring-defaults)

```clojure
[ring/ring-defaults "0.2.1"]
```

Then wrap your ring handler in the wrap-defaults function, providing either the library defaults, or merging some of your own.

```clojure
(:require [ring.middleware.defaults :refer [wrap-defaults site-defaults])

(def my-site-defaults
  "Customise site-defaults to use cookie-store instead of the default memory-store."
  (merge site-defaults
         {:session {:store (cookie-store {:key "a 16-byte secret"})}})

(defn app
  "The application handler with applied middleware. Takes a Datomic connection."
  [connection]
  (as-> (app-routes connection) $
        (wrap-defaults $ my-site-defaults))
```

The [as-> macro](https://clojuredocs.org/clojure.core/as-%3E) is handy for when there are thread-first and thread-last inconsistencies.

The above code snippet now expects all form requests other than GET and HEAD to be sent with a CSRF token. Example hiccup form with anti-forgery-field:

```clojure
(:require [ring.util.anti-forgery :refer [anti-forgery-field]])

(form-to
  [:post (str "/join")]
  (anti-forgery-field)

  (submit-button "Submit"))])])
```

Once added forms will have an anti forgery token that gets passed along with each form submission. The token is a random key unique to the current session.

```html
<input id="__anti-forgery-token" name="__anti-forgery-token" type="hidden" value="<generated anti forgery token here>">
```

The CSRF token helps prevent users being tricked into maliciously submissing a form. Any form submissions that don't contain the correct will be handled with a default exception that can optionally be customised defining an error response or error handler.