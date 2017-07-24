---
author: christeplovs
comments: true
date: 2013-09-19 15:23:50+00:00
layout: post
link: https://christeplovs.wordpress.com/2013/09/19/semantic-and-social-network-analysis-of-a-mooc-using-sakai/
slug: semantic-and-social-network-analysis-of-a-mooc-using-sakai
title: Semantic and Social Network Analysis of a MOOC using Sakai
wordpress_id: 463
categories:
- Work
---

Over the past two years I have worked with a client to create software to visualize semantic and social networks and I thought I would share some of the innovations that we have come up with.  I have recently extended that work so that it can be plugged into a Learning Management System (LMS).  We used Sakai as our first foray into LMS integration. Why?  Because an instructor who is planning to offer a Massive Open Online Course (a MOOC) uses Sakai, and it seemed to be a great opportunity to see how things would work. Basically, we are interested in looking at ties between users based on their contributions to the discussion forums.  We decided that the act of replying to a post would constitute a "tie".  We also had access to data regarding the topics that were being discussed, which provides some information about the content (or semantics).  We also have access to self-assessments about expertise:  participants rated their expertise on the topics that were used in the discussions.

I built the system using Python and Javascript (jQuery and D3).  Here are some screen caps that show some of the functionality of the system.

[![Screen Shot 2013-09-18 at 11.48.56 AM](http://christeplovs.files.wordpress.com/2013/09/screen-shot-2013-09-18-at-11-48-56-am.png?w=300)](http://christeplovs.files.wordpress.com/2013/09/screen-shot-2013-09-18-at-11-48-56-am.png)

The names are hidden to protect the privacy of the participants, but you get the idea.  Each individual is shown as a blue dot, and tie between individuals are shown using highly stylized arrowheads (this is based on research, not just a whim).















[![Screen Shot 2013-09-18 at 11.49.14 AM](http://christeplovs.files.wordpress.com/2013/09/screen-shot-2013-09-18-at-11-49-14-am.png?w=300)](http://christeplovs.files.wordpress.com/2013/09/screen-shot-2013-09-18-at-11-49-14-am.png)Of course an important part of coping with network representations, especially the ones that come from large groups like those found in MOOCs, is the ability to filter.  My clients wanted a "Theme Cloud" that showed the relative contributions to each of the discourse topics.  The theme cloud can be used to select those participants who are active in particular topics.  Multiple topics can be selected.













[![Screen Shot 2013-09-18 at 11.49.22 AM](http://christeplovs.files.wordpress.com/2013/09/screen-shot-2013-09-18-at-11-49-22-am.png?w=300)](http://christeplovs.files.wordpress.com/2013/09/screen-shot-2013-09-18-at-11-49-22-am.png)

Mousing over any individual node will bring up a "tooltip" style panel that shows the self-assessment of expertise information for each participant.  The researchers on the project are interested in examining the relationship between contributions and expertise and the data the underpin the representations are perfect for this sort of analysis.













The MOOC is just starting up now, and it will be interesting to see how the system performs with hundreds of participants and potentially thousands of contributions.
