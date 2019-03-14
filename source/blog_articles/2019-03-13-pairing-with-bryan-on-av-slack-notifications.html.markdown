---
title: Pairing with Bryan on AV Slack Nofifications
author: Sam Joseph
---

So the other day I thought wouldn't it be fun to pair with lots more people all over the world and write about it.  Where did the thought come from?  I've been trying to do more remote pairing for years.  Part of the reason I started AgileVentures was the idea that we could have a global pair programming community.  As part of that community I think I've paired with a hundred or so different people remotely around the world and made a lot of friends in the process.

I've also been working on a "Paironauts" concept for quite a while, a kind of Russian Roulette pairing system that would just drop you into pairing sessions with available people.  Paironauts has faltered due to the lack of a really stable pairing/teleconference solutions that has an API we can build upon.  I've long had a vision of a kind of pairing wall where you could browse at a distance lots of remote pair programming sessions, a bit like you can browse twitch streams.  Imagine the virtual equivalent of an office building filled with rooms with glass doors and walls on one side.  Walking through the building you can pass hundreds of different pairs and mobs doing code work and listen in to each group and decide to hang out and watch or, if permission is given, join mobs or turn pairs into mobs, or start new pairs/mobs with others who are hanging around and browsing what's going on.

Of course all this assumes that everyone's working on open source.  I guess the business model is then for those who need closed source to pay for the remote pairing/mobbing infrastrucure while everyone doing open source gets free access (just like GitHub).  Anyway, alot of the work I've done with the AgileVentures community has involved the additional constraint that the projects should be for a good cause, ideally with a charity client.  I'm often trying to entice folks in to work on our AgileVentures charity projects, and I've come to realise that maybe I'm missing out seeing what's going on elsewhere.

So, the wonderful GreaterThanCode slack community has oodles of lovely people and their own #pairing channel.  I can't quite remember the pique of inspiration, but on Tuesday 5th March posted this:

> hi all @here - I had this idea to try and remote pair with as many people as possible and maybe write a book about it - anyone interested in doing some remote pairing some time?

I'm still stuck trying to finish my book on "Emotional Open Source" so what the hell am I doing?  Starting another project is crazy - but perhaps the two will merge.  If I'm honest I find it difficult working alone for long stretches - I need to interact with people.  It's fun!  I got three quick responses in the channel and I started asking what tech stacks were interested in and as the conversation evolved I realised I really wanted the opportunity to work on other projects that I didn't know anything about.  As a result Bryan and I had a first pairing session.  I'd made a bookable calendar with a slot on Tuesdays, and hopefully it's the start of many more.

I was also fascinated by the [CodeForPhilly](https://codeforphilly.org/) site that Bryan introduced me too.  It seems to have all the project wrangling functionality that I've love AgileVentures to have, and more!  Projects can be proposed and go through a variety of states including "Commenting", "Maintaining", "Prototyping", "Hibernating", "Bootstrapping", "Testing" and "Drifting", and you can browse all the projects by stats, events, tech and topics, wonderful!  Bryan and I identified the [CypherPhilly](https://codeforphilly.org/projects/cypher_philly) project and both got set up with it.  I also found myself wonderfully smoothly into the CypherPhilly slack where we able to get round some early set up bugs, and learn that the project didn't have any open issues that were quite ready to be worked on.

Already I was hugely jealous of what CodeForPhilly has managed to achieve with their site.  It's focused on civic projects for the Philadelphia area and I just wish we had the same for the world - maybe one day the AgileVentures system and interface will get there, we'll see.  Anyway, in the absence of an issue to work on Bryan and I discussed putting off the session, but I'd been coding all day solo on closed source and having created the slot I said let's work on some AgileVentures open source which we got on and did.

As usual the first 30 minutes was dominated by setup, however in fairly short order we got into the [AgileVentures issue](https://github.com/AgileVentures/WebsiteOne/issues/2802) I'd started pairing on the week before with Marian.  Bryan did an initial migration and then we github ponged the code over to me and I wrote a first failing test and pushed back to Bryan.  Bryan got that to pass and then created a new failing test; we were managing a pretty tight ping pong, but our time was up and we had to get on.  You can see the general gist of what we were working on from the evolving spec:

```rb
  context '#slack_channel_codes' do
    context 'default' do
      it 'should return an empty array' do
        expect(hangout.slack_channel_codes).to eq []
      end
    end

    context 'event has slack channels' do
      let(:slack_channel) { FactoryBot.create(:slack_channel, code: 'abc123')}
      let(:event) { FactoryBot.create(:event, slack_channels: [slack_channel])}
      let(:hangout) { FactoryBot.create(:event_instance, event: event)}

      it 'should return the events slack channels' do
        expect(hangout.slack_channel_codes).to eq ['abc123']
      end
    end
  end
```

I was having all sorts of thoughts about factories versus mocks and there was an interesting question about the double indices in the migration:

```rb
class CreateJoinTableEventsSlackChannel < ActiveRecord::Migration[5.2]
  def change
    create_join_table :events, :slack_channels do |t|
      t.index [:event_id, :slack_channel_id], name: "slack_event_channel", unique: true
      t.index [:slack_channel_id, :event_id], name: "slack_channel_name_event", unique: true
    end
  end
end
```
Just looking it up the next morning I guess we are guaranteeing unique pairs, but still not sure if there's something important about declaring indices in both directions?  Some of the session had also been talking with Bryan about the different approaches to make progress on the feature, since part of the code was already in place:

1) deploy current code  
  --> manually adding the contents of the hash to the database  
2) adjust the project edit interface to be able to edit the project slack channel relations (removing the slack_channel field on project)  
3) add events - slack channel relation  
4) add UI to manipulate that  

We had chosen to work on item 3 and focus on the back end to put in place the cleanest solution to the overall problem, which was to ensure that our AgileVentures new member events correctly ping our slack New Members channel when the start, which neccessitates a relation between slack channels and events in our schema.  If you want to know the gory details Bryan very kindly consented to recording and sharing the session.

[![Bryan and Sam Pairing on some Rails code](https://img.youtube.com/vi/YcB4QR4XsqIM/0.jpg)](https://www.youtube.com/watch?v=cB4QR4XsqIM)

it was great fun, and I'm looking forward to more pairing sessions on Bryan, particularly the oppportuniry to get into the CypherPhilly project.  I'm also looking forward to pairing with all the other folks in the GreaterThanCode #pairing channel and discovering just how much more there is out there.   One day maybe I'll get my virtual pairing and mobbing building open to all - one day ...