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
Only a few days before the Fall semester was to start in August 2019, Regis University was suddenly attacked with ransomware.  IT panicked, and unplugged *everything*.  The wireless internet was down campus-wide.  Email was out.  The website was down, phones were off, and even parking payment stations were shut off.  And new Freshmen were *on there way* to campus to move in the next day.  It was a total cyber disaster.

In this post, I'll describe why and probably how the attack happened.  I'll talk about why Regis and many other universities will almost certainly be hacked again.  I'll also provide suggestions for easy precautions organizations can take to prevent ransomware attacks.

<!--more-->

### Background

Everything was quiet on the cyber-front, but secretly lurking were some hackers that had gained access to the entirety of Regis University's systems.  They had access to *everything* -- student data, courses, practically every computer on campus -- but had waited until the optimal time to attack.  That time was a few days before the new Fall semester.  Fall semester is the busiest time for universities, because new Freshmen are moving in, faculty and students are getting back from summer break, and the new school year is about to begin.  Then, suddenly in the middle of the night on a Wednesday, the ransomware software was activated.  The next morning at work, someone probably received a popup similar to this wannacry popup:

![wannacry popup](wannacry.jpg)
Source: https://www.crowdstrike.com/epp-101/what-is-ransomware/

However, the amount demanded was certainly much higher (probably in the 100s of thousands of dollars).  IT, of course, panicked.  Everything was unplugged.  I mean *everything*.  Internet was out campus-wide.  The website was down.  The phones were down.  You couldn't even use the printers.  Regis University was unreachable, completely offline, and new Freshmen were finishing up getting packed and ready to move into the dorms *the next day*.  This is what ground zero at a cyber-disaster looks like.

# Timing
The hackers had planned to install ransomware on Regis' systems, but had strategically waited until this crucial time to enact the ransomware in order to pressure Regis into paying up.  Unfortunately for these not-so-well-educated hackers, they didn't know that the overwhelming majority of US organizations [do not pay the ransom](https://www.malwarebytes.com/pdf/white-papers/UnderstandingTheDepthOfRansomwareIntheUS.pdf) for ransomware.  They should've looked at the data and paid those soft Canadians instead.  Who knows, maybe Canadian companies even apologize to the ransomers during the process.  Actually, from what I've read, it can be cheaper for an organization to pay the ransom to get on with business.  But then you are encouraging and funding criminals, which is not good.  Obviously, the best solution is to have good security to start with and avoid ransomware in the first place.

# Communication
So the timing of the attack was designed to get Regis to pay, but Regis didn't pay...probably.


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
