---
layout: post
title: Getting Started with NodeMCU on Windows
description: "Using NdoeMCU to program an ESP8266 NodeMCU devkit."
modified: 2015-10-03
tags: [nodeMCU, esp8266, IoT, programming]
image:
  feature: nodemcu-kit-crop.jpg
  credit: me
  creditlink: http://wordsforthewise.github.io
---
## Developing with NodeMCU on Windows

I've spent a considerable amount of time programming and learning about ESP8266 modules and NodeMCU. I started with the basic AT commands, tried the arduino interface for it, and finally settled on the NodeMCU solution.  I put together a [tutorial for NodeMCU on Windows](/docs/NodeMCUonWindows.pdf).  Basically, NodeMCU allows you to program the ESP8266 with the Lua language, which runs on an [open-source C-based firmware](https://github.com/nodemcu/nodemcu-firmware).  The caretakers of the firmware appear to be mostly Chinese-based, with a few others mixed in.  I have faith it will do well, but my impression after working with it for some months is that it will be best for the DIY community, and not for consumer electronics, where reliability is key--at least, not using the latest firmware release...there are a few bugs, including one with the MQTT .

I've made some libraries for it, <a href="https://github.com/wordsforthewise/ESP-8266_network-connect">one creates a server at 192.168.4.1 (working on making it redirect any site request) and allows you to choose a network to join</a>.  These are a work in progress.  Good luck and bless up...

To run ESPlorer in linux, <a href="http://esp8266.ru/esplorer/">download the zip file</a> and run ```java -jar ESPlorer.jar```

<iframe src="https://drive.google.com/file/d/0B02etzCXItm7bWRITGc0eHljY00/preview" width="640" height="480"></iframe>