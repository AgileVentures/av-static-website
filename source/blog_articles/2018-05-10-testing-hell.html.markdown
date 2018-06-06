Okay, so overnight the main developer has done some cleanup.  I'd accidentally checking in an `.only` somewhere.  I do like the `.only` feature of mocha allowing you run a single test with the same base command, but there's always the risk of checking it in if you're not careful.  Perhaps we should adjust the linter to remove them?  In the ruby world with rspec/cucumber we can specifiy the precise test easily from the command line by line number in the file, which has it's own disadvantages if you are editing the file, but anyway.  I pulled down the updates to the branch from the main developer, and kicked off a test run.  Blocked due to existing `config/` dir.  Hmm.  The tests put their own config in place and them move it back out of the way each time.  Sometimes that gets snarled up if we don't complete the tests (e.g. from within the debugger or whatever), remove that, but no joy.  I roll git back to the place I was yesterday (deleting config for good measure) - still the acceptance tests won't even start.  I'm getting error messages like:

```
ChromeDriver did not start with 10000ms
```

and 

```
Application not running
```

It's not just in the debugger - same from the command line.  Hmm, is my computer just in a weirded out state?  The app starts from the command line ... project management interface is there - opens a file normally.

I google "ChromeDriver did not start with 10000ms" - lots of hits. So options:

1) dig more deeply into this error
2) try rebooting computer
3) ignore acceptance tests

It's difficult because I just invested time over the last three days to get the acceptance tests running on my computer so that I might be able to start some project related acceptance tests and drive from them.  Now having seemingly been within grasp of that point something has changed and I'm in a worse state regarding the tests that I was previously.

Same problem on the develop branch ... could this possibly be due to me upgrading phantomjs for the paironauts project the other day?

It turns out that was unrelated (I think).  Ultimately this problem went away when I deleted the node modules and reinstalled ... sigh ...
