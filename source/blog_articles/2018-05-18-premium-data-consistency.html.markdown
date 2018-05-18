So we've had a couple of folks sign up for a free trial month of Premium this week.  One worked precisely as expected.  The other person signing up via PayPal didn't get updated in the site, and we didn't get any error messages from AirBrake.  One of time out of the PayPal callback hook or something more serious?

To ensure our data model does something sensible I need to get into the system and create the subscription manually.  I also need to add manual updates for the new folks we're sponsoring.  Would be great to have a button to press to make that all consistent ...

To manually update PayPal or Stripe subscriptions I have to review the code we have in the SubscriptionsController:

```rb
  def add_appropriate_subscription(user, sponsor = user)
    user ||= current_user
    return unless user

    if paypal?
      payment_source = PaymentSource::PayPal.new identifier: params['payer_id']
    else
      payment_source = PaymentSource::Stripe.new identifier: @stripe_customer.id
    end

    AddSubscriptionToUserForPlan.with(user, sponsor, Time.now, @plan, payment_source)
  end

  def send_acknowledgement_email
    payer_email = paypal? ? params['payer_email'] : params[:stripeEmail]
    if sponsored_user?
      Mailer.send_sponsor_premium_payment_complete(@user.email, payer_email).deliver_now
    else
      Mailer.send_premium_payment_complete(@plan, payer_email).deliver_now
    end
  end
```

So I log into the heroku rails console and create a payment source based on the identifier I get from PayPal:

```rb
payment_source = PaymentSource::PayPal.new identifier: 'I-123123123X'
```

I get a list of the plan types:

```
$ Plan.all.map {|p| [p.id,p.name]}
=> [[1, "Premium"], [2, "Premium Mob"], [3, "Premium F2F"], [4, "Premium Plus"], [5, "Associate"], [6, "NonProfit Basic"], [7, "NonProfit Extra"], [8, "NonProfit Enterprise"], [9, "Premium Mob Upgrade"], [10, "Premium"], [11, "Premium (first month free)"], [13, "Premium Half Hour"], [12, "Honorary Premium"]]
$ @plan = Plan.find 11
$ time = DateTime.parse('2018-05-16 08:50:25')
$ AddSubscriptionToUserForPlan.with(u, u, time, @plan, payment_source)
$ Mailer.send_premium_payment_complete(@plan, '----@gmail.com').deliver_now
```

I sent the welcome email for good measure as well ... that goes out - now I just have to ensure all the sponsored premiums are up to date ...
