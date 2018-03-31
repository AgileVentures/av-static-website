So there's feedback from the APSoc client, and clearly some more work to do on the LibreOffice Base application, but I don't think I can delay a review of the AV Premium members.  I used to be doing weekly reach outs, but with the new pairing and mobbing sessions I'm several weeks behind.  I also need to generate the latest monthly report for the trustees.  I've been adding data on mobs/pairing/meetings as I have them, so that shouldn't take so much time, but I also need to get the data in the WSO site accurate for Michael's machine learning enterprise.  For that purpose we've created an API that allows us to get a list of the status of all the Premium users.  Using the great `jq` library, I can get nicely formatted json on the command line using a command like:

```
curl -H "Authorization: Token token=1234" https://www.agileventures.org/api/subscriptions.json | jq
```

and that gives me data like so:

```json
  {
    "email": "<email>",
    "sponsor_email": null,
    "started_on": "2017-03-06",
    "ended_on": null,
    "plan_name": "Premium",
    "payment_source": "PaymentSource::PayPal"
  },
  {
    "email": "<email>",
    "sponsor_email": null,
    "started_on": "2017-03-09",
    "ended_on": null,
    "plan_name": "Premium",
    "payment_source": "PaymentSource::Stripe"
  },
```

Now what I need to do is go through ensuring this data is accurate and up to date.  First up I have an honorary member who hacked the PayPal endpoint in a security intrusion test.  This is not relevant data for the machine learning system.  The next is one who was sponsored, but now has a full time job and hasn't been making use of the Premium Mob membership.  They've demurred paying their own way, saying they need time to get things organised.  I think I could sensibly mark these two as honorary members since they both contributed so much on the open source side.  I go ahead and create an honorary membership:

```
irb(main):003:0> Plan.create name: 'Honorary Premium'
=> #<Plan id: 12, name: "Honorary Premium", free_trial_length_days: nil, third_party_identifier: nil, amount: nil, created_at: "2017-12-18 09:58:46", updated_at: "2017-12-18 09:58:46", category: nil>
```

I set the first user to honorary, and that bumps them to the bottom of the list, so I can try and hook up the correct sponsor email to the second premium.  That was pretty easy, but now I also need to indicate the end of the sponsorship and that it's passed on to another member.  I add an end to the sponsorship, but leave the pass on till I get to that sponsee.  I start making lists of who to check in with, who to send follow up forms to, and about which sponsee positions we need to fill.  Problem with this is that it seems to be taking me ages to crawl through making updates, considering whether to reach out to people, whether to follow up on inactive members.

Anyhow, I've managed the first five in the list and now we come up against a subscription with no associated user.  It was started on "2017-04-25", so can I match that with someone in the Stripe interface, since that's the payment type ... but none of the Stripe customers was created even close to that date (according to the stripe interface). Doh, no, ugh, it's PayPal.  Where we do have a member who started at that time.  It looks like I also created a subscription for that user a few days afterwards

```json
  {
    "email": "<email>",
    "sponsor_email": null,
    "started_on": "2017-04-29",
    "ended_on": null,
    "plan_name": "Premium Mob",
    "payment_source": "PaymentSource::PayPal"
  }
```

Sensibly I hook up correctly, and delete the inaccurate account, ah, but actually that's the initial Premium that the member had for four days before upgrading to Premium Mob, so to hook that up we need to adjust the database model to support multiple subscriptions.  Hmm, looks like I'll need to hit this every morning this week to get things sorted ... and maybe more ...
