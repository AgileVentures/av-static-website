So we're in the two weeks of the UK Easter holidays.  My schedule loosens slightly due to not taking my twin boys to school in the morning, but I'm not starting work much earlier.  There's helping them with their Japanese homework in the morning and getting back into my longer exercise routine now that the physio cleared my repaired knee for more activity.  Over the last ten days I've met more than a dozen different stakeholders for the NHS Healthy London Partnership (HLP) "wiki" project.  I've been drafting blogs about discussions with many of them and the "design sprint" we've been trying to do with them.  There's so much information - and it will take at least another two morning's blogs to process/write-up the rest.  Is my blogging an indulgence, or a great way to process all the information?

I'm still enchanted with the ideas that crop up all over the design literature and the business books like Google's Design Sprint work that point out that it's being able to see what people actually do, rather than necessarily what they say, that allows the building of effective interfaces and business processes.  I'm using blogging to organise my thoughts - avoid myself getting drawn into long discussions on Slack and Email and have an easily discoverable repository of the things I'm working out and learning.  Anyhow, we've got time booked tomorrow to start some early prototype testing with the HLP folks, so I don't think I can afford more blogging about stakeholder perspectives.  I've got to get down to actual work on the prototypes.  Reviewing the hosted services, the most promising were:

* Tettra
* Nuclino
* PbWorks

Pat reviewed some of the open source solutions and we have a couple of possibles there:

* BlueSpice (PHP)
* WAGN (Ruby/Rails)

I also ran through the [wikimatrix](http://www.wikimatrix.org/) site, answering the questions from their wizard like so:

1. Do you need a page history? --> Yes
2. Do you need WYSIWYG editing? --> Yes
3. Do you need commercial support? --> No
4. Do you need localization? --> No
5. Do you want it hosted? --> Yes
6. Do you need your own domain? --> Yes
7. Do you need branding? --> Yes

It suggests BredainKeeper, CentralDesktop, Confluence, EditMe, Incentive, Intodit, MindTouch, Netcipia, Papyrs, PBwiki, SamePage, Socialtext, Wagn, Wetpaint, Wikia, Wikispaces and Zoho Wiki - although only the entries for [Incentive](https://www.incentive-inc.com) and [MindTouch](https://mindtouch.com/) have been updated in the last two years.

There was an alternative path for self-hosting 

5. Do you want it hosted? --> No
6. Database or file system? --> Not sure
6. Do you want it free and open source? --> Yes
7. Do you need a particular programming language? --> Yes

Which led to nearly forty different suggestions - although alphawiki,  BlueSpice for MediaWiki, [DokuWiki (PHP)](https://www.dokuwiki.org), [Foswiki (Perl)](https://foswiki.org/), [MoinMoin (Python)](https://moinmo.in/), [Oddmuse (Perl)](https://oddmuse.org/wiki), [TiddlyWiki (NodeJS)](http://tiddlywiki.com/), [Tiki Wiki CMS Groupware (PHP)](https://tiki.org/tiki-index.php), [XWiki (Java)](http://www.xwiki.org/xwiki/bin/view/Main/WebHome) have been updated in the last two years.

I'm quite enamoured with Tettra's integration with Slack, but I'm really not clear if requiring Slack can work for the range of stakeholders required.  Perhaps more importantly, Tettra comes with a price tag of $5 a user a month, and it's either all public (which makes it free), or private, and doesn't support white label branding.  Nuclino is closer to $10 a month to get the basic version which included access rights management which we need.  For PbWorks the free version includes a limited number of users, and then it's $20 a user a month.

I guess the key issue is we do we try to go with a self-hosted solution where we are on the hook for maintenance and support, or go with a hosted solution where they'll be increasing costs as more users come on board over the next two years.  BlueSpice has a hosted verison which is €185 a month, which would be €4440 over the course of two years, which we could probably manage within the existing budget, but the rub here is that all these products are looking to lock us in, and then gradually charging more and more as the number of users increases.  At the moment we're being offered a fixed fee to develop a solution, but it's not clear if there's additional long term budget for growth of the system.  Either way the growing system will require more resources; whether that's our time, or hard cash to a hosting provider.

We've broken out the original specification into the following requirements:

* Enable stakeholders ... to register and login to the system
* Enable stakeholders ... to provide the functionality to submit and publish
* Enable stakeholders ... to add narrative and other comments about a specific topic on the wiki
* Provide a mechanism for a nominated party to be able to moderate content posted on the wiki. For example, email alerts when resources or comments are submitted and with the nominated third party able to review content and to give or deny permission to publish on the wiki
* Method for blocking any users who repeatedly submit comments or additions that are deemed inappropriate by the moderators
* Notify registered users each time new material or comments are posted.
* Enable easy and seamless links to the login page of the Wiki from other websites
* Provide "white label" to allow private branding
* Be protected from "spam" and false registrations
* Avoid commercial advertisement and commercial product placement
* Collate live data (including analytical data relating to usage) which can be obtained with 24 hours' notice
* Ensure that the system is updated as required and to provide assurance on back-up and recovery time

Pat's started assessing some of the options against this list, but a blocker for any solution may be the ease of the use of the interface.  I just got WAGN running locally.  It's a Rails app, and it works fine, but the adding an image functionality only works with a URL, and I can't see any way to upload a file:

![WAGN interface](https://www.dropbox.com/s/7qlzociw1s8gjfz/Screenshot%202017-04-06%2010.49.21.png?dl=1)

Maybe that's not a showstopper, but based on HLP staff feedback I was discarding other wiki options last week that were similarly spartan. 

I've also been working out a testing plan (or assessment script) for the testers to use tomorrow:

1. Create a new page
2. Edit the page
3. Add a heading
4. Add a list
5. Add a document
6. Add (and resize) an image
7. Add a comment

but I reflect we will need to create moderation testing scripts as well.  I'm concerned that the mediawiki syntax (in BlueSpice) won't be so good for many of the stakeholders, but we should at least try it tomorrow.  BlueSpice has a free hosted demo that can be tried out, similarly for Nuclino and PbWorks.  The question now is whether it's worth evaluating any self-hosting options.  I feel that WAGN is too spartan.  Perhaps I can try demos of some ... I had trouble getting set up with sample sites for Dokuwiki, Foswiki and MoinMoin, while I saw OddMuse had mediawiki style formatting.  TiddlyWiki looked fun, but it didn't really have WYSIWIG like Nuclino and PbWorks ... Similar issue with TikiWiki. However XWiki does have clean WYSIWYG support and a fully featured moderation system ... 

### Related Links:

* https://www.quora.com/What-is-the-best-tool-for-creating-free-online-private-Wikis
