---
title: AV EcoSystem Review Rumours of AgileBots Demise
date: 2017-10-03
tags: 
author: Sam Joseph
---

![demise](../images/demise.jpg)

Early to bed and early to rise makes a man ... do a 5K jog to the top of the nearest hill and get to the computer early to start working on tests of the new rubyfied agilebot.  I start by pulling the latest from develop and merging to my local branch.  Let's start by hitting each of the three regression errors I saw yesterday:

```sh
  1) SlackService.post_yt_link sends a post request to the agile-bot with the proper data
     Failure/Error: elsif hangout.type == "PairProgramming"
     
     NoMethodError:
       undefined method `type' for #<EventInstance:0x007f9a4d36c5b0>
     # /Users/tansaku/.rvm/gems/ruby-2.3.1/gems/activemodel-4.2.8/lib/active_model/attribute_methods.rb:433:in `method_missing'
     # ./app/services/slack_service.rb:62:in `post_yt_link'
     # ./spec/services/slack_service_spec.rb:52:in `block (3 levels) in <top (required)>'
```

That's from this test:

```rb
    it 'sends a post request to the agile-bot with the proper data' do
      hangout.yt_video_id = 'mock_url'
      hangout.project = Project.create( slug: 'localsupport', title: 'Local Support', description: 'hmmm', status: 'active')

      subject.post_yt_link(hangout)

      assert_requested(:post, 'https://agile-bot.herokuapp.com/hubot/hangouts-video-notify', times: 1) do |req|
        expect(req.body).to eq "title=MockEvent&video=https%3A%2F%2Fyoutu.be%2Fmock_url&type=PairProgramming&host_name=random&host_avatar=#{gravatar}&project=local-support"
      end
    end
```

Which happens to highlight a code path that I hadn't tested yet, where I'm still using the AgileBot CoffeeScript variable name. I can fix that easily by moving to:

```rb
  def post_yt_link(hangout)
    return unless Features.slack.notifications.enabled
    return if hangout.yt_video_id.blank?

    video = "https://youtu.be/#{hangout.yt_video_id}"

    if hangout.category == "Scrum"
      send_slack_message client, CHANNELS[:general], "Video/Livestream for #{hangout.title}: #{video}", hangout.user
    elsif hangout.category == "PairProgramming" # <-- problem was `hangout.type` here
      channel = channel_for_project(hangout.project)
      unless channel == CHANNELS[:cs169]
        send_slack_message client, CHANNELS[:general], "Video/Livestream for #{hangout.title}: #{video}", hangout.user
        send_slack_message client, channel, "Video/Livestream for #{hangout.title}: #{video}", hangout.user
      end
    end

  end
```

and then I get a new error:

```sh
  1) SlackService.post_yt_link sends a post request to the agile-bot with the proper data
     Failure/Error: send_slack_message client, CHANNELS[:general], "Video/Livestream for #{hangout.title}: #{video}", hangout.user
     
     NameError:
       undefined local variable or method `client' for SlackService:Module
     # ./app/services/slack_service.rb:65:in `post_yt_link'
     # ./spec/services/slack_service_spec.rb:52:in `block (3 levels) in <top (required)>'
```

Which is another easy fix (another code path not tested) - by adding the following code:

```rb
client = Slack::Web::Client.new
```

Of course part of me (and maybe you) is screaming, don't fix the regressions, you should be working out precisely the tests needed for the model you actually have, and worrying about regressions later.  However I can't stand the idea that I replicate a load of the existing tests, and then have to process those regression errors later.  Running through the regression errors gets my head back in the space - it's less effort mentally, bad perhaps, but I'm doing this in blogging fragments each morning.  No one is paying me big bucks to spend proper programming days on this.  I've got to go with what I can manage.  Also, and perhaps more importantly, the existing tests are supposed to be proper checks of what we want from this service, and that's not changing.

Anyhow, now I get another error:

```
  1) SlackService.post_yt_link sends a post request to the agile-bot with the proper data
     Failure/Error: client.chat_postMessage(channel: channel, text: text, username: user.display_name, icon_url: user.gravatar_url, link_names: 1)
     
     UncaughtThrowError:
       uncaught throw #<ArgumentError: Required arguments :channel missing>
     # /Users/tansaku/.rvm/gems/ruby-2.3.1/gems/slack-ruby-client-0.7.7/lib/slack/web/api/endpoints/chat.rb:69:in `throw'
     # /Users/tansaku/.rvm/gems/ruby-2.3.1/gems/slack-ruby-client-0.7.7/lib/slack/web/api/endpoints/chat.rb:69:in `chat_postMessage'
     # ./app/services/slack_service.rb:38:in `send_slack_message'
     # ./app/services/slack_service.rb:68:in `post_yt_link'
     # ./spec/services/slack_service_spec.rb:52:in `block (3 levels) in <top (required)>'
