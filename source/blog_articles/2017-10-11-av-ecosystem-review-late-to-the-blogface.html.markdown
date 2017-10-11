Well, I am very late to the blogface this morning after doing a 7.5km job and then experimenting with making an eggs benedict for the first time in my life.  To compound it I had two podcasts to post to Twitter and Slack and in an exhausted over-fed state I had little resistance to reviewing all my slack and email messages.  I stopped short of looking at all the twitter notifications.  There's definitely some kind of micro-message addiction going on here.   I can't remember what I was exicted to work on.  Maybe it was clipping the time/dates off the Slack messages.  I'm pretty pleased with how far we've come in dropping the old Agile bot (leading to $21 a month savings), and producing a new Ruby agile-bot as part of our Rails monolith that is now messaging like this:

![](https://dl.dropbox.com/s/wo71jxlx2knvr6m/Screenshot%202017-10-11%2010.31.38.png?dl=1)

I've added what I hope is a welcoming tone to both the reminder and the event title.  I suspect that the time/date stamp is being added to the event instance title, so that the date is available in views like "Previous scrums":

![](https://dl.dropbox.com/s/998m7v4fzmc747h/Screenshot%202017-10-11%2010.33.29.png?dl=1)

but it makes no sense in the context of the skype chat.  We did have one member a bit confused by the new syntax, since the direct links to the hangouts etc. were no longer visible.  Not ideal - perhaps we need a click to join message?  What I'd really like to work out is where in the code flow the date/timestamp is getting added to the event instance title ... a little search and I think it's being set in the form that starts the event instance in the event show page, which uses a topic helper in the EventHelper:

```rb
def topic(event, event_schedule)
  "#{event.name} - #{current_occurrence_time(event_schedule.first(1))}"
end
```

Now rather than changing that - perhaps I can set it up so that the post to Slack uses the event name and not the event instance title.  That would mean adding more mocks to the slack service spec - and also highlights the need for acceptance tests that exercise this pathway (and make the high level motivations clear).  If I'd got to the computer at 9am maybe - right now I just want to get a quick fix and move on.  Particularly I want to move on to even higher level review for Thursday and Friday, so can I slip this out?  Or will I get distracted by replacing the ActiveRecord models in the SlackService spec with mock_models ... which I do, but then I think, actually perhaps displaying the UTC time and day is kind of useful in our Slack since people are in different timezones and often referring to UTC can be helpful as a kind of baseline.  Hmmm, maybe I should just go for a quick refactoring ticket to scratch my code itch?

I've also got the dependabot upgrades to work through ... I've merged in the bump to rails 4.2.10.  I'm wondering if I should just splurge in all the rest that are passing ... well, anyway, I got a chunk of the SlackService spec working with mock_models:

```rb
    context('PairProgramming') do
      let(:websiteone_project) { mock_model Project, slug: 'websiteone' }

      let(:websiteone_hangout) { mock_model EventInstance, title: 'MockEvent', category: "PairProgramming", hangout_url: "mock_url", user: user, project: websiteone_project }

      it 'sends the correct slack message to the correct channels when associated with a project' do
        expect(slack_client).to receive(:chat_postMessage).with(general_channel_post_args)
        expect(slack_client).to receive(:chat_postMessage).with(websiteone_project_channel_post_args)
        expect(slack_client).to receive(:chat_postMessage).with(pairing_notifications_channel_post_args)

        slack_service.post_hangout_notification(websiteone_hangout, slack_client, gitter_client)
      end

      let(:no_project_hangout) { mock_model EventInstance, title: 'MockEvent', category: "PairProgramming", hangout_url: "mock_url", user: user, project: nil }

      it 'does not fail when event has no associated project' do
        expect(slack_client).to receive(:chat_postMessage).with(general_channel_post_args)
        expect(slack_client).to receive(:chat_postMessage).with(pairing_notifications_channel_post_args)

        slack_service.post_hangout_notification(no_project_hangout, slack_client, gitter_client)
      end

      let(:cs169_project) { mock_model Project, slug: 'cs169' }
      let(:cs169_hangout) { mock_model EventInstance, title: 'MockEvent', category: "PairProgramming", hangout_url: "mock_url", user: user, project: cs169_project }

      it 'should ping gitter and slack (but not general) when the project is cs169' do
        expect(gitter_client).to receive(:messages).with('56b8bdffe610378809c070cc', limit: 50).and_return([])
        expect(gitter_client).to receive(:send_message).with('[MockEvent with random](mock_url) is starting NOW!', "56b8bdffe610378809c070cc")
        expect(slack_client).to receive(:chat_postMessage).with(cs169_project_channel_post_args)
        expect(slack_client).to receive(:chat_postMessage).with(pairing_notifications_channel_post_args)

        slack_service.post_hangout_notification(cs169_hangout, slack_client, gitter_client)
      end

      let(:missing_url_hangout) { mock_model EventInstance, title: 'MockEvent', category: "PairProgramming", hangout_url: "  ", user: user }

      it 'does not post notification if hangout url is blank' do
        expect(slack_client).not_to receive(:chat_postMessage)

        slack_service.post_hangout_notification(missing_url_hangout, slack_client, gitter_client)
      end
    end
```

which is kind of an improvement - slightly faster than creating real ActiveRecord models.  Slightly less realistic, so pushing this away from integration and towards unit testing.  Re-arranging deck chairs?  I'd love to boil this code down to make it clean and transparent, but perhaps that's something for a pairing session where my pair partner can keep me on the straight and narrow.  Time to jump back up a few levels, starting tomorrow - let's check in on the changes to the "Getting Started" page and the flow of how people arrive there.
