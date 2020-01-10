---
layout: post
title: Regis Randsomware
description: A recount of how Regis probably got hacked and was ransomwared.
modified: 2019-09-30
tags:
  - cybersecurity
image:
  feature: "shutdown.jpg"
  credit: Me
  creditlink: ngeorge.us
published: false
---

### Summary
Regis University was recently cyberattacked with ransomware.  The experience, my time at Regis, and the cybersecurity stories I've been listening to lately (from the darknet diaries podcast, but also from a cybersecurity conference) have helped me to understand how this probably happened.  Although Regis will probably never actually figure out how it happened due to a lack of expertise and lack of ability to pay for a top-tier cybersecurity firm to investigate the situation,  I'll do my best to summarize what I think happened, why the attack succeeded, and why Regis and many other universities will almost certainly be hacked again.

### Background



### Bonus appendix: password strength

Password strength meters are [known](https://nakedsecurity.sophos.com/2015/03/02/why-you-cant-trust-password-strength-meters/) to be flawed, and Microsoft's password meter seems about average.  With the growth of cloud-based MS Office products, OneDrive, and other cloud services, this does not inspire confidence in MS's security capabilities.

Let's look at how Microsoft's password strength meter works (at least the one we are using from MS's cloud solutions).  Take for example, the password "microsoft is incompetent with security".  Microsoft rates this password as "weak", while most other password evaluators rank it as strong.  Here is a screenshot of Microsoft's password evaluator:

![MS password evaluator](images/ms_password_evaluator.png)

Meanwhile, other password evaluators, such as [rumkin.com](http://www.rumkin.com), LastPass, and My1Pass all show a very strong password:


![My1Pass](images/my1pass.png)

![rumkin](images/rumkin.png)


Of course, these sorts of things always remind me of the related [xkcd comic](https://xkcd.com/936/):

![xkcd password](images/password_strength.png)

What's worse than the fact that Microsoft under-rates strong passwords just because they are all lowercase is that Microsoft *overrates* weak passwords because they have a mix of digits and cased letters.  For example, the password "Abc123!!" is rated strong by Microsoft, but weak by almost everyone else:

![MS password](images/ms_pw.png)

![weak password](images/weak_pw.png)

![weak password rumkin](images/weak_pw_rumkin.png)

Only one site I checked ranked it as strong (http://www.passwordmeter.com/).  However, based on the small number of characters (8), this password should be considered weak no matter what in my opinion.

Let's see how long it would take to crack this "Abc123!!" password with [John The Ripper](https://www.openwall.com/john/), a well-known password cracking tool.