```

Which looks a little more complicated, but is actually just another chunk of code missing from the youtube posting path (which I hadn't tested manually).  The following should fix that:

```rb
channel = channel_for_project(hangout.project)
```
but it doesn't because the project created for the test doesn't have a corresponding channel in the test fixtures.  There's a number of changes that I could make to the large test `describe` block to fix all this:

```rb
  describe '.post_yt_link' do
    before { stub_request(:post, 'https://agile-bot.herokuapp.com/hubot/hangouts-video-notify') }

    let(:hangout) { EventInstance.create(title: 'MockEvent', category: "PairProgramming", hangout_url: "mock_url", user: user) }

    it 'sends a post request to the agile-bot with the proper data' do
      hangout.yt_video_id = 'mock_url'
      hangout.project = Project.create( slug: 'localsupport', title: 'Local Support', description: 'hmmm', status: 'active')

      subject.post_yt_link(hangout)

      assert_requested(:post, 'https://agile-bot.herokuapp.com/hubot/hangouts-video-notify', times: 1) do |req|
        expect(req.body).to eq "title=MockEvent&video=https%3A%2F%2Fyoutu.be%2Fmock_url&type=PairProgramming&host_name=random&host_avatar=#{gravatar}&project=local-support"
      end
    end

    it 'does not post youtube video link if yt_video_id is blank' do
      hangout.yt_video_id = '    '

      subject.post_yt_link(hangout)

      assert_not_requested(:post, 'https://agile-bot.herokuapp.com/hubot/hangouts-video-notify')
    end
  end
```

but the looming question is what level should these tests stub at.  Options are:

1) stub the internal method of the class under test
2) stub at the level of the Slack client
3) stub at the level of the post request to the Slack API

I could make arguments for each.  It's tempting to go for 3) because the existing test is stubbing there, but I feel that's the job of an acceptance test, which I hope we already have.  Stubbing at the internal method level feels too fine grained.  I'm going to use dependency injection and go for 2), stubbing at the level of the Slack client.  I change the method signature like so:

```rb
def post_yt_link(hangout, client = Slack::Web::Client.new)
```

and the tests like so:

```rb
  describe '.post_yt_link' do
    let(:hangout) { EventInstance.create(title: 'MockEvent', category: "PairProgramming", hangout_url: "mock_url", user: user) }
    let(:client) { spy(:slack_client) }

    it 'sends a post request to the agile-bot with the proper data' do
      hangout.yt_video_id = 'mock_url'
      hangout.project = Project.create( slug: 'websiteone', title: 'WebSiteOne', description: 'hmmm', status: 'active')

      subject.post_yt_link(hangout, client)

      expect(client).to have_received(:chat_postMessage).with(channel: 'C29J4QQ9W', text: '', username: '', icon_url: '', link_names: 1)
    end

    it 'does not post youtube video link if yt_video_id is blank' do
      hangout.yt_video_id = '    '
      subject.post_yt_link(hangout, client)

      expect(client).not_to have_received(:chat_postMessage)
    end
  end
