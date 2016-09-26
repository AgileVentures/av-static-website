---
title: Confident Coding
date: 2016-09-26
tags: pairing tickets refactoring domain model single table inheritance rspec shared_examples STI outside-in inside-out migrations
author: Sam Joseph
---

Friday I tried to improve on Thursday.  I proposed to Michael that we try to rotate driver/navigator roles more frequently and that we put aside the work on the Karma calculation; instead focusing on the domain model for the Premium members.  Michael had solo'd the creation of a cuke for a new Karma calculation rake task that would complement removing the `after_validation` hook from the User object.  Part of me was desperate to continue battling with the Karma calculation, but I convinced myself it would be better to leave that a PR, get feedback from Raoul in the meantime, and not continue building on that part of the system until that PR was merged.

I think I was slowly [letting go of a banana]() which is sometimes very challenging when you are a simple monkey like me; but I was coming to the conclusion I should put down that coconut, get some distance from it, and coming back to it in a few days might yield some better approaches.  Or at least the realisation that the path I thought was the good one, really was a good one; or that something more important might come up in the meantime :-)

So we switched to the Premium members, and again I noticed we didn't have tickets for several things that we'd been thinking about.  Specifically [refactoring Stripe components out of the user table](https://github.com/AgileVentures/WebsiteOne/issues/1299) and [automating Premium benefits](https://github.com/AgileVentures/WebsiteOne/issues/1300).  I wanted to take a leaf out of Avdi Grimm's "Confident Ruby" book and impose a solid narrative on the next piece of work we would do.  We'd been tripped up badly the day before with legacy factories, tests and models, so I thought we needed a bit of relatively smooth sailing to boost our spirits.

Smooth sailing can never be guaranteed of course.  You never know when a bug in a new version of a library is going to lead you up the garden path (VCR!), although if you can release your grip on the bananas then you might be pulled too far off track.  Still, Avdi makes a point about a "MacGyver Method" where you end up just using the tools that are lying around rather than telling the story you want to tell.  That happened to us with the legacy components in the Karma system where we burned a lot of time deciphering those components, rather than telling the story we wanted to about Karma.

So we started with the domain model.  This is uncharacteristic for me.  I'm usually all about defining the acceptance tests based on customer stories, and extracting the domain model later.  Outside-in, rather than Inside-out.  In my defence we'd done Outside-in driving the creation of the credit card payment functionality for Premium and PremiumPlus subscriptions.  We'd already validated that at least a few people would pay for premium services.  I'm suspicious of extensive domain models in a context where we're not even sure that the domain is one that anyone is interested in.  Since validating the model, I wanted to start integrating premium through the site more extensively, and that seemed like a good time to get down our domain assumptions.

It wasn't necessarily the best place to start, but we were looking at the DB schema considering the different parts we could pull out of the User model:

```rb
  create_table "users", force: :cascade do |t|
    t.string   "email",                  default: "",   null: false
    t.string   "encrypted_password",     default: "",   null: false
    t.string   "reset_password_token"
    t.datetime "reset_password_sent_at"
    t.datetime "remember_created_at"
    t.integer  "sign_in_count",          default: 0,    null: false
    t.datetime "current_sign_in_at"
    t.datetime "last_sign_in_at"
    t.string   "current_sign_in_ip"
    t.string   "last_sign_in_ip"
    t.datetime "created_at"
    t.datetime "updated_at"
    t.string   "first_name"
    t.string   "last_name"
    t.boolean  "display_email"
    t.string   "youtube_id"
    t.string   "slug"
    t.boolean  "display_profile",        default: true
    t.float    "latitude"
    t.float    "longitude"
    t.string   "country_name"
    t.string   "city"
    t.string   "region"
    t.string   "youtube_user_name"
    t.string   "github_profile_url"
    t.boolean  "display_hire_me"
    t.text     "bio"
    t.boolean  "receive_mailings",       default: true
    t.integer  "karma_points",           default: 0
    t.string   "country_code"
    t.integer  "timezone_offset"
    t.integer  "status_count",           default: 0
    t.string   "stripe_customer"
  end
```

Part of our recent focus had been on how we could reduce bloat on this table.  Adding the `stripe_customer` field had made us feel a little queasy.   However actually moving payment info or Karma wasn't going to reduce bloat; which would require pulling out an Address or SignIn class.  However it wasn't clear that either of those latter two would yield any short-term business value.  It's a bit like a game of Jenga.  There are some parts of the structure that if you pull at will cause a lot of blocks to come crashing down.  At least pulling out the payment and premium elements to other models would allow those parts of the system to evolve without further increasing the technical debt associated with the User table.,

Maybe we should have been using whiteboard software or pencil and paper, but I found myself writing the following with a few changes coming here and there:

```rb
  subscriptions
    t.integer  "user_id"
    t.string  'type'  # premium, premiumplus
    t.integer 'payment_source_id'
    t.boolean 'current'
    t.datetime  "signed_up_at"
    t.datetime 'cancelled_at'
  end

  payment_source
    t.string  'type' # stripe, craft_academy
    t.string  'payment_id' # stripe => stripe id ; craft_academy => ??
    t.datetime 'expires_at'
  end
  
```

See the video to follow how this evolved from one model to three models and then back to too.  However I did feel uncomfortable that we were being too specific at this level, so I pulled that snippet into a text document and re-framed it in terms of Ruby objects:

```rb
class Subscription > AR:Base
  belongs_to :user
  has_one :payment_source

  # type "premium, premiumplus"
  # active/current
  # started_at, ended_at
end

class PaymentSource >  AR:Base
  belongs_to :subscription

  # type 'CA', 'Stripe'
  # third_party_id
  # expiry?
end
```

Maybe we should have been writing domain model narrative as preference.  Clearly we were in danger of having the database, or rails tools infect our story.  That said I think the Rails class description provides a pretty high level language to talk about relationships between model entities.  We had settled on the idea that Users could have Subscriptions, which could be of different types (e.g. Premium or PremiumPlus) and so Subscriptions would belong to Users.  Subscriptions would also have PaymentSources which might be Stripe (credit cards) or CraftAcademy (the bootcamp that bundles AV Premium with their course).  We were also thinking about Subscriptions having start and end times, and the possibility of PaymentSources having expiry dates.  I felt we were starting in that direction of scope creep.  I wrote out the following features that we were thinking about:

* Key Feature - [allowing users to change subscription levels](https://github.com/AgileVentures/WebsiteOne/issues/1261)
  - requires research on how stripe handles change in subscription
* Future feature - expiring subscriptions
* Related features
  - when subscribing your stripe customer id should be updated
  - exposing change credit card functionality in individual page settings 

Expiring subscriptions should be put off to future work.  Related features could be put to the side given that we were happy that the domain model was compatible.  So then we used RSpec to test drive inour domain model elements.  I'll just show Subscription because the principle is the same for both.  I was also quite pleased that we did actually manage to stay behind the tests in terms of only adding application code or migrations that made tests pass.  Here's the Subscription Spec:

```rb
shared_examples 'a subscription' do
  it { should belong_to :user }

  it { should have_one :payment_source }

  it 'has the correct type' do
    expect(subject.type).to eq type
  end

  it { should validate_presence_of :started_at }

  it 'has ended_at' do
    expect(subject.ended_at).to be_nil
  end
end

describe Subscription, type: :model do
  let(:type) { nil }
  it_behaves_like 'a subscription'
end

describe Premium, type: :model do
  let(:type) { 'Premium' }
  it_behaves_like 'a subscription'
end

describe PremiumPlus, type: :model do
  let(:type) { 'PremiumPlus' }
  it_behaves_like 'a subscription'
end

```

Which generates output like this for each plan:

```rspec
Subscription
  behaves like a subscription
    should validate that :started_at cannot be empty/falsy
    should have one payment_source
    has the correct type
    should belong to user
    has ended_at
```

and is supported by the following app code:

```rb
class Subscription < ActiveRecord::Base
  belongs_to :user
  validates_presence_of :started_at
  has_one :payment_source, class_name: PaymentSource::PaymentSource
end

class Premium < Subscription
end

class PremiumPlus < Subscription
end
```

and this migration:

```rb
class AddSubscriptions < ActiveRecord::Migration
  def change
    create_table :subscriptions do |t|
      t.string :type
      t.datetime :started_at
      t.datetime :ended_at
    end
    add_reference(:subscriptions, :user)
  end
end
```

The experience of creating all the above (and similar for PaymentSource) was pretty pleasant.  We were rotating fast, relying only on code we were creating ourselves, and imposing a strong narrative on the domain model, the tests and so forth.  We used Single Table Inheritance (STI) to model some important domain entities with a minimal number of tables, and shared examples in RSpec to DRY out our specs.  Things got a shade more complex for PaymentSource, which we stuck in a module to avoid some naming conflicts:

```rb
module PaymentSource

  class PaymentSource < ActiveRecord::Base
    belongs_to :subscription
  end

  class CraftAcademy < PaymentSource
  end

  class Stripe < PaymentSource
  end
end
```

It was pretty much the smooth sailing the doctor had ordered.  We were feeling confident in our coding.  Some wrinkles were that RSpec seemed very slow to load.  Maybe we need to upgrade the gem, use the Spring preloader or something.  Writing this out I can see that there are ways that the models will need to evolve in terms of price info (currently stored on Stripe, do we need to import) and so forth, but the real test will be this week when we try to use this domain model addition to refactor the stripe customer id out of the user table, and support the appearance of membership upgrade buttons on the individual members profile pages.  Will our confident coding fall apart under the strain?  Will all this domain navel gazing turn out to be a waste of time and we really should have started with acceptance tests for user stories?  Let's see ...


Related videos:

* [Pairing on the domain models](https://www.youtube.com/watch?v=pXGkwvF_UH8)
* ["Kent Beck" Scrum](https://www.youtube.com/watch?v=QIn_wN2VE4k)





