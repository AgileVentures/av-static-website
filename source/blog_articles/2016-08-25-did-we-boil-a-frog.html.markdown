---
title: Did we Boil a Frog?
date: 2016-08-25
tags: html javascript ruby rails performance profiling UX
author: Sam Joseph
---

So there's a story about boiling frogs.  I have no idea if this is true and it doesn't really matter because it's just a metaphor, but apparently you can't just stick a frog in boiling water.  The frog will allegedly leap right out.  However if you take a frog and place it in some normal water and then slowly raise the temperature I hear that the frog will get boiled alive.  Charming; but the point of the story is to point out how we'll often get ourselves out of a difficult situation if the problem is immediately obvious, but we can end up getting stuck with great difficulties if the situation is only deteriorating slowly.

It's difficult because in life, as in coding, there are so many little things competing for our attention.  Which are the ones that are going to end up boiling us, and which are the ones that it is safe to ignore?  It's difficult to tell.  I don't think there's any magic formula to know with certainty what is the right thing to fix/address next.  Anyway, this is all an intro to some trouble we ran into on the LocalSupport project while I was travelling recently.  We'd been getting a fair few pull requests that needed review and merging, but we had an odd thing on the Continuous Integration (CI) where the CI system Travis would sometimes report drops in test coverage, but the build would pass, and the drops in test coverage seemed unrelated to the changes being made in the code.  On careful inspection of the build we'd see that there were actually a few failing tests, but the build was still reported successful, and the test coverage was dropping due to those incomplete tests.

I opened a [ticket](https://github.com/travis-ci/travis-ci/issues/5945) with Travis back in April, and they quickly pointed out that our rake task `test_with_coveralls` was exiting with a zero exit code even when the tests failed.  Exit codes are a Unix construct where any process can end with an integer code, with zero indicating success.  Travis relies on that exit code to indicate whether the test run has been successful or not.  I opened a [chore](https://www.pivotaltracker.com/story/show/119350175) in our tracker system and didn't immediately prioritise it because we could still see the failed tests in the Travis log, and I could push back on the individual PR submitters to get those tests fixed.  A couple of our developers looked into it and turned up that running cucumber in batch was leading to a zero exit code even when tests failed - it wasn't our custom rake task that was the problem - this was lower level.

Still I didn't quite flag the temperature was being raised until merging some PRs while travelling and taking my eye off the ball, I accidentally pulled in some code with failing tests.  I had forgotten to check the detailed Travis stack trace, and that code got out onto production.  Now this didn't actually cause a problem on production per se, because the failing tests were failing due to some missed refactoring steps in the tests themselves - the underlying code was still working.  However we did have an incident on production where an interaction with a third party API failed, and that led me to look back through the PRs and discover the failing tests being pulled in.

So this was a bit of a situation; we had a fairly major production issue that we needed to fix that seemed to be related to a PR with some failing tests, and we also had a non-green commit in master.  The accepted wisdom is that commits to master should always be green, so that any version of master can be reliably deployed, and also so that techniques like `git bisect` can work in order to track down tricky bugs.

I consulted with the other developers on the project and we agreed that we needed to roll back the code to before the buggy PR.  The set of PRs that we'd need to pull in again was relatively small:

```
* 0c73a66 2016-08-06 | Change ambiguous language in admin notification mails (#338) (HEAD -> develop, origin/develop, origin/HEAD) [Brychu]
* 293058b 2016-08-01 | Add users linked to orgs to db:seed (#337) [Brychu]
* 02e16be 2016-07-31 | Edit Heroku config for review app[fixes ##116276111] (#333) [Marouen Bousnina]
* 7c261a0 2016-07-05 | Volunteer location different from org location #78989204 (#325) (origin/staging, origin/master, staging, master) [Zmago Devetak]
```

although we also had a number of unmerged PRs that were going to also require updating to the new develop branch if we rolled back.  A note on our branches, we use a pipeline going from `develop` to `staging` to `master` (where master is what is deployed on production).  Each of these branches is deployed to a particular site:

* Production (master): https://www.harrowcn.org.uk
* Staging (staging): http://staging.harrowcn.org.uk
* Develop (develop): http://develop.harrowcn.org.uk

Pushing code to one of these git branches leads to an automatic deployment of the relevant Heroku instance.  Developers branch off develop to make bug fix or feature branches and then submit pull requests against develop. See our [CONTRIBUTING.md](https://github.com/AgileVentures/LocalSupport/blob/develop/CONTRIBUTING.md) for more details.  Every so often as the project manager and release engineer I'll take `develop` and rebase it into `staging` and then deploy that to look at how the latest `develop` runs with a more realistic replica of the production data, and then if that looks good I'll move that onto master (i.e. `production`).

So we needed to remove our broken tests from `develop`, `staging` and `master`, but by now I realised that these zero exit codes were boiling our project, us and in particular, me! `echo $?` after running any process gives us the exit code, allowing a quick mechanism for testing the result of a run.  Travis had been reliably reporting error fails in the past so I ran a `git bisect` process to work out when the problematic change in exit codes had been introduced.  This was not before I'd flailed around trying to understand what was going on - you can read my notes on that process in this [Cucumber ticket](https://github.com/cucumber/cucumber-ruby/issues/1004).  Ultimately I discovered that it was upgrading to Ruby 2.3.0 that had caused the problem for us.   And upgrading to Ruby 2.3.1 fixed the problem.  Phew! Travis was now reliably reporting failures on PRs again.

I was hoping that was the end of the story, but what happened next was really strange - the Jasmine tests started failing - or rather not running at all - and only on Travis - locally they were fine.  They were failing, saying they couldn't connect to localhost.  I ended up pulling my hair out upgrading other gems, and ultimately [patching](https://github.com/jasmine/jasmine-gem/pull/269) the Jasmine gem so that I could set the jasmine hostname to 127.0.0.1.  Finally I had a PR that would take us back into safe territory.  Shout out to [BanzaiMan](https://github.com/BanzaiMan) at Travis for his tireless support on the many tickets relating to all this.  I'm still not completely sure, but I think what caused the Jasmine tests to fail just after my 2.3.1 upgrade was Travis experimenting with an upgrade to IPv6 and had nothing to do with my changes.  You can read more in these tickets:

* https://github.com/travis-ci/travis-ci/issues/6452
* https://github.com/travis-ci/travis-ci/issues/5945#issuecomment-238966504

And so things are at least somewhat resolved.  We're almost back to where we were in terms of getting the previously merged PRs pulled back in, and in the process we've resolved the production issue with the 3rd party API.  However there's still clean up - and the other open (not merged PRs) that need to be dealt with.  Quite a journey, and I'm not sure if we killed the frog in the process ...






