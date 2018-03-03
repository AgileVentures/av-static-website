---
title: AV EcoSystem Review Back to Front
tags: 
author: Sam Joseph
---

![Back to Front](../images/back_to_front.jpg)

This morning was back to front as I squeezed in an AgileVentures trustee meeting and LocalSupport client meeting before blogging, all due to time constraints on the part of other meeting participants.  Now I'm struggling to sort my day out alongside processing the "ingressive"/"congressive" concepts introduced by Eugenia Cheng in the latest [greater than code]( https://www.greaterthancode.com/2017/07/05/episode-038-category-theory-for-normal-humans/) podcast that I was listening to.  At the risk of falling into my naval-gazing blogs of last year I start wondering if "ingressive" sports that have winners are "bad" and I agonise about whether the competitive football matches my boys take part in (and I coach for) are all retrograde steps preventing myself and my boys from contributing positively to society.  I do have fantasies of some kind of team activity for youngsters that involves charity work, but I can't immediately work out a way to get the kids excited about it ...

Anyway, at least my work is "congressive", I hope - trying to bring together groups of volunteers to help other charities with technology interventions.  Although often the one to one interactions can end up being "ingressive".  I've been trying so hard to "win" the smart person competition for so long, it's difficult for me to change my spots.  Still, today I'm trying to stab out the ruby-fied agile-bot.  I've not been adding extra publicity to the PR updates I've been doing.  I almost shy away from reviews of this latest code as there's so much to improve, but I can't bear not having an end to end operation that allows me to start tidying up and managing our slack messaging.  So, that naval-gazing aside, where are we?  We have a PR:

[https://github.com/AgileVentures/WebsiteOne/pull/1835](https://github.com/AgileVentures/WebsiteOne/pull/1835)

Let's merge it, which should get it onto develop, but I need it on staging to really check what's going on.  Okay, so maybe that's a window to work out what's happening with dependabot, which is opening new PRs every time a dependency updates.  Noisy at the moment - maybe it will settle down.  Either way I think the need to keep re-starting builds due to our intermittent fails is a real pain, so perhaps I can quickly get this "second run" system that we have on LocalSupport set up on WebSiteOne ...

What we have on Localsupport is a travis build that includes the following:

```
bundle exec rake cucumber:second_try
```

Let's pull in the latest develop updates and get a branch on WSO set up with that:

```
[tansaku@Samuels-MBP:~/Documents/Github/AgileVentures/WebsiteOne (develop)]$ 
→ git checkout -b 1803_setup_cuke_second_run_on_fail
Switched to a new branch '1803_setup_cuke_second_run_on_fail'
```

Now add the following to `lib/tasks/cucumber.rake`:

```rb
    desc 'Run all features, record any failures'
    Cucumber::Rake::Task.new({first_try: 'test:prepare'}, 'run all features and record failures') do |t|
      t.binary = vendored_cucumber_bin
      t.fork = true # You may get faster startup if you set this to false
      t.profile = 'first_try'
    end

    desc 'Run failures if any exist'
    Cucumber::Rake::Task.new({second_try: 'test:prepare'}, 'rerun any recorded failures') do |t|
      t.binary = vendored_cucumber_bin
      t.fork = true # You may get faster startup if you set this to false
      t.profile = 'second_try'
    end
```

and this to `config/cucumber.yml`:

```yml
first_try:  NEVER_FAIL=true  <%= std_opts %> --format rerun --out rerun.txt features --strict --tags ~@wip
second_try: NEVER_FAIL=false <%= std_opts %> @rerun.txt  --strict --tags ~@wip
```

and then we need to see if we adjust the semaphore running options to hit this.  In the process I go into `lib/tasks/ci.rake` and change it so it hits first_try and second_try (and not ci_cucumber) like so:

```rb
unless Rails.env.production?
  require 'rspec/core/rake_task'
  require 'cucumber/rake/task'
  require 'coveralls/rake/task'

  Coveralls::RakeTask.new

  Cucumber::Rake::Task.new(:ci_cucumber) do |t|
    t.cucumber_opts = '--tags ~@intermittent-ci-js-fail'
  end

  namespace :ci do
    desc 'Run Rspec and Cucumber then push coverage report to coveralls'
    task tests: [:spec, 'cucumber:first_try', 'cucumber:second_try', 'coveralls:push']
  end
end
```

I also update semaphore so that the Jasmine tests run first:

![](https://dl.dropbox.com/s/he1c9x865rja5i0/Screenshot%202017-10-06%2011.07.26.png?dl=0)

and I push and see ... the problem is the delayed feedback loop is that I need to wait for an intermittent failure to see if this all works.  If the [PR](https://github.com/AgileVentures/WebsiteOne/pull/1868) passes I guess I merge and see ... and the failing first try has broken the semaphore build:

```
Failing Scenarios:
cucumber -p first_try features/documents.feature:169 # Scenario: Insert media model rejects badly formatted youtube links
```

Ah, we also need the folowing in `features/support/env.rb`:

```rb
# To be used in conjunction with rerun option, so that we don't return a failing
# exit code until the second try fails
at_exit do
  exit 0 if ENV['NEVER_FAIL'] == 'true'
end
```

and we'll see if that works, but it might take the rest of the day ...




