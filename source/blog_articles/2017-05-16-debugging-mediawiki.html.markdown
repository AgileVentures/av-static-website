So I tried creating a new account for mediawiki on our staging box.  It created the following cookies:

![cookies created for new user account](https://www.dropbox.com/s/f73wmvq1g31uh6o/Screenshot%202017-05-15%2010.56.38.png?dl=1)

Three of these were reported created on the main wiki: cpPosTime, UseDC, UseCDNCache. I navigated to the main page and was logged out, although now there was just one cookie:

![single cookie after redirect](https://www.dropbox.com/s/mha525jx20y2yow/Screenshot%202017-05-15%2011.00.21.png?dl=1)

Navigating around I stayed logged out, and then tried logging on, which worked and generated a couple more cookies:

![more cookies after login](https://www.dropbox.com/s/h68yx9n2tz9jpv5/Screenshot%202017-05-15%2011.01.59.png?dl=1)

So the fact that this was happening on our staging box made it seem like this was a bitnami mediawiki issue, or possibly something to do with the extensions we had configured on both.  So far the only extensions configured on the staging site are the moderation extension and the wikieditor.  I could try disabling these two to see if we get the same effect, or I could even try a completely fresh Bitnami MediaWiki deploy ... but faster might be to search the Bitnami forums.  A few searches didn't reveal anything:

* https://community.bitnami.com/search?q=mediawiki%20logged%20out
* https://community.bitnami.com/search?q=mediawiki%20create%20account
* https://community.bitnami.com/search?q=mediawiki%20cookies
* https://community.bitnami.com/search?q=mediawiki_session

I was about to post to the Bitnami forum, when I tried disabling the wikieditor and the moderation extension, and then create account maintained a login.  I re-enabled the wikieditor appearing to confirm that the moderation extension was the culprit.  I did a few extra tests and opened a [ticket on the mediawiki-moderation github repo](https://github.com/edwardspec/mediawiki-moderation/issues/11).
