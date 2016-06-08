---
layout: post
title: Calibratin' air quality monitors
description: "Calibration of particulate sensors."
modified: 2015-09-18
tags: [AQM, air quality, sensors, science]
image:
  feature: calibratin.jpg
  credit: me
  creditlink: http://wordsforthewise.github.io
---

## Calibrating air quality sensors

I've been working with the Shiyei PPD42NS sensor, and the cheaper (by %50) Samyoung DSM501A.  My goal is to have a portable sensor that will keep track of the particulate matter you are breathing, and warn you if you hit dangerous levels.  

The first step is making sure the sensors are accurate enough for this purpose.  I bought a Dylos DC1100 Pro air quality monitor, which uses a laser and a fan to measure particles in the air.  It puts out a number that is number of 1-micron or larger particles per 0.01 ft3 (and a number for 5-micron or larger).  

# Procedure
![calib-setup.jpg](/images/aqm-calibration/calib-setup.jpg){: .center-image }

I built a small test setup, basically a stand for the Shinyei or Samyoung, with an Arduino UNO that runs the code to collect the data, and a piece of wood I light on fire to get some particles in the air.  Interestingly, the smoke from the fire shows up as >1um particles, but not as >5um particles.  [Two python scripts are run to save the data from the Dylos and Shinyei/Samyoung sensor.](https://github.com/wordsforthewise/shinyei-ppd42ns-arduino/tree/master/testing/with%20fan/calibration--serial%20data "correlations")  [Another python script is then run afterwards to join the data togther and back out a correlation.](https://github.com/wordsforthewise/shinyei-ppd42ns-arduino/blob/master/testing/with%20fan/calibration--serial%20data/getSensorCorrelation.py "get correlation")

## Results
# Samyoung DSM501A
The [Samyoung datasheet](https://github.com/wordsforthewise/ESP-8266-particle-sensor/blob/master/spec%20sheets/DSM501%20spec%20sheet.pdf) gives a [correlation](https://github.com/wordsforthewise/ESP-8266-particle-sensor/blob/master/spec%20sheets/DSM501A%20ratio%20to%20particle-001%20ft3.png), but it only has data for over 2500 particles/0.01 ft3.  After running some tests, I found the correlation could be extended into the finer resolution range.  From the datasheet, the correlation is linear:

![samyoung correlation.png](/images/aqm-calibration/samyoung correlation.png){: .center-image }

And the correlation from my tests was similar to the datasheet correlation(y = 630x): mine was y = 620x.

![corr.png](/images/aqm-calibration/corr.png){: .center-image }

Here's a comparison of the data from the Samyoung to data from a Dylos DC1700--pretty good agreement, for a sub-$10 sensor.  That data was taken every 15 seconds.

![compare.png](/images/aqm-calibration/compare.png){: .center-image }

## Conclusions

It seems like the Samyoung isn't super reliable below ~1-2k counts, which explains why their data sheet doesn't have a correlation for particle counts/0.1 ft^3 below 2500 particles.  So far the Shinyei seems slightly better, but I haven't been able to verify the correlation from the datasheet.

