I can't get our GDPR required changes out due to fails in the WSO build.  Over night it seems like Nico identified and fixed some failing cucumbers.  I have a set of failing rspec in my GDPR branch, but they're also appearing for me on the develop branch:

```
Failures:

  1) AgileVentures::BulkMailer returns email-addresses
     Failure/Error: expect(bulk_mailer.used_addresses.sort).to eq(User.all.map(&:email).sort)
     
       expected: ["ford@klocko.org", "juanita.koch@baumbach.org"]
            got: []
     
       (compared using ==)
     # ./spec/lib/agile_ventures_bulk_mailer_spec.rb:32:in `block (2 levels) in <top (required)>'

  2) AgileVentures::BulkMailer will return num sent mails
     Failure/Error: expect(AgileVentures::BulkMailer.new(@opts).run).to eq(2)
     
       expected: 2
            got: 0
     
       (compared using ==)
     # ./spec/lib/agile_ventures_bulk_mailer_spec.rb:18:in `block (2 levels) in <top (required)>'

  3) SendNewsletter processes was_sent is false until all recipients processed
     Failure/Error: expect(SendNewsletter.run).to_not be_nil
     
       expected: not nil
            got: nil
     # ./spec/jobs/send_newsletter_spec.rb:29:in `block (3 levels) in <top (required)>'

  4) SendNewsletter processes sends newsletter to adequate users per run
     Failure/Error: second_chunk = User.mail_receiver.order('id ASC').where('id > ?', first_chunk.last.id)
     
     NoMethodError:
       undefined method `id' for nil:NilClass
     # ./spec/jobs/send_newsletter_spec.rb:45:in `block (3 levels) in <top (required)>'

  5) SendNewsletter processes persists last processed user_id
     Failure/Error: expect(@newsletter.last_user_id).to be > 0
     
       expected: > 0
            got:   0
     # ./spec/jobs/send_newsletter_spec.rb:25:in `block (3 levels) in <top (required)>'

  6) SendNewsletter processes was_sent is true if all recipients processed
     Failure/Error: expect(@newsletter.was_sent).to be_truthy
     
       expected: truthy value
            got: false
     # ./spec/jobs/send_newsletter_spec.rb:37:in `block (3 levels) in <top (required)>'

  7) SendNewsletter processes specified chunk-size
     Failure/Error: expect { SendNewsletter.run }.to change { ActionMailer::Base.deliveries.count }.by( Newsletter::CHUNK_SIZE )
       expected `ActionMailer::Base.deliveries.count` to have changed by 5, but was changed by 0
     # ./spec/jobs/send_newsletter_spec.rb:18:in `block (3 levels) in <top (required)>'
     
Finished in 50.16 seconds (files took 14.7 seconds to load)
737 examples, 7 failures

Failed examples:

