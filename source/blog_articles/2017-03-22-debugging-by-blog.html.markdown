---
title: Debugging by Blog
date: 2017-03-22
tags: continuous integration, VCR, PuffingBilly, gitignore
author: Sam Joseph
---

![debugging](/images/debugging.png)

Right, on to part 2 of "debugging by blog", my attempt to force myself to work through code blockers while debugging.  Currently it's LocalSupport, where we've had a weird CI failure that wasn't replicated locally for myself or Zmago on our respective machines.  As I poked at it yesterday while "blog debugging" I found that the cached fixture files for VCR and PuffingBilly had been added to .gitignore.  This meant that the CI was hitting the 3rd party endpoints such as Google, Do-It, ReachSkills etc. as part of our CI test runs.  I can see why those folders would get added to .gitignore.  Whenever there is some change in the outgoing network connections from our code, those files will change.  Developers will see all these changes in their `git status` checks and not think them worth checking in.  They may even fully understand that they are creating a network sandbox around our code, but just not want to deal with the hassle of them.

The danger of not checking them in is that when developing a new feature, the cached network responses will be saved on a local developers machine, and then subsequent code changes may not necessarily produce the same outputs if the system was actively connected to the network.  I suspect this is what has happened with Zmago's pull request.  Now that I removed those folders from the .gitignore file and reset the VCR/PuffingBilly fixture caches I am able to replicate the error locally.  Here is the error:

```
[tansaku@Samuels-MBP:~/Documents/Github/AgileVentures/LocalSupport (133663043_add_reachskills_vol_ops)]$ 
→ cucumber features/volunteer_opportunities/reachskills_volunteer_opportunities.feature:32
[Coveralls] Using SimpleCov's 'rails' settings.
[DEPRECATION] `require 'billy/cucumber'` is deprecated. Please use `require 'billy/capybara/cucumber'` instead.
Using the default profile...
@vcr @javascript @billy
Feature: Reachskills volunteer opportunities
  As a member of the public
  So that I can see where organisations with volunteer opportunities are located
  I would like to see a map of reachskills volunteer opportunities

  Background:                                                          # features/volunteer_opportunities/reachskills_volunteer_opportunities.feature:7
    Given that the doit_volunteer_opportunities flag is enabled        # features/step_definitions/volunteer_op_steps.rb:60
    Given that the reachskills_volunteer_opportunities flag is enabled # features/step_definitions/volunteer_op_steps.rb:60
    And I run the import reachskills service                           # features/step_definitions/volunteer_op_steps.rb:35
    And cookies are approved                                           # features/step_definitions/authentication_steps.rb:98

  Scenario: See a list of current reachskills volunteer opportunities with a link to organisation page                        # features/volunteer_opportunities/reachskills_volunteer_opportunities.feature:32
    Given I visit the volunteer opportunities page                                                                            # features/step_definitions/navigation_steps.rb:49
      #    And wait for 3 seconds
    Then the index should contain:                                                                                            # features/step_definitions/basic_steps.rb:521
      | Fundraising Volunteer | The volunteer will be required to work with the fundraising consultant research trusts and corporate grant making foundations appropriate to the ... | Chalkhill Community Centre |
      expected to find text "Fundraising Volunteer" in "Search Text <snip> ..." (RSpec::Expectations::ExpectationNotMetError)
      ./features/step_definitions/basic_steps.rb:525:in `block (3 levels) in <top (required)>'
      ./features/step_definitions/basic_steps.rb:524:in `block (2 levels) in <top (required)>'
      ./features/step_definitions/basic_steps.rb:523:in `each'
      ./features/step_definitions/basic_steps.rb:523:in `/^the index should( not)? contain:$/'
      features/volunteer_opportunities/reachskills_volunteer_opportunities.feature:35:in `Then the index should contain:'
    Then I should see a link to "Chalkhill Community Centre" page "https://reachskills.org.uk/org/chalkhill-community-centre" # features/step_definitions/volunteer_op_steps.rb:80

Failing Scenarios:
cucumber features/volunteer_opportunities/reachskills_volunteer_opportunities.feature:32 # Scenario: See a list of current reachskills volunteer opportunities with a link to organisation page

