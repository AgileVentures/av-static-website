I've narrowed down the repeatedly failiing spectron tests to four offenders, two of which pass in isolation.  That leaves me the other two which are fail even when run separately and are part of the same chunk of actions related to loading a PDF file at startup.  The interesting thing is that these tests are checks of entering data and saving it that pass in other contexts, but are failing when the initial file is a PDF ... The same operation is completely fine if we are starting with a Word doc.  I'd like to run through the operations manually to see where were getting stuck, maybe even launch the tests from debug mode ... the difficulty with the details of the spectron tests is that it's not immediately obvious what they correspond to in the UI.  The first failure point is at 'type something, arrow down, then arrow up'.

If I work through it carefully I think I can make out the details from the function that does this action; a function that is used by both the PDF and Word related tests:

1. Insert the text 'This is my first translation' into the active target sentence
2. Check that text is where it should be
3. Press the down cursor key
4. Check the text in the new area is empty
5. Check that the text for translation is what we expect (we've moved on to the next source sentence)
6. Check the first suggested translation is as expected
7. Press the up cursor key
8. Check that we are back on the active target sentence with 'This is my first translation'
9. Check that we are back to a different source sentence
10. Check that the first suggestion is as we expect
11. Add some additional text
12. Press the return key
13. Check we are now on an active target sentence that is empty

So I try working through this manually, and I think that I can now see the problem is that the PDF conversion is introducing some unexpected line breaks, which makes the test fail on step 5, i.e. when we are checking for the source sentence to be a particular thing.  I believe these tests are passing on the main developers linux box, so this seems to relate to how OSX is parsing the PDF files ... 

I can set the textract system to not preserve line breaks, which avoids breaking the sentences at the end of lines.  However that also eliminates paragraph information, and even then the tests don't pass as there seems to be some ever so slight difference introduced at the point of the end of line - some additional unicode character or something.  So at some level I know this failing test is not too much to worry about, i.e. we still get some fairly reasonable behaviour in the interface, but we have a platform-specific wrinkle.  As it happens our main client is on OSX, so it would nice if there was a good general solution for parsing both Word and PDF files so that paragraphs would be maintained, but that extraneous line breaks could be completely eliminated.

Conceivably this might be something for textract to handle.  Remove only single linebreaks, vs removing multiple linebreaks, bu if the tests are all passing on the main developer's machine, then it seems like it might be fixable by adjusting the pdftotext or xpdf settings that are being used on my mac.   We can pass options from textract to pdftotext, but none of the options seem hugely relevant:

```
→ pdftotext --help
pdftotext version 4.00
Copyright 1996-2017 Glyph & Cog, LLC
Usage: pdftotext [options] <PDF-file> [<text-file>]
  -f <int>             : first page to convert
  -l <int>             : last page to convert
  -layout              : maintain original physical layout
  -simple              : simple one-column page layout
  -table               : similar to -layout, but optimized for tables
  -lineprinter         : use strict fixed-pitch/height layout
  -raw                 : keep strings in content stream order
  -fixed <number>      : assume fixed-pitch (or tabular) text
  -linespacing <number>: fixed line spacing for LinePrinter mode
  -clip                : separate clipped text
  -nodiag              : discard diagonal text
  -enc <string>        : output text encoding name
  -eol <string>        : output end-of-line convention (unix, dos, or mac)
  -nopgbrk             : don't insert page breaks between pages
  -bom                 : insert a Unicode BOM at the start of the text file
  -opw <string>        : owner password (for encrypted files)
  -upw <string>        : user password (for encrypted files)
  -q                   : don't print any messages or errors
  -cfg <string>        : configuration file to use in place of .xpdfrc
  -v                   : print copyright and version info
  -h                   : print usage information
  -help                : print usage information
  --help               : print usage information
  -?                   : print usage information
```

Interestingly pdftotext doesn't actually work on the command line from my mac, but seems to work okay from within the main app when it gets used by textract.  I hook the textract pdf conversion operation on the debugger and see that textract is using a separate node module called `pdf-text-extract`.  Here I can easily see the raw string coming in and I can see the `\n` characters in the breaks, and them doubled up as `\n\n` where the paragraphs are.  Would be nice to be able to distinguish between the two, but is this a job for textract, or a job for our software.  A simple fix for our test might be an additional layer of parsing that removed single but not multiple line breaks, but, we only want it for PDFs at the moment ...

There's room for me to investigate further to see exactly how `pdf-text-extract` does it's job, and what command line arguments it's specifying that allow it to get the conversion going correctly.  I've been trying the following directly on my commandline:

```
pdftotext -cfg ~/.xpdfrc -raw test.pdf test.pdf.txt
```
but it's still just generating junk ...
