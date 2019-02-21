---
layout: post
title: TensorFlow functions with Keras
description: Using TensorFlow function in combination with Keras models
modified: 2019-04-06
tags:
  - machine learning
  - sentiment analysis
  - data science
image:
  feature: "keras-tensorflow-logo.jpg"
  credit: Keras Blog
  creditlink: https://blog.keras.io/keras-as-a-simplified-interface-to-tensorflow-tutorial.html#using-keras-models-with-tensorflow
  published: true
---

### Summary
Recently, I was trying to use Cohen's Kappa as a metric with Keras.  I decided I would use the [TensorFlow contrib](https://www.tensorflow.org/api_docs/python/tf/contrib/metrics/cohen_kappa) function that already existed.  While trying to get TensorFlow working with Keras, I discovered there were no easily-findable documents describing how to do this.  The example from Keras' blog is a few years old, and wasn't working anymore.  So after figuring out how to get TensorFlow working with Keras, I decided to document it (for the children).

Why use TensorFlow with Keras?  TF, particularly the contrib portion, has many functions that are not available within Keras' backend.  Ideally you'd want to use Keras' backend for things like TF functions, but for creating custom loss functions, metrics, or other custom code, it can be nice to use TF's codebase.
<!--more-->

### The usual route
Usually you want to use Keras' backend to implement custom functions.  For example:

```python
import keras.backend as K

def customLoss(yTrue,yPred):
    return K.sum(K.log(yTrue) - K.log(yPred))
```
(borrowed from [here](https://stackoverflow.com/a/43821374/4549682))

However, the backend will be missing many functions from TensorFlow, like much of the contrib section.  Functions are also named differently.

As a solution, you can use TensorFlow with Keras in the following manner:

```python
import keras
import tensorflow as tf
from keras import backend as K

# build model...(not shown)

# custom metric with TF
def cohens_kappa(y_true, y_pred):
    y_true_classes = tf.argmax(y_true, 1)
    y_pred_classes = tf.argmax(y_pred, 1)
    return tf.contrib.metrics.cohen_kappa(y_true_classes, y_pred_classes, 10)[1]

# compile model...
model.compile(loss=keras.losses.categorical_crossentropy,
              optimizer=keras.optimizers.Adadelta(),
              metrics=['accuracy', cohens_kappa, tf_accuracy])

# IMPORTANT: init TF variables (only needed for some functions/implementations)
K.get_session().run(tf.local_variables_initializer())

# fit model
model.fit(x_train, y_train,
          batch_size=batch_size,
          epochs=epochs,
          verbose=1,
          validation_data=(x_test, y_test))
```

All the examples I could find on the web (which weren't many) used the `tf.global_variables_initializer()` function.  It turns out this no longer works (it must've worked in 2016 when Keras made their [blog post on the subject](https://blog.keras.io/keras-as-a-simplified-interface-to-tensorflow-tutorial.html#using-keras-models-with-tensorflow)).  Now it seems it should be `tf.local_variables_initializer()`.  This can be run with Keras' backend, or with TF directly.  With Keras' backend, it looks like it does in the code sample above (`K.get_session()` gets the TF session).  Initializing the variables with TF directly would look more like:

```python
sess = tf.Session()
sess.run(tf.local_variables_initializer())
```

There are a few examples in a Github repo I created [here](https://github.com/nateGeorge/tensorflow_with_keras).

#### One other small thing
The other small thing I learned through this is that to get the inverse of a one-hot encoded vector, all you have to use is argmax.

In the end, running TF with Keras turned out to be pretty simple.  All that needs to be done is to initialiaze the variables with `tf.local_variables_initializer()` after creating any code with TF functions, and before running `model.fit()`.  Again, this is only for certain TF functions that are creating/using TF variables within their source code.  Some other TF functions don't create any variables, so don't require the initialization.

Good luck with your neural nets, and praise be to our future machine overlords.

#### Version details
Some small details: the TensorFlow version used was 1.9.0 (GPU), and Keras was 2.2.4.  This was on an Ubuntu 18.0.4 LTS OS with Anaconda Python 3.6.8.
