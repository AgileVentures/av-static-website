I pushed out some changes to the main AgileVentures landing page last night.  I'd had a set ready from last week's sprint on Friday, but rather than push them straight out I'd held them on staging and posted an image of them to the #websiteone channel on Slack:

![reworked home page](https://www.dropbox.com/s/1em05z5kzqdk0ln/Screenshot%202017-03-06%2013.13.56.png?dl=1)

I'm glad that I did, as I got lots of great feedback.  I'd got a round of feedback in the "Kent Beck" scrum on Friday, but I wasn't feeling entirely comfortable with the result, and the reaction from several in the #websiteone channel was that it didn't look great and was a bit too wordy, so I held off to review things in Monday's marketing meeting.  Lara asked why move the button when it's already "working"; Marufa concurred there was to much text, and I was thinking maybe reduce the font size and add more whitespace padding/margin around it to make it all less busy.

However Lara also recommended an analytics plugin for Chrome which made me think rather differently.  The [Page Analytics](https://chrome.google.com/webstore/detail/page-analytics-by-google/fnbdnhhicmebfgdgglcdacdapkcihcoh?hl=en) plugin by chrome annotates a site with information about where users are clicking based on Google Analytics tracking.  It looks like this:

![page analytics plugin on AV.org](https://www.dropbox.com/s/5huv3vgkftr50wc/Screenshot%202017-03-07%2009.52.42.png?dl=1)

This is just a fantastic example of how data visualization can make all the different.  All the other different displays of data and flow through the site in Google Analytics had so far failed to help me feel I was understanding what was going on as people browsed the site.  The main thing that really popped out of this visualization is that 13% of those arriving at the page did click through on the round "code" banner, followed by ~5% on the "learn" banner.  We'd had feedback from at least one user that they hadn't realised the round banners were clickable, but clearly a non-trivial portion of recent visitors (the graphic above is from the last months traffic) are clicking through.

Now that might reflect the preponderence of those keywords being used in our AdWords campaigns.  Lara was saying the "Get started now to begin coding" button was working because we had 23% click through.  Although the way this plugin works it can't distinguish where a user is clicking; rather it seems it just checks which URL they land on next, as I see the "Sign up" link in the top right corner is also marked up with a 23%.  Now I assume that people are probably clicking through on the large visible button, but seeing that we clearly did have some non-trivial level of click through on the round banners, and that we had some questions about the proposed text only change, made me roll back the text change and just push out two other minor changes for guest users:

1) Change main call to action button text from "Sign up now to start coding" to "Get started now to begin coding"
2) Change scrum link from title of scrum to "Want to learn more? Listen in. Next projects review meeting in x hours"

These are less controversial and I hypothesize that the second might conceivably bring some more folks into hangouts, and we'll be able to tell intuitively over the course of the week if there are any new faces showing up.  Maybe we should be A/B testing the change to the button text, but I can justify that quick fix in terms on making the text consistent with other changes we've been making on the other pricing and Premium pages.

I'm left conflicted about the next steps.  It seems sensible to see how the small changes impact things.  For bigger changes to the landing page it feels like we need 4 or 5 variations which could be kicked around by the various interested teams, and then perhaps we could A/B test a couple of alternatives if we can narrow down to two that we like.  The concern that Pat raised is that the landing page doesn't immediately communicate our value proposition, and I tend to agree with him.  I think there are also a great number of other tweaks that could be made in other places to make the whole site look more pleasing and professional - things like the disabled direct signup component, the excessive and small font size text in several places.

Based on our sprint kick off on Friday I'm adjusting the site so that newly signed up users will be redirected to the getting started page.  That feels like a survivable experiment that might deliver some value.  However for all this tweaking I don't have much evidence that any of the AdWords activity will actually lead to revenue generation.  Clearly if we can get the site into the shape where it does act as a funnel for those who might really derive value from AgileVentures, then that would be fantastic.  However I suspect that most who are coming in through our "learning to code" related keywords might be better served by CodeCademy and FreeCodeCamp.  Presumably if we carefully tweak our AdWords over time we might be able to hit a seams of developers at junior, mid and senior level who'd want to get involved and take out Premium plans ...

However it's clear to me that so far the income we have has been generated by me reaching out directly to people via Email and Slack.  Getting people to sign up on our site of course swells the number we can email to, but then again if they are not the ones who'll benefit from AgileVentures then it's effort in vain.  Clearly the email newsletter needs to go out and we need to see what interest it generates.  I'm inclined to think that making some mobbing session videos available as examples of the benefits of Premium Mob might have more impact at the moment ...  I guess we keep experimenting and analyzing ...

###Related Videos

* ["Martin Fowler" Scrum](http://youtu.be/rMgERi4w8W0)
* [Marketing meeting](https://www.youtube.com/watch?v=QzR72HpWaQk)
* ["Kent Beck" Scrum](https://www.youtube.com/watch?v=-CMIoFYLuqI)

