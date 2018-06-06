---
title: AV EcoSystem Review Sorting and Reporting
author: Sam Joseph
---

![deploy](../images/deploy.jpeg)

Didn't complete the AV report yesterday, or deploy the new premium model code. All booked up with exciting Premium F2F sessions, and this morning I had to spend an hour catching up with emails for various possible gigs to cover what the charity work does not.  So, right, let's get that new code on staging ... which involves me rebasing to staging and pushing that up.  I didn't manually check develop ... that's partly because I'm turning the servers off to save money.  Funny, if I'd left them on over night I'd immediately have something I could check; it only takes 30 seconds (navigating to heroku, flicking a switch) to get them on, but sometimes that can feel like a real pain.  Anyway, got the develop server up, not even sure how to really check that things are really working ... I guess I sign up random guy as a premium member:

![](https://dl.dropbox.com/s/oyvrnyrbso5ouy4/Screenshot%202018-01-18%2010.37.43.png?dl=0)

and then upgrade to mob:

![](https://dl.dropbox.com/s/i95alz97si8nra1/Screenshot%202018-01-18%2010.38.28.png?dl=0)

and I don't get any errors and the site looks okay.  And so I should be able to see the subscription relations in the rails console:

```
[tansaku@Samuels-MBP:~/Documents/Github/AgileVentures/WebsiteOne (staging)]$ 
→ heroku run rails c -r develop
Running rails c on ⬢ websiteone-develop... up, run.5865 (Hobby)
Loading production environment (Rails 4.2.10)
irb(main):001:0> u = User.find_by email: 'random@random.com'
=> #<User id: 88, email: "random@random.com", created_at: "2016-02-24 11:09:56", updated_at: "2018-01-18 10:38:22", first_name: "Random", last_name: "Guy", display_email: false, slug: "random-guy", youtube_id: nil, display_profile: true, latitude: nil, longitude: nil, country_name: nil, city: nil, region: nil, youtube_user_name: nil, github_profile_url: nil, display_hire_me: false, bio: "rektktktk", receive_mailings: true, country_code: nil, timezone_offset: nil, status_count: 0, deleted_at: nil>
irb(main):002:0> u.subscriptions
=> #<ActiveRecord::Associations::CollectionProxy [#<Subscription id: 12, type: nil, started_at: "2018-01-18 10:38:22", ended_at: nil, user_id: 88, plan_id: 2, sponsor_id: 88>, #<Subscription id: 11, type: nil, started_at: "2018-01-18 10:37:49", ended_at: "2018-01-18 10:38:22", user_id: 88, plan_id: 1, sponsor_id: 88>]>
```

And that is all looking exactly as it should with one subscription being closed out and the new one started, so this code is winging its way to staging, where we can test with some more realistic data.  The build just passed there, but deploy will take a little longer - will leave that till tomorrow and push on with this report. Time to work out what AsyncVoter meetings we ran.  Hmm, maybe I need to bring up with the trustees some modifications to the report format ...

In the background I'm seeing if I can get Heroku managing the SSL certs for HarrowCN for us:

```
[tansaku@Samuels-MBP:~/Documents/GitHub/AgileVentures/LocalSupport (develop)]$ 
→ heroku certs:auto:enable -r staging
Enabling Automatic Certificate Management... done
=== Your certificate will now be managed by Heroku.  Check the status by running heroku certs:auto.
[tansaku@Samuels-MBP:~/Documents/GitHub/AgileVentures/LocalSupport (141687181_import_charity_data_from_commission)]$ 
→ heroku certs:auto -r staging
=== Automatic Certificate Management is enabled on harrowcn-staging

Domain                   Status
───────────────────────  ───────
staging.harrowcn.org.uk  Waiting
```

If that all works and we could get the AV WSO certs self-managed that would be one less thing I need to worry about, i.e. renewing letsencrypt certificates every three months.  Okay, so now the new code is deployed to staging - probably best to get that deploy completed, so that I can uncover any pain that might lurk there - creating the report doesn't pose such risks.  Okay, so tested on staging, looks good - pushing to master, and HarrowCN SSL looks good self managed (I guess?).  Could try production update there too ... updated the AsyncVoter report a little, still lots to do ...!
