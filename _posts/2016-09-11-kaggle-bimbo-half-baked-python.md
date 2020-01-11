---
layout: post
title: Doing data science--Prepping data for LSTM AI Neural Networks
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
  feature: "bimbo_logo.png"
  credit: kaggle
  creditlink: ngeorge.us
published: false
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
