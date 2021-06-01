---
title: Pairing with Mike on Human Connection VueJS
author: Sam Joseph (retired) 
---

[![Mike and Sam Pairing on some VueJS code](https://img.youtube.com/vi/fiX8IhiCYGs/maxresdefault.jpg)](https://www.youtube.com/watch?v=fiX8IhiCYGs)

The [Human Connection project](https://human-connection.org/en/) is an ambitious attempt to create an open source alternative to Facebook.  It is being developed by a German non-profit organisation, and already released an alpha version using ? and mongodb on the backend.  They are currently undergoing a complete rebuild using VueJS and GraphQL.

AgileVentures has been working with them closely to provide community coding support and advice on the agile development process.  As part of an AgileVentures Human Connection sprint the other week I had tried to start work on a [refactoring ticket](https://github.com/Human-Connection/Human-Connection/issues/296) relating to how URLs get prefixed by the back end part of the system.  AgileVentures mentor Mike had also been looking at this ticket and we set up a pairing session to try and get to the bottom of it.

The issue as described in the ticket is that:

> The server side rendered frontend serves our images at `/api/uploads`. All requests to `/api` are proxied to the backend, which serves images at `/uploads/`. Right now, the backend mutates all URLs in the response to be prefixed with `/api`. This is a design flaw as the backend knows how the frontend proxies requests.

The requested change is that:

> We should prefix all `<img src="URL">` urls in the frontend with /api so the backend does not need to do it for the frontend.

And the creator of the ticket helpfully included a screenshot of one of the post images and its html:

```html
<img src="/api/uploads/teaser-img...png">
```

However we've all been struggling with understanding how to approach the ticket, since in the new "Nitro" system there is no functionality to upload images, and the images in the seed data are all external to the system.  As far as we can tell it look like the screenshot is off the Nitro system with some data migrated from the legacy alpha system.  We asked for clarification and were told that the badges in the system were served over the API and that would be a good place to look for an example, however when Mike and I started investigating together all the badges were loading correctly and they didn't seem to involve the API (or the associated uploads folder):

```html
<img title="indiegogo_en_turtle" src="/img/badges/indiegogo_en_turtle.svg" class="hc-badge">
```

The backend code that's doing the rewriting is easy to find in `nuxt.config.js`:

```js
    '/api': {
      // make this configurable (nuxt-dotenv)
      target: process.env.GRAPHQL_URI || 'http://localhost:4000',
      pathRewrite: { '^/api': '' },
      toProxy: true, // cloudflare needs that
      headers: {
        Accept: 'application/json',
        'X-UI-Request': true,
        'X-API-TOKEN': process.env.BACKEND_TOKEN || 'NULL'
      }
```

Mike and I were also struggling a little with running the system.  Since we'd started working on the project it had switched from a multiple repo to a mono-repo design in order to help manage aspects of the project.  Mike had switched over and very kindly helped me adjust to the new setup, but I was still plagued with issues relating to the Neo4J desktop app, that had originally been working smoothly, but was now failing intermittently.

It was working for Mike, but it took rather a long time for the system to boot up on his machine and so we spent most of the time in the pairing session talking about the issue and trying to understand it better.  Towards the end Mike did get the whole system running on his machine and that's what allowed us to determine that the badges did not seem to involve the API or the uploads folder.

We updated the ticket with our discoveries, and also spent some more time plowing through the code trying to work out what we could be doing.  The Badges.vue file has the following markup for the badge image:

```vue
      <img
        :title="badge.key"
        :src="badge.icon"
        class="hc-badge"
      >
```

and there is no custom vue img component as far as we can tell; so we would assume this is the code that generates the relevant HTML using basic Vue rules.  Many of the custom Vue templates for the project are in a separate styleguide, where we can see the Vue code that generates the post that was originally identified in the ticket as having the api/uploads URL:

```vue
    <div
      class="ds-card-image"
      v-if="image || $slots.image">
      <!-- @slot Content of the card's image -->
      <slot 
        name="image"
        :image="image">
        <img
          :src="image"
          v-if="!error"
          @error="onError" >
      </slot>
    </div>
```

Again we see the use of the default `<img>` tag, and this makes me think that one approach would be a custom Vue `<img>` tag that could do some pre-processing on the image url. Looking at this again now I think that a quick fix would be to some quick filtering in this location to see if it worked.  Maybe a filter:

https://vuejs.org/v2/guide/filters.html

Or maybe something like the following would work?

```vue
    <div
      class="ds-card-image"
      v-if="image || $slots.image">
      <!-- @slot Content of the card's image -->
      <slot 
        name="image"
        :image="image">
        <img
          :src="{{ image.replace('/api','') "
          v-if="!error"
          @error="onError" >
      </slot>
    </div>
```

Although re-reading the problem description I wonder if the solution isn't just to remove the middleware that's doing the rewriting - I guess it all depends on the legacy data and what form it's in and really to know if we are doing the right thing here we need access to some of that data, or at least clarity about what sort of test case to write.  The end to end cypress tests for writing a post won't do as there's no functionality to upload an image.  The actual change I'm proposing above would be in the styleguide, which doesn't have tests, and component test coverage of the front end is not complete - we'd need a `PostCard.spec.js` file.   Hopefully Mike and I can get some feedback on the proposed approach before we pair on this again.
