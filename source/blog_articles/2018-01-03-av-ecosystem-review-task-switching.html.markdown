---
title: AV EcoSystem Review Task Switching
author: Sam Joseph
---

![task switching](../images/taskswitching.jpg)

They say that task switching is bad for getting things done.  Clearly some tasks require more concentration, but then there's also the issue of getting bored with the task at hand.  There's also the issue of switching between documenting (blogging?) a task and getting that task done.  Yesterday morning after about an hour (?) of blog-driven work on the APSoc membership database I switched to admin tasks and then the rest of the day.  It was about 5pm before I got some more free programming time and was able to punch in some time on WebSiteOne where I think I had more done test-driving the creation of a `current_subscription` method on our User model.  

Given a free choice I'd probably carry on with that right now, but I have a 5th Jan deadline that I committed to for the APSoc, so I best get that sorted first.  It was a good feeling to sort the "AutoValue" fields on the APSoc membership database so that data could be inserted, but there's something about the interplay between tests and code that I love.  The LibreOffice Base system is not really amenable to unit tests, although for all I know they have unit tests under the hood as part of the development suite.  However designing a client application via the graphical interface does not involve me writing any kind of tests and given the sensitivity of the application I'm not inspired to check out the acceptance testing frameworks that are available.  I burned a lot of time on a private client coding project trying to get comprehensive acceptance testing set up for Electron, and I'm becoming a lot more circumspect about the range of acceptance tests that I'll try to employ these days.

On the WebSiteOne project, I'm in a phase of Domain Driven Design, where I'm tweaking a relationship in the data model that I believe is needed to accurately replicate the nature of our Premium subscriptions.  I've started with making the database model change, and I'm working out from there - I'm in the unit tests for the model, and much as I say I love the interplay, yesterday was tricky where it seemed I had to discard a fast running mocked test for a slower integration test using FactoryGirl. I turned this:

```rb
  context 'look up stripe id from subscription' do
    let(:subscription) { mock_model(Subscription, save: true) }
    before { allow(subscription).to receive(:[]=) }

    it 'asks subscription for identifier' do
      expect(subscription).to receive :identifier
      subject.subscription = subscription
      subject.stripe_customer_id
    end
  end
```

into this:

```rb
  context 'supporting current subscription' do
    subject(:user) { FactoryGirl.create(:user, :with_karma) }
    let(:premium) { FactoryGirl.create(:plan, name: 'Premium') }
    let(:premium_mob) { FactoryGirl.create(:plan, name: 'Premium Mob') }
    let(:payment_source) { PaymentSource::PayPal.create(identifier: '75e') }

    # presence of type (no longer used) in the Subscriptions model is confusing ...
    # should get rid of all the STI classes ...

    let!(:subscription1) { FactoryGirl.create(:subscription, user: user, plan: premium, started_at: 2.days.ago, ended_at: 1.day.ago) }
    let!(:subscription2) { FactoryGirl.create(:subscription, user: user, plan: premium_mob, started_at: 1.day.ago, payment_source: payment_source) }

    it 'returns subscription that has started and has not ended' do
      expect(user.current_subscription.id).to eq subscription2.id
    end

    it 'asks subscription for identifier' do
      expect(subject.stripe_customer_id).to eq '75e'
    end

  end
```
  
I'm a bit uncomfortable with all this FactoryGirl stuff, but I'm exercising the following new method on the user model:

```rb
  def current_subscription
    current_subscriptions = subscriptions.where(ended_at: nil).where('started_at < :now', {now: DateTime.now})
    return nil if current_subscriptions.nil? or current_subscriptions.empty?
    current_subscriptions.first
  end
```

and stubbing it would require a rather long stub chain.  Ironically the APSoc system is also a database running SQL under the hood, and at points I end up writing queries that could be the subject of unit tests, but it's a different beast.  In the WSO code I've just changed what was a unit test into an integration test, and I'm slowly moving up the chain and realising I could have driven this from an acceptance test that ensured the state of subscriptions after an upgrade, although it wouldn't be a black box test ...

Last night I wasn't blog driving the WSO RoR coding as I wanted to move fast, but now I've burnt 30 minutes this morning (mixed in with trying to chat with BT about our intermittment business broadband issues).  Partly the blogging is supposed to help me stay focused on a task, but it can also be a distraction or a great way of thinking through a problem, depending on the circumstances.  So anyway, I've pulled up yesterday's blog with the list of outstanding tasks for the APSoc db.  Let's take a pass over things that might be sticky/tricky:

Outstanding items:

* Completing tab sequence changes
* Local Group sub-table
* Remove various fields from the db
* Reformat the content of Member_No
* Various layout changes
* Couple of UI interaction issues
* Standing Order Details form appears to be a duplicate

Okay, so I think the Member_No change might be the sticky one.  If I recall the client is suggesting making a change in the format of the membership number which might make for a tricky migration.  I'm tempted to just work though the list, making all the changes that are simple, and putting off anything that could take a little longer, specifically the Local Group sub-table and the Member_No thing for the moment.

Although as I look at it in more detail I see they want something else from the Member_No.  This is not the RecNo that is the "primary key".  It's a composite that they want to be built from "Member_Types_Code_Letter + RecNo".  Hmm, should this be a dynamically constructed field?  I'm not sure where the Member_Types_Code_Letter is coming from ... Didn't they just ask the CodeLetter be deleted from the membership types table?

Anyway, I've deleted the fields they didn't want in the membership table, so let's make sure that the membership form still works ... so I can view records and edit them after the table changes.  Here's where acceptance tests might save me from needing to exhaustively test all things like adding a new record ... perhaps I can delete the test one and re-add - that worked fine.

So the biggest surprise is that my work creating the Standing Order form appears to have been lost.  I could have sworn I went through creating that, but this bloody double save requirement is my bane.  It's not enough to click save on the form editor - you also have to click save on the main database page in order to ensure everything is actually saved to disk - insane.  I've just dredged up the memory about how to create the correct subform, and now the app has crashed again.  In my other parallel task BT says they have detected a possible fault on our line and they are looking into it - which is better news.  But LibreOffice is back to the spinning beach ball of death ... time to kill it and restart.  Argh, and now the entire file is corrupted - my entire two days work lost by the look of it ...

I spent 15 minutes kicking things around.  The backup files are all zero size, no go.  I think I'll start again tomorrow ... new process - take a backup each day --- maybe develop in dropbox?

