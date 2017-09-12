So I've looked back at my list from yesterday as I tried to work out how to start the week:

1) review the process of people coming in from adwords
2) fix the email SPF setting
3) more work on greeter bots
4) search for other areas of ecosystem that are blocked up
5) preparing that video for dropbox to debug github image embeds

It's looking like we've fixed the email SPF setting (thanks Federico!) and I've submitted the video to dropbox about their links not working in Github and Pivotal markdown, but they've not replied yet (checking the email sidetracked me while I unsubscribed from five things), so no joy on that getting fixed yet.  It might be procrastination, but I think I'll put off reviewing people flowing into the site for a week to see if I can get my easy  "insert-screenshot" functionality back.  I really want to put the new community-wide code of conduct into the website and the greeter bots, but the community is discussing it and so it might be good to leave that a couple of days (but no longer).  Thinking about important areas of the ecosystem, I've got hanging items in terms of WebSiteOne.  I've got a slew of content tickets relating to Premium that have been hanging around for ages, and I said in last Friday's WSO meeting that I'd take on the multiple source repository task.

One of our project maintainers had dropped in the WSO channel to mention this was an issue - that's a faily solid prod.  There's no direct revenue growth attached to it, but the health of the community relies on active healthy projects.  I've also got to get the board cleared up - every hanging ticket is extra noise that I (and others) have to process every time we look at it ...

Just looking at the board, I got distracted by other tickets - checking that LocalSupport deployed to production correctly and that the latest code on WSO staging is making sense ... I've kicked off the karma calculator there and should ask Praveen to check his features there ... but back to the board.   I've got one ticket for making the Premium page questions pop-out to reduce the amount of text on the page overall ... and that's what I've managed to do this morning. 

https://staging.agileventures.org/premium

At least edit that page on the staging server.  I've also saved an HTML copy into the staticpages view directory that will move the ticket from ready to in progress on the waffle board:

https://github.com/AgileVentures/WebsiteOne/pull/1816

I'm also thinking that ultimately we may need to make these pages slightly less editable, and more generated.  We'll see.  I think other devs need also to have copies of them locally.  Either way I'll get feedback on the staging server from the Marketing team before pushing live.

p.s. did a [node rep and included standardjs](https://gist.github.com/tansaku/6450f322f7505880872eb287c0ca36b0) 
