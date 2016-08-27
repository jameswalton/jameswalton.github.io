---
layout: post
title: "Persist sessions across server restarts with Buddy Auth"
date: 2016-08-27 15:21
comments: true
categories:
- clojure
- ring
- sessions
- buddy
---
A recent clojure project required the use of session based authentication; for this I chose the buddy.auth library with a session backend.
Buddy auth is a great library to quickly add session (or a few other types) based authentication to an application.
However one issue I had was that out of the box the sessions weren’t persisting across server restarts so during development users would be required to re-authenticate if the application was restarted.

A few stack overflow articles I saw reporting this issue didn’t help but I noticed that I was using the wrap-session middleware from ring.middleware.session.
After reading the documentation for this middleware I saw that out of the box it uses the memory-store but can optionally use a cookie-store (ring.middleware.session.cookie/cookie-store).

It was trivial to add the cookie-store to the application.

{% highlight clojure %}
(defn app [connection]
  (as-> (app-routes connection) $
        (wrap-authorization $ auth-backend)
        (wrap-authentication $ auth-backend)
        (wrap-session $ {:store (cookie-store {:key "a 16-byte secret"})})))
{% endhighlight %}

With this small addition the sessions are now encrypted in a cookie and persist across restarts.