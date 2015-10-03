---
layout: post
title: DIY Arduino/microcontroller air quality device
description: Plans for cheap air quality monitors.
modified: {}
tags: 
  - air quality
  - science
  - sensors
  - IoT
image: 
  feature: "dirty-air-thin.png"
  credit: LA Times/AP
  creditlink: "http://articles.latimes.com/2014/mar/25/science/la-sci-sn-air-pollution-deaths-world-health-organization-20140325"
published: true
---



### Summary

Fine particulate matter in the air from cars and trucks, cooking, fires, and industry can give you cancer, heart attacks, and shorten your lifespan.  Poor air quality can have a number of other bad effects.  You can make a DIY air quality monitor (AQM) for about $20 that can warn you of poor air quality.  You can add internet datalogging for another few bucks.  This will give you a rough measurement of the air quality of your environment, and can help you determine if you should do anything about it (get an air filter, etc).

### Background

Tiny particles in the air, generated from cars, trucks, powerplants, industry, and even cooking at home, are causing cancer, heart attacks, and premature death among other ailments.  It's only getting worse as our population grows and demands more transportation and energy.  Particularly dangerous are the tiny particulates less than 10 microns in size, especially those less than 2.5 microns in diameter (PM2.5).  These are about 100 times smaller than the width of a human hair, and can pass directly into the bloodstream, increasing risks for diseases like cancer and heart disease, among other things.  [An increase in concentration of PM2.5 by 10 ug/m3 (micrograms per meter cubed) equates to a 36% increase in lung cancer!](http://www.thelancet.com/journals/lanonc/article/PIIS1470-2045%2813%2970279-1/abstract)  [Coincidentally, the national average of PM2.5 in the US is about this value.](http://wwwn.cdc.gov/CommunityHealth/profile/currentprofile/CA/Orange/310019)

The scary thing is, the exhaust from combustion engines (cars, trucks, lawnmowers, weedwhackers, farm and industry equipment) actually spews a TON of very tiny particles that are very difficult and expensive to detect.  Here's a plot from a [scientific paper](http://www.sciencedirect.com/science/article/pii/S0048969799002144), showing a distribution of exhaust from a diesel engine:

![diesel emissions.png](/images/diy-aqm/diesel emissions.png)

