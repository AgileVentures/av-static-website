Well I've had a week off and I'm back at the keyboard planning to fix up some issues in the AV site.  I've managed to avoid looking at Slack and Email although I have been slightly distracted by fixing an issue with a graphic on the HLP wiki, but that was fortunately a super quick fix of a dropbox link.

Right, so before I get distracted (and execute my master plan to have others take the lead on more of AV events) I want to fix up this issue in the Premium pages.  It seems like we lost the expandible/collapsible sections in the Premium page when we switched to the new content model.  I'm going to put them back in ...

![](https://dl.dropbox.com/s/xrf2yd4w5za937s/Screenshot%202018-06-04%2009.56.07.png?dl=0)

is what it looks like on the site.  In the [github markdown](https://github.com/AgileVentures/AgileVentures/blob/master/PREMIUM.md) we've lost the custom HTML we need to include the expandible/collapsible sections.  That should be saved in the AV main database.  As I wait for the heroku command line to connect to the remote system so I can inspect the data I'm overwhelmed with a feeling of guilt for not having repeatedly follow up on looking at how people are moving through the site as was my intention over the course of this year.  I've been distracted by various paying projects and the rest.  It's a funny feeling ...

```
Loading production environment (Rails 5.1.6)
$ p = StaticPage.find_by slug: 'premium'
=> #<StaticPage id: 33, title: "Premium Membership", body: "<h1 id=\"premium-membership\">Premium Membership</h1...", parent_id: nil, slug: "premium", created_at: "2016-05-03 16:16:19", updated_at: "2018-05-11 03:00:47">
```

is the premium page, and now I need to access the body of a previous version ...

```
$ p.versions.order(:created_at).map {|v| v.id}
=> [2544, 2545, 2547, 2548, 2576, 2624, 2625, 2657, 2658, 2662, 2685, 2688, 2689, 2690, 2700, 2701, 2703, 2721, 2760, 2769, 2776, 2795, 2803, 3041, 3046, 3063, 3103, 3172, 3175, 3226, 3228, 3230, 3231, 3232, 3233, 3241, 3259, 3262, 3263, 3282, 3337, 3338, 3339, 3353, 3359, 3406, 3415, 3417, 3441, 3442, 3443, 3450, 3570, 3571, 3783, 3827]
```


```
$ p.versions[-2].object
```

gives me the body of the immediately previous premium page, which I straighten out with a local text editor and an online indentor, and then I update the https://github.com/AgileVentures/AgileVentures/blob/master/PREMIUM.md page.  Now I could wait for that to be auto-pulled in, but I want to see that the change is what I expect, so let's pull it in now.

```
$ heroku run rails fetch_github:content_for_static_pages -r production
```

But it gets stuck on this:

``
Trying to convert this page: PREMIUM.md caused the issue!
"\xC2" from ASCII-8BIT to UTF-8
```

Turns out all the £ symbols won't work for us.  Nico had already replaced those, but I had re-introduced them.  Seems like we can't easily get the line number where the odd characters are popping up:

https://github.com/gettalong/kramdown/issues/10

so I pulled the whole code locally and sliced up the file until I found the non-ascii apostrophe's that were still breaking things, and now we have the page back to how it looked before with the expandible triangles:

![](https://dl.dropbox.com/s/weiavn711dlv557/Screenshot%202018-06-04%2010.43.24.png?dl=0)

There's a related issue for the Premium Mob and F2F pages, but we have an issue with the URLs with the site hard coding to underscores and the AV/AV site converting the upcased underscored file names to lowercast hyphened - which is what our slug generation system does.  Anyway, even before I got snarled up in the non-ASCII chars I was thinking that was for another day ... At least the site is consistent and slightly less wordy now.
