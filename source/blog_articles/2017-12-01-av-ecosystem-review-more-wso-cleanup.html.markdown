So running the cuke tests I get this one bit of extraneous output:

```
Encountered Error in get: 404 Resource Not Found: {"code":"route_not_found","kind":"error","error":"The path you requested has no valid endpoint."}
```
and of course huge dumps of vcr and puffing billy cache changes.  I can clean that up with our `rake vcr_billy_caches:reset` commmand but I wonder what other folks do?  Perhaps I should ask the VCR folks?  I think the issue we have is that we need to throw out some of our old caches and reload, dealing with the pain that will cause ... maybe we need some VCR version of dependabot that will throw out old caches for us?

