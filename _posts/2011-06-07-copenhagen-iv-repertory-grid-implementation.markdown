---
author: christeplovs
comments: true
date: 2011-06-07 19:55:26+00:00
layout: post
link: https://christeplovs.wordpress.com/2011/06/07/copenhagen-iv-repertory-grid-implementation/
slug: copenhagen-iv-repertory-grid-implementation
title: 'Copenhagen IV: Repertory Grid Implementation'
wordpress_id: 153
categories:
- Work
tags:
- copenhagen
- facebook
- gae
- nexttell
- protovis
- rgt
---

I'm working on the use of [Repertory Grid Technique](http://christopherteplovs.wordpress.com/2011/05/23/copenhagen-iii-repertory-grid/) (RGT) for formative assessment.  We are interested in using Rep 5 but there seem to be delays in the ordering process and we are facing some pretty hard deadlines with the end of the elementary school year and the beginning of the summer (university) semester approaching.  So we don't really have much choice other than to implement our own RGT software.

I have been interested in [Google App Engine](http://code.google.com/appengine/) (GAE) [for some time](http://twitter.com/#!/cteplovs/status/14370400204), but up until now I had no good reason to pursue it seriously.

For the past couple of decades I have always had access to a machine running some variant of the unix operating system.  That all changed recently when I moved to what is, for the most part, a Microsoft shop.  Of course I have my trusty laptop running Mac OS X, which sports a lovely unix-like operating system under its flashy hood.  Prototyping on my laptop is easy for me, but when it comes to deploying software for others, running production services on my laptop just doesn't cut it.

I toyed with the idea of buying some space in the cloud but I thought it might be edifying to see if I could cope without my own (at least virtually) dedicated machine.

Over the past couple of years I have also become quite interested in python, particularly for scientific computing.  Python is a good fit with a lot of the work I'm doing with topic models.

We want to try using the RGT with some summer students at Copenhagen Business School.  Being good HCI folks we are naturally concerned about the user experience.  We hear a lot about the hassle of multiple login systems and the like so I need to deploy the software in a way that allows me to authenticate users with whatever system they're using.  I'm lucky in that the course in which I'm planning to deploy the software requires students to use Facebook, so I can use the Facebook authentication system.  Therefore, it makes sense to deploy my software as a [Facebook app](http://developers.facebook.com/).

I'm not only interested in using RGT, but also in improving the representation of the results.  The current "best practice" calls for either clustering using dendrograms, or using principal components analysis to create a "map" of the elements and the constructs.  We know, based on some pilot work and from talking to other researchers that the current representations of RGT certainly leave room for improvement.  I'm planning to create interactive visualizations for the representation of the Repertory Grids.  My current plan is to use a javascript-based visualization toolkit to create the interactive representations.  If I manage to do this well then we will have a system that allows learners to create and share visualizations of the conceptual understanding of a topic area.  That would meet the requirements for a prototype of a communication and negotiation layer for learner models.

Overall, then, I am planning to implement Repertory Grid Technique software as a Facebook app using Google App Engine as the back end and [protovis](http://vis.stanford.edu/protovis/) to create the representational layer.
