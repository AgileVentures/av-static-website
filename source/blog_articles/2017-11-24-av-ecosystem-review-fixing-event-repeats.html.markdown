So the Semaphore CI was busy burning away yesterday as it re-ran all the different dependabot pull requests, and a few of them have gone green ... and I can also now update the medium-editor PR to the base branch, which seemed to be blocked all day yesterday:

![](https://dl.dropbox.com/s/zs5jgyf1bl5hx2z/Screenshot%202017-11-24%2010.32.46.png?dl=0)

In the background I guess I'm supposed to be getting tweaks to the home page text out to production.  I rebase staging into production and push out, and see we've got a few things in there:

![](https://dl.dropbox.com/s/l5bpb26nuklllp8/Screenshot%202017-11-24%2010.35.53.png?dl=0)

Will be good to give them a chance to settle in on production ... there are eight dependabot PRs that are now green.  Shall I just dump them in to develop?  I do, but I'm burning time, and I didn't leave myself much time for blogging today and Friday's are now super busy with mobs and pairing.  I wanted to get into this deprecation warning about repeat_ends for events ... maybe after the WSO meeting later today ...

It would be great to work through and remove any gems we don't need.  Some related items here:

* https://stackoverflow.com/questions/14333963/how-to-find-unused-gems-in-my-gemfile/28628993#28628993
* https://github.com/pboling/gem_bench

Okay, I managed to get us down to a single page of pull-requests, and opened all the others to look at the fails to see if they're just intermittent fails.  There seems to be some really weird random fails from coveralls - that needs sorting too.

End of day, and I'm still no closer to working on this event `repeat_ends` issue.  Although I do feel like a new focus is called for - make the projects super developer friendly and easy to onboard to and that way we get more folks working on all the small fixes that would make the main AV site more welcoming ...
