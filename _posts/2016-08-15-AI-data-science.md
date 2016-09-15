---
layout: post
title: Doing data science: Prepping data for LSTM AI Neural Networks
description: Preparation and execution of an LSTM network on retail data.
modified: 2016-8-15
tags:
  - data science
  - AI
  - Artificial Intelligence
  - Neural Networks
  - Python
  - Hadoop
  - Spark
  - Apache
  - Scala
image:
  feature: "diy_hydro_pump.jpg"
  credit: me
  creditlink: nateGeorge.github.io
published: true
---





### Summary

Prep data for time-series predictions using straight-up Python, Hadoop MapReduce, Spark with Scala (all on EC2s), and implement an LSTM neural network (also on an EC2).

<!--more-->

### Intro

I recently completed the Udacity Machine Learning Nano Degree in a month.  I've got to say they left a gaping hole in my knowledge--how to deal with massive quantities of data.  Case in point: [the Kaggle data science competition sponsored by Grupo Bimbo](https://www.kaggle.com/c/grupo-bimbo-inventory-demand). The idea is to predict the exact demand stores will have for each product in the future.  Although not the only way to solve this problem, I thought it seemed apt to use a LSTM (long short-term memory) Neural Network, since those are made for time-series predictions.

### Exploring the data

#### The training data

First, download the data from the downloads page (train.csv is all we need for now).  FYI, I'm running these first Python scripts on an Amazon EC2 c3.2xlarge instance (8 CPUs and 15 GB RAM).  There are a lot of potential features that can be exploited here.  For example, Grupo Bimbo provides client and product descriptions, as well as returns, which we could use for more accurate prediction of demand.  However, all that's in the test dataset is shipping data, client ID, product ID, and the week number.  For now, we're going to limit the input features to those raw features (except week number): the shipping data (routes and suppliers), the client ID (Cliente_ID), the product ID (Producto_ID), and the previous few weeks of equalized demand (orders minus returns).


#### Pure Python to get us started

