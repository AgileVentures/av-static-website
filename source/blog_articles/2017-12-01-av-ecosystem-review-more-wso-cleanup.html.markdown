---
title: AV EcoSystem Review More WSO CleanUp
author: Sam Joseph
---

![cleanup](../images/cleanup.jpg)

So running the cuke tests I get this one bit of extraneous output:

```
Encountered Error in get: 404 Resource Not Found: {"code":"route_not_found","kind":"error","error":"The path you requested has no valid endpoint."}
```
and of course huge dumps of VCR and Puffing Billy cache changes.  I can clean that up with our `rake vcr_billy_caches:reset` commmand but I wonder what other projects do.  Perhaps I should ask the VCR folks.  I think the issue we have is that we need to throw out some of our old caches and reload, dealing with the pain that will cause ... maybe we need some VCR version of dependabot that will throw out old caches for us?

I posted the [following to the vcr-ruby mailing list](https://groups.google.com/forum/#!topic/vcr-ruby/8DoA7MUq-38):

> Hi everyone,

> We've been using VCR in our rails projects for a long time (and now puffing billy as well) and I'm concerned about how confusing cached network recordings are for new contributors that we onboard.

> We also seem to end up with a fair few new network cache files when tests fail and so on, which show up in `git status`.  New contributors wonder what these files are for, try to add the cache directories to .gitignore, or do other strange things.

> We've recently introduced a rake task `rake vcr_billy_caches:reset` to make it easy to reset the caches to the way they are in the current branch, but I wonder if there isn't some better long term solution.

> I wonder if we're using VCR incorrectly, or perhaps not updating our cached network files frequently enough.  I'm a little scared to delete large swathes of recorded files for the extra work that might pop up in terms of ensuring that everything works again, but perhaps I'm being silly.  It's just that it's all volunteer time, so well, anyway.  Here are the two main OS projects I'm running that use VCR:

> https://github.com/AgileVentures/LocalSupport

> https://github.com/AgileVentures/WebSiteOne

> I've added sections to our contributing docs, but I feel like all my text should probably be replaced with images or a video: https://github.com/AgileVentures/LocalSupport/blob/develop/CONTRIBUTING.md#acceptance-testing--caching

> I just wondered if others had similar experiences and any suggestions.

> Many thanks in advance

> Best, Sam

I also opened a [GitHub issue](https://github.com/vcr/vcr/issues/672) to also mention the idea of some sort of automated tool to delete old cache files.  

Annoyingly I'm almost out of time this morning, and I wanted to see if I could fix the `local_time` update that dependabot was suggesting, as well as cleaning out the cruft in the Cucumber logs.  I was also thinking that the ultra slow cukes needed to be worked on.  Hmmm, leave `local_time` to next week I guess ... urgh, and I left a hanging PR to update some of the Slack channels that get pinged ...
