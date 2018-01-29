---
title: AV EcoSystem Review New Mobs and Communication
author: Sam Joseph
---

![confusion](../images/mob_communication.jpg)


So I'm rushing myself to push out the days schedule on the Slack #general channel as I've scheduled two new mobs to start:

```
@here (note, all times in UTC)

TODAY'S EVENTS
==============

* 10:30 - 11:30 UTC : WebDev (OdinProject) Mob (for Premium Members - free trial if you like us on fb or follow us on twitter)
* 12:00 - 12:15 UTC : "Martin Fowler" Scrum and Pair Hookup - https://www.agileventures.org/events/martin-fowler-scrum-and-pair-hookup
* 13:00 - 14:00 UTC : Deep Learning Mob (for Premium Members - free trial if you like us on fb or follow us on twitter)
* 15:00 - 16:00 UTC: Phoenix 1.3 Guides Daily Mob (FREE) - 
https://www.agileventures.org/events/phoenix-1-3-guides-daily-mob-free
* 15:00 - 15:45 UTC : BetaSaaSers/AutoGraders client meeting - https://www.agileventures.org/events/autograders-client-meeting
* 16:15 - 16:30 UTC : "Kent Beck" Scrum and Pair Hookup - https://www.agileventures.org/events/kent-beck-scrum-and-pair-hookup
* 16:30 - 17:00 UTC : SHF Sprint review and planning - https://www.agileventures.org/events/shf-sprint-review-and-planning

Full details at: https://www.agileventures.org/events
```

I'm probably overloading myself.  Sunday afternoon I did two pairing sessions (one paid, one free trial) and the AV Community meeting and I'm getting set up for more pairing.  In the community meeting Michael said that mobs are a good source of revenue and that caused me to pause sice I think that while they definitely are a source of revenue, for the amount of time involved in supporting them we're not getting a wonderful return, particularly since we made them weekly and we've made the Premium mob fee cover attending any of the currently running mobs ...

I think both the mobs (and the F2F packages - given that they include all the mobs) are extraordinarily good deals compared to the prices of similar services from other online mentoring services; although I guess it's all a matter of opinion.

Either way I'm getting caught between getting station keeping activities done:

a) manual slack invites
b) manual daily schedule posts to Slack
c) gem upgrades on WSO
d) checking social media for follows for free trials

and running free trials and then actual mobs and pairing sessions themselves.  I was half expecting to have private paid projects taking up half my time, but that isn't the case at the moment, so I guess it makes sense to ramp up the mobs and pairing to bring in more AV revenue.

I need to run more Simplify, Eliminate, Delegate, Automate passes on what I'm doing.  I'm still holding on to posting the schedules myself because of the difficultly automating them.  Although they did seem to start working on another Slack instance, ... but I also like being able to tweak them daily.  Posting them also forces me to think about the schedule.  What I could do quickly this morning is actually try to re-automate the Slack invites.  At least run the task from the heroku production command line.  We've been using an undocumented endpoint for a year somewhat reliably, but it seems to have broken:

