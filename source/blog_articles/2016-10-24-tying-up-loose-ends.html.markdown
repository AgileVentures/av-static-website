---
title: Tying Up Loose Ends
date: 2016-10-24
tags: devops, heroku, pull-requests, automation, database, permissions, accounts, admin, pipeline
author: Sam Joseph
---

So after [Yak shaving](http://nonprofits.agileventures.org/2016/10/21/yak-shaving/) around Heroku automated PR deploys, we finally got them working.  Raoul joined us in the "Martin Fowler" scrum and through a process of both disabling the PR deploys, AND deleting the GitHub webhooks that Heroku had installed, the permissions issue was resolved.  The deploys themselves were now marked on GitHub as coming from me, and suddenly all the other strange database issues evaporated.  I did also get a reply from Heroku:


> The use of ruby-2.3.0 appears to be because you're bundling it into the vendor/ directory:

```
$ ls vendor/*/*

[…]

vendor/bundle/ruby:
2.3.0
```

> We advise against checking vendor into source control, and relying solely on bundler's installation of Gemfile contents instead.

> The Postgres error appears to be because your database.yml hard codes some connection information:

```
development: &dev
  adapter: postgresql
  encoding: unicode
  database: websiteone_development
  pool: 20
  username: postgres
  password:

[…]

production:
  <<: *dev
  database: websiteone_production
As you can see in the error, the error is that the postgres user attempts to connect, and fails. After it fails, Rails falls back to the value located in $DATABASE_URL. You can change this to simply:

production:
  url: <%= ENV['DATABASE_URL'] %>
```

which all sounded like good advice, but this was all for errors that were no longer occurring - at least if they were, they were no longer blocking the deploy.  I asked them about that:

> Thanks Jason, that's weird because we don't have vendor/bundle/ruby in our source tree: https://github.com/AgileVentures/WebsiteOne/tree/develop/vendor/ any idea how it's getting in there during deploy?

and I got back the following:

> Regarding the vendor/bundle/ruby question I think there may have been some issue with the build cache on this occasion that was holding the 2.3.0 over from a previous deploy. If you have any trouble in future you could you try running the following commands:

```
$ heroku plugins:install https://github.com/heroku/heroku-repo.git
$ heroku repo:purge_cache -a appname
$ git commit --allow-empty -m "Purge cache"
$ git push heroku master
```

> That will ensure you start from a cold cache for that deploy. Let me know if you have any further questions.

So that's that, except that all these automated PR apps are costing us money, and I'm not sure they are being deployed automatically.  The LocalSupport auto-deploy appears to be stuck due to an app limit of 100, and I'm starting to suspect that's because the local support develop server is on my personal account.  If I move it to one of our group accounts we'll start incurring more charges, so I found a little script to [delete heroku apps in batch](https://gist.github.com/naaman/1384970), which I modified slightly to work with a hand picked list:

```
while read app; do heroku apps:destroy --app $app --confirm $app; done < delete_heroku_list 

Destroying ⬢ afternoon-stream-8326 (including all add-ons)... done
Destroying ⬢ agile-chamber-2464 (including all add-ons)... done
Destroying ⬢ agile-dawn-9072 (including all add-ons)... done
Destroying ⬢ agile-thicket-6113 (including all add-ons)... done
Destroying ⬢ ancient-bastion-6361 (including all add-ons)... done
Destroying ⬢ ancient-dusk-4608 (including all add-ons)... done
```

I could have burnt time doing a regex for the format of `<word>-<word>-<four-digits>` to find and delete all my apps without custom names, but that would have burnt time for something I'm unlikely to need to do again, and it took a minute to get the file `delete_heroku_list` to have the names of all the crufty old apps I haven't used in years.

So my Heroku dashboard is looking a lot cleaner.  I've got my two main project pipelines in my "favourites", and my Heroku navigation is a lot more manageable:

![](https://www.dropbox.com/s/iaf134csxh7ij1g/Screenshot%202016-10-24%2010.09.56.png?dl=1)

Although that doesn't immediately seem to have enabled automated PR deploys on LocalSupport - which still reports the 100 app limit exceeded.  I guess that will take time to flush through the system, or indeed I might need to try this new `purge_cache` technique that's mentioned above, but those are Yak's to be shaved on another day.  I only just started on the data migration script for the Premium plus upgrade feature, which needs to be finished, and there's a hell of a lot of other critical admin tasks to get to.  At the minium this ties up some of the loose ends from last week, when I was worried the Yak shaving would continue indefinitely.  And at the very least I've got to a reasonably satisfying task switching point ...

### Related Videos

* ["Martin Fowler" scrum](https://youtu.be/_hAm_6T8r18)
* ["Kent Beck" scrum](https://www.youtube.com/watch?v=feu722TBjo4)






 