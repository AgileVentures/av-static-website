However hard I work, it never seems like I've done enough.  We got the NHS Social Prescribing wiki "live" on Friday, and I also managed to do a round of updates and fixes on the MOOC.  Superficially I should be pleased at having gotten all that "done", but there are a set of usability issues on the wiki that are bothering me.  Maybe the ultimate solution is the philosophical one of not being attached to any future outcome so that one can enjoy every moments inner peace and stillness.  More often that not I am agonizing about the things not done, or whether the things I am doing are having the effects I intend, e.g. blogging supposedly centering me in the morning, but potentially being wasteful navel gazing ... :-)

So let's put this blog to work, and look at the issues that are bothering me about the wiki.  I had been hoping to work on them on Friday morning, but time seemed to get sucked into getting them into a trello board:

![HLP wiki trello board](https://www.dropbox.com/s/4nqawtimebrqe43/Screenshot%202017-05-15%2009.53.52.png?dl=1)

and then in a Skype call with the new HLP change manager.  That in itself was a major breakthrough and involved getting one of the NHS tech staff to provide a separate laptop with Skype enabled.  Huge relief as I could now show the change manager various aspects of the wiki operation without having to commute across town.  As we were Skyping the updated version of the terms & conditions and privacy policy came in, and by the time I'd gotten that approved by the communications manager, added that to the website, sorted out some very basic help guides etc. I'd used up my allotted time in the day for work on the wiki.  On reflection I think it was the right focus.  While part of me yearned to be working on the technical issues, having the correct legal cover was crucial.  Also, connecting with the change manager was unblocking them to finish putting all the content from the HLP PDF document into the wiki, and the first (and possibly only) experience that most people were likely to have of the wiki were going to be the content and the viewable portions.  One thing that was clear from the previous week of testing was that we needed a lot more content in there for this to be of any use to anyone.

So going back to the Trello board I could move the legal work into the "completed" column.  Another 'view' item I had created was about improving the landing page.  I'd put two items in that:

1. Linking all the key topics
2. Making all the useful links at the bottom go to our own internal pages

The change manager had been able to do the former after I'd reviewed the process of making links with them.  There were however another set relating to Self-Care that would be linked to pages created from the second HLP PDF document, but that hadn't been signed off by the "decider" so I'm stuck here not being able to click the relevant checkbox.  Pedant? I know from Friday's experience that I could easily burn a lot of time just editing things around, do I decided to leave this ticket "in progress".  It's odd because there are lots of simple editing tasks, e.g. creating multiple new pages, that are relatively straightforward for me, particularly with my multi-monitor setup, that are quite time consuming for novice users.  At the same time, there are lots of more technical issues that those novice users can't make progress on, so I must drag myself over to the more serious technical issues. 

One of the particularly niggling ones is that creating an account seems to log folks in temporarily, but then they get logged out when the move off the page.  I had opened a [support ticket](https://trello.com/invite/b/5J4lZIaT/53a4831a8f2f8dc0de936756c66e10d3/hlp-wiki) on the main mediawiki site about this, and got a response:

> definitively it's not something I can reproduce on a local instance

> On your wiki, after creating account, I see those cookies being set: cpPosTime, UseDC, UseCDNCache

> On next page I'm logged out, and when logging in again, those cookies are being set: bitnami_mediawiki_session, bitnami_mediawikiUserID, bitnami_mediawikiUserName

> However, on a local 1.28, I get all cookies set after creating the account. the _session cookie is set when loading the createaccount page, but then replaced with another after the account gets created. However it doesn't get replaced on your wiki on creating an account, and I have no idea why

I'm so tempted to dive in here.  My first action might be to replicate what the mediawiki person is indicating.  I'm also tempted to do the complete setup and install of the whole system on a clean machine.  Although that smells slightly of procrastinating.  I could just start checking the behaviour on our staging instance that doesn't have the visual editor and SSL as a way to attack this problem faster, but then again I have two more "in progress" tickets.  One is the side navigtation issue which I've got done, so stick that in completed.  However, the "Getting Started" and "Help" pages that I've created are still just basic stubs, and it has to be a priority to fill those out further ... and there might be other things to add to that side navigation?  Close the ticket, make a new one on help ... wait - I have a ticket on making the help pages and uploading my training materials there ... I added another ticket on the "Getting Started" page, and spent a few moments moving that into the backlog and moving more important items to the top of the backlog; closing out some other tickets that I've dealth with.

This brings me to crux of my stalled decision-making process.  Filling out the help pages with my training materials feels like super high priority, but the training materials include the sign up process, which has this niggling bug, which if I can fix will change the way we document the site.  To cut this gordian knot I think I'll upload the training materials, documenting the current issue ... using that as a way to explore the issue, in the way that I sometimes work blog - so wiki work if you will.   I'll hedge against being unable to fix the issue by documenting it.  In the same process I'll be getting wiki usage documentation up.  Two birds, one stone?  Although I might be tempted just to check if we have the same issue on our staging wiki first ...

Okay, so I have a plan, and the Trello board is updated.  I have a niggling doubt that I haven't reviewed all the meetings from last week and that I'm missing some other critical issues that need fixing.  Other annoying ones that I do have in the Trello "icebox" include:

* Create pages defaults to the basic editor which many find confusing
* Moderation notices fail in some cases
* The visual editor doesn't fail gracefully

These are another knot in that if we can't rely on the visual editor failing gracefully then it might not be such a good idea to make it the default for create pages ...  Anyway.  One knot at a time.  Plan is:

* Get coffee
* Work on first knot
* Review
* Work on second knot

See you tomorrow :-)
