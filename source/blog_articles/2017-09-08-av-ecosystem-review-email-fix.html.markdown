---
title: AV EcoSystem Review Email Fix
date: 2017-09-08
tags: 
author: Sam Joseph
---

![fix(../images/fix.png)

So, following my blog and email of yesterday I got the following response from Gandi:

> Normally, it is the recipient's email server rules that play to deliver the message to the recipient's box.

> To decrease the chance to be considered as spam by the recipient mail server, you can add the SPF record in your zone file.

> To do this, you can refer to the instructions page here:

> https://doc.gandi.net/en/dns/zone

> Please delete the current SPF record below :

```
@ 10799 IN TXT "v=spf1 a mx include:_spf.elasticemail.com ~all"
```

> The SPF records to be added is as below (please use edit mode : EXPERT):

```
@ 10800 IN TXT "v=spf1 include:_mailcust.gandi.net ?all"
```

> Above records indicate when mail is sent from the email server Gandi, normally, it is not spam.

> Note that changes to your zone file will take around 3 hours to propagate on the Internet. Please wait for this propagation time before doing test.

> For more information about the SPF record, please see the online documentation:

> http://wiki.gandi.net/en/dns/zone/spf-record

and I had the following from Federico

> This is the step-by-step video tutorial that Zoho Mail provides: https://docs.zoho.com/embed/28z4w99ee43ded3f14b3182bf0041c35042b7

> Ok. That one was purely SPF. This one is for DKIM _(3-min):_ https://docs.zoho.com/embed/281y6174f134d5f244817a38c7d32e4cd4c41

> If you’d rather scan text (DKIM): https://www.zoho.com/mail/help/adminconsole/dkim-configuration.html
SPF: https://www.zoho.com/mail/help/adminconsole/spf-configuration.html

So there's a lot to review here, and I'm going to calm myself with an [athletic node test rep](https://gist.github.com/tansaku/8e2ee25a007d9b706390513d53bbe191).  Ah, calming and I think 3 minutes to complete the rep.  So what's the deal with this email?

Gandi is asking me to delete the spf1 record for elasticemail. Now this might have a negative impact on our newsletter sending but I guess I should just go ahead since we won't be sending a newsletter for a while ... I scanned the text information on SPF and DKIM from Federico.  Not sure if we also need a DKIM but in the first instance I'll do what Gandi instructs, removing:

```
@ 10800 IN TXT "v=spf1 a mx include:_spf.elasticemail.com ~all"
```

and replacing it with 

```
@ 10800 IN TXT "v=spf1 include:_mailcust.gandi.net ?all"
```

so this will take up to 3 hours to propagate, and I should try a test of email sending after that.  Ah delayed feedback debugging, how I loathe thee :-)  And in the meantime I could try and follow Gandi's other instructions to try and fix sending from sam@agileventures.org from my iPhone ... and that's fixed now too, yay!