```

Off the top of my head I'm not sure exactly what all the componets are that the client should receive as argument components, so I'm just putting in blanks and will run it and pull out the exact elements, checking that they are what I expect, and then hopefully make the test go green; ... and I get what I'm expecting:

```
  1) SlackService.post_yt_link sends a post request to the agile-bot with the proper data
     Failure/Error: expect(client).to have_received(:chat_postMessage).with(channel: 'C29J4QQ9W', text: '', username: '', icon_url: '', link_names: 1)
     
       #<Double :slack_client> received :chat_postMessage with unexpected arguments
         expected: ({:channel=>"C29J4QQ9W", :text=>"", :username=>"", :icon_url=>"", :link_names=>1})
              got: ({:channel=>"C0TLAE1MH", :text=>"Video/Livestream for MockEvent: https://youtu.be/mock_url", :username...con_url=>"https://www.gravatar.com/avatar/47548e7f026bc689ba743b2af2d391ee?d=retro", :link_names=>1}) (1 time)
                   ({:channel=>"C29J4QQ9W", :text=>"Video/Livestream for MockEvent: https://youtu.be/mock_url", :username...con_url=>"https://www.gravatar.com/avatar/47548e7f026bc689ba743b2af2d391ee?d=retro", :link_names=>1}) (1 time)
     # ./spec/services/slack_service_spec.rb:53:in `block (3 levels) in <top (required)>'
```

which reminds me that actually we hit the end point twice, but I can also test for that explcitly like so:

```rb
    let(:expected_post_args) do
      {
        text: 'Video/Livestream for MockEvent: https://youtu.be/mock_url',
        username: user.display_name,
        icon_url: user.gravatar_url,
        link_names: 1
      }
    end

    it 'sends the correct slack message to the correct channels' do
      hangout.yt_video_id = 'mock_url'
      hangout.project = Project.create(slug: 'websiteone', title: 'WebSiteOne', description: 'hmmm', status: 'active')

      subject.post_yt_link(hangout, client)

      expect(client).to have_received(:chat_postMessage).with(expected_post_args.merge!(channel: 'C0TLAE1MH'))
      expect(client).to have_received(:chat_postMessage).with(expected_post_args.merge!(channel: 'C29J4QQ9W'))
    end
```

Although I have to admit, there were a few rounds of thrashing to get there :-)  So I happened to start on the `.post_yt_link` method, and so basically I'll now need to do similar for the `.post_hangout_notification` method tests, although I'm feeling a little nervous about using spies, since I'd like to be checking that we don't send any messages we don't expect:

```rb
  describe '.post_hangout_notification' do
    let(:hangout) { EventInstance.create(title: 'MockEvent', category: "PairProgramming", hangout_url: "mock_url", user: user) }
    let(:client) { spy(:slack_client) }

    it 'sends the correct slack message to the correct channels' do
      hangout.project = Project.create(slug: 'cs169', title: 'Edx', description: 'hmm', status: 'active')

      subject.post_hangout_notification(hangout)

      expect(client).to have_received(:chat_postMessage).with(expected_post_args.merge!(channel: 'C0TLAE1MH'))
      expect(client).to have_received(:chat_postMessage).with(expected_post_args.merge!(channel: 'C29J4CYA2'))
    end

    it 'does not fail when event has no associated project' do
      subject.post_hangout_notification(hangout)

      expect(client).to have_received(:chat_postMessage).with(expected_post_args.merge!(channel: 'C0TLAE1MH'))
    end

    it 'does not post notification if hangout url is blank' do
      hangout.hangout_url = "     "

      subject.post_hangout_notification(hangout)

      expect(client).not_to have_received(:chat_postMessage)
    end
  end
```  
    
but I have some more general errors still from untested codepaths:

```
  1) SlackService.post_hangout_notification sends the correct slack message to the correct channels
     Failure/Error: send_slack_message client, CHANNELS[:general], "#{hangout.title}: #{hangout.link}", hangout.user
     
     NoMethodError:
       undefined method `link' for #<EventInstance:0x007fe269d2ca50>
     # /Users/tansaku/.rvm/gems/ruby-2.3.1/gems/activemodel-4.2.8/lib/active_model/attribute_methods.rb:433:in `method_missing'
     # ./app/services/slack_service.rb:23:in `post_hangout_notification'
     # ./spec/services/slack_service_spec.rb:18:in `block (3 levels) in <top (required)>'

  2) SlackService.post_hangout_notification does not fail when event has no associated project
     Failure/Error: CHANNELS[project.try(:slug).to_sym]
     
     NoMethodError:
       undefined method `to_sym' for nil:NilClass
       Did you mean?  to_s
     # ./app/services/slack_service.rb:49:in `channel_for_project'
     # ./app/services/slack_service.rb:11:in `post_hangout_notification'
     # ./spec/services/slack_service_spec.rb:25:in `block (3 levels) in <top (required)>'
