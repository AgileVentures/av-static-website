---
title: AV EcoSystem Review Locking Things Down
tags: 
author: Sam Joseph
---

![locking](../images/locking_pliers.jpg)

So I had to put off blogging for a while today.  My station keeping regulars have been starting to get in the way of certain emails and admin that I keep putting off.  That done I've got to get a google meta-tag out to the main site to unlock appointment functionality in google calendar.  We've also got the issue of some new folks (accidentally?) editing the getting started pages, which is becoming a pain, and I'm thinking adding another feature flag there is the way to go ...

I've already got the google tag on develop and I push that towards staging ... we've also got the performance issue, and I've had the thought that we could try turning off the "Next Event" functionality on the main site for a week and see if anyone misses it.  But okay, let's get a feature flag on the page editing set up.  That's pretty straightforward in `static_pages/show.html.erb`:

```erb
    <% if user_signed_in? and Features.edit_static_pages.enabled %>
      <ul>
        <li><%= custom_css_btn 'edit', 'fa-2x fa fa-pencil-square-o', mercury_edit_path, id: 'edit_link',
                               data: {save_url: static_page_mercury_update_path(@page.slug)} %>
      </ul>
    <% end %>
```
or so I thought.  A quick byebug showed a dangerous edge there:

```sh
[16, 25] in /Users/tansaku/Documents/GitHub/AgileVentures/WebsiteOne/app/views/static_pages/show.html.erb
   16:       <div id="static_page_title" style="display: inline;" data-mercury="simple" data-type="editable"><%= @page.title %></div>
   17:     </h1>
   18:   </div>
   19:   <div id="page-side-panel">
   20:     <% byebug %>
=> 21:     <% if user_signed_in? and Features.edit_static_pages.enabled?%>
   22:       <ul>
   23:         <li><%= custom_css_btn 'edit', 'fa-2x fa fa-pencil-square-o', mercury_edit_path, id: 'edit_link',
   24:                                data: {save_url: static_page_mercury_update_path(@page.slug)} %>
   25:       </ul>
(byebug) Features
Features
(byebug) Features.edit_static_pages
#<Config::Options enabled=true>
(byebug) Features.edit_static_pages.enabled?
nil
(byebug) Features.edit_static_pages.enabled
true
```
fixed that and got it up.  Glad I didn't postpone getting the google verification tag in the pipeline for this, but really want to get this out too.  In the meantime the build on staging passed and I was able to check the verification tag on staging.  Pushed that on to production.  I noticed that I also have the new Puma upgrade, so I thought I'd kick off a load test on staging to see what improvement that gives us (if any).  Emails back from that test - 23ms improvement - nothing to write home about.

Aha, production deployed, site verified and Google calendar enabled for the Google AgileVentures nonprofit team - result? We'll see ...
