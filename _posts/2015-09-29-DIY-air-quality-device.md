---
layout: post
title: DIY Arduino/microcontroller air quality device
description: "Plans for cheap air quality monitors."
modified: 2015-09-29
tags: [air quality, science, sensors, IoT]
image:
  feature: dirty-air-thin.png
  credit: LA Times/AP
  creditlink: http://articles.latimes.com/2014/mar/25/science/la-sci-sn-air-pollution-deaths-world-health-organization-20140325
---

### Summary

You can make a DIY air quality monitor (AQM) for about $20.  You can add internet datalogging for another few bucks.  This will give you a rough measurement of the air quality of your environment, and can help you determine if you should do anything about it (get an air filter, etc).

### Parts

To do this, you will need:

arduino (I used a nano)
LCD display (I used a 1602 16x2 HD44780 Character LCD /w IIC/I2C Serial Interface Adapter Module)
Shinyei PPD42NS device
Some female-female jumper cables
USB power adapter if you want to plug it into the wall

### Setup

First, get your LCD working.  There's a few guides (<a href="http://blog.mklec.com/how-to-use-iici2c-serial-interface-module-for-1602-lcd-display/">[1]</a>, <a href="http://tronixlabs.com/news/tutorial-serial-i2c-backpack-for-hd44780compatible-lcd-modules-with-arduino/">[2]</a>, <a href="http://arduino-info.wikispaces.com/LCD-Blue-I2C">[3]</a>) I found out there to help, but first, solder on the I2C adapter to the LCD in the correct fashion:

Next make sure you can get the display up and working.  The example websites I found describe a way to do it using an older version of the <a href="https://bitbucket.org/fmalpartida/new-liquidcrystal/downloads">LiquidCrystal library</a> (1.2.1), which will work.  The nifty thing is, it seems to work with the latest version (1.3.2).  To install, download the zip file, unzip into your Arduino libraries folder (for me it was C:\Users\Nate\Documents\Arduino\libraries), then rename it to 'LiquidCrystal'.
Make sure to close your Arduino IDE program if open, and restart it to refresh the directory cache.  I used the example given from mklec.com, with small modifications:

{% highlight c++ %}

/**
 * I2C/IIC LCD Serial Adapter Module Example
 * Tutorial by http://mklec.com
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

{% endhighight %}

Once you have that working, construct the container for the sensor.  We need to be able to hold the particle senser upright since it uses a resistor to create a hot-air draft for an air flow.  

Finally, connect the Shinyei PPD42NS sensor, and upload this code:

{% highlight c++ %}


{% endhighight %}

This will measure the number of particles >1 micron in 0.01 cubic feet, and update the reported value on the screen every 30s.  On the second line, it will translate that value into a meaninful description, like horrible, good, excellent, etc.

Next, add <a href="">datalogging</a>.

### Details

To make sure the digital pins could supply enough juice to the LCD, I went ahead and measured the current.  It turned out to be just under 20mA, which is the maximum recommended constant current output from the digital pins, huzzah!  If this weren't the case, we'd have to expend a little extra effort to split the 5V power from the board, since on the UNOs and nanos I have, only one 5V pin exists.

The Shinyei PPD42NS is connected to the 5V pin directly, since it can take up to 90mA (but only takes 70mA on the one I measured).