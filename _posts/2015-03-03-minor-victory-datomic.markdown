---
layout: post
title: "A Minor Victory with Datomic"
date: 2015-03-03
permalink: /minor-victory-datomic
tags: datomic, clojure, database
---

I just converted an app's database from SQL to Datomic. I started on Friday and finished on Monday. It went smoother than expected.

### Background

I've been following Datomic for a while. I'm familiar with how it works on a conceptual level but hadn't used it in anger until now.

The app I'm working on had a fairly elaborate SQL schema. The size of the backend code is on the small side but it has a few complex queries that bring together lots of row-shaped data and transform them into highly nested tree-shaped data.

As the app developed, requirements inevitably changed, and so query code needed to change. Updating data transformation code was cumbersome and slow. I couldn't help but feel I was wasting time.

After seeing the new [Datomic pull API](http://docs.datomic.com/pull.html) I started seriously considering switching from SQL to Datomic.

I decided to give myself a day to assess Datomic, and if it seemed promising/realistic, a weekend to do the hard work of recreating the schema, migrating data, and updating app code (Clojure backend and JavaScript frontend). If things went south, I'd just go back to SQL.

### How it went

To my surprise, it actually worked. Recreating the schema and migrating data was tedious, but rewriting queries was cake.

Thanks to the pull API, the size of the app's query code shrank significantly. Bloated queries and transformations became minimal, declarative queries comprised of simple data structures.

The old query code wasn't even using straight SQL; it was using a library designed to perform relationship-aware queries (kind of like an ORM). Even still, Datomic was far superior.

Here's an example query from the app (which manages sporting events for dogs). It shows how you can build up complex, nested pull patterns programmatically. Sub-patterns elided for brevity.

{% highlight clojure %}

(def entry-pattern
  ['*
   {:entry/status ['*]
    :entry/dog [:db/id :dog/call-name]
    :entry/handlers [:db/id :person/first-name :person/last-name]
    :entry/event event-pattern
    :entry/chosen-offered-classes ['*
                                   {:chosen-offered-class/offered-class
                                    offered-class-pattern}]}])

(defn get-entries-by-owner [db owner-id]
  (d/q '[:find [(pull ?entry pattern) ...]
         :where [?owner :person/dog-ownerships ?do]
                [?do :dog-ownership/dog ?dog]
                [?entry :entry/dog ?dog]
         :in $ ?owner pattern]
       db owner-id entry-pattern))

{% endhighlight %}

The data returned is shaped according to the pattern, which is what I ultimately need to send client-side. One-to-many and many-to-many relationships get handled no problem.

You don't want to see what the old query code looked like.

The frontend -- JavaScript and Flux/React -- didn't really need any changes. It was already consuming trees of JSON data. There's a backend layer which leverages [Schema](https://github.com/Prismatic/schema) to convert database data into client-side-friendly JSON. The main change here was to remove attribute namespaces in transit to the client, and re-add them on the way back.

### Stumbling blocks

* Error messages are bad. Really bad. Even worse than Clojure. Most stacktraces point somewhere deep inside the closed-source implementation of Datomic and offer little information beyond "your query is broken".
* Installation was cumbersome, especially Leiningen integration using GPG credentials.
* Many constraint checks that used to be handled by the database must now be handled in the app ([Schema](https://github.com/Prismatic/schema), which I was already using, helps mitigate this).
* No built-in query pagination.

### Notable features I didn't fully grok before

* No essential entity "types". Any entity can have any attribute. Any ref can point to any entity. Ids are shared among all entities. This could seem liberating or terrifying depending on your viewpoint.
* Attribute namespaces make it easy to create cross-cutting conceptual groupings which are inconvenient to create in SQL.
* Nil is not an allowed value. I'm ok with this. I did have to make an adapter layer for CRUD operations.
* Following references (and back references) is so much nicer than joining on foreign keys.

