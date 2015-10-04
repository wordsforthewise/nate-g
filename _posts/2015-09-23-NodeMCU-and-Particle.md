---
published: false
---

## NodeMCU vs Particle

I've spent a considerable amount of time programming and learning about ESP8266 modules and NodeMCU. I started with the basic AT commands, tried the arduino interface for it, and finally settled on the NodeMCU solution.  I put together a [tutorial for NodeMCU on Windows]({{site.baseurl}}/docs/NodeMCUonWindows.pdf).  Basically, NodeMCU allows you to program the ESP8266 with the Lua language, which runs on an [open-source C-based firmware](https://github.com/nodemcu/nodemcu-firmware).  The caretakers of the firmware appear to be mostly Chinese-based, with a few others mixed in.  I have faith it will do well, but my impression after working with it for some months is that it will be best for the DIY community, and not for consumer electronics, where reliability is key.

I've since moved most of my efforts to the [Particle Photon board](https://store.particle.io/), because they have a significant amount of backend infrastructure built up.  I'm developing and targeting consumer products for sale, where reliability and OTA updates are important--NodeMCU doesn't have that stuff built in yet.  The [ESP8266 devkit](http://www.aliexpress.com/item/FLASH-NodeMcu-Lua-WIFI-jaringan-papan-pengembangan-Berbasis-ESP8266/32448650599.html) is still about 3-4x cheaper than the Spark Particle, and the ESP8266 [ESP-12 board](http://www.aliexpress.com/item/Free-Shipping-ESP8266-serial-WIFI-model-ESP-12-ESP-12E-ESP12E-Authenticity-Guaranteed-ESP12/32349990031.html) is about 6x cheaper than the Particle P1 module ($2 vs $12).