1 scenario (1 failed)
7 steps (1 failed, 1 skipped, 5 passed)
0m4.523s
```

The problem here is that this test is relying on some data from a ReachSkills opportunity that is no longer on the ReachSkills site.  The test passes if I update the details on the volunteer opportunity that is being checked for in the test.  Now the critical thing is to commit the relevant VCR files into git in along with the test and everything should pass on CI.  Irritatingly, failing test runs and runs of tests on other branches can generate a lot of noise in the VCR/PuffingBilly caches, so I use the following strategy to get ready for a commit:

```
[tansaku@Samuels-MBP:~/Documents/Github/AgileVentures/LocalSupport (133663043_add_reachskills_vol_ops)]$ 
→ rm -rf features/req_cache
[tansaku@Samuels-MBP:~/Documents/Github/AgileVentures/LocalSupport (133663043_add_reachskills_vol_ops)]$ 
→ rm -rf features/vcr_cassettes
[tansaku@Samuels-MBP:~/Documents/Github/AgileVentures/LocalSupport (133663043_add_reachskills_vol_ops)]$ 
→ git checkout features/req_cache
[tansaku@Samuels-MBP:~/Documents/Github/AgileVentures/LocalSupport (133663043_add_reachskills_vol_ops)]$ 
→ git checkout features/vcr_cassettes
```

Although it does get more complicated than that if some files have already been added or committed.  Then I run the working tests to generate the VCR and PuffingBilly cache files which show up in git status like so:

```
[tansaku@Samuels-MBP:~/Documents/Github/AgileVentures/LocalSupport (133663043_add_reachskills_vol_ops)]$ 
→ gst
On branch 133663043_add_reachskills_vol_ops
Your branch is up-to-date with 'zmago/133663043_add_reachskills_vol_ops'.
Changes to be committed:
  (use "git reset HEAD <file>..." to unstage)

	modified:   .gitignore

Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git checkout -- <file>..." to discard changes in working directory)

	modified:   db/schema.rb
	modified:   features/volunteer_opportunities/reachskills_volunteer_opportunities.feature

Untracked files:
  (use "git add <file>..." to include in what will be committed)

	features/req_cache/get_maps.google.com_003bbc256d1363a112f914cfb75db8bfd1ba9186.yml
	features/req_cache/get_maps.google.com_4907fe14430b7875f162d6a087ab9fe810cef4c4.yml
	features/req_cache/get_maps.google.com_4b140e4c1e7dd61c9ed03042a6ea3173b0f93c21.yml
	features/req_cache/get_maps.google.com_59e27d9935cf723e2f772afbbb6222428b8f7303.yml
	features/req_cache/get_maps.google.com_903f013d4d1032501baea6f534b38e924321b4b6.yml
	features/req_cache/get_maps.google.com_9bb449f46f323b9bb6bf3ffb8d4e3d1f4c0bdb9b.yml
	features/req_cache/get_maps.google.com_b10b2089367dc41afe11f20775ff8cd4aad2f31e.yml
	features/req_cache/get_maps.google.com_b43294d3c3efeaa9c1138646be862df98e08a75a.yml
	features/req_cache/get_maps.google.com_d02253d8c9679439fe590dae8bf9b93cadefe83c.yml
	features/req_cache/get_maps.googleapis.com_f0a259f6b2f5f73b6d54fa224b954d14213b7bc0.yml
	features/req_cache/get_maps.gstatic.com_7b3f3d37460a085af155862dc94328ad3916985d.yml
	features/vcr_cassettes/Reachskills_volunteer_opportunities/
```

which I can now add, and things should pass reliably on CI.  So my original diagnosis of a JavaScript timing issue was incorrect.  VCR/PuffingBilly are powerful tools, but it seems rather easy for developers of all skills levels to impale themselves on them.  I'm not sure what the best way forward is.  It reminds me of Donald Norman's "The Design of Everyday Things" in which he points out how an incorrect mental model of something can make its use terribly problematic.  His example is from a fridge thermostat, but I can see exactly the same pattern here.

The real difficulty in some ways is the interaction between the caching of network interaction and the git version management.  We've got some similar issues on WebSiteOne.  More blogs to come on this I think.  I also wanted to get onto outstanding questions on other PRs from Marouen and Olena, but that will need to wait till tomorrow as it looks like the MOOC might be cranking up to go live and so I'm needing to get alot of things in gear very quickly.  At least blogging about the debugging process is helping ensure that I actually get some sticky things unblocked, rather than just navel gazing about why all socio-technical processes seem so hard :-)



