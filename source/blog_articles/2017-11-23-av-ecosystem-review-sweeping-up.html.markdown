---
title: AV EcoSystem Review Sweeping Up
author: Sam Joseph
---

![broom](../images/broom.jpg)

So confused about where to direct my efforts.  Maybe it doesn't matter?  Maybe I should be avoiding code like Avdi Grimm?  I'm going to start by trying to clear up all the dependabot PRs.

I just used my administrator privileges to merge in upgrades to:

* [Brakeman](https://github.com/AgileVentures/WebsiteOne/pull/1898)
* [Bullet](https://github.com/AgileVentures/WebsiteOne/pull/1899)
* [Bundler-Audit](https://github.com/AgileVentures/WebsiteOne/pull/1900)
* [Jasmine](https://github.com/AgileVentures/WebsiteOne/pull/1901)

I'm not really clear what the first three gems do.  Jasmine allows us to unit test our JavaScript.  Trying to update all these four in succession, leading to merges getting stuck through the GitHub UI.  I've banged on them and I think they're all through now.  I pull those changes locally to update my gems locally.

One project manager experienced a 500 error last night that I assume was performance related, but we get nothing from systems like AirBrake that are supposed to tell us about these things.  I sort of feel like for a system of this size, what we need is four full time engineers to keep maintaining it all.  Maybe upgrading to Rails 5 is the key?  Maybe spending no time with my family? Or maybe re-doing the whole thing in WordPress like Avdi and Chuck ...

Anyway, I'm putting my head down and getting every dependabot automated gem upgrade PR through now.  I generally shy away from making multiple changes like this, but I'm sick of the dependabot PRs drowning out the human PRs.  Of course even once I've got the green ones through, there are all those that have come through and caught on the CI.  Now some are clearly failing because code needs to change, but others will just be from intermittent jasmine fails.  I think if we could rip out Mercury and replace it with a new editor, and that might get rid of half the intermittent fails, and maybe clear the way to get onto Rails 5.  However, I am unclear precisely what Rails 5 really buys us - maybe the ability to use websockets cleanly and so avoid a lot of stale data for our users in their browsers.  That said, realistically we could fix some of the current staleness issues with timers.

The difficultly with this dependabot system is that we've got hundreds of gems and there's always going to be gem versions being bumped.  So even if I get through the current lot, there'll be more to come.  Perhaps the trick is to get new members merging these PRs?  Although that would mean needing to give them merge privileges ... the ones where there's work required are generally tricky and not necessarily a good place for new members.  Perhaps just having them take the green PRs and running them locally to double check the CI?

So as I try to merge in more, I get conflicts in the Gemfile and Gemfile.lock, which I try to patch up.  I go to all the other dependabot failing PRs and update their branches.  Am I just doing a very long slow drawn out `bundle update` here?  Every single gem has to be tested with each other, in each combination.  The other issue is that every PR puts a load on our CI and our CI blocks deployment, so I've probably set us up to be unable to deploy anything today.

I've got a chunk of gems upgraded and I'm running the tests locally to check, but I don't have so much faith in the tests. Hmmm.  What I do really want to do is get that new editor that Matt started working on and update the branch.  Matt's been MIA for the longest of the different contributors.  I'm also seeing various messy messages in our build output.  When unclear about direction I guess maybe it's best just to focus on cleaning things up ...

Frustratingly when I run RSpec in progress mode I can't see which tests the deprecation warnings are coming from.  The main ones I'm getting are:

```
DEPRECATION WARNING: You attempted to assign a value which is not explicitly `true` or `false` ("never") to a boolean column. Currently this value casts to `false`. This will change to match Ruby's semantics, and will cast to `true` in Rails 5. If you would like to maintain the current behavior, you should explicitly handle the values you would like cast to `false`. (called from block (3 levels) in <top (required)> at /Users/tansaku/Documents/GitHub/AgileVentures/WebsiteOne/spec/models/event_spec.rb:244)
```

```
Unused parameters passed to Capybara::Queries::SelectorQuery : ["/auth/github"]
```

```
WARNING: Using the `raise_error` matcher without providing a specific error or message risks false positives, since `raise_error` will match when Ruby raises a `NoMethodError`, `NameError` or `ArgumentError`, potentially allowing the expectation to pass without even executing the method you are intending to call. Actual error raised was #<ActiveRecord::StatementInvalid: PG::NotNullViolation: ERROR:  null value in column "user_id" violat...
: UPDATE "authentications" SET "user_id" = $1, "updated_at" = $2 WHERE "authentications"."id" = $3>. Instead consider providing a specific error class or message. This message can be suppressed by setting: `RSpec::Expectations.configuration.on_potential_false_positives = :nothing`. Called from /Users/tansaku/Documents/GitHub/AgileVentures/WebsiteOne/spec/models/authentication_spec.rb:12:in `block (2 levels) in <top (required)>'.
```

Ah, actually some of the error messages are telling me where the problems are arising ...  of course kicking off a cucumber test run locally blocks me from doing any coding work on the project for 10 minutes+ since we still have these stupidly long running tests in places ... I guess I will do my station keeping (social media updates) until they finish...

Only one failure in the cukes:

```
Encountered Error in get: 404 Resource Not Found: {"code":"route_not_found","kind":"error","error":"The path you requested has no valid endpoint."}
```

Having merged the conflict in the Medium-editor branch, I'm not even getting the chance to update the branch, so let's at least try to clear up some cruft in the RSpec output.

The first item seems a little tricky, although it looks like we do need to fix it before we move to Rails 5, and it seems like I can fix it at the spec level as simply as adding this to the event model:

```rb
  def repeat_ends=(repeat_ends)
    super repeat_ends == 'on'
  end
```

and fixing the factories to ensure they used 'on' and 'never' rather than booleans.  The issue here is that the underlying column type for `repeat_ends` is boolean, but the model is working with it as a string.  Of course the real question is how the acceptance tests perform now.  I get a sequence of errors - if I'm lucky they are all about factories (being used in the Cukes).  They are not - they are actually using the full form, and we have javascript running in the browser to set things up - and it's not showing certain fields.  There's some complexity to fix all this, and even if I have the tests passing, I won't be sure I haven't introduced a bug into our events management.

Fixing the `raise_error` issue is a trivial change to the spec:

```rb
  it 'must have an associated user' do
    # Bryan: validations done at database level to avoid complications, but will raise exceptions
    @auth.user_id = nil
    expect{ @auth.save }.to raise_error ActiveRecord::StatementInvalid # <-- add this error type here
  end
```

And I decide to delete all the view specs, as those warnings are telling me that many of them aren't testing what they should be.  In the ideal world I'd work through every single one and work out where they're corresponding to in terms of missing acceptance test functionality, but part of the overall problem here is not having the resources to maintain so much code.  I think I need to hack and slash a bit to make some kind of progress.  Maybe I can spend tomorrow debugging the events `repeat_ends` issue and I'll have the RSpec output cleaned out.  Not sure that's where my attention is most needed, but when I really can't decide what high level issue to focus on, I guess it's not a bad thing to do some sweeping up ...









