---
title: Red Green Refactor
date: 2016-10-27
tags: BDD TDD cucumber step scenario outline mock sandbox stripe rails models inflector
author: Sam Joseph
---



While the AsynVoter folks got down to mobbing on the first feature I got on with some solo-coding on payment end points for the new intermediate level Premium payment plans.  I did want to try out [ruby-stripe-mock](https://github.com/rebelidealist/stripe-ruby-mock) because while I do like the absolutist cover and re-recordability of the vcr/puffing-billy sandbox, the number of additional files that need to be checked in make associated pull requests hard to decipher.  Case in point, the [pull request](https://github.com/AgileVentures/WebsiteOne/pull/1366) generated from the day's solo-coding consists of 132 files, of which only about 10 are files that I edited code in.  It would be great if branch comparisons on GitHub and RubyMine could filter out auto-generated files.

Under the circumstances of having only a little time and wanting to move at speed, I stuck with our sandboxes.  I just needed these endpoints up to see if anyone would actually sponsor us at these new price points for these new offerings.  Maybe [ruby-stripe-mock](https://github.com/rebelidealist/stripe-ruby-mock) would be a simple win, but I was still unsure about whether it would really sandbox the browser interaction.  My questions to the stripe-ruby-mock community in Gitter and and GitHub issues had not gained me much clarity.  I knew the CraftAcademy folks had had some success with the gem, but still I had less than an hour.

I went straight for new feature tests which were almost clones of existing ones:

```gherkin
  Scenario: Sign up for premium mob membership
    Given I visit "/charges/new?plan=premiummob"
    And I should not see "Sign Me Up For Premium!"
    And I click "Sign Me Up For Premium Mob!"
    When I fill in appropriate card details for premium mob
    Then I should see "Thanks, you're now an AgileVentures Premium MOB Member!"
    And The user should receive a "Welcome to AgileVentures Premium MOB" email
```

I now had four like this - ripe for a "Scenario Outline" to DRY things out, but it was not time to refactor yet.  Throughout this session I think I identified a critical point about when to refactor.  Don't refactor when you're in the process of creating new tests or new code.  Refactor when you have that in place and when the tests are green.  Old advice but is blazed bright on my cortex.  Also every time you DRY things out you introduce a dependency, you reduce an axis of freedom that might be needed by the next incoming feature.  Cautious with that DRYing and refactoring.

I commented out the feature for the f2f plan and focused on getting this single mob payment end point.  First stop was the incorrectly named charges controller.  I knew now that really this should be the subscription (or possibly plan?) controller.  Refactor that later.   I adjusted the new method to support a premiummob template:

```rb
  def new
    render 'premiummob' and return if premiummob?
    render 'premiumplus' and return if premiumplus?
    render 'premium'
  end
```

and added a private method:

```rb
  def premiummob?
    params[:plan] == 'premiummob'
  end
```

I noticed that our private `update_user_to_premium` was now out of date, and actually had an unidentified bug that meant new PremiumPlus users would not be represented properly.  I could fix that by creating a `plan_class` method that would correctly identify the plan type.  

```rb
  def update_user_to_premium(stripe_customer)
    return unless current_user
    UpgradeUserToPremium.with(current_user, Time.now, stripe_customer.id, PaymentSource::Stripe, plan_class)
  end

  def plan_class
    return PremiumMob if params[:plan] == 'premiumob'
    return PremiumPlus if params[:plan] == 'premiumplus'
    Premium
  end
```

Really I wanted my feature tests to include some mechanism to check that the correct plan type was being stored in the database, but that would require bleeding in to another feature which would be displaying the plan types correctly for users.  Actually we did have that, but for logged in members, and we still weren't requiring login for these payment end points.  I didn't want problems with account sign up preventing people from sign up, although now we have 20+ people signed up that starts to look a bit paranoid.  I can probably add that restriction in and get feature tests that are not too tied to the database, but I chose not to get side-tracked by that right then and there.

I updated the various view templates and ran the feature test. It was failing on the email step.  I needed to support more acknowledgement email templates.  I let the code expand again:

```rb
  def plan_name
    return 'premium_mob' if params[:plan] == 'premiummob'
    return 'premium_plus' if params[:plan] == 'premiumplus'
    'premium'
  end

  def send_acknowledgement_email
    Mailer.send(acknowledgement_email_template, params[:stripeEmail]).deliver_now
  end

  def acknowledgement_email_template
    "send_#{plan_name}_payment_complete".to_sym
  end
```

There were certainly opportunities for refactoring, but I ignored them and pushed on to get the acceptance green.  I was a little disturbed that my creation of a new class `PremiumMob` was not throwing an error.  I turned aside to create specs for that:

```
describe PremiumMob, type: :model do
  let(:type) { 'PremiumMob' }
  it_behaves_like 'a subscription'
end
```

And knowing that implementing the next end point would highlight the places for refactoring I did the same as above for the PremiumF2F plan.  I now had the two key endpoints that I wanted, and I could refactor with some security.  I actually started the refactoring in the cucumber steps (see the PR for details) and held off against using the Scenario Outline as the RubyMine automated refactoring was failing and I had 20 minutes left.  The cucumber steps were cleaner and I focused my attention on the charges controller.  I was feeling uncomfortable about these `plan_name` and `plan_class` methods that were effectively case statements.  I needed to keep `plan_name` as it was doing a not easily automatable mapping from the ids of the plans in the Stripe side to the snake_case versions in use on the Rails side:

```rb
  def plan_name
    return 'premium_mob' if params[:plan] == 'premiummob'
    return 'premium_f2f' if params[:plan] == 'premiumf2f'
    return 'premium_plus' if params[:plan] == 'premiumplus'
    'premium'
  end
```

Looking at this now I see I can refactor this further by replacing it with a hash data structure, but yesterday I used it to DRY out the `new` action:

```rb
  def new
    render plan_name
  end
```

and the `plan_class` method:

```rb
  def plan_class
    return PremiumF2F if params[:plan] == 'premiumf2f'
    plan_name.camelcase.constantize
  end
```

Note that there's an exceptional case in the above to handle some Rails Inflector screwiness that comes from the number 2 in the `PremiumF2F` class, which itself has to sit in a file named `premium_f2_f.rb` file to be loaded.  I think I need all the different subscription types (`Premium`, `PremiumF2F`, `PremiumPlus`) to sit in their own module to avoid the funky file name.  Not sure if that will remove the exceptional case from `plan_class`.  I was out of time, but the tests were green and I'd DRYed out some of the nastiest dampness from the charges controller.  Lots more to get done, but aside from code navel gazing, the key thing here is whether the new endpoints would generate any revenue.  In a quick mental calculation I worked out we would need 10 F2F sign ups, 20 Mob sign ups and another 40 premiums to make us truly stable. Wonder if we can do that before Xmas?  That really would be an awesome Yuletide gift!  

Even if we don't, I've highlighted a core insight for myself about an old truth.  Red, Green and THEN refactor :-) Looking back over the years there's so much trouble that's come from refactoring too early.  Let the code expand first, and then contract when it's green and try not to build in too much genericism.  Or at least I'm saying that because the PR build is green - we'll see what happens when we deploy ...

Related Videos:

* ["Kent Beck" Scrum](https://www.youtube.com/watch?v=1QPgTuAkzUE)
* ["Martin Fowler" Scrum](https://www.youtube.com/watch?v=I8njkwFwTRc)




 