[https://github.com/slackhq/slack-api-docs/issues/30#issuecomment-341399842](https://github.com/slackhq/slack-api-docs/issues/30#issuecomment-341399842)

and so I've been invited manually through the Slack UI.  Maybe I can see some sort of error if I test via the rails console.  I've been using the following to get the most recent user emails:

```rb
User.last(50).each {|u| puts u.email} ; nil
```

Here's our invite code:

```rb
class SlackInviteJob
  include SuckerPunch::Job

  SLACK_INVITE_URL = 'https://agileventures.slack.com/api/users.admin.invite'

  def perform(email)
    uri = URI.parse SLACK_INVITE_URL
    response = Net::HTTP.post_form(uri, {
        email: email,
        channels: 'C02G8J689,C0285CSUH,C02AA0ARR',
        token: Slack::AUTH_TOKEN
    })
    error = nil
    json_response = JSON.parse response.body
    slack_error_message = json_response['error']
    json_response
  rescue => e
    error = e
  ensure
    unless json_response.present? and json_response['ok']
      AdminMailer.failed_to_invite_user_to_slack(email, error, slack_error_message).deliver_later
    end
  end
end
```

```rb
SlackInviteJob.new().perform('guravareddy.jetlee@gmail.com')
```

Which gives me the following error:

```
ActiveJob::SerializationError: Unsupported argument type: NameError
	from /app/vendor/bundle/ruby/2.3.0/gems/activejob-4.2.10/lib/active_job/arguments.rb:71:in `serialize_argument'
	from /app/vendor/bundle/ruby/2.3.0/gems/activejob-4.2.10/lib/active_job/arguments.rb:35:in `block in serialize'
	from /app/vendor/bundle/ruby/2.3.0/gems/activejob-4.2.10/lib/active_job/arguments.rb:35:in `map'
	from /app/vendor/bundle/ruby/2.3.0/gems/activejob-4.2.10/lib/active_job/arguments.rb:35:in `serialize'
	from /app/vendor/bundle/ruby/2.3.0/gems/activejob-4.2.10/lib/active_job/core.rb:86:in `serialize_arguments'
	from /app/vendor/bundle/ruby/2.3.0/gems/activejob-4.2.10/lib/active_job/core.rb:72:in `serialize'
	from /app/vendor/bundle/ruby/2.3.0/gems/activejob-4.2.10/lib/active_job/queue_adapters/inline_adapter.rb:14:in `enqueue'
	from /app/vendor/bundle/ruby/2.3.0/gems/activejob-4.2.10/lib/active_job/enqueuing.rb:71:in `block in enqueue'
	from /app/vendor/bundle/ruby/2.3.0/gems/activesupport-4.2.10/lib/active_support/callbacks.rb:117:in `call'
	from /app/vendor/bundle/ruby/2.3.0/gems/activesupport-4.2.10/lib/active_support/callbacks.rb:555:in `block (2 levels) in compile'
	from /app/vendor/bundle/ruby/2.3.0/gems/activesupport-4.2.10/lib/active_support/callbacks.rb:505:in `call'
	from /app/vendor/bundle/ruby/2.3.0/gems/activesupport-4.2.10/lib/active_support/callbacks.rb:498:in `block (2 levels) in around'
	from /app/vendor/bundle/ruby/2.3.0/gems/activesupport-4.2.10/lib/active_support/callbacks.rb:343:in `block (2 levels) in simple'
	from /app/vendor/bundle/ruby/2.3.0/gems/activejob-4.2.10/lib/active_job/logging.rb:14:in `block (3 levels) in <module:Logging>'
	from /app/vendor/bundle/ruby/2.3.0/gems/activejob-4.2.10/lib/active_job/logging.rb:43:in `block in tag_logger'
	from /app/vendor/bundle/ruby/2.3.0/gems/activesupport-4.2.10/lib/active_support/tagged_logging.rb:68:in `block in tagged'
```

and that does not make sense to me.  What I'd hope is that all the error handling that we've installed would tell us when things like this are going wrong.  No.  Nor does AirBrake seem to tell us about this kind of stuff.  Configuration is all off I guess.

Ah, okay, there is a piece of our code in the extended error message:

```
	from /app/vendor/bundle/ruby/2.3.0/gems/activesupport-4.2.10/lib/active_support/callbacks.rb:92:in `__run_callbacks__'
	from /app/vendor/bundle/ruby/2.3.0/gems/activesupport-4.2.10/lib/active_support/callbacks.rb:778:in `_run_enqueue_callbacks'
	from /app/vendor/bundle/ruby/2.3.0/gems/activesupport-4.2.10/lib/active_support/callbacks.rb:81:in `run_callbacks'
	from /app/vendor/bundle/ruby/2.3.0/gems/activejob-4.2.10/lib/active_job/enqueuing.rb:67:in `enqueue'
	from /app/vendor/bundle/ruby/2.3.0/gems/activejob-4.2.10/lib/active_job/configured_job.rb:13:in `perform_later'
	from /app/vendor/bundle/ruby/2.3.0/gems/actionmailer-4.2.10/lib/action_mailer/message_delivery.rb:112:in `enqueue_delivery'
	from /app/vendor/bundle/ruby/2.3.0/gems/actionmailer-4.2.10/lib/action_mailer/message_delivery.rb:68:in `deliver_later'
	from /app/app/jobs/slack_invite_job.rb:21:in `perform'
```

It looks like somehow our AdminMailer is failing ... Can I run the internals directly?  I can and it seems like it worked for me, but interestingly I couldn't get the slack auth token via `Slack::AUTH_TOKEN`.  I had to hard code it.  I also tried it with nil, and seemed to be able to get an error message delivered.   I guess we need error catching on the admin mailing, and I need to defend against nil tokens maybe?  Perhaps I can fix that all up tomorrow ...






