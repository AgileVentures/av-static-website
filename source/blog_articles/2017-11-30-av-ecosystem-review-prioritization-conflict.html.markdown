Blargh.  Let's get the latest fix deployed on WebSiteOne.  Recently these blogs are little WebSiteOne admin activities and then the release process gets smeared out over the day.  What would it be like to actually focus on a single project for a day or, gasp, for a whole week?  I've also made the process more complicated by switching off the staging and develop servers when we're not using them, in order to save money.  It's also easy to burn time on these dependabot gem upgrades, although do I sense I'm getting a handle on them now?  Could even turn back on the PR updates in the #websiteone channel?

I'm suddenly paranoid about the google login failing, should I be on that?  Let's try to clean up first.  We've got pending notices in the jasmine tests and rspec.  I've got [half an idea](https://github.com/AgileVentures/WebsiteOne/pull/1858#issuecomment-348141856) about how to fix the `local_time` gem upgrade that dependabot is recommending, but heuristic: I already pulled in one dependabot today, stay on target.

Jasmine has this set of pending statements:

```
Pending:
        eventDatePicker hides event repeat options by default
          awaiting refactor of related form for use in fixture

        eventDatePicker shows event repeat options when event is set to repeat
          awaiting refactor of related form for use in fixture

        eventDatePicker shows event repeat optional ending when event is set to end
          awaiting refactor of related form for use in fixture

        eventDatePicker hides event repeat optional ending when event is set to never end
          awaiting refactor of related form for use in fixture
```

and RSpec this:

```
  1) Karma add some examples to (or delete) /Users/tansaku/Documents/GitHub/AgileVentures/WebsiteOne/spec/models/karma_spec.rb
     # Not yet implemented
     # ./spec/models/karma_spec.rb:4

```

The eventDatePickerSpec concern seems to be that the fixture is too large.  Just delete the test? It says it is awaiting refactor of the form, but that doesn't look like it will be happening anytime soon ..., so I make some specs with a fixture in a separate file:

```js
describe('eventDatePicker', function(){

  beforeEach(function () {
    // Michael is concerned that fixture is large and involved and creating too complex a seam
    loadFixtures('event_datepicker.html');
    eventDatepicker.init();
  });

  it('hides event repeat options by default', function () {
    expect($('#repeats_options')).toBeHidden();
    expect($('#repeats_weekly_options')).toBeHidden();
    expect($('.event_option')).toBeHidden();
  });

  it('shows event repeat options when event is set to repeat', function () {
    $('#event_repeats').val('weekly')
    $('#event_repeats').trigger('change')
    expect($('#repeats_options')).not.toBeHidden();
    expect($('#repeats_weekly_options')).not.toBeHidden();
    expect($('.event_option')).not.toBeHidden();
  });

  it('shows event repeat optional ending when event is set to end', function () {
    $('#event_repeats').val('weekly')
    $('#event_repeats').trigger('change')
    expect($('#repeat_ends_on_label')).not.toBeHidden();
    expect($('#repeat_ends_on')).not.toBeHidden();
  });

  it('hides event repeat optional ending when event is set to never end', function () {
    $('#event_repeats').val('weekly')
    $('#event_repeats').trigger('change')
    $('#event_repeat_ends_string').val('never')
    $('#event_repeat_ends_string').trigger('change')
    expect($('#repeat_ends_on_label')).toBeHidden();
    expect($('#repeat_ends_on')).toBeHidden();
  });

});
```

and making it all work involves me breaking out the js debugger and stepping through to see which elements are being hidden when:

![](https://dl.dropbox.com/s/zsckxs1juv27wax/Screenshot%202017-11-30%2010.47.52.png?dl=0)

I feel paranoid that this is all yesterday's tech.   I notice the JasmineJQuery library is looking for a new maintainer, and apparently some folks are using https://github.com/tmpvar/jsdom

I could also be refactoring out some of the operations in this test ... I equivocate and go for it:

```js
describe('eventDatePicker', function(){

  beforeEach(function () {
    // Michael is concerned that fixture is large and involved and creating too complex a seam
    loadFixtures('event_datepicker.html');
    eventDatepicker.init();
  });

  var set_event_repeats_to_weekly = function(){
    $('#event_repeats').val('weekly')
    $('#event_repeats').trigger('change')
  }

  var set_event_repeat_ends_to_never = function(){
    $('#event_repeat_ends_string').val('never')
    $('#event_repeat_ends_string').trigger('change')
  }

  it('hides event repeat options by default', function () {
    expect($('#repeats_options')).toBeHidden();
    expect($('#repeats_weekly_options')).toBeHidden();
    expect($('.event_option')).toBeHidden();
  });

  it('shows event repeat options when event is set to repeat', function () {
    set_event_repeats_to_weekly();
    expect($('#repeats_options')).not.toBeHidden();
    expect($('#repeats_weekly_options')).not.toBeHidden();
    expect($('.event_option')).not.toBeHidden();
  });

  it('shows event repeat optional ending when event is set to end', function () {
    set_event_repeats_to_weekly();
    expect($('#repeat_ends_on_label')).not.toBeHidden();
    expect($('#repeat_ends_on')).not.toBeHidden();
  });

  it('hides event repeat optional ending when event is set to never end', function () {
    set_event_repeats_to_weekly();
    set_event_repeat_ends_to_never();
    expect($('#repeat_ends_on_label')).toBeHidden();
    expect($('#repeat_ends_on')).toBeHidden();
  });

});
```

It works and I'm rewarded with fully passing jasmine specs, no pending, and I think the intention of the code above is now slighlty better in terms of self-documenting the intention by method names rather than comments.  We still have the issue that the fixture is too large, but that can be updated as and when.  Really the parts of the fixture that are relevant are just a few items being shown and hidden.  Ideally the fixture could be auto-generated, but really my concern here is just clearing the deck for others to work on things.

I create a ticket for clearing pending items.  Can I also get the RSpec sorted?  The Karma class is this:

```
class Karma < ActiveRecord::Base
end
```

and the spec is

```
require 'spec_helper'

RSpec.describe Karma, type: :model do
  pending "add some examples to (or delete) #{__FILE__}"
end
```

I don't know that the Karma model is yet doing anything special that needs testing.  I think this is boiler plate.  I'm going to delete the pending message, since I hate deleting files where I might want to add something later, hmmm.  I go for:

```
require 'spec_helper'

RSpec.describe Karma, type: :model do
  # no new functionality besides standard ActiveRecord functions yet
end
```

and then I screw up by pushing to develop instead of creating a branch.  Urgh, I amend the commit name to say it fixes the pending issue, but can't force push that up to develop because it's protected.  Blargh.  I remove the protection, force that up, and then put the protection back on.  Not ideal. Getting there.  Gotta finish up the blog.  Shame to pollute my inner space with this constant pressure to work faster, do more.  Need to meditate on that, but no time :-)

Anyhow, I think what I've got is now a pretty clean output from both Jasmine and RSpec.  Now if we really want to clean up the cucumber output then we've got to move on to removing this Mercury editor.  So I update that [branch](https://github.com/AgileVentures/WebsiteOne/pull/1767) and I guess I work on that tomorrow, pulling examples from what I do into the new README ... also our coverage tracking seems to be broken.  Need to get that fixed up too ... no rest for the wicked ... and do respite from the prioritization conflicts ...
