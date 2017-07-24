---
author: christeplovs
comments: true
date: 2009-10-06 19:53:10+00:00
layout: post
link: https://christeplovs.wordpress.com/2009/10/06/mac-os-x-10-6-continued/
slug: mac-os-x-10-6-continued
title: Mac OS X 10.6, continued
wordpress_id: 94
---

CouchDB is now installed -- much gnashing of teeth with old macports material in the way.

Building Knowledge Forum is interesting:


MACOSX_DEPLOYMENT_TARGET=10.5 make TARGET_PLATFORM=Darwin CONFIGURATION=Debug FAT_DESIRED=n DARWIN_SYS_ROOT=/Developer/SDKs/MacOSX10.5.sdk -j2


MACOSX_DEPLOYMENT_TARGET=10.5 make TARGET_PLATFORM=Darwin CONFIGURATION=Debug FAT_DESIRED=n DARWIN_SYS_ROOT=/Developer/SDKs/MacOSX10.5.sdk -j2


I wonder how we are with mingw32.