[Eric Couto shared a nice way to load this data with minimal memory usage](https://www.kaggle.com/ericcouto/grupo-bimbo-inventory-demand/using-82-less-memory).  Once we've got it loaded as a pandas dataframe, we can save it in the convenient [feather format](https://github.com/wesm/feather), which can save and load data much faster than pandas alone (while still using the pandas dataframe).

Using this script, we can load the training data, save it as a feather file, sort it for further processing, and save that again as a feather file.

{% highlight python %}

import pandas as pd
import numpy as np
import time
import feather

# Full table:   6.1Gb
# This version: 1.1Gb (-82%)
types = {'Semana': np.uint8, 'Agencia_ID': np.uint16, 'Canal_ID': np.uint8,
         'Ruta_SAK': np.uint16, 'Cliente_ID': np.uint32, 'Producto_ID': np.uint16,
         'Demanda_uni_equil': np.uint32}

# read_csv takes a long time relative to feather
print('[INFO]: reading training data into pandas dataframe')
startTime = time.time()
train = pd.read_csv('../input/train.csv', usecols=types.keys(), dtype=types)
timeTook = time.time() - startTime
timeTookS = round(timeTook % 60, 3)
timeTookM = int(timeTook / 60)

print('took ' + str(timeTookM) + ' m, ' + str(timeTookS) + ' s to load \
       training data with pandas')

print('[INFO]: saving feather data...')
feather.write_dataframe(train, '../input/train.min.feather')

startTime = time.time()
print('[INFO]: loading feather data...')
train = feather.read_dataframe('../input/train.min.feather')
timeTook = time.time() - startTime
timeTookS = round(timeTook % 60, 3)
timeTookM = int(timeTook / 60)

print('took ' + str(timeTookM) + ' m, ' + str(timeTookS) + ' s to load \
       training data with feather')

print('[INFO]: sorting training data...')
trainSort = train.sort_values(by=['Cliente_ID', 'Producto_ID', 'Semana'])
print('[INFO]: saving feather data...')
feather.write_dataframe(trainSort, '../input/trainSort.feather')

{% endhighlight %}

Our output looks like this:

{% highlight bash %}

[INFO]: reading training data into pandas dataframe
took 0 m, 44.483 s to load training data with pandas
[INFO]: saving feather data...
[INFO]: loading feather data...
took 0 m, 7.257 s to load training data with feather
[INFO]: sorting training data...
[INFO]: saving feather data...

{% endhighlight %}

We can see feather is much faster than pandas, by about 6x in this case.

Next, we want to take the sorted training data (you'll see why we sorted it here), and loop through it, creating a single row in a dataframe for each Cliente_ID/Producto_ID combo.  We take the mode of the Ruta_SAK and other shipping data, because every once and a while a single Ruta_SAK is used to get some extra supply (as in, one supplier didn't have enough Twinkies, so they had to order from two warehouses--thus two Ruta_SAKs).

{% highlight python %}

from __future__ import print_function
import numpy as np
import pandas as pd
import feather
import time
import scipy.stats.mstats as scsm

print('[INFO]: loading training data...')
trainSort = feather.read_dataframe('../input/train.min.sorted.feather')
print('[INFO]: finished loading.')

# always test first on a small subset of data
sampleTrain = trainSort.iloc[0:1000]

features = []
featWeeks = []
firstRow = trainSort.iloc[0]
prevProd = firstRow.Producto_ID
prevCli = firstRow.Cliente_ID
weeksDemands = []
rutas = [firstRow.Ruta_SAK]
canals = [firstRow.Canal_ID]
agencias = [firstRow.Agencia_ID]
oneFeat = [firstRow.Agencia_ID, firstRow.Canal_ID,
           firstRow.Ruta_SAK, firstRow.Cliente_ID, firstRow.Producto_ID]

startTime = time.time()

print('[INFO] processing training sample data')
for row in sampleTrain.itertuples():
    if row.Producto_ID == prevProd and row.Cliente_ID == prevCli:
        weeksDemands.append([row.Semana, row.Demanda_uni_equil])
        rutas.append(row.Ruta_SAK)
        canals.append(row.Canal_ID)
        agencias.append(row.Agencia_ID)
    else:
        if len(set(rutas)) > 1:
            rutaMode = scsm.mode(rutas)[0][0]  # annoying indexing
            oneFeat[2] = rutaMode
            weeksDemandsDict = {}
            for w, d in weeksDemands:
                if w not in weeksDemandsDict.keys():
                    weeksDemandsDict[w] = d
                else:
                    weeksDemandsDict[w] += d

            weeksDemands = []
            for w in weeksDemandsDict.keys():
                weeksDemands.append([w, weeksDemandsDict[w]])

        if len(set(canals)) > 1:
            canalMode = scsm.mode(canals)[0][0]  # annoying indexing
            oneFeat[1] = canalMode
            weeksDemandsDict = {}
            for w, d in weeksDemands:
                if w not in weeksDemandsDict.keys():
                    weeksDemandsDict[w] = d
                else:
                    weeksDemandsDict[w] += d

            weeksDemands = []
            for w in weeksDemandsDict.keys():
                weeksDemands.append([w, weeksDemandsDict[w]])

        if len(set(agencias)) > 1:
            agMode = scsm.mode(agencias)[0][0]  # annoying indexing
            oneFeat[0] = agMode
            weeksDemandsDict = {}
            for w, d in weeksDemands:
                if w not in weeksDemandsDict.keys():
                    weeksDemandsDict[w] = d
                else:
                    weeksDemandsDict[w] += d

            weeksDemands = []
            for w in weeksDemandsDict.keys():
                weeksDemands.append([w, weeksDemandsDict[w]])

        if len(set([w[0] for w in weeksDemands])) < 7:
            weeksDemandsDict = {}
            for w, d in weeksDemands:
                if w not in weeksDemandsDict.keys():
                    weeksDemandsDict[w] = d
                else:
                    weeksDemandsDict[w] += d

            weeksDemands = []
            for w in weeksDemandsDict.keys():
                weeksDemands.append([w, weeksDemandsDict[w]])

        if len(weeksDemands) < 7:
            meanDemand = int(round(np.mean([i[1] for i in weeksDemands]), 0))
            weeks = [i[0] for i in weeksDemands]
            for i in range(3, 10):
                if i not in weeks:
                    weeksDemands.append([i, meanDemand])

            weeksDemands = sorted(weeksDemands, key=lambda x: x[0])

        oneFeat.append([i[1] for i in weeksDemands])
        features.append(oneFeat)
        # featWeeks.append([i[0] for i in weeksDemands]) # save memory

        # reset variable for next client/product
        rutas = [row.Ruta_SAK]
        canals = [row.Canal_ID]
        agencias = [firstRow.Agencia_ID]
        weeksDemands = [[row.Semana, row.Demanda_uni_equil]]
        oneFeat = [row.Agencia_ID, row.Canal_ID,
                   row.Ruta_SAK, row.Cliente_ID, row.Producto_ID]
        prevProd = row.Producto_ID
        prevCli = row.Cliente_ID

    if row.Index % 100 == 0:
        print(str(round(row.Index / float(1000) * 100, 2)) + '% complete')

# finish up last data point
if len(set(rutas)) > 1:
    rutaMode = scsm.mode(rutas)[0][0]  # annoying indexing
    oneFeat[3] = rutaMode
    weeksDemandsDict = {}
    for w, d in weeksDemands:
        if w not in weeksDemandsDict.keys():
            weeksDemandsDict[w] = d
        else:
            weeksDemandsDict[w] += d

    weeksDemands = []
    for w in weeksDemandsDict.keys():
        weeksDemands.append([w, weeksDemandsDict[w]])

if len(set([w[0] for w in weeksDemands])) < 7:
    weeksDemandsDict = {}
    for w, d in weeksDemands:
        if w not in weeksDemandsDict.keys():
            weeksDemandsDict[w] = d
        else:
            weeksDemandsDict[w] += d

    weeksDemands = []
    for w in weeksDemandsDict.keys():
        weeksDemands.append([w, weeksDemandsDict[w]])

if len(weeksDemands) < 7:
    meanDemand = int(round(np.mean([i[1] for i in weeksDemands]), 0))
    weeks = [i[0] for i in weeksDemands]
    for i in range(3, 10):
        if i not in weeks:
            weeksDemands.append([i, meanDemand])

    weeksDemands = sorted(weeksDemands, key=lambda x: x[0])

oneFeat.append([i[1] for i in weeksDemands])
features.append(oneFeat)

timeTook = time.time() - startTime
timeTookS = round(timeTook % 60, 3)
timeTookM = int(timeTook)

print('[INFO]: took ' + str(timeTookM) + ' m, ' +
      str(timeTookS) + ' s to process training sample data')

{% endhighlight %}

The output looks as such:

{% highlight bash %}

[INFO]: loading training data...
[INFO]: finished loading.
[INFO] processing training sample data
0.0% complete
10.0% complete
20.0% complete
30.0% complete
40.0% complete
50.0% complete
60.0% complete
70.0% complete
80.0% complete
90.0% complete
[INFO]: took 0 m, 0.097 s to process training sample data

{% endhighlight %}

Now for the real thing.  We iterate through the whole dataframe, condensing each set of Cliente_ID/Producto_ID weekly demand data into a 1D row vector.  I just changed a few things from the testing script: `for row in trainSort.itertuples():` and

{% highlight python %}

if row.Index % 50000 == 0:
    print(
        str(round(row.Index / float(trainSort.shape[0]) * 100, 2)) + '% complete')

{% endhighlight %}


{% highlight python %}

from __future__ import print_function
import numpy as np
import pandas as pd
import feather
import time
import scipy.stats.mstats as scsm

print('[INFO]: loading training data...')
trainSort = feather.read_dataframe('../input/train.min.sorted.feather')
print('[INFO]: finished loading.')

features = []
featWeeks = []
firstRow = trainSort.iloc[0]
prevProd = firstRow.Producto_ID
prevCli = firstRow.Cliente_ID
weeksDemands = []
rutas = [firstRow.Ruta_SAK]
canals = [firstRow.Canal_ID]
agencias = [firstRow.Agencia_ID]
oneFeat = [firstRow.Agencia_ID, firstRow.Canal_ID,
           firstRow.Ruta_SAK, firstRow.Cliente_ID, firstRow.Producto_ID]

startTime = time.time()

print('[INFO]: processing training sample data')
for row in trainSort.itertuples():
    if row.Producto_ID == prevProd and row.Cliente_ID == prevCli:
        weeksDemands.append([row.Semana, row.Demanda_uni_equil])
        rutas.append(row.Ruta_SAK)
        canals.append(row.Canal_ID)
        agencias.append(row.Agencia_ID)
    else:
        if len(set(rutas)) > 1:
            rutaMode = scsm.mode(rutas)[0][0]  # annoying indexing
            oneFeat[2] = rutaMode
            weeksDemandsDict = {}
            for w, d in weeksDemands:
                if w not in weeksDemandsDict.keys():
                    weeksDemandsDict[w] = d
                else:
                    weeksDemandsDict[w] += d

            weeksDemands = []
            for w in weeksDemandsDict.keys():
                weeksDemands.append([w, weeksDemandsDict[w]])

        if len(set(canals)) > 1:
            canalMode = scsm.mode(canals)[0][0]  # annoying indexing
            oneFeat[1] = canalMode
            weeksDemandsDict = {}
            for w, d in weeksDemands:
                if w not in weeksDemandsDict.keys():
                    weeksDemandsDict[w] = d
                else:
                    weeksDemandsDict[w] += d

            weeksDemands = []
            for w in weeksDemandsDict.keys():
                weeksDemands.append([w, weeksDemandsDict[w]])

        if len(set(agencias)) > 1:
            agMode = scsm.mode(agencias)[0][0]  # annoying indexing
            oneFeat[0] = agMode
            weeksDemandsDict = {}
            for w, d in weeksDemands:
                if w not in weeksDemandsDict.keys():
                    weeksDemandsDict[w] = d
                else:
                    weeksDemandsDict[w] += d

            weeksDemands = []
            for w in weeksDemandsDict.keys():
                weeksDemands.append([w, weeksDemandsDict[w]])

        if len(set([w[0] for w in weeksDemands])) < 7:
            weeksDemandsDict = {}
            for w, d in weeksDemands:
                if w not in weeksDemandsDict.keys():
                    weeksDemandsDict[w] = d
                else:
                    weeksDemandsDict[w] += d

            weeksDemands = []
            for w in weeksDemandsDict.keys():
                weeksDemands.append([w, weeksDemandsDict[w]])

        if len(weeksDemands) < 7:
            meanDemand = int(round(np.mean([i[1] for i in weeksDemands]), 0))
            weeks = [i[0] for i in weeksDemands]
            for i in range(3, 10):
                if i not in weeks:
                    weeksDemands.append([i, meanDemand])

            weeksDemands = sorted(weeksDemands, key=lambda x: x[0])

        oneFeat.append([i[1] for i in weeksDemands])
        features.append(oneFeat)
        # featWeeks.append([i[0] for i in weeksDemands]) # save memory

        # reset variable for next client/product
        rutas = [row.Ruta_SAK]
        canals = [row.Canal_ID]
        agencias = [firstRow.Agencia_ID]
        weeksDemands = [[row.Semana, row.Demanda_uni_equil]]
        oneFeat = [row.Agencia_ID, row.Canal_ID,
                   row.Ruta_SAK, row.Cliente_ID, row.Producto_ID]
        prevProd = row.Producto_ID
        prevCli = row.Cliente_ID

    if row.Index % 50000 == 0:
        print(
            str(round(row.Index / float(trainSort.shape[0]) * 100, 2)) + '% complete')

# finish up last data point
if len(set(rutas)) > 1:
    rutaMode = scsm.mode(rutas)[0][0]  # annoying indexing
    oneFeat[3] = rutaMode
    weeksDemandsDict = {}
    for w, d in weeksDemands:
        if w not in weeksDemandsDict.keys():
            weeksDemandsDict[w] = d
        else:
            weeksDemandsDict[w] += d

    weeksDemands = []
    for w in weeksDemandsDict.keys():
        weeksDemands.append([w, weeksDemandsDict[w]])

if len(set([w[0] for w in weeksDemands])) < 7:
    weeksDemandsDict = {}
    for w, d in weeksDemands:
        if w not in weeksDemandsDict.keys():
            weeksDemandsDict[w] = d
        else:
            weeksDemandsDict[w] += d

    weeksDemands = []
    for w in weeksDemandsDict.keys():
        weeksDemands.append([w, weeksDemandsDict[w]])

if len(weeksDemands) < 7:
    meanDemand = int(round(np.mean([i[1] for i in weeksDemands]), 0))
    weeks = [i[0] for i in weeksDemands]
    for i in range(3, 10):
        if i not in weeks:
            weeksDemands.append([i, meanDemand])

    weeksDemands = sorted(weeksDemands, key=lambda x: x[0])

oneFeat.append([i[1] for i in weeksDemands])
features.append(oneFeat)

timeTook = time.time() - startTime
timeTookS = round(timeTook % 60, 3)
timeTookM = int(timeTook / 60)

print('[INFO]: took ' + str(timeTookM) + ' m, ' + str(timeTookS) +
      ' s to create features ')

startTime = time.time()

print('[INFO]: saving features')

fullLen = len(features)
with open('../input/featuresList.ssv', 'wb') as f:
    for i in range(len(features)):
        f.write('; '.join([str(j) for j in features[i]]) + '\n')
        if i % 500000 == 0:
            print(str(int(round(float(i) / fullLen * 100, 0))) + '% done')

timeTook = time.time() - startTime
timeTookS = round(timeTook % 60, 3)
timeTookM = int(timeTook / 60)

print('took ' + str(timeTookM) + ' m, ' +
      str(timeTookS) + ' s to save features ')

{% endhighlight %}

This takes a *long* time.  When I ran it, it took at least 7 hours on my laptop.  Then, I accidentally overwrote the output file, by trying to open it like `open(raw_feats_file, 'rb')`.  It's a major bummer how one character can erase so much work ('wb' instead of 'rb', write binary and read binary).  The reason this runs so slow is because we're only using 1 CPU core and going through the data linearly.  Instead, we could use Hadoop, or Spark to speed things up a lot.  First, I used Hadoop MapReduce to take advantage of cluster computing.

#### Hadoop MapReduce



#### Apache Spark







![nano hole](/DIY_hydro_pump/nano_hole.jpg){: .center-image}
*<center>Drill 2 1/4" holes next to each other and bust through the middle part with the drill bit to make it a uniform-width hole.</center>*

![pg7 hole](/DIY_hydro_pump/pg7_hole.jpg){: .center-image}
*<center>Drill through the side with a 1/2" spade bit for the PG-7 cable gland.</center>*

![installed pg7](/DIY_hydro_pump/pg7_installed.jpg){: .center-image}
*<center>Install the PG-7.</center>*

#### Buck converter

In fact, this step is not necessary unless you are using a board that cannot accept 12V as an input.  Arduino Nanos can accept up to 20V as an input (7-12V recommended), so the buck isn't necessary for this.  But for MCUs like the NodeMCU or Particle Photon (which can only take 5V max in), the Buck is necessary.  Also, Bucks are typically 80-90%+ efficient, meaning you will use less power at 5V than at 12V for your Arduino.

Use a multimeter and tune the buck converter so it outputs 5V from a 12V input.  This way you will be sure the Buck will work before you install it.

![tune buck](/DIY_hydro_pump/tune_buck.jpg){: .center-image}
*<center>Turn the screw to tune the Buck output to 5V.</center>*

Solder female jumpers to the output of the Buck converter.  Solder two sets of black and red wires to the input of the Buck converter.  The female jumpers go to power the Arduino, the input wires are for 12V input power and power to the pump(s)

![solder buck](/DIY_hydro_pump/solder_buck.jpg){: .center-image}
*<center>Two sets of wires to the input, for 12V input power and power to the pump(s), and female jumpers on the output.</center>*

Now pass the black and red wires going to the Buck input through the cable gland, and solder on a female DC jack.  Drill holes (~1/8") using the Buck holes as guides.  Affix the Buck with 2 3x12mm nuts and bolts.

![affix buck](/DIY_hydro_pump/affix_buck.jpg){: .center-image}
*<center>Buck converter installed and Arduino holes are drilled.</center>*

#### Install Arduino
Put the Arduino in the case where it will go, and use the holes as templates for drilling (~1/16") holes in the case.  Then open the holes with a 5/64" bit.  Affix the Arduino with M2x16mm nuts (and optionally bolts--I found the nuts screw firmly into the Arduino holes).

![affix arduino](/DIY_hydro_pump/affix_arduino.jpg){: .center-image}
*<center>Arduino and Buck installed.  I only had some M2x12mm and M2x20mm bolts lying around, so I used those instead of M2x16mm.</center>*

#### Preparing the Pumps

You need to solder some DC jacks to the pump wires, and a diode.  The DC jacks usually have the outer part as negative, and the inner part as positive.  This makes sense for safety, because it's much easier to touch the outside of a DC jack than the inside.

![measuring DC jack](/DIY_hydro_pump/DC_measure.jpg){: .center-image}
*<center>Measuring the voltage on the DC jack.  Black is connected to the outside of the barrel, white to the inside.</center>*

![measuring DC jack closeup](/DIY_hydro_pump/DC_measure_close.jpg){: .center-image}
*<center>Closeup of measuring the voltage on the DC jack.  Black is connected to the outside of the barrel, white to the inside.</center>*

Solder a diode from the outside (negative) to the inside (positive) of the female DC jack for the pump.  The white band on the diode should be pointing to the positive terminal on the DC jack.  This will dissipate any current that flows in reverse when the power is suddenly shut off to the pump.

![solder DC jack](/DIY_hydro_pump/pump_DC_soldered.jpg){: .center-image}
*<center>A diode (pointing from negative to positive, i.e. the white band nearest the positive connection).</center>*

Before you do this next step, make sure you have the wires already passed through the DC jack cover so you can slide it over the jack when you finish soldering.  Solder the red wire from the pump to the inside of the jack, and the black wire to the outside.

![solder DC jack](/DIY_hydro_pump/pump_DC_jack.jpg){: .center-image}
*<center>Female DC jack to pump fully soldered.</center>*

#### Transistors

The 2N5551 transistor is NPN, meaning we need to put a positive bias on the Base (relative to the Emitter) to turn on the pathway from the collector to the Emitter.  

![transistor diagram](/DIY_hydro_pump/transistor_diagram.png){: .center-image}
*<center>PNP and NPN transistor diagrams.</center>*

![2N5551 transistor diagram](/DIY_hydro_pump/2N5551.png){: .center-image}
*<center>The 2N5551 transistor.</center>*

The exact amount of resistance needed depends on the input voltage to the Base, the transistor itself, and the current used by the pump.  Solder a 33 Ohm resistor from the left to the middle of the transistor (Emitter to Base).  This will make sure the transistor is normally off.

![transistor setup diagram](/DIY_hydro_pump/transistor_setup.png){: .center-image}
*<center>How to make the connections on the transistor.</center>*

Solder a 330 Ohm resistor from the center leg (Base) to a female jumper.  This will go to the Arduino for control.

Finally, solder the rightmost leg to a black wire, which will connect to the outside of a DC barrel jack for connecting to a pump.

![transistor soldered](/DIY_hydro_pump/transistor_soldered.png){: .center-image}
*<center>The transistor fully soldered with the resistors and female jumper.</center>*

I then put some bigger heatshrink around the top of the transistors, to protect any exposed metal from accidental connections.

#### DC Jacks to the Pumps

Solder the black wire from the transistor Collector leg to the outside terminal of a male DC barrel jack for the pump.  Solder a red wire to the inner terminal of the male DC jack, and the other end to the positive input from the buck converter.

![DC jack to pump](/DIY_hydro_pump/DC_jack_to_pump.png){: .center-image}
*<center>Arduino and Buck installed.</center>*

Solder the red wire from this DC jack to the red input power wire (coming from the Buck converter), and cover with in heatshrink.

#### Program the Arduino

I installed multiple pumps and transistors, and so checked which one was which by plugging in the female jumpers to the 5V pin on the nano and finding which DC jack it triggered.  I then arranged the DC jacks from short to long, connecting the shortest DC jack to the D2 pin on the Nano, next shortest to D3, etc.

![finished product](/DIY_hydro_pump/finished.png){: .center-image}
*<center>The finished product, with the DC jacks arranged from shortest (D2 pin) to longest (D5 pin).</center>*

{% highlight c++ %}

char* names[] = {"mini Tomato", "blueberry"};
int pins[] = {2, 3};
unsigned long timeOn[] = {20000, 0}; // time that the pump stays on for each 10000 = 10s pin in order
unsigned long timeOff[] = {3600000, 0}; // time pump stays off -- 3600000 = 1 hr
unsigned long startTime[] = {0, 0}; // time pump was turned on, in ms
bool pumpOn[] = {true, false};
bool disable[] = {false, true};

int pumps = sizeof(pins)/sizeof(int);

void setup() {
  Serial.begin(9600);
  Serial.println(pumps);
  initializePumps();
  //testPumps();
  digitalWrite(pins[0], 1);
  Serial.println("pump on");
  startTime[0] = millis();
  pumpOn[0] = true;
}

void loop() {
  for (int i = 0; i<pumps; i++) {
    if (disable[i]) {continue;};
    if (pumpOn[i])
    {
      if (millis() - startTime[i] > timeOn[i])
      {
        Serial.println("pump off");
        digitalWrite(pins[i], 0);
        startTime[i] = millis();
        pumpOn[i] = false;
      }
    }
    else if (not pumpOn[i])
    {
      if (millis() - startTime[i] > timeOff[i])
      {
        digitalWrite(pins[i], 1);
        Serial.println("pump on");
        startTime[i] = millis();
        pumpOn[i] = true;
      }
    }
  }
}

void testPumps() {
    for (int i = 0; i<pumps; i++) {
    Serial.print("testing: ");
    Serial.println(names[i]);
    digitalWrite(pins[i], 1);
    delay(1000);
    digitalWrite(pins[i], 0);
    delay(1000);
  }
}

void initializePumps() {
  for (int i = 0; i<pumps; i++) {
    pinMode(pins[i], OUTPUT); // make sure out pin is set to 'off'
    //i.e. pulls pin to ground
  }
}

{% endhighlight %}

The `testPumps()` function will go through each of the pumps and turn it on for a second, printing the name of the assigned pin in the terminal.  Press ctrl+shift+M to access the terminal from the Arduino IDE (or Tools->Serial Monitor).
