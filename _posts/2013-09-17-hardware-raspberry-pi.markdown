---
author: christeplovs
comments: true
date: 2013-09-17 13:42:53+00:00
layout: post
link: https://christeplovs.wordpress.com/2013/09/17/hardware-raspberry-pi/
slug: hardware-raspberry-pi
title: 'Hardware I: The Raspberry Pi'
wordpress_id: 449
---

Yesterday, I received my [Raspberry Pi](http://www.raspberrypi.org/).

[![_IMG_0497](http://christeplovs.files.wordpress.com/2013/09/img_0497.jpg?w=300)](http://christeplovs.files.wordpress.com/2013/09/img_0497.jpg)

The [Raspberry Pi](http://canada.newark.com/) is a credit-card sized computer that runs linux, and it represents something pretty amazing to me. It's promoted as a computer that can be used to teach students how to program, but that's not the main appeal to me.  I am particularly interested in how the device can interface with the "real world".  I suppose part of this appeal is a long-standing desire to play with hardware.  I remember playing with breadboards and ICs when I was in high school, and I certainly spent a fair bit of my paycheque from my first job at Radio Shack on electronics.  But I never managed to find the time to really scratch the hardware itch once I hit university.  So it's something of a homecoming for me to start experimenting with hardware again.

I'm curious to know how people react to their [Raspberry Pi](http://canada.newark.com/).  Specifically, I wonder how people deal with the hurdles of getting the device to boot up, select an operating system, and then log into it.  I thank Bob McLean, a mentor, friend, and my M.A. supervisor for introducing me to the world of linux back in 1995.  That foundation, coupled with many years of working with linux, made the whole process of understanding what the RPi was doing a lot easier.  I more or less followed the [Quick Start](http://www.raspberrypi.org/wp-content/uploads/2012/04/quick-start-guide-v2_1.pdf) instructions and after many minutes I was rewarded with a very familiar boot screen:

[![_IMG_0504](http://christeplovs.files.wordpress.com/2013/09/img_0504.jpg?w=300)](http://christeplovs.files.wordpress.com/2013/09/img_0504.jpg)

This looks a lot like any generic linux startup machine, and everything continued to go well.

[![_IMG_0505](http://christeplovs.files.wordpress.com/2013/09/img_0505.jpg?w=300)](http://christeplovs.files.wordpress.com/2013/09/img_0505.jpg)

I confirmed that the X11 server works, but then opted to run the device headless and use secure shell (ssh) to log into it.

I was keen to get the device talking to the "outside world", so I quickly wired up the breadboard with some LEDs, and compiled the gpio package on the RPi to allow me to control it.  Again, making use of experience with linux and feeling comfortable with commands like sudo and apt-get, I was pleased when I saw a green LED light up.  I know, it's a small victory and it would be entirely underwhelming for most people but I got a huge kick out of it.

[![_IMG_0506](http://christeplovs.files.wordpress.com/2013/09/img_0506.jpg?w=225)](http://christeplovs.files.wordpress.com/2013/09/img_0506.jpg)

I am imagining the possibilities that this offers, particularly as an innovative way to introduce a "teaching and learning analytics appliance" into classrooms. The details of what this might entail are still a bit fuzzy, but I'm dreaming big on this. Of course it doesn't mean lighting up LEDs. But what about something with Google Glass? Or how about NFC or RFID? How could the RPi help us learn about the spread of ideas? What about maintaining a learner model on an RPi? Those are just some quick thoughts and they're fraught with problems, but still very exciting. The price point for these devices is great: about CAD35. That means you can outfit an entire classroom of 22 students for less than the cost of an iPhone 5s!
