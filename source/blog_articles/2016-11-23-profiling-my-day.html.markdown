---
title: Profiling My Day
date: 2016-11-23
tags: eliminate, simplify, delegate, automate, bash script, drie, premium, agile ventures, google hangouts toolbox, planning, prioritising, profiling, measuring 
author: Sam Joseph
---

![profile](/images/profile.jpg)

I profiled my day yesterday in an attempt to apply the Eliminate, Simplify, Automate, Delegate (ESAD) mantra and create space for the important things :-)  Here's my rough profile of the day:

```
09:00 - 10:00 writing blog (5mins on getting that up and in Slack #blog_drafts)

10:15 - 10:40 reviewing Slack and email - starting a WSO vote

10:40 - 11:00 reviewing previous days blog and pushing out

11:00 - 11:30 rebuilding Slack client after creation and renaming of teams

11:30 - 12:15 scrum and debugging with Premium Mob Member

12:30 - 13:30 edX emailing, AV emailing, Social Media update, negotiating with company about a paid project, editing and promoting blog for Kenyan Bootcamp code9ty

14:45 - 15:00 rebooking physio appt

15:00 - 15:45 reviewing asyncvoterUI and LS PRs (starting hangout 2nd time adding my AV banner)

15:45 - 16:15 scrum - catch up with co-founder Thomas, voting with Rose and student

16:15 - 17:00 meeting with heroku about our charges

17:00 - 17:30 chatting with sponsor drie about hosting services
```

So let's apply the ESAD criteria to each in turn.  I don't know that there's anyway to simplify the process of actually writing the blog.  I could make them shorter, or even eliminate them, perhaps doing them less frequently.  I'm reluctant to do that as I find them a nice grounding start to the day, the prevents me from just sitting down and reacting to whichever seems the most urgent message on Slack or in email.  Intuitively the feedback I'm getting on the blogs is that they are often too long, have too much code, not enough images, but also that they've helped others understand my perspectives and what we are trying to achieve with AgileVentures.  I can't really delegate them (although I'd love to have more in the community blogging), and the only place for automating them would be to work on the final portion of pushing them to GitHub and Slack where each day I have to do the following:

1. Copy from TextEdit to Sublime
2. Save in correct location and format for middleman blog (e.g. 2016-11-03-pithy-title.html.markdown)
3. Add middleman blog header, edit date there, edit title and edit keywords
4. Navigate to my av-static-website terminal
5. Type `git add .`
6. Type `git commit -am 'new blog'`
7. Type `git push`
8. Open a browser in the blogs directory of the av-static-website: i.e. https://github.com/AgileVentures/av-static-website/tree/master/source/blog_articles
9. Navigate to the link for the days blog and copy it
10. Switch to Slack and paste to the #blog_drafts channel with `@channel new blog draft <link>`

Now this only takes a minute or two, but I am doing it every day, and having written it out I think several parts could be automated or simplified.  It might take 30-60 minutes to get automated in the way I want, so I'd need to feel sure that I was going to write another 15-30 blogs before I'd start to see time savings.  Possibly I could write a bash script ... there are also things that can go wrong if I have to deal with merge conflicts (I should always `git pull` first).  I could just blog on medium and be done with it.  I've avoided that partly because I want to share drafts with anyone who joins #blog_drafts and have them able to submit PRs, although perhaps a better solution is to just make everyone interested a members of the AgileVentures organisation on Medium.

I start to think about whether there are some existing automations, like setting up pure PR notifications on the #blog_drafts channel, and submitting my blog as a PR from a branch.  That might seem like crazy extra work, but it connects to something else I'd like to automate further; the process of submitting a PR from the command line, which seems to involve me re-typing the same thing several times over, e.g. 

1. `git checkout -b #waffle_id_exciting_feature_name`
2. doing and committing the work (commit message often includes 'exciting feature name')
3. `hub pull-request -b develop` 
4. editing the PR description in vim to include the text `fixes #waffle_id`

I keep thinking here that if I could get something like a default commit message based on the branch name, then I could avoid typing the waffle_id and feature name twice.  In the blog case there are no waffle ticket ids to link up, but I guess I'm looking at being able to type something like the following from the command line:

```
$ blog_draft <blog title>
```

which would do the following steps:

a) `git pull`  
b) `git checkout -b new_blog<blog_title>`  
b) `git add source/blog_articles`  
c) `git commit -m 'new blog on <blog title>'`  
d) `hub pull-request -b develop`  

and with the right notifications a bash script doing the above could ping the #blog_drafts channel.  With a Ruby script I could probably get finer tweaks so that it actually sent  `@channel new blog draft <link>` to #blog_drafts.

