---
title: Saddle Sore
date: 2017-06-06
tags: meetings, scrum, SSL, MOOC, certbot, letsencrypt, communication, emotions, thoughts, team
author: Sam Joseph
---

![saddle sore](/images/saddle-sore.jpg)

Yesterday it was back in the saddle after a break and I'm feeling a little saddle sore already :-) Fairly productive first day I guess - a lot of meetings and a few critical admin tasks taken care of.  Let's look back at those meetings:

* Noon Martin Fowler scrum - caught up with Matt and Lokesh, and talked through Lokesh's PR on LocalSupport
* 1pm  Marketing meeting - caught up with Michael, Lara, Federico, Marufa, Blandine and Matt - managed to finish up in 45 mins
* 4:15pm Kent Beck scrum - just me and Michael - just a quick catch up
* 4:45pm Impromptu WSO coordination meeting - Michael, myself and Matt - talked through SSL certificate upgrade and GitHub issues
* 5:45pm AV Community meeting - Michael, JP, myself and Matt - talked about funding me and the upcoming design sprint

The critical admin things I got done were revising the AV charity tender documents that will hopefully allow me to generate some income from all the work I put into AV, sending emails to a mix of charity and private clients (past, current and potential future), and deploying the latest code for WSO, which had the pleasing side-effect of re-directing all the MOOC student pairing sessions into the correct chat channels (rather than spamming Slack #general).  I also managed to go through another round of creating private premium chat channels and engaging with AV mentors.

There's not much room for coding in all this.  There's a bit of talking about it.  I was able to just snatch at the security renewal issue between the WSO meeting and the AV Community meeting.  It seems like the following is enough to renew the certificate:

```
$ sudo certbot certonly --manual
Saving debug log to /var/log/letsencrypt/letsencrypt.log
Please enter in your domain name(s) (comma and/or space separated)  (Enter 'c'
to cancel):develop.agileventures.org
Cert is due for renewal, auto-renewing...
Renewing an existing certificate
Performing the following challenges:
http-01 challenge for develop.agileventures.org
```

assuming that one types in the correct domain.  Given that the challenge is replied to correctly from the server, one gets new certificates saved locally and they can be updated in heroku like so:

```
$ sudo heroku certs:update /etc/letsencrypt/live/develop.agileventures.org/fullchain.pem /etc/letsencrypt/live/develop.agileventures.org/privkey.pem -r develop
```

so I need to get that done for the staging and production servers.  It also seems that the fixed part of the challenge key is not changing and so we definitely need to move that into an environment variable (if not change it completely) and then also work out if there's some further way of automating this.  Apparently we can run `certbot renew` but when I tried a dry run, it indicates I need to configure an authentication script:

```
The error was: PluginError('An authentication script must be provided with --manual-auth-hook when using the manual plugin non-interactively.',). Skipping.
```

So I'm tempted to punt on the automation and focus on this first renewal manually and the change that will pull the fixed part of our token into an environment variable.  Going to make that the priority after this blog is completed.

Going back to yesterday's meetings, I had been feeling pretty good about the marketing meeting in that we had worked through all the business pretty efficiently and avoided long and meandering discussions.  I was focusing on not "blocking" anyone.  Ideas and suggestions were celebrated.  That they might have been mentioned before, or that they might have logically inconsistencies was only subtly alluded to, if at all.  I had a strong sense of "the more people in the meeting, the more important it is to avoid disagreement".  That might sound like awful group think, but meetings are a kind of trap that people often feel they can't leave.  Long discussions with point and counter-point should be moved into other meetings where everyone has "opted-in" specfically for that debate.  At least that's my best guess at the moment - the "efficiently-run" meeting seemed to go down well.

There was confusion in my mind about whether the Agile Book Club podcast would go ahead since one member was suggesting a change of day.  Since we still hadn't mixed episodes 2-10 I was tempted to try and use that slot for mixing rather than recording more.  That didn't work out either and since I'm off early on Friday I suggested we try and do an impromptu WebsiteOne meeting.  That backfired in that the podcast member who had suggested the change of day hadn't meant for this week, and my sense was that was frustrating for them.  Maybe I'm imagining a more intense set of emotions from the person than was there.  We were only communicating in Slack.  I ended up feeling pretty bad about it with the incident going round and round in my mind all night and now this morning.  Why should I end up focusing on that negative in a day with several other positives?  Is that evolutionary?  Are hurt feelings the danger signs that paleolithic humans needed to pay attention to in order to survive?

I think I've projected/imagined higher emotional states on people in the past? Maybe, or perhaps it's that I've just apologised too profusely to the point where I've just added irritation to injury?  The podcast member was re-iterating that we should have just asked.  I had an outstanding question to them in Slack about the change of day.  We didn't have the previous week's Slack message confirming the topics that we'd talked about having.  The member in question is in a distant timezone which seems to make having the podcasts on Monday somewhat precarious because it makes them first thing on Monday morning for that person, and so there's not much room to give alerts about potential changes of plan.

I know that I find it difficult if something I'm planning for gets changed at the last minute.  I had been hoping that the individual might have joined us for the WSO meeting or AV community meeting, but I didn't push things, sensing (or imagining) their frustration.  Why is this so sticky for me?  Similarly when my son's football team loses (I'm the coach).  I think it's my "hard ego shell" as Eckhart Tolle and Steve Taylor might call it.  I'm very attached to an image of myself as a punctual, reliable, organised, dedicated, effective, good and considerate person.  Things that appear to reflect on me as inconsiderate or ineffective are painful.  Logically what they reflect is that I'm human and make mistakes.  The stickiness is me beating myself up about the damaged self-image ... maybe :-)

There's something else here I'm grappling with.  Communication.  We've got all these meetings going on, most of them being recorded and shared on YouTube, and that helps the wider team catch up sometimes, but realistically most people are not going to have time to watch the videos.  I think I (and others?) can end up with the false sense of having communicated something.  You say something in five meetings, maybe even to the same small group of people, and you unconciously think you've communicated it.  That might work well in a paleolithic village where the process of collocated gossiping meant pretty much everyone knows everything quickly.  Much as I want to spend more time coding, I think the real problem to crack is communication internally and externally.  I suspect most people coming into AgileVentures from outside are confused about what we offer, and I think we have poor information flow internally about things like the AV community meeting.

That came up from a big chat in #av-community-talk and we added an event on the main site, but then there's still the occasional comment about "how would I find out about this sort of thing?" and I guess it's either accept that you browse your way to knowledge on things and that there's no reliable way of reaching out to everyone, or we solve that problem of working out what is the one correct way to communicate with all the of the people in our distributed team.  I suspect that people are getting their information from different sources ... email, slack, their calender, meetings, private chats with friends etc.  Maybe the AV Community Talk meeting needs to go in the newsletter.  Maybe the site needs more popups to alert people to things in a drip campaign fashion.  There was a great feedback feature on an e-commerce site I was using last night.  When I paused on the payment page for a long time it asked me for quick feedback and I said the shipping prices were too high.  I guess that's the kind of information we'll be hoping to get from the design sprint, but if our site could be rigged to get ongoing quick impressions from people about joining up, making purchasing decisions etc ... Interesting times.  I think the one thing I can be sure of is a few more saddle sores ...