Most of the particles are in the range of 0.01 to 0.1 micron!  [That's so tiny it can actually penetrate your skin.](http://www.ncbi.nlm.nih.gov/pubmed/16614727)  Whoa!

![surprised-monkey.jpg](/images/surprised-monkey.jpg)

In the summer/fall of 2014, I started having sinus headaches and other sinus/mucuous issues.  At the time I thought my air quality might have something to do with it (it was actually mostly from chemical air pollution in a solar cell factory I worked in), so I started researching how to measure it.  Turns out the cheapest devices are cost around $200+ (the nifty [Speck](http://store.specksensor.com/products/speck) sensor, the [Dylos sensor](http://www.amazon.com/Dylos-DC1100-Standard-Quality-Monitor/dp/B000XG8XCI/ref=sr_1_4?ie=UTF8&qid=1443847115&sr=8-4&keywords=dylos) that was available at the time, and the [Aircasting device](aircasting.org)).

After some google-y googling, [I found this post](http://www.howmuchsnow.com/arduino/airquality/grovedust/), which described how to set up a DIY particulate monitor based on the Shinyei PPD42NS sensor.  The following are instructions on how to build your own, with a live display.

### Parts

To do this, you will need:

<ul>
<li>arduino (I used a nano)</li>
<li>LCD display (I used a 1602 16x2 HD44780 Character LCD /w IIC/I2C Serial Interface Adapter Module, labeled FC-113)</li>
<li>Shinyei PPD42NS device</li>
<li>Some female-female jumper cables</li>
<li>USB power adapter if you want to plug it into the wall</li>
<\ul>

### Setup

First, get your LCD working.  There's a few guides (<a href="http://blog.mklec.com/how-to-use-iici2c-serial-interface-module-for-1602-lcd-display/">[1]</a>, <a href="http://tronixlabs.com/news/tutorial-serial-i2c-backpack-for-hd44780compatible-lcd-modules-with-arduino/">[2]</a>, <a href="http://arduino-info.wikispaces.com/LCD-Blue-I2C">[3]</a>) I found out there to help, but first, solder on the I2C adapter to the LCD in the correct fashion:

![2015-09-30 18.06.45.jpg]({{site.baseurl}}/_posts/2015-09-30 18.06.45.jpg)

This cheap ol' chinese solder I have doesn't wet surfaces well for some reason.  Oh well, better solder is in the mail and this works well enough.

Next make sure you can get the display up and working.  The example websites I found describe a way to do it using an older version of the <a href="https://bitbucket.org/fmalpartida/new-liquidcrystal/downloads">LiquidCrystal library</a> (1.2.1), which will work.  It also seems to work with the latest version (1.3.2).  To install, download the zip file, unzip into your Arduino libraries folder (for me it was C:\Users\Nate\Documents\Arduino\libraries), then rename it to 'LiquidCrystal'.
Make sure to close your Arduino IDE program if open, and restart it to refresh the directory cache.  I used the example given from mklec.com, with small modifications:

{% highlight c++ %}

/**
 * I2C/IIC LCD Serial Adapter Module Example
 * 
 * Instructions at http://blog.mklec.com/how-to-use-iici2c-serial-interface-module-for-1602-lcd-display
 *
 * This uses the Liquid Crystal library from https://bitbucket.org/fmalpartida/new-liquidcrystal/downloads GNU General Public License, version 3 (GPL-3.0)
 * Pin Connections:
 *      SCL = A5
 *      SDA = A4
 *      VCC = pin 12 // barely can supply enough current for the LCD (20mA required, and is max output of digital pins)
 *      GND = GND
 */
#include <Wire.h>
#include <LCD.h>
#include <LiquidCrystal_I2C.h>

LiquidCrystal_I2C	lcd(0x27,2,1,0,4,5,6,7); // 0x27 is the I2C bus address for an unmodified module

void setup()
{
    pinMode(12, OUTPUT);
    digitalWrite(12, HIGH);
    lcd.setBacklightPin(3,POSITIVE);
    lcd.setBacklight(LOW); // NOTE: You can turn the backlight off by setting it to LOW instead of HIGH
    lcd.begin(16, 2);
    lcd.clear();
}

void loop()
{
    lcd.setCursor(0,0);
    lcd.print("whatever");
    lcd.setCursor(0,1);
    lcd.print("hello world");
    delay(2000);
    lcd.clear();
    lcd.setCursor(0,0);
    lcd.print("doo doo");
    delay(2000);
}

{% endhighlight %}

Once you have that working, construct the container for the sensor.  We need to be able to hold the particle senser upright since it uses a resistor to create a hot-air draft for an air flow.  

Finally, connect the Shinyei PPD42NS sensor, and upload [mah code from github](https://github.com/wordsforthewise/DIY-AQM/tree/master/arduino-and-shinyeiPPD42NS).

This will measure the number of particles >1 micron in 0.01 cubic feet, and update the reported value on the screen every 30s.  On the second line, it will translate that value into a meaninful description, like horrible, good, excellent, etc.

Next, add <a href="">datalogging</a>.

### Details

To make sure the digital pins could supply enough juice to the LCD, I went ahead and measured the current.  It turned out to be just under 20mA, which is the maximum recommended constant current output from the digital pins, huzzah!  If this weren't the case, we'd have to expend a little extra effort to split the 5V power from the board, since on the UNOs and nanos I have, only one 5V pin exists.

The Shinyei PPD42NS is connected to the 5V pin directly, since it can take up to 90mA (but only takes 70mA on the one I measured).
