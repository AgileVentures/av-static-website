---
title: AV EcoSystem Review Catching Everything Possible
author: Sam Joseph
---

![confusion](../images/catching.png)

9:20am blogface start - go me!  So I really want to get on to working on jitsi on the server, but I got my teeth into this Slack invite yesterday, and I really want to push up some more robust code that will allow this process of inviting new users into Slack to be re-automated.  Yesterday I seemed to be able to successfully invite someone from the rRails command line on Heroku, which makes it seem like Slack's unofficial admin invite endpoint is still working, but that there is problem with the code when running in production.  Here's the code again:

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

There seemed to be some error when calling the AdminMailer `ActiveJob::SerializationError: Unsupported argument type: NameError` which I don't understand. I google it and there's an [SO post](https://stackoverflow.com/questions/40894727/activejobserializationerror-unsupported-argument-type-time-datetime) on how this might be related to JSON not being able to handled by a job.  Possibly the `slack_error_message` being a nested JSON object that can't be handled by the AdminMailer.

The other thing I was seeing was that from the Rails command line the `Slack::AUTH_TOKEN` is nil, although it definitely worked in the past.  I see we have a couple of closed tickets for previous fixes of this kind of issue:

* [feature switched off](https://github.com/AgileVentures/WebsiteOne/issues/1434]
* [development token expired](https://github.com/AgileVentures/WebsiteOne/issues/1247]

I've renewed the token and the feature is active.  Right now I'm struggling with finding a ticket to attach this to.  I think I'm going to open a chore just to improve the robustness of the SlackInviteJob:

[https://github.com/AgileVentures/WebsiteOne/issues/1962](https://github.com/AgileVentures/WebsiteOne/issues/1962)

and I started an async vote on that.  I'm going to start by reviewing the test for this job:

```rb
describe SlackInviteJob do
  subject { SlackInviteJob.new }
  let(:email) { 'random@random.com' }
  before { stub_const('Slack::AUTH_TOKEN', 'test') }

  it 'sends a post request to invite the user to slack and returns the proper response' do
    stub_request(:post, SlackInviteJob::SLACK_INVITE_URL).
        to_return(body: '{"ok": true}')
    
    response = subject.perform(email)

    expect(response['ok']).to be_truthy
    assert_requested(:post, SlackInviteJob::SLACK_INVITE_URL, times: 1) do |req|
      expect(req.body).to eq "email=#{CGI.escape email}&channels=#{CGI.escape 'C02G8J689,C0285CSUH,C02AA0ARR'}&token=test"
    end
  end

  context 'when invite attempt fails notify admin' do
    let(:error) { StandardError.new 'boom!' }

    before do
      stub_request(:post, SlackInviteJob::SLACK_INVITE_URL).and_raise(error)
    end

    it 'sends the email later' do
      expect(AdminMailer).to receive_message_chain(:failed_to_invite_user_to_slack, :deliver_later)
      subject.perform(email)
    end

    it 'includes information about user and error' do
      allow(AdminMailer).to receive_message_chain(:failed_to_invite_user_to_slack, :deliver_later)
      expect(AdminMailer).to receive(:failed_to_invite_user_to_slack).with(email, error, nil)
      subject.perform(email)
    end
  end

  context 'when slack response not ok should notify admin' do
    before do
      stub_request(:post, SlackInviteJob::SLACK_INVITE_URL)
          .to_return(body: '{"ok": false, "error": "already invited"}')
    end

    it 'sends the email later' do
      expect(AdminMailer).to receive_message_chain(:failed_to_invite_user_to_slack, :deliver_later)
      subject.perform(email)
    end

    it 'includes information about user and error' do
      allow(AdminMailer).to receive_message_chain(:failed_to_invite_user_to_slack, :deliver_later)
      expect(AdminMailer).to receive(:failed_to_invite_user_to_slack).with(email, nil, "already invited")
      subject.perform(email)
    end
  end
end
```

which basically looks okay, and passes locally.  What I'm suspicious of is this `Slack::AUTH_TOKEN`.  We have a Slack initializer:

```rb
Slack.configure do |config|
  config.token = ENV['SLACK_AUTH_TOKEN']
  config.logger = nil
end
```
which is part of the [SlackRubyClient](https://github.com/slack-ruby/slack-ruby-client) that we're using.  There's no mention of `Slack::AUTH_TOKEN` in that gem, and locally I can't get it to work, although I can refer to the token directly:

```
[3] pry(main)> Slack.config
=> Slack::Config
[4] pry(main)> Slack.config.token
=> "xoxb-asdafasdfafafsafsasfasfafsaff"
[5] pry(main)> Slack::AUTH_TOKEN
NameError: uninitialized constant Slack::AUTH_TOKEN
from (pry):5:in `<main>'
```

I think I'm going to follow my first intuition which was to add dependency injection to this gem rather than stubbing the constant as we do.  Also I guess what we don't have is an acceptance test that might flush out this kind of bug.  Hmm.  Let's start with dependency injection ... I go with:

```rb
  def perform(email, token = Slack.config.token)
    raise StandardError('token is nil') if token.nil?
    uri = URI.parse SLACK_INVITE_URL
    response = Net::HTTP.post_form(uri, {
        email: email,
        channels: 'C02G8J689,C0285CSUH,C02AA0ARR',
        token: token
    })
```

I feel like I want better backup for the AdminMailer, but I am really at a loss as to how to defend against this serialization error.  As I google more I get the sense that the problem may come from trying to serialize the thing to deliver later.  Somehow serialising doesn't work for some data structures and that's needed for `deliver_later` so I'm going to try with:

```rb
AdminMailer.failed_to_invite_user_to_slack(email, error.try(:to_json), slack_error_message.try(:to_json)).deliver_later
```
which is now being tested with a partial match:

```rb
expect(AdminMailer).to receive(:failed_to_invite_user_to_slack).with(email, String, nil)
```

because running `to_json` on the error ends up including the full stack chase and the line numbers will vary from machine to machine, so we just match against a String.  Okay, some progress I think - let's get this into production and see what happens :-)
