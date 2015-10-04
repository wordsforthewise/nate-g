---
layout: post
title: Getting Started with NodeMCU on Windows
description: "Using NdoeMCU to program an ESP8266 NodeMCU devkit."
modified: 2015-10-03
tags: [nodeMCU, esp8266, IoT, programming]
image:
  feature: nodemcu-kit.jpg
  credit: me
  creditlink: http://wordsforthewise.github.io
---
## Developing with NodeMCU on Windows

I've spent a considerable amount of time programming and learning about ESP8266 modules and NodeMCU. I started with the basic AT commands, tried the arduino interface for it, and finally settled on the NodeMCU solution.  I put together a [tutorial for NodeMCU on Windows]({{site.baseurl}}/_posts/NodeMCUonWindows.pdf).  Basically, NodeMCU allows you to program the ESP8266 with the Lua language, which runs on an [open-source C-based firmware](https://github.com/nodemcu/nodemcu-firmware).  The caretakers of the firmware appear to be mostly Chinese-based, with a few others mixed in.  I have faith it will do well, but my impression after working with it for some months is that it will be best for the DIY community, and not for consumer electronics, where reliability is key.

I've made some libraries for it, one creates a server at 192.168.4.1 (working on making it redirect any site request) and allows you to choose a network to join.  These are a work in progress.  Good luck and bless up...
