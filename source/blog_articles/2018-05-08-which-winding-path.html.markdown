So yesterday I saw the source of the the problem for some of my failing tests, which is that PDF's are being processed slightly differently on my OSX box and the lead developers linux system.  The path of least resistance is to look more into `pdf-text-extract` which is that the `textract` system is using to process PDF files.  The combination of VSCode debugging and the mode_modules folder means that I can breakpoint in the `pdf-text-extract` code itself and see precisely the command it uses to operate pdftotext:

```
 pdftotext -enc UTF-8 -raw test.pdf test.pdf.txt
```

which now works on the commandline for me.  Now none of the pdftotext options (which we looked at yesterday) do anything that would strip single line breaks for us ..., so that pushes us back up the chain to either textract, or our own code.  I've opened a ticket on the textract repo:

https://github.com/dbashford/textract/issues/156

Currently textract uses the following regexes to eitehr preserve or strip linebreaks:

```js
WHITELIST_PRESERVE_LINEBREAKS = /[^A-Za-z\x80-\xFF\x24\u20AC\xA3\xA5 0-9 \u2015\u2116\u2018\u2019\u201C|\u201D\u2026 \uFF0C \u2013 \u2014 \u00C0-\u1FFF \u2C00-\uFFFF \.,\?""!@#\$%\^&\*\(\)-_=\+;:<>\/\\\|\}\{\[\]`~'-\w\n\r]*/g  // eslint-disable-line max-len
WHITELIST_STRIP_LINEBREAKS = /[^A-Za-z\x80-\xFF\x24\u20AC\xA3\xA5 0-9 \u2015\u2116\u2018\u2019\u201C|\u201D\u2026 \uFF0C \u2013 \u2014 \u00C0-\u1FFF \u2C00-\uFFFF \.,\?""!@#\$%\^&\*\(\)-_=\+;:<>\/\\\|\}\{\[\]`~'-\w]*/g  // eslint-disable-line max-len
```

There was some advice on matching single new lines in [this stackoverflow post](https://stackoverflow.com/questions/18011260/regex-to-match-single-new-line-regex-to-match-double-new-line) which I then added to textract, but then that didn't do quite what I wanted - seeming to remove all line breaks anyway ... however if I place it before the main textract regexes it seems to do the trick.  I opened a very rough work in progress pull request: 

https://github.com/dbashford/textract/pull/157

but the real question is what will the tests do for us now ... and they now pass, which is good.  We do seem to still have a difference between two PDF test files where one does have double line breaks for paragraphs and the other does not, but not much more we can do on that at this point.  The PDF tests now also pass in batch, but I get six errors, some of which I'm not sure I've seen before ... I take the fix out of the textract module to see if we get back to our original four, but no, there's six again - different subset including the PDF related fails ... which is making it feel like some of these are going to be difficult to track down timing issues.

Perhaps sensibly I'd run each of these individually to check for batch vs single run issues.  Or I just move on and rely on the main developers machine to be the ground truth ...


