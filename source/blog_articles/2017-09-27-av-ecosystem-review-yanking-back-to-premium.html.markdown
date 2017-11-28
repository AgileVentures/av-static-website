So in the backdrop yesterday a javascript bug in the multiple repos for projects feature led me to some refactoring that also fixed the bug, which I thought was pretty neat:

```js
WebsiteOne.define('Projects', function () {
  return {
    ensure_github_url_numbering: function () {
      var sourceRepositories = $('#project_form').find('.nested-fields');
      var sourceRepositoriesSize = $(sourceRepositories).size();

      if (sourceRepositoriesSize > 1) {
        for (var i = 1; i < sourceRepositoriesSize; i++) {
          $(sourceRepositories[i]).find('.repo_field_label').html('GitHub url (' + (i + 1) + ')')
        }
      }
    }
  }
});

$(document).on('ready', function () {
  WebsiteOne.Projects.ensure_github_url_numbering()
  
  $('#source_repositories').on('cocoon:after-insert', function (e, added_repo) {
    WebsiteOne.Projects.ensure_github_url_numbering()
  });
})
```

I had realised that the github url numbering was not being performed on initial page load, and so I extracted the function that performed it into our application name space and now call it both on page load and when the form is dynamically updated.  Should I have done the refactoring earlier?  I don't think that would have highlighted the issue particularly, and I left off factoring because I wanted to see what direction I'd be pushed in next.

Anyhow, so the siren song of the Waffle board is calling, since I've started a spike to see if we can replace the agile-bot with a chunk of Ruby code, and that has an interesting interaction with a feature that Corey wants to work on.  Still we don't have anything in the "Please check" column, and I must force myself to get to these outstanding Premium content tickets that I've got assigned to me in the "ready" column:

![](https://dl.dropboxusercontent.com/s/xqtuv7s55czfr5p/Screenshot%202017-09-27%2009.48.32.png?dl=1)

Although I'm also desperate to get in some of the changes suggested in the marketing meeting as a result of feedback that suggests folks are not understanding the additional value that comes from Premium Mob and F2F.  From my action items there I can do a quick update of the overview page like so:

![](https://dl.dropboxusercontent.com/s/apwfkulugmnj4z8/Screenshot%202017-09-27%2009.52.42.png?dl=1)

and I tweaked the language at the top, but I'm not convinced this will have much impact:

![](https://dl.dropboxusercontent.com/s/z1e41padz22gvao/Screenshot%202017-09-27%2009.56.35.png?dl=1)

I'm thinking that images of people in hangouts in the Premium Mob and F2F pages will be what makes the difference.  Okay, let's start with the mob page.  I previously took a copy of the Premium page in html into version control, although we are still storing it in the db.  Potentially confusing, but good backup for when I do things like accidentally blow away the changes I make on staging.  So I'm running out of time, but the key thing to make progress on the ticket is to turn the list of Premium Mob features into a set of whys, which I did in a blog:

* Mob programming sessions with an assigned AV Mentor each week
* Professional Development Planning Support
* Free accesss to Udemy Complete Elixir & Phoenix Bootcamp course (usual price $50)
* Free accesss to Udemy React Redux course (usual price $180)
* 25% discount on "Effective Testing with RSpec 3"
* 10% discount on Upcase membership
* Free access to the "Phoenix Inside Out" book series

--> Related Questions

* You want to level up on the latest hot technologies like Elixir and React?
* You want to get a personalised professional development plan to achieve your goals?
* You want to master mob programming online?

And for reference since it took 10 minutes to find it - my blog on that is [http://nonprofits.agileventures.org/2017/06/26/private-design-sprint-day-minus-one-blog/](http://nonprofits.agileventures.org/2017/06/26/private-design-sprint-day-minus-one-blog/).

So editing up the HTML I got half the Premium Mob page done, which is at least a start.  Will finish tomorrow:

![](https://www.dropbox.com/s/ko5h8eraiwy5l63/Screenshot%202017-09-27%2010.38.32.png?dl=1)
