---
title: Azure MediaWiki Stack Part 1
date: 2017-05-18
tags: MediaWiki
author: Sam Joseph
---

![mediawiki](/images/MediaWiki.svg)


Okay, so how did we put this complete NHS HLP wiki instance together?  Let's go through it step by step:

1. Create a Bitnami account, and go to the console for "Azure Launchpad"
2. Choose the Bitnami MediaWiki Stack which provides a one-click install solution for MediaWiki (1.28.2)
3. Choose Basic A0 type with shared virtual core and 0.75Gb of RAM, and select 'West Europe'
4. Wait for that to launch

![box launching](https://www.dropbox.com/s/q7jtjvxeshg8eql/Screenshot%202017-05-17%2011.13.31.png?dl=1)

5. ssh into the box using the provided password and start the configuration process
6. edit `/home/bitnami/apps/mediawiki/htdocs/LocalSettings.php` (I like `nano`)  

  a) adjust `$wgSitename` and `$wgMetaNamespace`  
  b) adjust `$wgEmergencyContact` and `$wgPasswordSender` to `"hlpwikiadmin@agileventures.org";`  
  c) set `$wgEnotifWatchlist     = true;` to enable watchlist notifications  
  d) set `$wgUseInstantCommons   = true;`  to allow images from wikicommons  
  e) disable anonymous edits by adding the following at the end of the file:  

      ## Access Options

      $wgGroupPermissions['*']['edit'] = false;
      $wgGroupPermissions['*']['createpage'] = false;
      
 f) add email notification settings (requires azure sendmail addon):  
 
      ## Email notifications

      $wgUsersNotifiedOnAllChanges = array('User', 'Tansaku');

      $wgSMTP = [
          'host'     => 'ssl://smtp.sendgrid.net',
          'IDHost'   => 'sendgrid.net',
          'port'     => '465',
          'auth'     => true,
          'username' => '<GET FROM AZURE SENDMAIL ADDON>',
          'password' => '<GET FROM AZURE SENDMAIL ADDON>',
      ];

      $wgDefaultUserOptions['enotifwatchlistpages'] = true;
      
 g) consider enabling logging - we did have it working at one point with the following:  


      ## Logging

      $wgDebugLogFile = "/home/bitnami/apps/mediawiki/logs/debug-{$wgDBname}.log";


  but log file is not being updated at the moment ...

      bitnami@bitnami-mediawiki-8a65:~/apps/mediawiki/logs$ ls -la
      total 12
      drwxrwxr-x 2 bitnami root    4096 May  1 10:09 .
      drwxr-xr-x 7 root    root    4096 Apr 28 12:30 ..
      -rw-rw-r-- 1 bitnami bitnami 2006 May  1 15:41 debug-bitnami_mediawiki.log
      
  Was there another setting we missed?  Anyway, should be off in production for performance (I imagine)

7. Add the Cite Extension

    ```
    ## Cite Extension

    wfLoadExtension( 'Cite' );
    ```
    
8. VisualEditor setup
   
   a) follow instructions at https://www.mediawiki.org/wiki/Extension:VisualEditor 
   b) `wget https://extdist.wmflabs.org/dist/extensions/VisualEditor-REL1_28-93528b7.tar.gz` in extensions directory `~/apps/mediawiki/htdocs/extensions` 
   c) add configuration to end of `LocalSettings.php`
    
    ```
    ## Visual Editor

    wfLoadExtension( 'VisualEditor' );

    // Enable by default for everybody
    $wgDefaultUserOptions['visualeditor-enable'] = 1;

    // Optional: Set VisualEditor as the default for anonymous users
    // otherwise they will have to switch to VE
    // $wgDefaultUserOptions['visualeditor-editor'] = "visualeditor";

    // Don't allow users to disable it
    $wgHiddenPrefs[] = 'visualeditor-enable';

    // OPTIONAL: Enable VisualEditor's experimental code features
    #$wgDefaultUserOptions['visualeditor-enable-experimental'] = 1;
    ```

 d) add the Parsoid service following these instructions: https://www.mediawiki.org/wiki/Parsoid/Setup but note that first we have to install nodejs, which involved pulling in the nodesource:

    ```sh
    $ wget -qO- https://deb.nodesource.com/setup_4.x | sudo bash -
    $ sudo apt-get install nodejs
    ```
     
and the above worked fine last week, but now I am getting:

```
sudo apt-get install parsoid
Reading package lists... Done
Building dependency tree       
Reading state information... Done
E: Unable to locate package parsoid
```

and now it just worked again ... okay ... network time outs I guess, but that's crushed my blog flow.  Find out how this continues in part 2 ...




