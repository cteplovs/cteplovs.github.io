---
author: christeplovs
comments: true
date: 2007-06-20 18:02:00+00:00
layout: post
link: https://christeplovs.wordpress.com/2007/06/20/google-web-toolkit-gwt/
slug: google-web-toolkit-gwt
title: Google Web Toolkit (GWT)
wordpress_id: 27
---

While I'm backing up my laptop's hard drive, in preparation for reformatting the bloody thing due to a problem with an "invalid node structure", I thought I'd write up some notes on my foray into GWT.  
  
I was intrigued by the idea of using Java to generate maintainable JavaScript.  I'm convinced that the level of interactivity that AJAX affords is totally adequate for much of what I want to do.  I'm really not a big fan of JavaScript, though.  
  
So I downloaded the [GWT](http://code.google.com/webtoolkit) and looked through the samples.  I found [Chapter 10](http://www.manning-source.com/books/hanson/sample-ch10_GWTiA.pdf) of the [GWT book from Manning](http://www.manning.com/hanson/) really quite helpful in guiding me through setting up an RPC.  
  
I had some trouble setting up Eclipse to export the war file for deployment on Tomcat.  Apparently many other people have too.  In the end, I used some ideas from [Chad Bourke](http://charisacademy.com/GWT/createwar.htm) (via the [GWT Developer Forum](http://groups.google.com/group/Google-Web-Toolkit) but wound up doing something like  


<blockquote>  
cd Semantics2  
./Semantics2-compile  
ant -f semantics2.ant.xml package  
cp semantics2.jar www/org.ikit.Semantics2/WEB-INF/lib/  
cd www/org.ikit.server  
zip -r ../../Semantics2.war .  
scp ../../Semantics2.war remote.server:/path/to/tomcat/webapps  
  
</blockquote>

  
  
In all, it seems that it's quite possible to extend Knowledge Forum by setting up an RPC service that talks to the ZTB API and then serializes the necessary bits to send across the wire to the client.  It seems reasonably fast, too.
