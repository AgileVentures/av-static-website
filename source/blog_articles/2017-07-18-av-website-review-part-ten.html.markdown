So I think I should step away from the project greeting bots and just check the new sign up flow as it gets deployed.  First thing is that I need reCaptcha keys on the heroku servers:

![](https://www.dropbox.com/s/9wti29f0ihs7s7e/Screenshot%202017-07-18%2009.39.37.png?dl=1)

Fixing that gives us the new reCaptcha sign up page:

![](https://www.dropbox.com/s/hncj4kvhndkebst/Screenshot%202017-07-18%2009.44.07.png?dl=1)

which I manually test and it seems to work on develop and the rest of the site seems okay.  I notice that the new JitSi button hasn't been deployed, which likely means we have another intermittent failure on the CI and indeed we do.  For reference here it is:

```
    Given that there are 25 past scrums # features/step_definitions/scrums_steps.rb:28
      PG::UniqueViolation: ERROR:  duplicate key value violates unique constraint "index_users_on_slug"
      DETAIL:  Key (slug)=(zella-lubowitz) already exists.
      : INSERT INTO "users" ("first_name", "last_name", "email", "encrypted_password", "slug", "bio", "created_at", "updated_at") VALUES ($1, $2, $3, $4, $5, $6, $7, $8) RETURNING "id" (ActiveRecord::RecordNotUnique)
      ./features/step_definitions/scrums_steps.rb:29:in `/^that there are (\d+) past scrums$/'
      features/scrums.feature:8:in `Given that there are 25 past scrums'
      
      
      Failing Scenarios:
cucumber features/scrums.feature:10 # Scenario: Scrums index page renders a timeline of scrums for users to view in descending order
```

It's getting a bit ridiculous recently and it feels like we are back to having a couple of intermittent fails before any CI build will complete.  They are usually JavaScript issues, but the one above is odd because it appears database related, but I guess it could still be some sort of JavaScript race condition.  Michael and I spent a lot of time sandboxing and re-implementing the cukes to reduce variability, but while things got better for a while, the problem is still there.  A short term fix is adding more of the regular culprits to the don't run on CI list.   I wish there was a more consistent approach.  Maybe the Rails 5 upgrade, if we upgrade Capybara etc. that might help ...

I can't now do further testing on the flow through the system (or the new JitSi buttons) until the CI passes again and deploys to either staging or develop.  It's super frustrating when I want to stay focused at resolving usability issues in the interface that I'm getting distracted by the deploy logistics.  I wonder if the better solution is to avoid JavaScript dependent acceptance tests ... The difficulty with intermittent failures on CI is the feedback loop is so slow.  I guess we could start paying Semaphore for faster builds, but I think we'd need to bring in more funds to be able to afford that.

Well in the meantime I can ask all the project maintainers what message they'd like in their greeter bots ...
