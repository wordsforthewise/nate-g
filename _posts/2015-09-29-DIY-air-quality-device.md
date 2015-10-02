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

You can make a DIY air quality monitor (AQM) for about $20.  You can add internet datalogging for another few bucks.  This will give you a rough measurement of the air quality of your environment, and can help you determine if you should do anything about it (get an air filter, etc).

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

Finally, connect the Shinyei PPD42NS sensor, and upload this code:

{% highlight c++ %}

/*
 * This uses the Liquid Crystal library from https://bitbucket.org/fmalpartida/new-liquidcrystal/downloads GNU General Public License, version 3 (GPL-3.0)
 * Pin Connections:
 * LCD I2C:
 *      SCL => A5
 *      SDA => A4
 *      VCC => pin 12
 *      GND => GND
 * Shinyei PPD42NS:
 *      JST Pin 1  => Arduino GND
 *      JST Pin 3  => Arduino 5VDC
 *      JST Pin 4  => Arduino Digital Pin 3

Dylos Air Quality Chart (1 micron)+
1000+ = VERY POOR
350 - 1000 = POOR
100 - 350 = FAIR
50 - 100 = GOOD
25 - 50 = VERY GOOD
0 - 25 = TOTALLY EXCELLENT, DUDE!
 */

#include <SoftwareSerial.h>
#include <MemoryFree.h>
#include <stdlib.h>
#include <Wire.h>
#include <LCD.h>
#include <LiquidCrystal_I2C.h>

LiquidCrystal_I2C	lcd(0x27,2,1,0,4,5,6,7); // 0x27 is the I2C bus address for an unmodified module

// custom charaters for progress bar
byte p1[8] = {
  0x10,
  0x10,
  0x10,
  0x10,
  0x10,
  0x10,
  0x10,
  0x10};

byte p2[8] = {
  0x18,
  0x18,
  0x18,
  0x18,
  0x18,
  0x18,
  0x18,
  0x18};

byte p3[8] = {
  0x1C,
  0x1C,
  0x1C,
  0x1C,
  0x1C,
  0x1C,
  0x1C,
  0x1C};

byte p4[8] = {
  0x1E,
  0x1E,
  0x1E,
  0x1E,
  0x1E,
  0x1E,
  0x1E,
  0x1E};

byte p5[8] = {
  0x1F,
  0x1F,
  0x1F,
  0x1F,
  0x1F,
  0x1F,
  0x1F,
  0x1F};
  
// values for progress bar
#define lenght 16.0
double percent=100.0;
unsigned char b;
unsigned int peace;
bool firstTime = true; // for showing a countdown screen on the LCD

// settings for dust sensor
const byte numReadings = 36;     // moving average period
const byte particlePin = 3; // P1 pin, second from left on board
unsigned long duration;
unsigned long startTime_ms;
unsigned long sampleTime_ms = 5000;
int timeRemaining;
unsigned long lowPulseOccupancy = 0;
float ratio = 0;
float concentration = 0;
float average;
int particles; // for converting the average to an integer, fractions of a particle don't make sense
int firstTimeCounter = 1;
float alpha = 2/(float(numReadings) + 1); // exponential moving average recursive weighting, using conventional 2/(N + 1)

void setup() {
  // ### setup LCD screen ###
  pinMode(12, OUTPUT);
  digitalWrite(12, HIGH);
  lcd.setBacklightPin(3,POSITIVE);
  lcd.setBacklight(HIGH);
  lcd.begin(16, 2); // critically, this must come before createChar
  lcd.createChar(0, p1);
  lcd.createChar(1, p2);
  lcd.createChar(2, p3);
  lcd.createChar(3, p4);
  lcd.createChar(4, p5);
  
  lcd.clear();
  
  Serial.begin(9600);
    
  pinMode(particlePin,INPUT);
  
  startTime_ms = millis(); // get the current time in milliseconds
  lcd.setCursor(0,0);
}

void loop() {
  if (firstTime) {
    printStatus();
  }
  duration = pulseIn(particlePin, LOW);
  lowPulseOccupancy = lowPulseOccupancy+duration;

  if ((millis()-startTime_ms) >= sampleTime_ms) // if the sample time has been exceeded
  {
    ratio = lowPulseOccupancy/(sampleTime_ms*10.0);  // Integer percentage 0=>100; divide by 1000 to convert us to ms, multiply by 100 for %, end up dividing by 10
    concentration = 1.1*pow(ratio,3)-3.8*pow(ratio,2)+520*ratio; // using spec sheet curve for shinyei PPD42ns

    // calculate the exponential moving average:
    if (firstTime) {
      average = concentration;
      firstTime = false;
    }
    else {
      average = alpha * concentration + (1 - alpha) * average;
    }
    particles = int(average); // fractions of a particle don't make sense
    
    lcd.clear();
    lcd.setCursor(0, 0);
    Serial.print("average: ");
    Serial.println(particles);
    Serial.print("concentration: ");
    Serial.println(concentration);
    if (average > 1000) { // air quality is VERY POOR
      lcd.print("very poor");
    }
    else if (average > 350) { // air quality is POOR
      lcd.print("poor");
    }
    else if (average > 100) { // air quality is FAIR
      lcd.print("fair");
    }
    else if (average > 50) { // air quality is GOOD
      lcd.print("good");
    }
    else if (average > 25) { // air quality is VERY GOOD
      lcd.print("very good");
    }
    else { // air quality is EXCELLENT (<25 counts)
      lcd.print("excellent");
    }
    lcd.setCursor(0, 1);
    lcd.print(particles);
    lowPulseOccupancy = 0;
    startTime_ms = millis(); // reset the timer for sampling at the end so it is as accurate as possible
  }
}

void printStatus() {
  lcd.setCursor(0, 0);

  percent = (millis() - startTime_ms)/float(sampleTime_ms)*100.0;

  lcd.print("startin up...");
  timeRemaining = (sampleTime_ms - (millis()-startTime_ms))/1000;
  if (timeRemaining > 9) {
    lcd.print(timeRemaining);
    lcd.print("s");
  }
  else {
    lcd.print(" ");
    lcd.print(timeRemaining);
    lcd.print("s");
  }
  
  lcd.setCursor(0,1);

  double a=lenght/100*percent;

  // drawing black rectangles on LCD

  if (a>=1) {

    for (int i=1;i<a;i++) {

      lcd.write(byte(4));

      b=i;
    }

    a=a-b;

  }

  peace=a*5;

  // drawing charater's colums

  switch (peace) {

  case 0:

    break;

  case 1:
    lcd.write(byte(0));

    break;

  case 2:
    lcd.write(byte(1));
    break;

  case 3:
    lcd.write(byte(2));
    break;

  case 4:
    lcd.write(byte(3));
    break;

  }

  //clearing line
  for (int i =0;i<(lenght-b);i++) {
    lcd.print(" ");
  }
}

{% endhighlight %}

This will measure the number of particles >1 micron in 0.01 cubic feet, and update the reported value on the screen every 30s.  On the second line, it will translate that value into a meaninful description, like horrible, good, excellent, etc.

Next, add <a href="">datalogging</a>.

### Details

To make sure the digital pins could supply enough juice to the LCD, I went ahead and measured the current.  It turned out to be just under 20mA, which is the maximum recommended constant current output from the digital pins, huzzah!  If this weren't the case, we'd have to expend a little extra effort to split the 5V power from the board, since on the UNOs and nanos I have, only one 5V pin exists.

The Shinyei PPD42NS is connected to the 5V pin directly, since it can take up to 90mA (but only takes 70mA on the one I measured).
