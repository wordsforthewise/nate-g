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
published: true
---

### Summary
Only a few days before the Fall semester was to start in August 2019, Regis University was suddenly attacked with ransomware.  IT panicked, and unplugged *everything*.  The wireless internet was down campus-wide.  Email was out.  The website was down, phones were off, and even parking payment stations were shut off.  And new Freshmen were *on their way* to campus to move in the next day.  It was a total cyber disaster.

Here, I'll describe why and how the attack probably happened.  I'll talk about why Regis and many other universities will almost certainly be hacked again.  I'll also provide suggestions for easy precautions organizations can take to prevent ransomware attacks.

<!--more-->

### Background

Everything was quiet on the cyber-front, but secretly lurking were some hackers that had gained access to the entirety of Regis University's systems.  They had access to *everything* -- student data, courses, practically every computer on campus -- but had waited until the optimal time to attack.  That time was a few days before the new Fall semester.  Fall semester is the busiest time for universities, because new Freshmen are moving in, faculty and students are getting back from summer break, and the new school year is about to begin.  Then, suddenly in the middle of the night on a Wednesday, the ransomware software was activated.  The next morning at work, someone probably received a popup similar to this wannacry popup:

![wannacry popup](wannacry.jpg)
Source: https://www.crowdstrike.com/epp-101/what-is-ransomware/

However, the amount demanded was certainly much higher (probably in the 100s of thousands of dollars).  IT, of course, panicked.  Everything was unplugged.  I mean *everything*.  Internet was out campus-wide.  The website was down.  The phones were down.  You couldn't even use the printers.  Regis University was unreachable, completely offline, and new Freshmen were finishing up getting packed and ready to move into the dorms *the next day*.  This is what ground zero at a cyber-disaster looks like.

### Timing

