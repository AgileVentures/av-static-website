Ahhh, so the rebuilding the mediawiki stack on azure leaks over three days and I suspect it may be more.  Yesterday I got stuck trying to open a port for the visual editor and I suspect there will be other blocks, but that's why I'm forcing myself through this to have it all documented so it's not a pain when we are really short on time.  I also got distracted playing with inkscape this morning, but that's another story.

I'm not sure that I have time, but I couldn't help checking to see if removing the security rule would prevent the VisualEditor from working, and unless there's some kind of caching going on that I understand, it looks like the security rule really isn't necessary.  I certainly suspected that since it isn't connected up to anything else in the system.  So in principle I can delete those security rules and that's one less thing to maintain and organise.  Confusing, because we definitely need security rules like that to work with AWS.  Presumably we'd need them for some other kinds of configuration on Azure ...

So the next thing to set up is the SSL, which I remember as somewhat complicated AND there's making sure that the Visual Editor continues to work with the SSL on.  There's also the moderation component, the wikieditor, and the banner.  Also I'd like to see if there's some way to run MediaWiki's cucumber tests

Although I see from the manual that we musn't run the unit tests on a production wiki: https://www.mediawiki.org/wiki/Manual:PHP_unit_testing/Running_the_unit_tests  Perhaps the browser testing is safer: https://www.mediawiki.org/wiki/Browser_testing but it looks like these might also make a mess of a production system.

Hmm, well maybe I go for the low hanging fruit first.  Let's install the WikiEditor in LocalSettings.php:

```php
## Load WikiEditor

wfLoadExtension('WikiEditor');

# Enables use of WikiEditor by default but still allows users to disable it in preferences
$wgDefaultUserOptions['usebetatoolbar'] = 1;

# Enables link and table wizards by default but still allows users to disable them in preferences
$wgDefaultUserOptions['usebetatoolbar-cgd'] = 1;

# Displays the Preview and Changes tabs
$wgDefaultUserOptions['wikieditor-preview'] = 1;

# Displays the Publish and Cancel buttons on the top right side
$wgDefaultUserOptions['wikieditor-publish'] = 1;
```

and the Moderation extension:

```php
## Moderation Extension

wfLoadExtension( 'Moderation' );

$wgGroupPermissions['sysop']['moderation'] = true; # Allow sysops to use Special:Moderation
$wgGroupPermissions['sysop']['skip-moderation'] = true; # Allow sysops to skip moderation
$wgGroupPermissions['bot']['skip-moderation'] = true; # Allow bots to skip moderation
$wgGroupPermissions['checkuser']['moderation-checkuser'] = false; # Don't let checkusers see IPs on Special:Moderation

$wgAddGroups['sysop'][] = 'automoderated'; # Allow sysops to assign "automoderated" flag
$wgRemoveGroups['sysop'][] = 'automoderated'; # Allow sysops to remove "automoderated" flag

$wgModerationNotificationEnable = true;
$wgModerationNotificationNewOnly = false;
$wgModerationEmail = $wgEmergencyContact;

```

which requires the code to be checked out of git into a `Moderation` directory in the `extensions` folder which also requires installing git:

```
$ sudo apt-get install git
$ git clone https://github.com/edwardspec/mediawiki-moderation
```

All the instructions for that are here: https://www.mediawiki.org/wiki/Extension:Moderation#Installation but note that we also need to ensure that the root git directory gets renamed from `mediawiki-moderation` to `Moderation`.  I can see that's all working as expected on IE11 and also that the maintainer of the Moderation extension has pushed my suggested change to the moderation banner into the master branch of the Moderation GitHub repo.

There's also the CSS to set up, so that we can have our banner throughtout

```
## Allow Common.css to load on restricted pages 

$wgAllowSiteCSSOnRestrictedPages = true;

```

and then drop the following into the Mediawiki:Common.css page:

```
/* CSS placed here will be applied to all skins */

#mw-head{
    margin-top:123px;
}
.mw-wiki-logo{
    background-image: none;
}

#hlp-banner {
   /* background:url('https://www.dropbox.com/s/2yokso0fkd3bivt/Screenshot%202017-05-08%2010.40.18.png?dl=1'); */
    width:100%;
    min-height:123px;
}

#block-mhl-hlp-mhl-hlp-logo {
    background: #003992;
}

#block-mhl-hlp-mhl-hlp-logo .content {
    position: relative;
}

#block-mhl-hlp-mhl-hlp-logo .block-inner {
    margin: auto;
    max-width: 1200px;
    text-align: right;
}

#block-mhl-hlp-mhl-hlp-logo .content:before {
    background: url(https://www.healthylondon.org/sites/all/themes/nhslondon/assets/img/hlp-people2.png) left bottom no-repeat;
    background-size: 135px 115px;
    float: left;
}

#block-mhl-hlp-mhl-hlp-logo .content:after {
    background: url(https://www.healthylondon.org/sites/all/themes/nhslondon/assets/img/hlp-people1.png) right bottom no-repeat;
    background-size: 130px 115px;
    float: right;
}

#block-mhl-hlp-mhl-hlp-logo .content:before, #block-mhl-hlp-mhl-hlp-logo .content:after {
    content: '';
    display: inline-block;
    height: 120px;
    margin-top: 5px;
    width: 140px;
}

@media (max-width: 767px) { 
    #block-mhl-hlp-mhl-hlp-logo img {
      width: 220px;
    }
}

@media (max-width: 767px) {
    #block-mhl-hlp-mhl-hlp-logo .content:after, #block-mhl-hlp-mhl-hlp-logo .content:before {
        display: none;
    }
}
```

If I recall I made some edits to the Vector template to get the HLP banner in there.  Here's the diff of before and after:

```
97,106d96
< 		<div id="hlp-banner">
<                   <div id="block-mhl-hlp-mhl-hlp-logo" class="block block-mhl-hlp">
<                     <div class="block-inner">
<                       <div class="content">
<                         <a href="https://www.healthylondon.org/" class=""><img typeof="foaf:Image" src="https://www.healthylondon.org/sites/default/files/styles/large/public/healthlondon-logo-ncs1_0.png" alt=""></a>
<                       </div>
<                     </div>
<                   </div>
<                 </div>
<                 <div id="hlp-wrapper">
248c238
<             </div>
---
> 
```
Which is enough to produce our nice resizable banner.  Okay time to get that SSL in there.  I had thought this was going to be a real pain on Azure, but actually it's all pretty much Apache configuration.  Maybe I get into that tomorrow, as I suspect there will be some sticking points ... I've posted some notes about how I did it last time:

https://community.bitnami.com/t/installing-certbot-for-lets-encrypt-ssl-certificate/46431/2

and I need to follow up with the developer there.  Let's try for that in part 4 and if I'm lucky I'll also get to testing the backup stuff ... and also test importing all the data from the production system into the backup system.

