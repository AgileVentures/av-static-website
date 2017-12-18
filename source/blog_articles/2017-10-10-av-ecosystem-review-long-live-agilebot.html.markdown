title: AV EcoSystem Long Live AgileBot
tags: 
author: Sam Joseph
---

![live long and prosper](../images/live_long_and_prosper.png)

The AgileBot rubyification yesterday was not without a slight speed bump yesterday as I had set the `LIVE_ENV` parameter incorrectly on production and got a 500 error when I tried to start the Martin Fowler scrum.  However we still managed a scrum, although I was slightly more stressed than usual with my internet also dropping in and out, but we had two full scrums yesterday with a new project proposed in each.  It seems like the welcoming language that I added is working backwards through time to revitalize the scrums - let's see how that goes today :-)

Anyhow, as soon as the scrum was over I was able to fix my slip, and the "Kent" Beck scrum went out just as intended on the correct Slack channels, as did the Autograders client meeting and the SHF project meeting, which automatically pinged the relevant channels, yay!  So now that's working I think I can safely shut down the separate node/hubot AgileBot instances and save us $14 a month in Heroku fees.

![](https://dl.dropbox.com/s/0f1hjompo116pzr/Screenshot%202017-10-10%2009.53.44.png?dl=1)

That's a good feeling.  Equivalent (monetarily at least) to signing a new Premium member.   In the process I also saw that Redeemify production is costing us $7 a month.  I turned that off.  Would be great to get more cost savings with the AgileVentures' WebSiteOne monolith that's currently costing us just under $90 a month.  $50 is two dynos on the Heroku standard plan:

![](https://dl.dropbox.com/s/ornq35b2xudjt05/Screenshot%202017-10-10%2009.57.56.png?dl=1)

I'd really like to better understand what's using up all this memory and processing on the main site.  We seem to be over our 1GB memory most of the time.  Michael and I had previously identified a time zone gem as being a bit of a culprit there.  We have a ticket for this - perhaps it should be my focus after the next high level review:

[https://github.com/AgileVentures/WebsiteOne/issues/1288](https://github.com/AgileVentures/WebsiteOne/issues/1288)

According to New Relic the app server time is distributed like so

![](https://dl.dropbox.com/s/br0mx5p34sxm12j/Screenshot%202017-10-10%2010.02.28.png?dl=0)

I wonder if that implies that we should be optimising the VisitorsController?  Which also makes me think about creating a middleman site to be a light weight host of the landing page ...

Anyhow, what I really wanted to do this morning was knock off a couple of quick features that "should" be easy now that we have the agile bot in Ruby.  

1. hyperlink the URLs to videos/hangouts  
2. ensure that videos also get shared to project channels  
3. remove the dates from the automated pings  

I start with 1) and start by refactoring the message contents in the test to DRY them out:

```rb
    let(:message) { 'MockEvent: mock_url' }
    let(:here_message) { "@here #{message}" }
    let(:channel_message) { "@channel #{message}" }
```

Checked that stayed green, and then did a similar refactoring in the app code itself:

```rb
    message = "#{hangout.title}: #{hangout.hangout_url}"
    here_message = "@here #{message}"
    channel_message = "@channel #{message}"
```

and got that green.  So now I could test drive the new hyperlink format like so:

```rb
let(:message) { '<mock_url|MockEvent>' }
```
see that fail and then make the change in the app code:

```rb
message = "<#{hangout.hangout_url}|#{hangout.title}>"
```

and then see all the tests pass.  Now what I needed to do was see that this had the correct effect in a Slack instance, so I started the rails instance locally, and checked I had the Slack authorization token pointing to the test Slack instance.  Then, once I had properly sourced the bash file and removed the safeties on posting to Slack from local I was able to see we were getting the correct message hyperlinking that I wanted (for so long!):

![](https://dl.dropbox.com/s/a3k1yxm570u6s63/Screenshot%202017-10-10%2010.29.07.png?dl=0)

And that was so easy that I was tempted to do the same with the video links, and since I was there I also ensured the videos got shared with project channels ... and I got that in a PR:

![https://github.com/AgileVentures/WebsiteOne/pull/1887](https://github.com/AgileVentures/WebsiteOne/pull/1887)

and there we go.  I still really want to remove the unecessary dates from the slack pings, but hopefully I can do that tomorrow and then still have time to do a high level review with Thursday and Friday.  Then perhaps the performance review next week ...?

## Related Videos

* ["Martin Fowler" Scrum](https://youtu.be/6lpTEsVnK5Y)
* ["Kent Beck" Scrum](https://youtu.be/vHDRXgGsSiM)

p.s. I threw in a [refactoring ticket](https://github.com/AgileVentures/WebsiteOne/issues/1888) as there's the code could look better, and by the time I got back with my coffee the build passed (thanks cucumber second retry) and I got it merged in to see if we could try it before the next scrum ...


