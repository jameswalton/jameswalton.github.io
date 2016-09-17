---
layout: post
title: "Testing Datomic queries and pulls using d/with"
date: 2016-09-17 18:14
comments: true
categories:
- clojure
- datomic
- testing
---
There’s amazing functionality for unit or integration testing Datomic ships with the datomic.api library. [d/with](http://docs.datomic.com/clojure/#datomic.api/with) applies transaction data to a provided database without mutating the underlying source. This gives the ability to ask "what if?" style questions to existing data, but also facilitates setting an instance of a Datomic database to a known state. This can then be used to test queries and pulls that return expected values.

A simple contrived example of testing a query that returns the people over the age of 30:

```clojure
(let [db (-> (d/db conn)
             (d/with [{:db/id (d/tempid :db.part/user)
                       :user/email "test@example.com"
                       :age 29}
                      {:db/id (d/tempid :db.part/user)
                       :user/email "bob@example.com"
                       :age 33}])
             :db-after)]

  (is (= 1 (number-of-people-over-30 db))))
```

The underlying number-of-people-over-30 Datomic query is responsible for finding the people who have an :age over 30 and performing a count on the result.

Another example is the use of w/with and Datomic pull syntax to get the friends of a user. The example assumes a user email can be used as a lookup ref.

```clojure
(let [db (-> (d/db conn)
             (d/with [{:db/id (d/tempid :db.part/user)
                       :user/email "test@example.com"
                       :user/friends []}
                      {:db/id (d/tempid :db.part/user)
                       :user/email "bob@example.com"
                       :user/friends [{:user/id 9}
                                      {:user/id 18}
                                      {:user/id 37]}]])
             :db-after)]

  (is (= {:user/friends [{:user/id 9} {:user/id 18} {:user/id 37}]}
         (d/pull db [{:user/friends [:user/id]}] [:user/email "bob@example.com"]))))
```

I create an in-memory Datomic database and seed it for testing purposes. My test suite runs (unscientifically measured) quickly and I have confidence in my code.