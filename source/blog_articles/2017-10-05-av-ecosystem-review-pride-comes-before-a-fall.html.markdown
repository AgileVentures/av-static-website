---
title: AV EcoSystem Pride Comes Before A Fall
tags: 
author: Sam Joseph
---

![pride](../images/pride.jpg)

So they say that pride comes before a fall.  Just after being so pleased with myself for fixing the strange double leakage issue introduced by the new Slack gem (pass in Rails.logger), I got notices that I'd posted some secret tokens to GitHub.  In committing the latest vcr cassettes I had inadvertently included the Slack and Gitter api tokens.  It's a relatively easy fix with the following code in the VCR config:

```rb
  c.filter_sensitive_data('<SLACK_AUTH_TOKEN>') { ENV['SLACK_AUTH_TOKEN'] }
  c.filter_sensitive_data('<GITTER_API_TOKEN>') { ENV['GITTER_API_TOKEN'] }
```

but I'll have to regenerate the existing tokens ... anyway, we're running out of week to get this feature fixed up.  The key issues outstanding (now that we have all the existing tests passing) are:

1) managing the different Slack and Gitter room ids for staging and production
2) deciding whether to add explicit cukes that check that the Slack and Gitter APIs are hit
3) filling out all the pending unit tests

I think I've got to start with item 1.  On agile-bot, Michael and I set up a separate ENV var (`LIVE_ENV`) to specify whether the "production" Slack should be hit ... I had half a mind to check which URL the instance was running against, but probably easier to re-use the `LIVE_ENV` concept.  I take a stab with the ugly:

```rb
  if ENV['LIVE_ENV'] == 'production'
    CHANNELS = {
        "asyncvoter": "C2HGJF54G",
        "autograder": "C0UFNHRAB",
        "betasaasers": "C02AHEA5P",
        "binghamton-university-bike-share": "C033Z02P9",
        "codealia": "C0297TUQC",
        ...
        "pairing_notifications": "C02BNVCM1",
        "standup_notifications": "C02B4QH1C"
    }
    GITTER_ROOMS = {
        "saasbook/MOOC": "544100afdb8155e6700cc5e4",
        "saasbook/AV102": "55e42db80fc9f982beaf2725",
        "AgileVentures/agile-bot": "56b8bdffe610378809c070cc"
    }
  else
    CHANNELS =    {
        "cs169": "C29J4CYA2",
        "websiteone": "C29J4QQ9W",
        "general": "C0TLAE1MH",
        "pairing_notifications": "C29J3DPGW",
        "standup_notifications": "C29JE6HGR",
    }

    GITTER_ROOMS = {
        "saasbook/MOOC": "56b8bdffe610378809c070cc",
        "saasbook/AV102": "56b8bdffe610378809c070cc",
        "AgileVentures/agile-bot": "56b8bdffe610378809c070cc"
    }
  end
```

Which maybe should be loaded from a file - I have a funny reticence to load things from files.  I've never been so comfortable with grabbing stuff from files - a legacy from Java where it was such a pain?  In Ruby it's pretty damn trivial.  I guess when I have stuff formatted in one way, stepping through and reformatting for yaml, or what have you, causes my motivation to collapse.  What we really want in the end is to be looking all these up from the Slack API, so I'm going to leave that for a refactoring, get the rest of the unit tests to pass and try to get this onto staging to see if my claims that we can replace the agile-bot are close to true.

So I stab about a bit and with a fair amount of replication of tests (and some refactoring), I get the output of the RSpec test to this:

```
SlackService
  .post_hangout_notification
    PairProgramming
      sends the correct slack message to the correct channels
      does not post notification if hangout url is blank
      does not fail when event has no associated project
      should ping gitter and slack (but not general) when the project is cs169
    Scrums
      does not post notification if hangout url is blank for pairing
      should ping slack when the project is cs169
      does not fail when event has no associated project
      sends the correct slack message to the correct channels
  .post_yt_link
    Scrums
      does not post youtube video link if yt_video_id is blank
      sends the correct slack message to the correct channels
    PairProgramming
      does not ping slack when the project is cs169
      does not post youtube video link if yt_video_id is blank
      sends the correct slack message to the correct channels
```

Which I think is a lot better coverage than we have had.  What I'd really like is a local report of coverage for this file for these tests.  There's also a lot of replication in the SlackService file itself, but I think I will leave a major refactor here until I've seen all our existing functionality replicated.  First, let's get this PR merged and onto staging, where I can test all the settings against hitting a backup Slack instance, and then ease this into production ...


