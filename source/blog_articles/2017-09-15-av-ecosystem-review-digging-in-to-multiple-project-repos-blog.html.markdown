---
title: AV EcoSystem Review Digging into the Multiple Project Repos
date: 2017-09-14
tags: 
author: Sam Joseph
---

![digging](../images/digging.jpg)

So, struggling through tears for my father (just listened to a >Code episode on mental health) I'm bringing myself to focus on continuing this user rerequest feature about multiple repos for AV projects.  Let's warm up by making sure all our local code is up to date:

```
[tansaku@Samuels-MBP:~/Documents/Github/AgileVentures/WebsiteOne (master)]$ 
→ git checkout develop
Switched to branch 'develop'
Your branch is up-to-date with 'origin/develop'.
[tansaku@Samuels-MBP:~/Documents/Github/AgileVentures/WebsiteOne (develop)]$ 
→ git pull origin develop
From http://github.com/AgileVentures/WebsiteOne
 * branch              develop    -> FETCH_HEAD
Already up-to-date.
[tansaku@Samuels-MBP:~/Documents/Github/AgileVentures/WebsiteOne (develop)]$ 
→ git checkout 761_multiple_source_repository 
Switched to branch '761_multiple_source_repository'
[tansaku@Samuels-MBP:~/Documents/Github/AgileVentures/WebsiteOne (761_multiple_source_repository)]$ 
→ git pull origin 761_multiple_source_repository 
From http://github.com/AgileVentures/WebsiteOne
 * branch              761_multiple_source_repository -> FETCH_HEAD
Already up-to-date.
```

that process forced me to look at the waffle board, where I'm seeing that I have the changes to the Premium page in the "please check" column and that got approved by the marketing folks yesterday, so I have a chance to get that completed ... and it's done.  I'm glad I did save a copy of the html text in source control as I think I blew away all record of it on the staging server yesterday when I cloned production to staging, phew.  So that's deployed and the "please check" column is cleared.

Now I don't have much time for working on the multiple repo feature, but perhaps I can get some kind of interface spike to allow the addition of extra form fields, and I managed to do just that.  I found the right place and bashed around with JavaScript for a bit, and I've now got the next step of the cucumber test passing.

The code is super messy, but I've used [jQuery `insertafter`](http://api.jquery.com/insertafter/) to give me the new form fields when I click an "Add repo" button:

```js
WebsiteOne.define('Projects', function() {
  return {
    setupAddRepoButton: function () {
      $('#add_repo_field_button').click(function(event){
        event.preventDefault()

        var new_html = '<div class="form-group"> \
            <label class="control-label" for="project_github_url">GitHub link</label>\
            <input class="form-control" \
                   placeholder="https://github.com/projectname" name="project[github_url]" id="project_github_url">\
              </div>'
        $(new_html).insertAfter('#project_github_url')
        return false
      })
    }
  }
});

$(document).on('ready page:load', WebsiteOne.Projects.setupAddRepoButton)
```

I'm completely unsure about these `WebsiteOne.define('Projects'` things we have through the project.  Apparently that's for managing module dependencies with requirejs.  Not sure if we are actually using that - good to have a consistent namespace though.  Anyhow I'm pleased to get something that kicks out a few new form fields:

![](https://www.dropbox.com/s/yxf3kviciw5n4n2/Screenshot%202017-09-15%2010.14.00.png?dl=1)

Although it feels like I'm handrolling javascript here for a feature that must have been built over and over again.  Aha [https://github.com/ncri/nested_form_fields](https://github.com/ncri/nested_form_fields) well perhaps I should have a go with that then :-)  Still good to refresh my understanding of this wiring ...

There's also [https://github.com/ryanb/nested_form](https://github.com/ryanb/nested_form) but that hasn't been updated since 2013 ...


