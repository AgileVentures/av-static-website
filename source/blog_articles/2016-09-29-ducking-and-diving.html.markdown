---
title: Ducking and Diving
date: 2016-09-29
tags: tickets demeter violation migrations try dot ampersand ruby rails feature integration unit tests
author: Sam Joseph
---

So having partially recovered from the cold I was able to pair program again today.  Interleaving work on the Karma calculation and the premium memberships, we now had an outstanding PR for premium upgrade buttons, so we ducked back to the Karma work of the previous week where we had got tangled up in all sorts of trouble with legacy tests, factories and objects.  The ardour of frustration had cooled.  I was not now in such a hurry to "solve" the problem.  I think that really helped.  We'd broken up the follow on refactorings into two tickets; one to get the karma total out of the user table, and the other to transfer all the rest of the intermediate calculations into the Karma model itself.

We started on removing karma from the user table.  The approach here was just to create the migration and see what errors we flushed out:

```rb
class RemoveKarmaFromUserTable < ActiveRecord::Migration
  def change
    remove_column :users, :karma_points, :integer
  end
end
```

I was torn about this sort of hacky approach, versus the other ticket which would see us refactoring away the KarmaCalculation service.  Still that bigger refactoring had more to go wrong, so it made sense to take the simpler step first.  Well maybe it wouldn't be simpler, but we needed to flush out any unknowns before we went on to the bigger refactoring.  It also felt good to be removing something from the bloated user table.

So we proceeded with the migration and flushed out the following 16 failures in the specs:

```
rspec ./spec/views/users/index.html.erb_spec.rb:48 # users/index.html.erb renders User name link with href
rspec ./spec/views/users/index.html.erb_spec.rb:41 # users/index.html.erb should display a list of users
rspec ./spec/views/users/index.html.erb_spec.rb:91 # users/index.html.erb display user status there should be 4 green dots
rspec ./spec/views/users/index.html.erb_spec.rb:85 # users/index.html.erb display user status display green dot for online users
rspec ./spec/views/users/index.html.erb_spec.rb:97 # users/index.html.erb display user status do not display green dot for offline users
rspec ./spec/views/users/index.html.erb_spec.rb:103 # users/index.html.erb display user status displays the user's status with a speech bubble
rspec ./spec/views/users/index.html.erb_spec.rb:18 # users/index.html.erb advanced filtering should display an advanced filter form
rspec ./spec/views/users/index.html.erb_spec.rb:32 # users/index.html.erb advanced filtering timezone select is populated with titles
rspec ./spec/views/users/index.html.erb_spec.rb:25 # users/index.html.erb advanced filtering projects select is populated with project titles
rspec ./spec/views/users/index.html.erb_spec.rb:62 # users/index.html.erb renders the users count in the sentence above shows different sentence if invalid users count
rspec ./spec/views/users/index.html.erb_spec.rb:56 # users/index.html.erb renders the users count in the sentence above has valid users count
rspec ./spec/controllers/users_controller_spec.rb:10 # UsersController#index should assign the results of the search to @users
rspec ./spec/services/karma_calculator_spec.rb:10 # KarmaCalculator for new members should assign 0 karma points to members who have not yet been created
rspec ./spec/services/karma_calculator_spec.rb:23 # KarmaCalculator for existing members for old members should assign karma points to members
rspec ./spec/services/karma_calculator_spec.rb:34 # KarmaCalculator for existing members for members attending hangouts gives points for hangout participation
rspec ./spec/views/layouts/application.html.erb_spec.rb:73 # layouts/application.html.erb should return 200 for all link visits
```

which were all variations on the expected error message of:

```
column users.karma_points does not exist
```

We started by fixing up the Karma controller so that it switched its references from `user.karma_points` to `user.karma.karma`.  That latter looked silly so we switched it to `user.karma.total` with another migration:

```rb
class RenameKarmaKarmaToTotal < ActiveRecord::Migration
  def change
    rename_column :karmas, :karma, :total
  end
end
```

