---
title: AV EcoSystem Review Athletic Reps
date: 2017-09-06
tags: 
author: Sam Joseph
---

![meeting](/images/reps.jpg)

So I've managed to get to the blog-face a little earlier today despite Slack/Twitter/Email distractions, but I'm going to stick to the "adding tests" flow that I started yesterday as a way of getting some athletic reps in on node testing.  That said, we've got a fairly serious bleed on our emails getting marked as spam that may prevent me from spending much more time on code in these sessions.  Can I afford a 15 minute rep on node tests every morning for the next two weeks?  So many regular station keeping activities that the days are packed!

Anyhow, so at least from yesterday we have some tests in the `greeter_bot` project.  Let's get to the completely different `project_greeter_bot` project.  Where would I start?  Grab the issue id for creating tests from the project itself, that's [https://github.com/AgileVentures/project_greeter_bot/issues/5](https://github.com/AgileVentures/project_greeter_bot/issues/5) - funny how it feels like breaking the ice on something in order to push through the little "I'm not sure where to start" feeling.  Bring up the terminal in the right directory.  No outstanding pull requests on the project, pull the latest code from master:

```sh
[tansaku@Samuels-MBP:~/Documents/Github/AgileVentures/project_greeter_bot (master)]$ 
→ git pull origin master
remote: Counting objects: 1, done.
remote: Total 1 (delta 0), reused 1 (delta 0), pack-reused 0
Unpacking objects: 100% (1/1), done.
From https://github.com/AgileVentures/project_greeter_bot
 * branch            master     -> FETCH_HEAD
   bc396db..46944ce  master     -> origin/master
Updating bc396db..46944ce
Fast-forward
 server.js | 14 +++++++-------
 1 file changed, 7 insertions(+), 7 deletions(-)
```

create a branch for the new tests

```sh
→ git checkout -b 5_add_tests
Switched to a new branch '5_add_tests'
[tansaku@Samuels-MBP:~/Documents/Github/AgileVentures/project_greeter_bot (5_add_tests)]$ 
```

install mocha

```sh
[tansaku@Samuels-MBP:~/Documents/Github/AgileVentures/project_greeter_bot (5_add_tests)]$ 
→ npm install mocha --save-dev
project_greeter_bot@0.0.0 /Users/tansaku/Documents/GitHub/AgileVentures/project_greeter_bot
└─┬ mocha@3.5.0 
  ├── browser-stdout@1.3.0 
  ├─┬ commander@2.9.0 
  │ └── graceful-readlink@1.0.1 
  ├── debug@2.6.8 
  ├── diff@3.2.0 
  ├─┬ glob@7.1.1 
  │ ├── fs.realpath@1.0.0 
  │ ├─┬ inflight@1.0.6 
  │ │ └── wrappy@1.0.2 
  │ ├─┬ minimatch@3.0.4 
  │ │ └─┬ brace-expansion@1.1.8 
  │ │   ├── balanced-match@1.0.0 
  │ │   └── concat-map@0.0.1 
  │ ├── once@1.4.0 
  │ └── path-is-absolute@1.0.1 
  ├── growl@1.9.2 
  ├── json3@3.3.2 
  ├─┬ lodash.create@3.1.1 
  │ ├─┬ lodash._baseassign@3.2.0 
  │ │ ├── lodash._basecopy@3.0.1 
  │ │ └─┬ lodash.keys@3.1.2 
  │ │   ├── lodash._getnative@3.9.1 
  │ │   ├── lodash.isarguments@3.1.0 
  │ │   └── lodash.isarray@3.0.4 
  │ ├── lodash._basecreate@3.0.3 
  │ └── lodash._isiterateecall@3.0.9 
  └─┬ supports-color@3.1.2 
    └── has-flag@1.0.0 
```

and chai, since I have trouble remembering the `assert` syntax:

```sh
[tansaku@Samuels-MBP:~/Documents/Github/AgileVentures/project_greeter_bot (5_add_tests)]$ 
→ npm install chai --save-dev
project_greeter_bot@0.0.0 /Users/tansaku/Documents/GitHub/AgileVentures/project_greeter_bot
└─┬ chai@4.1.2 
  ├── assertion-error@1.0.2 
  ├── check-error@1.0.2 
  ├── deep-eql@3.0.0 
  ├── get-func-name@2.0.0 
  ├── pathval@1.1.0 
  └── type-detect@4.0.3 
```

So I searched for a `mocha init` commmand equivalent to `rspec --init` or `bundle init` and there is one, but it seems to generate a client side scaffold with html etc.--so not what we need here.  Feels like there's room for a little automation there ... I've opened a [feature suggestion](https://github.com/mochajs/mocha/issues/2989) with Mocha, and in the meantime updated the projects package.json with:

```sh
  "scripts": {
    "test": "mocha test/**/*.js"
  },
```

and dumped the following in `test/greeter_spec.js`

```sh
[tansaku@Samuels-MBP:~/Documents/Github/AgileVentures/project_greeter_bot (5_add_tests)]$ 
→ mkdir test
[tansaku@Samuels-MBP:~/Documents/Github/AgileVentures/project_greeter_bot (5_add_tests)]$ 
→ touch test/greeter_spec.js
```

```js
require { expect } from 'chai';

describe('Project Greeter bot', function(){
  it('should have greetings', function(){
    expect(1).to.equal(1);
  });
});
```

and got this error:

```
[tansaku@Samuels-MBP:~/Documents/Github/AgileVentures/project_greeter_bot (5_add_tests)]$ 
→ npm test

> project_greeter_bot@0.0.0 test /Users/tansaku/Documents/GitHub/AgileVentures/project_greeter_bot
> mocha test/**/*.js

/Users/tansaku/Documents/GitHub/AgileVentures/project_greeter_bot/test/greeter_spec.js:1
(function (exports, require, module, __filename, __dirname) { require { expect } from 'chai';
                                                                      ^

SyntaxError: Unexpected token {
```

I've not remembered some of the syntax from yesterday.  This is the point of the rep - find the pain point that you would not find if you just copied and pasted the code.  Can I now solve this without resorting to looking up the solution ... It's so tempting to just go look at the correct syntax, but I will force myself to slowly read the error message, `SyntaxError: Unexpected token {` so maybe just try dropping the curly braces ... no both the following fail:

```js
require expect from 'chai';
```

```js
require { 'expect' } from 'chai';
```

Okay, going to look at yesterday's code ... ah, here's what it should be:

```js
var chai = require('chai');
var expect = chai.expect;
```

hmm, have I been throwing in a mix of Python/React syntax there? :-)  Okay, hopefully this pain point will help me remember - would be great to repeat every day for a few to nail it down - now let's see if I can hook up the test to real code again.

Took a bit of a big bite here - moved all the js code into a lib dir, moved all the greetings text/variables to a `greetings.js` file, renamed the test files, made loads of things consts, updated package.json with `"start": "node lib/server.js"` and tried the following at the end of the `greetings.js` file in order for it to be pulled into both test and `server.js`:

```js
module.export = greetings;
```

which did not work ...

```sh
[tansaku@Samuels-MBP:~/Documents/Github/AgileVentures/project_greeter_bot (5_add_tests)]$ 
→ npm test

> project_greeter_bot@0.0.0 test /Users/tansaku/Documents/GitHub/AgileVentures/project_greeter_bot
> mocha test/**/*.js

module.js:471
    throw err;
    ^

Error: Cannot find module 'greetings'
```

so I'm not remembering - try some alternatives?

```js
default module greetings;
```

```js
export.module = greetings;
```

neither of these work - I think I'm throwing in React syntax again - what's the solution?  Ah I was closer to start with, the export needs to be pluralized:

```js
module.exports = greetings;
```
but still not working - ah path needed in test with:

```js
const chai = require('chai');
const expect = chai.expect;

const greetings = require('../lib/greetings');

describe('Project Greeter bot', function(){
  it('should have greetings', function(){
    expect(greetings.greeter).to.include('something');
  });
});
```

Yes, that's it.  Okay, must remember the pluralization on `module.exports = ...` and that this is different from React's `export default <function>`.  A quick update of the tests to something a little more meaningful, a check that things basically still work running the server locally against the old AV mentors instance and the pull request is in:

[https://github.com/AgileVentures/project_greeter_bot/pull/23](https://github.com/AgileVentures/project_greeter_bot/pull/23)

and I start having thoughts about all the other little pieces to make this into a serious, accessible project:

1. use of develop/production modes to avoid confusion over slack IDs
2. coherent instructions about getting the project running for new developers
3. checking this deploys to Azure
4. setting up GitHub notifications on a #bot-notify channel
5. adding projects to AV getting started and projects ...

For another day I guess - back to the grindstone ...

p.s. just wondering does the code of conduct hold the maintainers to treat all the contributors with respect as well as the contributors treating each other with respect? looks like it does ...
 
