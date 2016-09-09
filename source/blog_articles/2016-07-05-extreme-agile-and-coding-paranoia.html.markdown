---
title: Extreme Agile and Coding Paranoia
date: 2016-07-05
tags: agile extreme stripe features pullrequest PRs
author: Sam Joseph
---

So Michael was enjoying independence day and I got into some solo programming on the premium payment pathways for the AgileVentures site.  We'd now had two different premium users express slight surprise with the rather spartan payment complete page (it just said "Thanks, you're now an AgileVentures Premium Member!") and the lack of any email notification.  I've been trying to take Agile to the extreme and only ever deliver the absolute minimum to get a feature off the ground.  I'm also following advice that one shouldn't rush to make changes based on every individual piece of user feedback; getting the same feedback from two users gives you a little more confidence that something you are working on is going to be more broadly appreciated.

We'd had an interesting discussion in the previous Friday's kickoff/retrospective about how sites in general were much more polished in terms of their payment frameworks.  Michael had been asking if this policy of extreme Agile was part of the plan.  For me, absolutely yes.  As a charity AgileVentures is extremely resource constrained so doing the absolute minimum makes sense.  Is there a danger that users donating their hard earned cash might feel that we were being less than professional?  Maybe so, but we're at the proof of concept stage; will anyone donate regularly to AgileVentures as a charity, and will they be encouraged by a small pool of professional development benefits?  By getting out the payment framework quickly we've answered that in the affirmative, and now several users have pointed to the same possible improvements, it's time to get right on them.

There are other reasons than being resource-constrained though.  Every time you try and deliver a feature there's a process where you end up saying "well what about this?", and "what about this other thing?".  As the developer you're anticipating how the feature is going to be used, and sometimes you're wrong, and you end up adding something you didn't really need.  Of course sometimes you're right, but even if you are it means your feature PR is more complex - there's more to go wrong, more for your project manager to review, it takes longer and so on.