Reaching through from one object into another like `user.karma.total` is a Demeter violation.   We shouldn't know or assume too much about the objects we are collaborating with, but we knew our next ticket was going to refactor away the KarmaCalculation service, so we didn't want to get pulled in to fixing that immediately.  After getting the KarmaCalculation specs to pass we had a few views to update to use `user.karma.total` instead of `user.karma_points` and they were soon passing.  The user controller needed a slightly more complex change from:

```rb
User.page(params[:page]).per(15)
    .includes(:status, :titles)
    .filter(set_filter_params)
    .allow_to_display
    .order(karma_points: :desc)
```

to

```rb
User.page(params[:page]).per(15)
    .includes(:status, :titles, :karma)
    .filter(set_filter_params)
    .allow_to_display
    .order('karmas.total DESC')
```

to cope with the fact that we were now ordering the main user view based on a field in another table.  That done, all the specs were passing, and we ran the cukes to see if any of the features were broken. Before we did that I had half a mind to start deleting the view specs that were failing, and even the controller specs.  I've lost any motivation to write view and controller specs that effectively unit test parts of the view and controller in isolation.  It feels like the features and integration tests will check that logic, and that "unit" tests of controllers and views encourage logic to appear in those areas when all complex stuff should be being pushed into models, services, gems and remote services where possible.  That's what we've done with some success in our work on ProjectScope.

However, the fixes for the specs had been relatively simple, and the fear of deleting something that might have use in the future (dangerous paranoia?) kept me from hacking and slashing.  The idea of more extensive unit tests of controllers and views is that they can find you problems faster than the slow running acceptance tests.  Ironically our acceptance tests don't take much longer to run than our specs, and anyway for all this to work you need to trust that your specs are testing the right things.  In fact we did get a failure in the cukes, which was related to the way we were setting up our default user objects - there was no Karma object associated with them by default, so calling `user.karma.total` was failing with no method `total` on nil object.  Our Demeter violation was biting us.

Now we could have been less confident and fixed this with `user.karma.try(:total)` or even the new ampersand dot syntax `user.karma&.total`, but given that we were already uncomfortable with the Demeter violation, we went another, perhaps more confident, route.  We TDD'd a new method on the User object:

```rb
describe '#karma_total' do
  subject(:user) { FactoryGirl.create(:user) }

  it 'returns 0 when user initially created' do
    expect(user.karma_total).to eq 0
  end

  context 'once associated karma object is created' do
    subject(:user) { FactoryGirl.build(:user, karma: FactoryGirl.create(:karma, total: 50)) }

    it 'returns non zero' do
      expect(user.karma_total).to eq 50
    end
  end
end 
```

Effectively we created the sort of method we had before, i.e. `karma_points` but called it `karma_total`, and the user object could now confidently return the Karma total under any circumstances:

```rb
def karma_total
  return karma.total if karma
  0
end
```

Much more confident, and now everything was green.  For a long time I've been talking about how much I like Objective-C's set up where calling a method on nil just results in nil.  The ampersand dot in Ruby promises that much, but now I've been infected by Avdi's "confidence" meme.  I also just watched this talk by Peter Bhat Harkins, which was almost going to be called "kill nil":

<iframe width="560" height="315" src="https://www.youtube.com/embed/tg3YjMqWNj0" frameborder="0" allowfullscreen></iframe>

and I'm starting to see nil as evil.  Well at least something to address by avoiding passing it around rather than using `try` or ampersand-dot which screws with our readability.  Anyhow we'd managed to follow a drive-by methodology, doing the minimum work necessary to get our pull request out.  We'd fixed the Demeter violation for "getting" Karma where it was breaking our feature tests, but we left the Demeter violation for "setting" Karma in the KarmaCalculation service, which we were planning to refactor in the next ticket.

We'd finished early so we got in some quick PRs to upgrade to Ruby 2.3.1, remove some old Vagrant scripts and started on a new ticket for improving hangout telemetry.  A reasonable afternoon's work.  We'd ducked and dived and at the end of it I felt we'd taken a reasonable middle road down the profusion of coding and project heuristics that infest my mind.  Let's see what tomorrow brings :-)

Related Videos:

* [Pairing Video](https://www.youtube.com/watch?v=fPGJ5lon92M)
* ["Kent Beck" scrum](https://www.youtube.com/watch?v=r6pWaOVKyRM)
 




