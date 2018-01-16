Urgh, minor headache again today, drinking lots of water.  Night before last a Halloween sweeties over-indulged child had caused me broken sleep which explains yesterday.  Ended up drinking coffee again, hmm. Somewhat Slack/Email distracted.  Really odd thing is that the fix that I pushed out for the Youtube link over-updates in Slack is that it seems to have fixed the morning "Martin Fowler" scrum, but not the afternoon "Kent Beck" scrum.  Of all the different possible outcomes I could have expected ... that is so unexpected.

I would have imagined that the fix would work, or not work, but not that it would work for one scrum and not the other.  To refresh our memories the fix was that in the recent hangouts methods we were adding the `duration` FixNum and not the actual number of minutes when setting the window that we search for previously running hangouts in:

```rb
  def recent_hangouts
    event_instances
      .where('updated_at BETWEEN ? AND ?', 1.days.ago + duration.minutes, DateTime.now.end_of_day)
      .order(updated_at: :desc)
  end
```

the above code now has `duration.minutes` and everything fits with my mental model, except this working for one scrum and not the other.  I'm drawn to move on to other things, but this niggles me.  Let's import the production data to my local machine - instructions are here:

[https://devcenter.heroku.com/articles/heroku-postgres-import-export](https://devcenter.heroku.com/articles/heroku-postgres-import-export)

```sh
$ heroku pg:backups:capture -r production
$ heroku pg:backups:download -r production
$ pg_restore --verbose --clean --no-acl --no-owner -h localhost -d websiteone_development latest.dump
```

now run the rails console and let's investigate. 

```
[tansaku@Samuels-MBP:~/Documents/Github/AgileVentures/WebsiteOne (develop)]$ 
→ be rails c
Loading development environment (Rails 4.2.10)
[1] pry(main)> DateTime.now.end_of_day
=> Thu, 02 Nov 2017 23:59:59 +0000
```

I wonder if it relates to the time zone on the server?

```
→ heroku run rails c -r production
Running rails c on ⬢ websiteone-production... ⣟ connecting, run.9360 ⢿Standard-1X)
                                              ⣷
Running rails c on ⬢ websiteone-production... up, run.9360 (Standard-1X)
DateTime.now.end_of_day

Loading production environment (Rails 4.2.10)
irb(main):001:0> DateTime.now.end_of_day
=> Thu, 02 Nov 2017 23:59:59 +0000
```

Okay, both server and my local machine running in UTC it seems ...  I potter about looking at the scrums, and I think I know the issue.  We are looking at the `updated_at` time rather than `created_at` and if I push the video attachment later then the previous days scrum is showing up in the active window.  So options include switching like so:

```rb
  def recent_hangouts
    event_instances
      .where('created_at BETWEEN ? AND ?', 1.days.ago + duration.minutes, DateTime.now.end_of_day)
      .order(created_at: :desc)
  end
```

`updated_at` was more important when we had the Google hangout API live pinging our system, which doesn't happen now.  Maybe we can get that again with Jitsi, but even then I'm not sure this `recent_hangouts` method needs to work against the updated_at info - let's make that change and see if if breaks any tests ... while that's running I can think about the test that might expose the flaw ... (all the hangout cukes pass).

Funny running the full set I got a new error I never saw:

```
SecurityError: DOM Exception 18: An attempt was made to break through the security policy of the user agent.

  phantomjs://code/connection.js:9
  phantomjs://code/connection.js:9 in Connection
  phantomjs://code/main.js:8 in Poltergeist
```

In the meantime I think I saw how a simple change to `updated_at` in the database setup for my original bug fix test could expose this wrinkle on the issue ... I working back and forth, but yes, if I set the last `updated_at` value of the previous scrum to after the duration of the event then I see the bug I was getting in production.  So the current bug-wrapping scenario stays as it was, but for good measure I add a comment to the issue in question:

```gherkin
  # wraps bug described in https://github.com/AgileVentures/WebsiteOne/issues/1809
  Scenario: Event doesn't ping old youtube URL
    Given the date is "2014 Feb 5th 6:59am"
    And that we're spying on the SlackService
    When I manually set a hangout link for event "Repeat Scrum"
    Then the Hangout URL is posted in Slack
    And the Youtube URL is not posted in Slack
```

and then having checked that the test captures the bug (perhaps ideally the test itself should explicitly set the `updated_at` value?) and then fixed it again I push things up to see how the CI handles them.  I'll add that change as a refactoring ticket and move on I think - running out of time today.  Really want to get this all into production and have it sorted to put my mind at rest ...

[https://github.com/AgileVentures/WebsiteOne/issues/1939](https://github.com/AgileVentures/WebsiteOne/issues/1939)

As [Cess says "sips coffee, grabs another ticket"](https://medium.com/@cess/agile-ventures-experience-629c0e3028b0) although I'll be grabbing an admin task or two I think ... :-)

In the meantime I think I saw how a simple change to `updated_at` in the database setup for my original bug fix test could expose this wrinkle on the issue ... I working back and forth, but yes, if I set the last `updated_at` value of the previous scrum to after the duration of the event then I see the bug I was getting in production.  So the current bug-wrapping scenario stays as it was, but for good measure I add a comment to the issue in question:

```gherkin
  # wraps bug described in https://github.com/AgileVentures/WebsiteOne/issues/1809
  Scenario: Event doesn't ping old youtube URL
    Given the date is "2014 Feb 5th 6:59am"
    And that we're spying on the SlackService
    When I manually set a hangout link for event "Repeat Scrum"
    Then the Hangout URL is posted in Slack
    And the Youtube URL is not posted in Slack
```

and then having checked that the test captures the bug (perhaps ideally the test itself should explicitly set the `updated_at` value?) and then fixed it again I push things up to see how the CI handles them.  I'll add that change as a refactoring ticket and move on I think - running out of time today.  Really want to get this all into production and have it sorted to put my mind at rest ...


