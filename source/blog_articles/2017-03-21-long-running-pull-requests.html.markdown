So the SSL encryption is all now working on [AgileVentures production site](https://www.agileventures.org/).  The slight login oddities on staging appear to have been just staging oddities.  I think maybe we could address them by re-cloning the production database to staging.  That would require upgrading the database plan there, but I guess we can afford that extra $9 a month to make sure our staging environment is closer to production.  Having staging replicate production as closely as possible is generally a good idea to increase the chances that production bugs can be solved on staging.  I took a risk yesterday pushing out to production, and it could have blown up in my face.  I followed a hunch and it seemed to work out.  I probably should pay fate back by doing that upgrade.

Anyhow, so that means I completed 1 of the 4 story points that I committed to in last weeks WebSiteOne sprint.  I haven't taken on any new points for this next sprint, and will see if I can close the others out this week.  We had the LocalSupport kick-off meeting yesterday and that's not quite following the same format at the WebSiteOne kick-off where the stakeholders bring tickets for discussion and voting.  The big difference is that any AgileVentures community member is a client for WebSiteOne.  For LocalSupport we have a specific charity client who I'm now meeting every two weeks.  Maybe both sprints would be better off being two weeks long rather than a single week, although with WebSiteOne I'm still happy voting every week, even if we don't start on tickets.  That said, it seems like its important to work on tickets soon after you vote on them, otherwise the voting goes stale and it becomes wasted effort.

In the LocalSupport kick-off yesterday there were both new and old PRs that needed to be dealt with.  Rather than voting on new tickets I suggested we focus on getting them sorted.  That gives me an idea for a model for the kick-off meetings going forward, that they could alternate between being voting sessions and board/PR clearing sessions.  I think the danger with fortnightly or bi-weekly meetings is that it's confusing to remember whether the meeting is on or not, and it's simpler and you get better attendance if the meeting is weekly.  Maybe the activity of the meeting can be determined as a function of the state of the project board.

Anyhow, the state of the LocalSupport project was with five open pull requests, three of which were long-running:

* [Post our volunteer ops to Do-it via API key (Marouen)](https://github.com/AgileVentures/LocalSupport/pull/427)
* [Incorporate reachskills volunteer opportunities (Zmago)](https://github.com/AgileVentures/LocalSupport/pull/421)
* [Added unit test and a placeholder for integrated test (Marcelo)](https://github.com/AgileVentures/LocalSupport/pull/424)

The last was a learning activity on a refactoring ticket from Marcelo, and I got his permission to close it and that we would work on it again when he was free.  Marcelo is busy with his day job, and that's totally fair enough.  It's a shame as I think there is a kind of critical mass for learning from pull-requests.  If the gaps between working on something are too great you end up forgeting pieces of the things you were focusing on and the results of earlier effort are lost.  Zmago's PR related to ReachSkills integration is stuck with a failure that's only happening on CI, and Marouen had just finished a big PR for posting volunteer opportunities to the DoIt API.  Marouen needs input and I know it will take me a run up to get into it and give good feedback.  Zmago's CI-only (JavaScript timing?) failure is one of the frustrating long debug cycle issues that's a real pain to fix.  I spent the first part of the kick off outlining these PRs to Mahesh, Sigu, Cess and Stella.  As relative newcomers to the project it would be difficult for these four to move us forward on the outstanding PRs, but I encouraged them to take a look over and comment if they could.  Every little helps :-)

Then the second half of the meeting we focused on the codeclimate issues that were being flagged up on Cess and Stella's pull requests.  They were mainly class length and method length issues.  Reducing class size on the relevant controller would involve pulling out a service; a refactoring that I judged too complicated to get into right there.  I focused on the class length issue and how we could refactor out new methods, and even use before/after actions in the Rails controller.  We wrapped up looking at Mahesh's proposal to add pry.binding support.  I wasn't convinced that pry.binding let us do anything we couldn't do with byebug, but Sigu pointed out that there was no harm in having both, and letting devs choose whichever they prefer.  I agreed, but I wonder if I'd quashed Mahesh's enthusiasm.  That's the old Sam coming back.  I hear statements like "pry.binding let's us step into methods and has code completion", and I know I can do both these things in byebug, so the academic in me wants to show that.  However showing that can be considered a block.  Perhaps better to just to express unalloyed enthusiasm for pry.binding to encourage Mahesh to submit a PR?

I've got another cold and I wasn't on the ball yesterday.  I don't know what the right thing is to do.  My natural flow of mind is just to say what I think, and if something seems unnecessary I say so, but that can quash people's enthusiasm.  Does it follow that learners need to be working on toy projects where it doesn't matter what code they add and an explosion of complexity is a learning experience that doesn't have any negative impact on the end-user?  At some point folks do need to graduate beyond toy projects if they want to increase the chances of building things of value to others.  Balancing the different needs of the different project stakeholders.  In this case I think I should have just expressed enthusiasm for the "offer".  It wasn't worth making the point about byebug works.  It could have been an opportunity for me to learn more about pry.binding.  Instead of demo-ing byebug features I could have had Mahesh work me through the pry.binding install locally and I could have started the PR with him in collaboration.  Oh well, we live and learn :-)

But still, the critial thing that I should have been directing my attention to was Zmago and Marouen's pull requests.  Zmago is a Premium Mob member and Marouen is a Premium mentor.  I'm blogging about this all partly to force myself to analyze the sisues more deeply. Zmago had tried adding some time-outs to his CI failing acceptance tests, but now the CI seemed to be stuck on an unrelated install error.  Zmago had previously given me access to his forked repo, so I thought I'd try to roll back to the previous error and take a closer look.  In the process of doing that I realised I could grab the previous error from the older Travis logs.  What we were getting is this:

```
 Scenario: See a list of current reachskills volunteer opportunities with a link to organisation page                        # features/volunteer_opportunities/reachskills_volunteer_opportunities.feature:32
    Given I visit the volunteer opportunities page                                                                            # features/step_definitions/navigation_steps.rb:49
    Then the index should contain:                                                                                            # features/step_definitions/basic_steps.rb:521
      | Fundraising Volunteer | The volunteer will be required to work with the fundraising consultant research trusts and corporate grant making foundations appropriate to the ... | Chalkhill Community Centre |
      expected to find text "Fundraising Volunteer" in " ...
      
cucumber -p first_try features/volunteer_opportunities/reachskills_volunteer_opportunities.feature:32
```

and

```
Google Geocoding API error: over query limit.
  Scenario: Reachskills volunteer opportunities are opened in a new page # features/volunteer_opportunities/reachskills_volunteer_opportunities.feature:42
    Given I visit the volunteer opportunities page                       # features/step_definitions/navigation_steps.rb:49
    Then I should open "Fundraising Volunteer" in a new window           # features/step_definitions/volunteer_op_steps.rb:98
      Unable to find link "Fundraising Volunteer" (Capybara::ElementNotFound) ...
      
cucumber -p first_try features/volunteer_opportunities/reachskills_volunteer_opportunities.feature:42      
      
```

I noticed a "Google Geocoding API error: over query limit." message - I wondered what was happening with the cache.  The billy and vcr caches should be preventing the CI tests from hitting the real Google server. That lead me to notice that Piotr added the vcr and puffing billy caches to the .gitignore file last November. 

```
features/vcr_cassettes
features/req_cache
```

Probably as part of the bootstrap upgrade.  I hadn't notice.  It's understandable - those changing caches make PRs and checking git status a pain.  I hadn't noticed that change.  Could that be a factor here?  A red herring - even with the time out's removed Zmago's CI was still stuck on installing a gem ...

```
Installing eventmachine_httpserver 0.2.1 with native extensions
```

was that some other weird timeout - unrelated?  CI was passing for Stella and Cess. I decided to remove the vcr and billy fixtures from .gitignore.  That showed me loads of local files in the caches on git status, arising from my local test runs.  I deleted all caches (labelled fixtures) locally and re-checked out from git, and then kicked off a full cucumber test run to bring the local caches up to date.  The plan being to get full caches from a complete test run checked in and then pushed up to CI.  Perhaps that would allow Zmago's PR to pass?  Actually it allowed me to replicate the error locally! ... to be continued!


### Related Videos:

* ["Kent Beck" scrum and LocalSupport kick off](https://www.youtube.com/edit?o=U&video_id=D62ZcBOnNsc)

p.s. for cucumber and rspec I'd really like to adjust the output on CI so that it was progress dots for the passing tests, and full stack traces for the failure - I should investigate or open an [issue](https://github.com/cucumber/cucumber-ruby/issues/new)

p.p.s. opened [one](https://github.com/cucumber/cucumber-ruby/issues/1094)