The hackers had planned to install ransomware on Regis' systems, but had strategically waited until this crucial time to enable the ransomware in order to pressure Regis into paying up.  Unfortunately for these not-so-well-educated hackers, they didn't know that the overwhelming majority of US organizations [do not pay the ransom](https://www.malwarebytes.com/pdf/white-papers/UnderstandingTheDepthOfRansomwareIntheUS.pdf) for ransomware.

![ransom paid bar chart](ransom_paid_stats.png)
Source: https://www.malwarebytes.com/pdf/white-papers/UnderstandingTheDepthOfRansomwareIntheUS.pdf

They should've looked at the data and ransomed those softy Canadians instead.  Who knows, maybe Canadian companies would even apologize to the ransomers during the process.  Actually, from what I've read, it can be cheaper for an organization to pay the ransom to get on with business.  But then you are encouraging and funding criminals.  Also, sometimes the decryption software reportedly doesn't work, meaning you paid the ransom but you didn't get your files back.  Obviously, the best solution is to have good cybersecurity to start with and avoid ransomware in the first place.

### Communication

So the timing of the attack was designed to get Regis to pay, but Regis didn't pay...probably.  Regis' management has been extremely tight-lipped about the whole situation, not sharing any details and never even confirming it was a ransomware attack.   However, it was almost certainly a ransomware attack.  Files on some computers were encrypted, and from other interactions with people I'm certain it was a ransomware attack.  The College of Computer Information and Sciences (CCIS) cybersecurity programs claim to be planning on using [information from the cyberattack for teaching](https://www.9news.com/article/news/local/next/regis-cyberattack-lesson/73-524cce47-efb6-42d6-97fb-a4931d0cc965), but I doubt they will ever have any useful information to utilize.  The IT department and CCIS are extremely siloed, and the IT department doesn't seem to want to share any information.  The only thing that seems to be a 'teaching point' is business continuity, or having a contingency plan to keep operations going when normal IT systems go down.  However, that isn't cybersecurity -- it's business and operations.  

### Remediation

The response by Regis' IT department was not the best.  Their solution was to replace every single computer on campus with a new computer with a solid-state drive, and rebuild *every single* IT system.  Four months later, several IT systems are still not operational.  Of course, this sort of purge is extremely expensive and time consuming with around 3000 computers to replace and dozens of systems to bring back online.  Many people went without computers for months, and the entire process took at least 3 months.  While this does guarantee hackers are no longer on the new computers, perhaps they stuck around in something else like a printer, or maybe someone didn't surrender their computer like they were supposed to which may still have malware on it.  Or, maybe someone is reusing a password, or gets phished again.  If the hackers still have way into the systems at all, the entire rebuild of systems was a complete waste of resources.

### How the attack probably happened: phishing

In the few years leading up to the attacks, we would receive phishing emails around once a week or more.  Phishing is a type of social engineering where an email is sent which asks for login credentials, contains a malware-laden attachment, or redirects the user to a malware link.  Most of the attempts were garbage, like this one:

```
Due to a recent email scam that has been going around Regis University, Most Student Self Service accounts have been compromised.

Your account access has been indefinitely revoked. Log on to Regis University Self Service to have your account verified and reactivated now.

[Click here](https://help--desk-updates.weebly.com/) to log on

Thank you,

Regis University Help Desk.
```

It's an obvious phishing attempt with classic signs:

- poor grammar
- strange department/site that doesn't exist ("Self Service")
- weird link
- asking for username/password
- invoking fear and pressuring the receiver to act quickly
- strange 'from' email address

Some of the attempts were apparently very good.  I didn't receive them, but heard about one which claimed to be the provost, and referenced some actual events on campus.  It asked the user to click on a link to review documents related to some projects going on with faculty.  The phisher must've had access to someone's email and actually read through it to see what was going on at Regis.  Then the phisher carefully crafted an email which looked legitimate, except it had a malicious link.

#### How the hackers got in

I'm highly confident hackers got in to Regis' systems via phishing.  There are about 1000 employees on campus, and with the amount of phishing emails we got weekly, it was a matter of time before someone accidentally clicked on a bad link, or even entered their password on a phony site.  The average age at Regis is not too young either, and I'd guess older folks are more susceptible to phishing.

However, hackers could've also gotten in easily without phishing.  Logins to all Regis systems (including sensitive student records, an all-access VPN, and email) were, and still are, only secured by a single password for each user.  There was no multi-factor authentication, and still isn't.  Worse, the password requirements were very weak.  I think the minimum password was 8 characters, which could be brute-forced with a botnet or GPU cluster with ease.  If even one of the Regis systems allowed unlimited login attempts, a bot could easily be set up to randomly guess passwords until the login succeeded.

Another option would be password reuse.  The sites [dehashed](https://www.dehashed.com/) and other [alternatives](https://www.saashub.com/dehashed-alternatives) allow people to get passwords for emails.  Some big hacks, like the [Xbox Underground](https://darknetdiaries.com/episode/45/) saga, took advantage of password reuse to gain access to systems.

#### How the hackers moved laterally

Once the hackers were into the system (probably via the VPN), some sort of script was added to the scripts that ran when people logged in to their Windows machines.  This was in part how the hackers moved laterally through systems, although with the total lack of information from ITS and upper management, the whole story will probably never be known.

#### Who the hackers were

Again, the lack of information prevents us from knowing who the hackers exactly were.  However, I heard rumors they were from Singapore.  Obviously they have not been brought to justice, and probably never will be.

### How to prevent cyberattacks

With some basic cybersecurity, organizations can harden their systems considerably against hackers.  Here are some basic principles that can be used:

#### Require multi-factor authentication (MFA)

MFA is becoming pretty standard across most login systems these days, and can be done with several devices.  This includes apps on phones like Google Authenticator, devices like [Yubikeys](https://www.yubico.com/) and [hardware/security tokens](https://en.wikipedia.org/wiki/Security_token), and SMS MFA.  For starters, at least 1 form of MFA can be required, but for sensitive data, 2 or more MFA protocols can be required.  As far as I know, the only ways to hack through MFA is either SIM-swapping or social engineering over the phone.  The social engineering approach works by calling the person who is being hacked, pretending to be IT support, and asking for the MFA code (e.g. if their password is already compromised and hacker only needs the MFA code to login).

#### Require strong passwords that change periodically and consider a password manager

Passwords should be fairly long (16+ characters in my opinion) and complex.  This can be done with password managers such as LastPass, which make it easy to generate complex passwords and save them.  Of course the LastPass or password manager account needs to be protected with a strong password and MFA as well.  A password manager should be recommended, because it allows one to create complex passwords that are saved with a software.  It does open up an attack vector (the password manager's systems may be flawed), but to me this seems better than writing down dozens or hundreds of complex login passwords.  It's definitely better than reusing passwords for multiple accounts.

Another key point for passwords is to require them to be changed periodically.  Some universities and organizations require password changes every 3 to 6 months which is a good idea.  Old passwords can be compromised by phishing or brute force.

One problem with creating a strong password is that password strength meters are [known](https://nakedsecurity.sophos.com/2015/03/02/why-you-cant-trust-password-strength-meters/) to be flawed.  Regis is now using a Microsoft system for password management, and Microsoft's password meter seems about average.  With the growth of cloud-based MS Office products, OneDrive, and other cloud services, this does not inspire confidence in MS's security capabilities.  Essentially, MS's password strength meter underrates strong passwords and overrates weak ones.  The password strength appendix at the end delves deeper into this.

#### Lock accounts after frequent login attempts

It shouldn't take more than a few attempts to login to an account.  Several attempts, especially in rapid succession, are probably a hacker trying to get in.  After 3 or 5 failed login attempts, accounts should probably be locked and require some sort of other authentication to get back in.  This could be a combination of a MFA code, video call, or something that would be hard for a hacker to social engineer their way through.

#### Train employees to recognize and report phishing emails and social engineering attempts

At Regis there has never been any phishing training, even though it would be very easy to do.  Phishing training is standard at many large companies, like Apple and Workday.  And it can work: for example, Coinbase [prevented a nasty phishing attack](https://cointelegraph.com/news/coinbase-says-it-prevented-a-crafty-phishing-attack-to-exfiltrate-keys), likely in part due to employee training.


#### Basic cybersecurity hygiene

Lock computers if leaving the room.  Don't allow just any device to plug into ethernet ports.  Keep software up-to-date.  Before the attack, several computers were running older versions of Windows 7.  After the attack all computers were upgraded to Windows 10.  Personally, I use Linux, because most malware is targeted at Windows.  One other part is to avoid VPN access to critical networks if possible.  Before the attack, Regis allowed VPN access to campus ethernet with a single weak password.  There are certainly many [more facets to cyber hygiene](https://cybersecurityforum.com/cybersecurity-faq/what-is-cyber-hygiene.html), but these are a few.

#### Hire a penetration testing team

Penetration testers (pen testers) are people who are hired to test the cyber and physical security of an organization.  They are expensive to hire, but are well worth it in the long run.  It's important to hire someone or a team who actually creates a thorough report and educates the IT team on improvements they can make.

#### Have a contingency plan and test it

After the attack at Regis, it was a circus.  We ended up using Gmail, Slack, and person cellphones to communicate, but this took days or weeks to get set up.  We didn't have a good contingency plan, and didn't have good central repositories of alternate contact information for employees and students.  Having alternate contact information (like phones and emails) for everyone involved is crucial, so that communications can continue to flow.  There should be one day a year, at a non-critical time, where the plan is tested -- e.g. email is shut down and alternative communications channels are tested.  You don't want to be testing the plan for the first time if the emergency actually happens (just like a fire drill).

### Effects of the attack

The effects of the attack have been widespread.  Not only has it hurt Regis financially by introducing a massive IT cost to replace and service computers and loss of tuition revenue, but the emotional cost and effect on employee psychology has been longstanding and insidious.  For the first week or two where there was no internet on the entire campus, many people ended up working from home.  It was nice to have an extension to the summer break at first.  But then all sorts of tiring pains crept in.  Standard electronic forms (like registration forms) which were easy to fill out online in minutes became hard copies that had to be handwritten and physically walked across campus to various people and buildings.  Communication systems were sporadically created over weeks.  People didn't have computers for months, and simply sat on their hands (especially hourly-paid employees).  Communication regarding IT restoration timelines and priorities was not clear and ever-changing.  These pains compounded, and employees were feeling down and tired, especially before the Christmas break.  Negative psychological effects could have longstanding implications for the university in terms of employee quality.

### Why this attack will happen again at other universities and possibly Regis

Since the attack, phishing emails have decreased substantially.  It's not clear if stronger email filters were applied, or if most of the phishes were from the same hackers that ransomed Regis.  But email phishing is only one vector of attack -- hackers could brute force to get passwords, phish people over the phone, or even bribe someone.  For now, Regis seems safe, but the chances are high a similar attack will happen again.  The reason being cyber hygiene at Regis is still somewhat weak, especially in the MFA and password areas.  It will certainly happen at other universities, because there must be some out there in a similar situation to Regis before the attack -- lots of phishing emails and weak cyber hygiene.  This doesn't have to be this way though.  With a little investment in a cybersecurity professional, phishing training, and good cyber hygiene, organizations can prevent ransomware attacks like what happened at Regis.  Unfortunately for us, the current mentality is extremely short-term, and much of any investment in cybersecurity will probably be shot down at most places due to the cost.  This means ransomware attacks will continue to happen, and organizations will respond to them after the fact instead of preparing ahead of time.  But if you're reading this and have the power to effect change at you organization, it doesn't have to be this way.  You can push for improvements to cybersecurity, and if you're in a high enough position, maybe even dictate positive change.


#### Appendix: Password strength meters

Let's look at how Microsoft's password strength meter works (at least the one Regis is using from MS's cloud solutions).  Take for example, the password "microsoft is incompetent with security".  Microsoft rates this password as "weak", while most other password evaluators rank it as strong.  Here is a screenshot of Microsoft's password evaluator:

![MS password evaluator](images/ms_password_evaluator.png)

Meanwhile, other password evaluators, such as [rumkin.com](http://www.rumkin.com), LastPass, and My1Pass all show a very strong password:

![My1Pass](images/my1pass.png)

![rumkin](images/rumkin.png)

Of course, these sorts of things always remind me of the related [xkcd comic](https://xkcd.com/936/):

![xkcd password](images/password_strength.png)

What's worse than the fact that Microsoft under-rates strong passwords just because they are all lowercase is that Microsoft *overrates* weak passwords because they have a mix of digits and cased letters.  For example, the password "Abc123!!" is rated strong by Microsoft, but weak by almost everyone else:

Microsoft:
![MS password](images/ms_pw.png)

My1Pass:
![weak password](images/weak_pw.png)

Rumkin:
![weak password rumkin](images/weak_pw_rumkin.png)

Only one site I checked ranked it as strong (http://www.passwordmeter.com/).  However, based on the small number of characters (8), this password should be considered weak no matter what in my opinion.

If I had more time, I'd see how long it would take to crack this "Abc123!!" password with [John The Ripper](https://www.openwall.com/john/), a well-known password cracking tool.