As a project manager myself (as I've mentioned in other blogs) I would always always always rather receive the simpler pull request first.  There's a kind of zen aesthetic going on.  What is the absolute minimum you can get away with?

Let's be specific. Here's the [feedback](https://github.com/AgileVentures/WebsiteOne/issues/1175) we got from a user signing up for premium that echoed what a previous user had said:

> hey Sam, just signed up for the premium membership, couple of things I noticed:

> * I wasn’t signed in to AgileVentures, how does the premium membership link with my existing account?
* I did not receive any confirmation email for the transaction, which I kind of expect because a monetary transaction was involved
* Having signed up for it, I am redirected to a page that simply says “Thanks, you're now an AgileVentures Premium Member!”, would be nice to have more information about what to do next and what it all means (maybe even restating the premium bonuses as actions)

Working through this I started to update our premium membership [Epic](https://github.com/AgileVentures/WebsiteOne/issues/936)

* [ ] improve sign up process (#1175)
  - [ ] need to be signed in to pay for premium/premium plus
  - [ ] email notification of transaction (#1072)
  - [ ] payment page should have more details about what to do next (#1182)

I created tickets for an [email notification of the transaction](https://github.com/AgileVentures/WebsiteOne/issues/1072), and [adding more details to the payment complete page](https://github.com/AgileVentures/WebsiteOne/issues/1182).  I'm keen to get the payment linked in to the existing free AgileVentures membership, but I judged that as too complex for the time I had available.  What I was looking for was what was the minimal set of changes I could make to improve things based on the users feedback, in the context of what I know about the system, feedback from other users and the project direction I'm pushing for.

Changing the contents of the payment complete page seemed simplest, and perhaps not even worthy of any additional tests.  One of the wrinkles here is that our premium plan has a 7 day free-trial, which is mentioned in the detailed description, but has not been noticed by many.  The Premium Plus plan has no free trial, so users signing up for that have received an email notification of transaction immediately from Stripe, and have not expressed the same concerns about Premium users regarding notification.

Not that that doesn't mean we shouldn't have our own notification email, but clearly the un-noticed 7-day free trial leads to some confusion.  At the risk of my own feature creep I put in a first PR that updated the payment page to mention an AgileVentures mentor would be in touch to ensure the new customer gets all their benefits, and added a note about the free trial to the premium charge page:

https://github.com/AgileVentures/WebsiteOne/pull/1183/files

Re-reading it now I see I should probably also include a link to the benefits list on that page - something I can add later today.  Some might say this PR is ridiculously short, but as a project manager I love it.  I can quickly and easily see the suggested changes.  It's a lovely minimal way of addressing at least part of the issue, but takes almost no time to create.  Not that I'm saying we should stop there - hardly! I imagine many 100's of other changes coming through little by little to make the site really look as professional as possible.  However maybe there are never going to be more than a couple of dozen premium members?  Maybe they'll be many more, but we'll grow slowly, repeatedly getting feedback.  That's a wonderful extreme Agile approach if you ask me.

But I didn't stop there.  I had one small PR in the bag, but felt like the email notifications would be good to knock off.  I could of said, well to email the user I should ensure that the user is logged in, and then use that email to send them confirmation of payment, but we have had various [issues with our sign up](http://blog.agileventures.org/agile-approach-to-login-signup-problems/) (that we are process of fixing), and I'm not in a rush to put other barriers in the way of those who want to contribute.  Ultimately we want everything integrated.  I'm leaning towards highlighting premium, premium plus and mentor member status in user profiles, and if we do start to scale then things need to be automated; but it seems crazy to me to automate before you know there's significant demand.  I get an update from Stripe of the email address of anyone who signs up.  In the context of a couple of dozen users I can reach out to them individually and they might even appreciate the personal touch!

A couple of quick tests showed me that users couldn't use the Stripe payment mechanism without entering an email.  This might be a different email from their AgileVentures user account (assuming they had one), however if the priority is ensuring they receive a confirmation email, then the email they enter to Stripe will do, so I quickly adjusted the existing Cucumber tests of payment to include an email confirmation:

```gherkin
  Scenario: Sign up for premium membership
    Given I visit "/charges/new"
    And I should not see "Sign Me Up For Premium Plus!"
    And I click "Sign Me Up For Premium!"
    When I fill in appropriate card details for premium
    Then I should see "Thanks, you're now an AgileVentures Premium Member!"
    And The user should receive a "Welcome to AgileVentures Premium" email
```

which uses the following step:

```ruby
And /^(?:The user|I) should receive a "(.*?)" email$/ do |subject|
  expect(ActionMailer::Base.deliveries.size).to eq 1
  expect(ActionMailer::Base.deliveries[0].subject).to include(subject)
end
```

The scenario was rather imperative and I wasn't improving things, but I wasn't going to get sidetracked by Cucumber refactoring at this point.  I got my failing acceptance test, and jumped straight in to a unit test of the mailer:

```ruby
describe Mailer do

  describe 'send_premium_payment_complete_message' do
    it 'sends payment complete message' do
      mail = Mailer.send_premium_payment_complete('candice@clemens.com')
      expect(mail.from).to include('info@agileventures.org')
      expect(mail.reply_to).to include('info@agileventures.org')
      expect(mail.to).to include('candice@clemens.com')
      expect(mail.subject).to include('Welcome to AgileVentures Premium')
      expect(mail.body.raw_source).to include('Thanks for signing up for AgileVentures Premium!')
      expect(mail).to have_default_cc_addresses
    end
  end

  # other tests removed for brevity

end
```

I then had a failing unit test moving me through the standard acceptance-unit-test cycle that we know and love.  I adjusted the mailer like so:

```ruby
class Mailer < ActionMailer::Base
  default from: 'info@agileventures.org', reply_to: 'info@agileventures.org', cc: 'support@agileventures.org'

  def send_premium_payment_complete(email)
    mail(to: email, subject: 'Welcome to AgileVentures Premium')
  end

  # other methods removed for brevity
end
```

This got me a bit further and adding a view for the mailer made all the tests go green.  The full contents of the email could then be tuned, but the acceptance test still wasn't green, which required some additional hook up in the controller:

```ruby
  def create
    @plan = params[:plan]

    customer = Stripe::Customer.create(
        email:  params[:stripeEmail],
        source: params[:stripeToken],
        plan:   @plan
    )

    Mailer.send_premium_payment_complete(params[:stripeEmail]).deliver_now

  rescue Stripe::CardError => e
    flash[:error] = e.message
    redirect_to new_charge_path
  end
```

Acceptance test green.  This was a nice simple path round the acceptance-unit-test cycle.  It made me reflect again about whether the unit tests were buying much.  The argument for the acceptance test seems strong - particularly if we make it more declarative, it's great high level documentation of how the system is supposed to behave.  The unit test of the mailer is checking quite a lot more than the acceptance test, ensuring the `reply_to`, from fields etc. are correct.  That also serves as documentation I guess, although less readable.  There's a lot of replication in our mailer tests, and I could see a refactoring there using some shared examples, but I put that off in order to focus on the next feature of adding in a separate confirmation email for premium plus.  That judgement call about when to refactor seems really tricky to me.  There are all these calls to craftsmanship, but I think we have to be super-cautious about biting off more than we can chew in a resource-constrained project.

Still I feel uneasy.  I created a [refactoring issue](https://github.com/AgileVentures/WebsiteOne/issues/1189) to make myself feel better, but I worry that I might be accused of not taking enough pride in my work.  What I almost never hear is my rejoinder that when you DRY something out you are introducing a dependency.  I really like to see things replicated thrice before I bend my back to DRY them.  To an extent I'm more concerned about craftsmanship in the user interface.  Beautifully crafted code is great, but if the end user experience falls short, what's the point?

I'm also aware that clever ways of DRYing out code can actually make things harder for developers coming after us, and as Sandi Metz says, we need to think about refactoring in the context of how many changes we anticipate will be hitting certain areas of the project in the near and middle terms.  I proceeded to repeat the acceptance-unit-cycle test for the premium plus confirmation email which went pretty much as above, and led to a bit more complexity in the controller.  


```ruby

Mailer.send("send_premium#{premiumplus? ? '_plus' : ''}_payment_complete".to_sym, params[:stripeEmail]).deliver_now
```

Having forgone DRYing out the specs, I went for a little refactoring and generated a few private methods like so:

```ruby
  def create
    @plan = params[:plan]

    customer = Stripe::Customer.create(
        email:  params[:stripeEmail],
        source: params[:stripeToken],
        plan:   @plan
    )

    send_acknowledgement_email

  rescue Stripe::CardError => e
    flash[:error] = e.message
    redirect_to new_charge_path
  end

  private

  def premiumplus?
    params[:plan] == 'premiumplus'
  end

  def send_acknowledgement_email
    Mailer.send(acknowledgement_email_template, params[:stripeEmail]).deliver_now
  end

  def acknowledgement_email_template
    "send_premium#{premiumplus? ? '_plus' : ''}_payment_complete".to_sym
  end
```

Refactoring specs is a little nerve-wracking because there's always the danger that you neuter the test.  At least with refactoring test covered code, the tests have your back.  One thing I like about the above is that I'm using method names to document my intention.  The earlier more complex method takes time to digest in your mind.  Is this too many small methods?  Maybe.  It's a style I saw a lot at MakersAcademy and I am on the fence about interpreting code like this.  Well I think I have the most trouble when almost every element in a method is another custom method call.  There's not really any DRYing here.  We use the `premiumplus?` method in some other controller methods.  The `send_acknowledgement_email` and `acknowledgement_email_template` are just using method names as a replacement for commenting the code. They're both only used once.  Lots to think over, but I left it there since more changes are likely soon.  The tests were all green, and so I got a [PR](https://github.com/AgileVentures/WebsiteOne/pull/1184) in.

There were some other areas of paranoia that assailed me afterwards.  Was there a sad path I should have been covering?  Should I have ensured that the mailer method names matched the strings we were using in the URIs to indicate the plan names - which are also IDs in Stripe itself?  That might have made this code cleaner ... is it worth coming back to any of these (or the RSpec refactoring) today, or should we push on with free-tier membership integration?  Is it even worth reflecting on the relative value of each; better to toss a coin and just keep working on whatever I think of next ...? :-)

Whatever I do next I'll be happy as long as it's extremely Agile!







