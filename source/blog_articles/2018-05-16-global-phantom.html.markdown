Since I installed the phantomjsbeta to try to fix up the #paironauts tests acceptance tests have failed for the private node project I am working on - and I'm wondering if that's due to the global install I did.  I can see I have a few items installed globally:

```
→ npm ls -gp --depth=0 | awk -F/ '/node_modules/ && !/\/npm$/ {print $NF}'
exp
phantomjs
phantomjs25-beta
yarn
```

the bash command comes from https://stackoverflow.com/a/9283646/316729

However I've got no indication that phantomjs is actually used by the private node project.  I try removing them:

```
$ npm -g rm phantomjs
npm WARN rm not removing /Users/tansaku/.nvm/versions/node/v9.5.0/bin/phantomjs as it wasn't installed by /Users/tansaku/.nvm/versions/node/v9.5.0/lib/node_modules/phantomjs
removed 104 packages in 0.729s
[tansaku@Samuels-MBP:~/Documents/Github/AgileVentures/SocialPrescribingWiki (master)]$ 
→ npm -g rm phantomjs25-beta
removed 84 packages in 0.606s
```
I had to force the removal of the non-beta one:

```
→ npm -g rm -f phantomjs
npm WARN using --force I sure hope you know what you are doing.
up to date in 0.04s
```

but the acceptance tests still don't work - okay.  I was just grabbing for phantomjs since that was a global install and I couldn't think of any other reason why the acceptance tests for the electon project would all just stop working, but the first error we get is:

```
ChromeDriver did not start within 10000ms
```

maybe I should blow away the node_modules and try again, and that seems to fix it ...

