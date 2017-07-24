---
author: christeplovs
comments: true
date: 2011-06-13 19:11:45+00:00
layout: post
link: https://christeplovs.wordpress.com/2011/06/13/copenhagen-v-codename-fargo-2/
slug: copenhagen-v-codename-fargo-2
title: 'Copenhagen V: Codename ''FARGO'''
wordpress_id: 688
categories:
- Work
tags:
- fargo
- gae
- nexttell
- protovis
- repgrid
---

It's been a week since I described what I was going to try to do with Repertory Grid on Facebook.  It's been a very productive week and I am pleased with the early (alpha) version of a Facebook app that is codenamed FARGO (Formative Assessment using Repertory Grid Online).  

I started with the demo app that Facebook distributes called ["Run with Friends"](http://apps.facebook.com/runwithfriends/) and worked it up into an app that can elicit a Repertory Grid, show some results, and (optionally) post some of the results to a Facebook page.  There is still a fair bit of work to be done before it's ready for use but the early results are encouraging.  It has also been a real boost to get feedback from other NEXT-TELL team members.

I was faced with an interesting problem from an Human-Computer Interaction perspective:  how do I elicit a "real" grid, given that not everyone who will be testing the app is in the same course?  What do these people have in common that could be used as elements in a Repertory Grid?   It occurred to me that because we're using Facebook as the front end I could request access to the list of users' friends and then use a random sampling of their friends to elicit constructs.  There's a lot of interesting (psychological) research to be done on that sort of topic, but that's not my primary focus.  I am most interested in creating better visualizations of the data that comes from a repertory grid.  As a proof of concept I hooked up the repertory grid output to a dynamic visualization using protovis to create a network diagram.  As I expected the network diagram, in its current form, isn't particularly useful to or usable by end users.  No big surprises there!

I want to backtrack a bit change the app to elicit some feedback on some of the more traditional representations of the repertory grid, such as the (raw) matrix of elements and constructs, as well as the clustering output.  The former is easy to implement.  The latter may require me to hook up to Rep 5 to get the clustering done, or perhaps I can just use a python library to do so the clustering on the server end of things.  The [python-cluster](http://sourceforge.net/projects/python-cluster/) library looks promising (I need a 100% python implementation due to restrictions put in place by Google App Engine).  I could then use the [dendrogram visualizations](http://vis.stanford.edu/protovis/ex/dendrogram.html) provided by protovis.

So those are the next steps:  get some of the more traditional representations hooked up and get more feedback from folks who are willing to test out the app.  I also need to provide a bit more context for the "lead-in", as well as doing a better job at the end so that when one decides to share via Facebook one is redirected somewhere nicer that just the same page.  And in the event that the user doesn't want to share via Facebook I need to make the link to their visualization available.

I also need to start thinking about how to generalize this out to be about formative assessment on any topic... perhaps allowing some sort of "preconfiguration" for a particular topic.  Rep 5 does this but the UI is a bit unfriendly.  So I need to strive a balance of something that just works and the ability to customize it to different domains.
