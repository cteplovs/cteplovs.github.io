---
author: christeplovs
comments: true
date: 2009-08-01 16:51:18+00:00
layout: post
link: https://christeplovs.wordpress.com/2009/08/01/the-bindery/
slug: the-bindery
title: The Bindery
wordpress_id: 17
tags:
- database
- KnowledgeForum
- replication
---

In an [earlier post](http://christeplovs.wordpress.com/2009/07/31/iknowweknow/) I mentioned that I was reviving the Replication functionality that I had tried many years ago with Knowledge Forum.  Another idea that I have had regarding the utility of replication is in the creation of new Knowledge Forum databases.  Right now when one starts a new database it's empty.  Often we want to bootstrap a database with at least some content, or we may want to essentially "fork" the content of another database.  Here's a sort of quick proposal for such work:

Over the past decade we have seen the creation of literally hundreds of Knowledge Forum databases.  The decision to create a new database is often militated by pragmatic factors: privacy, lack of clutter, and technical considerations about the scalability of Knowledge Forum database have been the most salient limiting factors.

The decision to create a new, empty database is often the wrong one, as it provides no prior work for the participants to build on.  The lack of ability to build on prior work also eliminates much of the potential for ideas to live on past the time when the authors of the ideas make their contributions.

I propose the creation of a web-based service that will allow users to create new databases that pull together resources that are currently encapsulated in other databases.

In part this is an extension of the original “Replicator” functionality but it goes much further.   The work creates a comprehensive resource library that can be used as the basis of the International Association’s Knowledge Society Network.

At its core The Bindery is a catalogue of Knowledge Forum databases.  Users can select any number of views from databases to which they have access and then “bind” those views into a single, new database.  The newly created database also becomes part of the catalogue so we set up a continuous improvement cycle.

The work described as “Phase I” consists of creating the web-based UI, creating one-time-only replication conduits, and the activation of the resulting databases.  “Phase II” work comprises the Socio-Semantic Recommender System.  “Phase III” work consists of the creation of “live” conduits to synchronize views in real time as well as the importation of data from external sources such as Wikipedia.

Phase I

The user interface for The Bindery is a browser-accessible HTML-based system.  At this time there is no particular preference for the choice of a programming language, although something like PHP, Ruby, or Python seem reasonable.  The design of the system will focus on the user experience.  The primary user of the system in this Phase is someone with “manager” access in one or more databases.  In other words, someone who understands some of the administrative aspects of maintaining Knowledge Forum databases.  The Bindery will have its own authentication system, and users will need to be assigned an ID (or we could tap into OpenID).  Once the user logs in, he can start adding databases to which he has access.  The database may be already known to The Bindery or it may be new.  In either case, the user must supply authentication information to prove that he has access to the Knowledge Forum database at an appropriate level (manager or editor in this phase).  Once authenticated against the Knowledge Forum database the user can select one or more views to be bound into a new database.  An important aspect is that  views can come from any number of databases.  The notes in a view that has been selected to be bound into a new database will be copied over, and “intelligent” handling of auxiliary resources such as authors, supports, scaffolds, and so on will make the binding process smooth and will remove problems experienced by users of the earlier Replicator.

The Bindery will track which views have been bound and will report on these, where appropriate due to ethical concerns, to other users of the system.

I envision a diverse array of view types that can be bound into a new database: curriculum guidelines, exemplary work, examples of draft work, published views, views with help documentation, and so on are all possible candidates for binding into new databases.

Phase II

A key feature of The Bindery is the recommender system.  It is based on assessment of semantic similarity using Latent Semantic Analysis, which when used in conjunction with social recommender systems (“Users who selected this view also selected the following views…”), will result in an immensely powerful system for the continual improvement of ideas.

This Phase will use some of the innovations from my thesis: the ability to generate vector representations of whole views (easily done by performing vector addition on the member note vectors) and then examine other views for similarities builds on the work I did for the Knowledge Space Visualizer.  This is an example of a “Background Operation” that we have long talked about but have seldom realized.

Phase III

The creation of new databases in Phases I and II is a one-time-only event.  This Phase would see the creation of the necessary facilities that would allow cross-database synchronization of views.
