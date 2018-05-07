---
title: AV EcoSystem Review Email Failings
date: 2017-09-07
tags: 
author: Sam Joseph
---

So I think the main bleed in the AV ecosystem may now be some of the problems we are having with sending emails and them ending up in spam filters.  Hold that thought for a moment and I'll just do my node/mocha/chai [athletic rep for the morning](https://gist.github.com/tansaku/718547c89be1ef216dda4e973ea67075)

I guess I can afford to do that for a few mornings in a row and start exploring where I can speed up ..., but back to our email issues.  So we sent out 10 emails with a special offer (from support@agileventures.org) last week and as far as I can tell, every single one ended up in spam.  Annoyingly I have to go to slack to grab some info about it, but there's not too much to distract me there today, phew.  So one person who got the email in their spam filter reported that they saw this message from Google:

![](../images/Screenshot_2017-09-05-16-59-32.png)

The message is "Why is this message in Spam? It is in violation of Google's recommended email sender guidelines.".  This week we had a new Premium Mob member sign up and so far none of the emails from me via sam@agileventures.org appears to have arrived with them.  I also get warnings of authentication failures from hotmail for several emails I've sent over the last 6 months.  We need to get to the bottom of this.  Can I replicate the failure above (from gmail) and get more info on what's failing ...

So I re-send one of the free trial emails and I find it in my gmail spam folder.  I thought I sent a test of this email and it got through to my sam@neurogrid.com account.  It did.  Anyway, I can now click through to "learn more" on the email to see why the message got marked as spam.  It links to https://support.google.com/mail/answer/81126?hl=en#authentication

```
Why is it important to authenticate your messages?

Authentication ensures that your messages can be correctly classified. Emails that lack authentication are likely to be rejected or placed in the spam folder, given the high likelihood that they are forged messages used for phishing scams.

In addition, unauthenticated emails with attachments may be outrightly rejected, for security reasons.

To ensure that Gmail can identify you:

* Use a consistent IP address to send bulk mail.
* Keep valid reverse DNS records for the IP address(es) from which you send mail, pointing to your domain.
* Use the same address in the 'From:' header on every bulk mail you send.

We also recommend the following:

* Sign messages with DKIM. We do not authenticate messages signed with keys using fewer than 1024 bits.
* Publish an SPF record.
* Publish a DMARC policy.
````

Now one thing I did do with these 10 message was adjust the From address to be `support@agileventures.org` which Thunderbird allows me to do, but maybe that triggered Gmail's spam filter.  That said, I've had problems just sending from sam@agileventures.org as well, so can that be the whole story? ...

The hotmail failures I have had are like the following:

```
Feedback-Type: auth-failure
User-Agent: XMR/2.2
Version: 1.0
Original-Mail-From: <sam@agileventures.org>
Arrival-Date: Fri, 23 Jun 2017 10:02:07 -0700
Message-ID: <b6a7803d-c6ff-16cd-ed5f-e37569ef4ba5@agileventures.org>
Authentication-Results: hotmail.com; spf=softfail (sender IP is 217.70.183.196; identity alignment result is pass and alignment mode is relaxed) smtp.mailfrom=sam@agileventures.org; dkim=none (identity alignment result is pass and alignment mode is relaxed) header.d=agileventures.org; x-hmca=fail header.id=sam@agileventures.org
Source-IP: 217.70.183.196
Auth-Failure: spf
Reported-Domain: agileventures.org
DKIM-Domain: agileventures.org
```

So I've sent off an email to Gandi:

```
Hi Gandi,

We've been having trouble sending email from our domain agileventures.org, specifically from sam@agileventures.org which is a gandi mail account.  We are getting reports from hotmail (see below).  These are not bulk or spam email, but messages being sent to individual members of our charity from my Thunderbird email client.

I also note that I cannot send email from sam@agileventures.org from my iPhone, although I can receive.

Are there settings on Gandi's site, or even mail settings that we control that could be used to address these issues to ensure that email gets through?

Many thanks in advance

Best, Sam
```

and I guess I will take another pass on this tomorrow, bring it up in the marketing meeting etc.  The funny thing is that hotmail failure messages have often been accompanied by delivery of the message as evidenced by sequences of replies and responses that I'm having with the people emailed ...
