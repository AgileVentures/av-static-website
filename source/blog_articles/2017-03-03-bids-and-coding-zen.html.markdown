One of the reasons I got disenchanted with academia was the process of writing grant applications for research funds.  It seemed to involve a huge amount of work to describe the work you would do if someone gave you some money.  There'd be several weeks work writing a 10 or 20 page document followed by an all-nighter to get it all together for the deadline and then submit through some complex web interface.  Then wait six months to get three or four paragraphs of feedback about why the proposal hadn't been accepted.  Clearly some people in academia were recipients of government grants.  I was more successful in bringing in industry money where I could talk to someone face to face.  The problem with industry grants is that they would dry up when the market turned.

Anyhow, fast forward to my current focus on charities and freelancing.  It's grant writing all over again, although the turn-around time is a bit faster.  I've been receiving a lot of support from lovely people in the charity sector regarding fund-raising, but I seem to be back in this situation of asking for money and getting turned down for one reason or another.  I wonder if the problem is that I'm always hedging my bets?  Being completely unable to quantify the chances of receiving a grant it's almost impossible to work out how much effort to put into the application process.  I just went through one where the person guiding me was saying we were pretty much guaranteed to get the contract, to be phoned late last night to be told that another bidder had unexpectedly been able to come in and offer a solution and point to a much stronger background in the are the grant was focused on.

Going forward I'll have to make it clear that phoning me late at night is not on.  I'm involved in a couple of other bids for freelance work at the moment.  All of them difficult to quanify in terms of how much money they'll bring in and the likelihood of actually landing them.  I'll give it till April and then I'll have to put the search for income into a higher gear ...

All that speculative bidding process juxtaposes with moments of coding zen I've felt this week.  I was tidying up the three WSO PRs I made this week while my twin boys were doing their karate class, and starting adding comments about issues I was seeing.  I managed to pull some logic out of a view into a helper:

```erb
<% event_name = current_user ? event['name'] : 'Want to learn more? Listen in. Next projects review meeting '%>
```

became

```rb
  def event_name_or_invitation_to_guest_user(event)
    current_user ? event['name'] : 'Want to learn more? Listen in. Next projects review meeting '
  end
```

in `layout_helper`

making the original erb become:

```erb
<%= link_to event_name_or_invitation_to_guest_user(event), event_path(event['id']) %>
```

which I think is an improvement.  I think the intent is not conveyed a little more succinctly now, although I'm not sure that LayoutHelper is necessarily the best place for the code, but one thing at a time.  Getting that in place and adding a few [inline comments on my own PR](https://github.com/AgileVentures/WebsiteOne/pull/1566) I experienced a moment of coding zen.  My concerns about a couple of places in the code were addressed with changes, and I was recording other concerns.  Next step refactoring tickets.  The Zen feeling came from a brief sensation of wholeness - that it was okay for the code to have issues, but that we have a mechanism for addressing those issues over time.  Now if only there was some way to achieve a similar Zen state as regards funding applications :-)

###Related Videos:

* ["Martin Fowler" Scrum](https://www.youtube.com/watch?v=XbbTJQYNMeQ)
* ["Kent Beck" Scrum](https://www.youtube.com/watch?v=fsHiZG2_UjQ)