So I could clearly do the above analysis on all the sections of the day.  Blog getting too long?  Quick review.  Daily review of Slack and Email?  Can't really avoid, just have to avoid getting sucked in to low priority things.  I try to pull each question or request into my action plan document and order and then take passes through my action plan to work out what order to do things in.  The asynchronous voting is already the subject of ongoing automation.  

Reviewing the previous days blog and pushing that out requires some more automatable steps e.g.

a) merge any PRs  
b) `git pull`  
c) read through and make minor corrections (10mins?)  
d) `git add`  
e) `git commit -m 'tweaks'`  
f) `git push`  
g) `rake publish`  
h) checking http://nonprofits.agileventures.org/blog/  
i) adjusting image sizes, making sure blog looks okay  

The issue with both the initial blog generation and publishing the previous days draft is that the bulk of the time is not automatable without a serious AI.  I guess the question is whether the surrounding command line cruft is worth automating. Even if it's not, it could be fun, and possibly beneficial if the automation can make PRs from the command line slightly less work ...

The Slack rebuild was (hopefully) a one off.  The scrums - I do two a day, and they are capped at 15 mins, but I often end up doing another 15 mins after the scrum - today the first scrum was followed by 15 mins debugging with a Premium Mob member, which was fun and (I think) worth it.  The second scrum 15 min follow on was a catch up with my Co-Founder so pretty important ...  Although scrums can certainly serve to break up flow on a longer more complex piece of work; it's also critical to coordinate with the team, and these Scrums are our welcome mat and showcase.  I am still conflicted about whether they are worth the effort to maintain.  Google Hangouts on Air and our plugin seem stable-ish again after our outage of a couple of weeks back, although I am needing to re-start hangouts occasionally, and the fact that my lower third banner is not saved means I'm re-adding that for every scrum and hangout - which burns 30 seconds every time, although it's helped me memorise the AV colour code (#ee7335).

I'm unclear if the [google hangout toolbox](https://www.hangouttoolbox.com/) is still being maintained.  I just posted the following to their G+ support group:

> The lower third still works for me, but I can't save it - I have to re-enter the details every time.  Is the code for this open source?  Is there someone actively maintaining it?

> Here are some more details - I can set up my lower third like so:

![setting up lower third](https://www.dropbox.com/s/nz4gwqb0pbfhfwc/Screenshot%202016-11-23%2010.04.34.png?dl=1)

> I have the setting to enable auto load and autoload last used lower:

![auto reload settings](https://www.dropbox.com/s/8zr12w8e7sf4wtu/Screenshot%202016-11-23%2010.05.34.png?dl=1)

> and here's me having reloaded the hangout and the saved presets have gone :-(

![saved presets missing :-(](https://www.dropbox.com/s/24ik2p5qauunua6/Screenshot%202016-11-23%2010.07.36.png?dl=1)

> Any ideas?

> Many thanks in advance

So I can see the names of the developers on the site.  I'll leave that [G+ post](https://plus.google.com/+SamJoseph/posts/QyAdAzTXPuG) for a couple of days and then maybe reach out to the maintainers.

Michael wasn't around so I reviewed PRs instead of pairing.  Felt good to get into the details of the new AsyncVoter React client and get to some other outstanding PRs.  PR review is what Premium members get priority on.  I'm still not sure if this is the key thing that Premium's want, but reviewing PRs takes a lot of work and time, and I believe it is very beneficial for learning.  Other meetings in the day were one offs and can't really be ESADed.  Other key area for ESADing is the edX and AV emailings.  I think I've got the Social Media update fairly automated.  It involves reviewing an older blog, tweaking and then pushing to Medium, which automatically pushes to Twitter and Facebook, although with boring "I just published" text.  I push to LinkedIn manually, but it's the edX and AV emailings that are time consuming.  The edX ones go through MailChimp, but they don't give me enough access to automate them.  For the AV mailings, I really need to look into the Amazon SES service.  I'm just finishing a run of mailing all the AV members individually through my Thunderbird client.  I have a couple of scripts to semi-automate this, but I am trying to reach out personally to everyone.

I do feel a bit like a spammer, but it's a one off, and I'm responding individually to every email back, and we've signed up about ten new premium members as a direct result of these personal mailings over the last two months.  Longer term we need some way of enabling people to opt-in/out of emails.  Lots to do there.  I'm going to try profiling a few more days to see if I can find some sweet spots to ESAD, and if you have suggestions about how I can operate more efficiently I'm all ears! :-)

###Related Videos

* ["Martin Fowler" scrum](https://www.youtube.com/watch?v=OiBuIltBooM)
* [Solo work on Async Voter](https://www.youtube.com/watch?v=4rRIwgGasKI)
* ["Kent Beck" Scrum](https://www.youtube.com/watch?v=-Sn4r03hNL4)