rspec ./spec/lib/agile_ventures_bulk_mailer_spec.rb:29 # AgileVentures::BulkMailer returns email-addresses
rspec ./spec/lib/agile_ventures_bulk_mailer_spec.rb:17 # AgileVentures::BulkMailer will return num sent mails
rspec ./spec/jobs/send_newsletter_spec.rb:28 # SendNewsletter processes was_sent is false until all recipients processed
rspec ./spec/jobs/send_newsletter_spec.rb:40 # SendNewsletter processes sends newsletter to adequate users per run
rspec ./spec/jobs/send_newsletter_spec.rb:22 # SendNewsletter processes persists last processed user_id
rspec ./spec/jobs/send_newsletter_spec.rb:34 # SendNewsletter processes was_sent is true if all recipients processed
rspec ./spec/jobs/send_newsletter_spec.rb:17 # SendNewsletter processes specified chunk-size
```

They are all email related and I wonder if the new letter-opener gem is the culprit here ..., but perhaps a git rebase will help ... but maybe I can go even faster with a git hist:

```
* 4cdd5db1 2018-05-15 | VCR got updated when working github related stories. (#2379) (HEAD -> develop, origin/develop, origin/HEAD) [Nicolas Sebastian Vidal]
* 283162de 2018-05-15 | This features were hanging around but the functionality no longer exists. (#2378) [Nicolas Sebastian Vidal]
* 20b3cb0e 2018-05-15 | fix failing test step (#2377) [Mariam Ahhttouche]
* 15d34e9c 2018-05-15 | Set tests to fail on first_try (#2376) [Nick Schimek]
* ee2b5cd8 2018-05-14 | Bump brakeman from 4.2.1 to 4.3.0 (#2372) [dependabot[bot]]
* ca4453a0 2018-05-14 | Bump puffing-billy from 1.1.0 to 1.1.1 (#2371) [dependabot[bot]]
* 31b77d6c 2018-05-14 | Bump airbrake from 7.3.2 to 7.3.3 (#2370) [dependabot[bot]]
* ac4c01ac 2018-05-14 | Add email interceptor (#2363) [Mariam Ahhttouche]
* cbd44555 2018-05-10 | Bump selenium-webdriver from 3.11.0 to 3.12.0 (#2353) (origin/staging, staging, master) [dependabot[bot]]
* f0b9cff5 2018-05-10 | Bump rack-cache from 1.7.1 to 1.7.2 (#2358) [dependabot[bot]]
* f11cf62b 2018-05-10 | Update flash spec step (#2359) [Mariam Ahhttouche]
* 4d4a5c38 2018-05-09 | Fix failing test (#2356) [Mariam Ahhttouche]
* 524b1d09 2018-05-09 | change css for static pages line hieght fixes #2297 (#2357) [Duffy]
* b222983d 2018-05-09 | Change env to Rails.env (#2352) [Mariam Ahhttouche]
* af07ff40 2018-05-09 | Bump octokit from 4.8.0 to 4.9.0 (#2354) [dependabot[bot]]
* a8546cb8 2018-05-09 | Bump airbrake from 7.3.1 to 7.3.2 (#2355) [dependabot[bot]]
* 2fbd4e7c 2018-05-09 | Show creator and modifier info on event show page (#2347) [Nick Schimek]
* 7a60c0bd 2018-05-08 | Bump stripe-ruby-mock from 2.5.3 to 2.5.4 (#2351) [dependabot[bot]]
* 9fe358c9 2018-05-07 | Bump capybara-screenshot from 1.0.19 to 1.0.21 (#2349) [dependabot[bot]]
* 121a795d 2018-05-07 | Bump airbrake from 7.3.0 to 7.3.1 (#2348) [dependabot[bot]]
* 1902caa2 2018-05-07 | Fix schema. (#2350) [Nicolas Sebastian Vidal]
* 4c5a1aa4 2018-05-07 | Adjust email about projects joins (#2344) [Mariam Ahhttouche]
* 94cc8069 2018-05-04 | Remove chatlio from websiteone. (#2346) [Nicolas Sebastian Vidal]
* 79842ce6 2018-05-03 | Load README from GitHub (#2302) [Nicolas Sebastian Vidal]
* 55e86bf8 2018-05-03 | Allow users to adjust their email preferences (particularly for project joins) (#2336) (origin/master) [Mariam Ahhttouche]
```

VSCode allows me to identify the point that letter opener was added super fast:

![](https://dl.dropbox.com/s/oju7bsvije5c5f6/Screenshot%202018-05-15%2010.05.26.png?dl=0)

I check back out to just before that point and run one of the tests that failing on develop, but still failing ...

```
$ git checkout 94cc8069
$ be rspec ./spec/lib/agile_ventures_bulk_mailer_spec.rb:29
```

okay git rebase it is then.  I think I have to identity a point when then test passes first - I try to go further back, but even a couple of years back that test is failing, while it is passing for Nico on his local machine.

They are failing in this CI https://semaphoreci.com/agileventures/websiteone/branches/feature-gdpr_compliance-2216/builds/10

did I accidentally merge my GDPR into develop locally?  I trash my local develop branch and re grab from the remote:

```
[tansaku@Samuels-MBP:~/Documents/Github/AgileVentures/WebsiteOne (staging)]$ 
→ git branch -D develop
Deleted branch develop (was 4cdd5db1).
[tansaku@Samuels-MBP:~/Documents/Github/AgileVentures/WebsiteOne (staging)]$ 
→ git checkout origin/develop
Note: checking out 'origin/develop'.

You are in 'detached HEAD' state. You can look around, make experimental
changes and commit them, and you can discard any commits you make in this
state without impacting any branches by performing another checkout.

If you want to create a new branch to retain commits you create, you may
do so (now or later) by using -b with the checkout command again. Example:

  git checkout -b <new-branch-name>

HEAD is now at 4cdd5db1... VCR got updated when working github related stories. (#2379)
[tansaku@Samuels-MBP:~/Documents/Github/AgileVentures/WebsiteOne ((4cdd5db1...))]$ 
→ git checkout -b develop
Switched to a new branch 'develop'
```

but still the same failure, and it passes on Mariam's machine too.  So I guess I could try:

1) reboot
2) fresh install of WSO
3) dive into the error

I choose 3) and I think I've worked it out. I think it's my db - I'm migrated to set the receive_mailings to be false - and it's breaking all this send_newsletter stuff that was never used ... moving to other commits is not affecting that.  Urgh databases.  Hmm, so I end up stripping out the entire legacy newsletter infrastructure, and all the remaining specs pass.  I push that up for our CI to run.  I guess I'll kick off the cukes locally.  Let's start with the sign up features - gah, signing up with no consent is failing ..., but that's down to a silly error in my test set up.  I wish calling:

```rb
create_visitor(receive_mailings: !consent.nil?)
```

would match with:

```rb
def create_visitor(receive_mailings = false)
```

I should probably be looking up ruby named variables.  Seem like it should work if we did:

```rb
def create_visitor(receive_mailings: false)
```

Yes, that works and we've nicely named all the parameters.  Next it's coordinate with the other developers in slack to sort out some issues that we're having getting all the cucumber tests to pass.  Seems like we did it and develop is now green.  Need to move on and get this out into production.  It works in production, but all the Github and Google signups are now going to default to not giving email consent ...
