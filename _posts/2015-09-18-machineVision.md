---
layout: post
title: Python machine vision
description: "Using machine vision to get data from an LCD."
modified: 2015-09-18
tags: [cameras, machine vision, pythohn, McGuyver]
image:
  feature: MV.jpg
  credit: me
  creditlink: http://wordsforthewise.github.io
---

### Python machine vision LCD OCR WTF

I just finished getting a camera through a <a href="http://wordsforthewise.github.io/easyCAP">usb adapter visible in python</a>, and now I'm getting the machine vision set up.  I found someone who <a href="">already did all the hard stuff</a>.  I used this <a href="http://dangerousprototypes.com/docs/File:Lcd-numbers.jpg">LCD numbers image</a> since it had the same font as the display on the Dylos air quality monitor.  I had to <a href="persp.png">adjust the perspective</a> to make it flat for the machine vision training though, so I used gimp to do that.  Then I realized there was a 0 with a line under it, so I <a href="bad0.png">replaced that with another 0 from lines below</a>.  After that, I started training the program, but it was <a href="4-1.png">seeing 4s and 1s</a>.  I had to change the threshhold for detection from 
{% highlight python %}
thresh = cv2.adaptiveThreshold(blur,255,1,1,11,2)
{% endhighlight %}
to this
{% highlight python %}
thresh = cv2.adaptiveThreshold(blur,255,0,1,25,-2)
{% endhighlight %}
The <a href="http://docs.opencv.org/modules/imgproc/doc/miscellaneous_transformations.html">docs</a> describe this function a bit, although I can't get the cv2.CV_ADAPTIVE_THRESH_MEAN_C to print, so I'm not entirely sure which adaptive_method I'm using (I think I changed it from gaussian, 1, to linear, 0).  I empirically found the higher block size and lower constant seem to work best for this image.  After that, it <a href="images/yay.png">works well</a>.