```

which are some little syntax misses of mine.  I fix those and then after a lot of hammering I end up with this:

```rb
describe SlackService do
  subject(:slack_service) { SlackService }

  let(:user) { User.new email: 'random@random.com' }

  before { Features.slack.notifications.enabled = true }

  describe '.post_hangout_notification' do
    let(:hangout) { EventInstance.create(title: 'MockEvent', category: "PairProgramming", hangout_url: "mock_url", user: user) }
    let(:client) { double(:slack_client) }

    # @TODO Improve the message format to hide the URL details ... and welcome newcomers ...
    # @TODO we should pass in CHANNELS and GITTER ROOMS work out a way to handle that based on deployed instance, e.g staging, production etc.

    let(:general_channel_post_args) do
      {
          channel: 'C0TLAE1MH',
          text: 'MockEvent: mock_url',
          username: user.display_name,
          icon_url: user.gravatar_url,
          link_names: 1
      }
    end
    let(:project_channel_post_args) do
      {
          channel: 'C29J4QQ9W',
          text: '@here MockEvent: mock_url',
          username: user.display_name,
          icon_url: user.gravatar_url,
          link_names: 1
      }
    end
    let(:pairing_notifications_channel_post_args) do
      {
          channel: 'C29J3DPGW',
          text: '@channel MockEvent: mock_url',
          username: user.display_name,
          icon_url: user.gravatar_url,
          link_names: 1
      }
    end

    it 'sends the correct slack message to the correct channels for pairing' do
      hangout.project = Project.create(title: 'websiteone', description: 'hmm', status: 'active')

      expect(client).to receive(:chat_postMessage).with(general_channel_post_args)
      expect(client).to receive(:chat_postMessage).with(project_channel_post_args)
      expect(client).to receive(:chat_postMessage).with(pairing_notifications_channel_post_args)

      slack_service.post_hangout_notification(hangout, client)
    end

    it 'does not fail when event has no associated project for pairing' do

      expect(client).to receive(:chat_postMessage).with(general_channel_post_args)
      expect(client).to receive(:chat_postMessage).with(pairing_notifications_channel_post_args)

      slack_service.post_hangout_notification(hangout, client)
    end

    xit 'should ping gitter when the project is cs169'
    xit 'sends the correct slack message to the correct channels for scrums'
    xit 'does not fail when event has no associated project for scrums'
    xit 'does not post notification if hangout url is blank for scrums'

    it 'does not post notification if hangout url is blank for pairing' do
      hangout.hangout_url = "     "

      expect(client).not_to receive(:chat_postMessage)

      slack_service.post_hangout_notification(hangout)
    end
  end

  describe '.post_yt_link' do
    let(:hangout) { EventInstance.create(title: 'MockEvent', category: "PairProgramming", hangout_url: "mock_url", user: user) }
    let(:client) { spy(:slack_client) }
    let(:expected_post_args) do
      {
        text: 'Video/Livestream for MockEvent: https://youtu.be/mock_url',
        username: user.display_name,
        icon_url: user.gravatar_url,
        link_names: 1
      }
    end

    it 'sends the correct slack message to the correct channels for pairing' do
      hangout.yt_video_id = 'mock_url'
      hangout.project = Project.create(slug: 'websiteone', title: 'WebSiteOne', description: 'hmmm', status: 'active')

      slack_service.post_yt_link(hangout, client)

      expect(client).to have_received(:chat_postMessage).with(expected_post_args.merge!(channel: 'C0TLAE1MH'))
      expect(client).to have_received(:chat_postMessage).with(expected_post_args.merge!(channel: 'C29J4QQ9W'))
    end

    xit 'sends the correct slack message to the correct channels for scrums'
    xit 'does not post youtube video link if yt_video_id is blank for scrums'

    it 'does not post youtube video link if yt_video_id is blank for pairing' do
      hangout.yt_video_id = '    '
      slack_service.post_yt_link(hangout, client)

      expect(client).not_to have_received(:chat_postMessage)
    end
  end
end
```

For some reason I can't seem to move away from spies on the yt_link posts, but everything is green and I've added pending tests for all the other paths we need to check and TODOs for the other improvements we need.  Hopefully a fresh look tomorrow will get this to the point we can try out on staging ...



