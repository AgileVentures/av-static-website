So leaving it overnight has not magically fixed the corrupted database file, and it seems I've lost several hours work from the last few days, and I can't figure out any easy way to recover it.  Brent was suggesting git, but the annoying thing about this LibreOffice Base (MSOffice/LotusApproach) style system is that the data is stored along with db schema and interface.  I can't really put this in a public git repository.  I could set up a private git account on GitHub through AgileVentures, for which this is a project, but I'm going to take a stab at a dropbox backed up approach.  Cloning the system at critical points.

I start by adjusting all the tables so that the autovalue is set on the primary fields, which should clear up the main issue the client experienced in the alpha version, which was an inability to save data.  I'm now working with the odb in dropbox and I can see the version history going past:

![](https://dl.dropbox.com/s/4imbyibc39ilidm/Screenshot%202018-01-04%2010.00.23.png?dl=0)

I'd moved off dropbox before because I was worried that the dropbox syncing might be causing problems and a previous attempt to backup from dropbox hadn't worked, but I think I've worked out that there's a double save operation required and I'll keep tracking the dropbox updates to check I'm getting proper backups and I don't lose any more work ...

Now onto trying to fix the Standing Order form that I was sure I did before Xmas, but appears to have been lost (not properly saved), the editing of which caused the main crash yesterday ...

So I get the form updated (i.e. the subform that connects to the recurring sub table), and it seems okay, but then I try to add a new form field and we get the spinning beach ball of death again.  This was the point yesterday when I killed the system and we lost all the data.  I note now that dropbox is stuck trying to upload the latest version.  Maybe the trick is that I have to create a new subform from scratch rather than modifying the existing one, but this is a silly game of guess what's causing the problem ... hmm, should I switch back to working on the PC?  Should we have just gone with MSAccess, or tried harder to convince the client to go with a cloud based solution?  Personally I'm still enamoured with the feature that the client loved about Lotus Approach which was their ability to tweak the forms themselves.

Anyway, this time I was less hasty about killing the application and in about five minutes, while I looked up the latest version of LibreOffice (I think there's a new version out) and fired up my PC, the libre on my Mac unlocked and I was able to save it ... it locked up again a little time later, so I killed it and was able to recover with the latest version of LibreOffice for mac.  I sorted out the Standing Order Details form.  Here's a screenshot to prove it:

![](https://dl.dropbox.com/s/ve6vacheeuz4a69/Screenshot%202018-01-04%2011.58.33.png?dl=0)

Carefully saving after each change, and now I guess I'll have to push on the rest tomorrow, after making a backup copy.
