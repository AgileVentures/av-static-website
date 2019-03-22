---
title: Pairing with Ben on Python Flask Seasurf
author: Sam Joseph
---

[![Ben and Sam Pairing on some Python code](https://img.youtube.com/vi/q2VPTch6hto/maxresdefault.jpg)](https://www.youtube.com/watch?v=q2VPTch6hto)

Another Tuesday, another remote pairing session with a new person.  Ben had carefully picked out an open source plugin for Python flask (micro-web system) that was related to the closed source work he had been doing.  Their server needed to provide cookie support for some clients and Oauth for others.  This mean they wanted Cross-Site Response Forgery (CSRF) support in some instances and not others.  

Ben had made the necessary modifications to his fork of the [flask plugin 'seasurf'](https://github.com/maxcountryman/flask-seasurf) and had solved the issue for his main project, but his [pull request](https://github.com/maxcountryman/flask-seasurf/pull/84) to introduce the changes into the main seasurf project had been turned down due to errors flagged by the CI system.  While everything was working fine in Python 3.6 it seemed that some older versions of Python were seeing test failures.

Ben had pointed out that the project had similar failures on its master branch but there hadn't been any follow up, and so we were planning to dig into these errors to look for a solution, so that ultimatley the change could be merged into the main project and Ben could remove his fork as a dependency in their production system.

Ben already had a hunch about the problem in that dictionaries in Python 3.6 had changed such that they had a reliable default order.  The test failures in the older versions of Python were clearly tied to checking for cookies as part of tests like these:

```py
    def test_can_manually_validate_exempt_views(self):
        with self.app.test_client() as c:
            rv = c.post('/manual')
            self.assertIn(b('403 Forbidden'), rv.data)
            cookie = self.getCookie(rv, self.csrf._csrf_name)
            token = self.csrf._get_token()
            self.assertEqual(cookie, token)

    def getCookie(self, response, cookie_name):
        cookies = response.headers.getlist('Set-Cookie')
        for cookie in cookies:
            key, value = list(parse_cookie(cookie).items())[0]
            if key == cookie_name:
                return value
        return None
```

In the above code we'd get failures for the `self.assertEqual(cookie, token)` step indicating that the `cookie` variable was coming back as `None`.  We started the session with Ben talking me through the logic of this code.  Ben had set up docker and docker-compose to allow him to run his code in Python 3.5:


```docker-compose.yml
version: '2'
services:
  seasurf:
    build:
      context: .
      dockerfile: Dockerfile
    working_dir: /opt/app
    command: python3 -m unittest
    volumes:
      - ./:/opt/app
```

```Dockerfile
FROM python:3.5

RUN pip3 install flask

CMD ['python3', '/opt/app/test_seasurf.py']
```

allowing him to run the test suite in Python 3.5 via the following command

```
docker-compose run --rm seasurf
```
 
Where he was seeing intermittent failures for both his own tests and legacy tests. This was strengthening his hunch that in the older versions of Python dictionary ordering was not fixed, given rise to errors in some cases and not others depending on how dictionary entries were returned.  It all seemed to come down to the way the `getCookie` method was extracting information from the `Set-Cookie` header.  Ben popped in some print statements so we could see what was going on, and I encouraged setting up to run a single test at a time, which it turns out we could via syntax like this:

```
python3 -m unittest -q test_seasurf.SeaSurfTestCaseSkipValidation.test_skips_validation
```

it took a little fiddling with the docker-compose file to get the same working there, but pretty quickly we were hitting a single test and Ben had modified the `getCookie` code to search for the particular key by lookup, rather than as it currently was, just assuming that the key we wanted was the first in the list of items in the cookie dictionary:

```py
    def getCookie(self, response, cookie_name):
        cookies = response.headers.getlist('Set-Cookie')
        for cookie in cookies:
            value = parse_cookie(cookie).get(cookie_name)
            if value:
                return value
        return None
```  

We checked that Ben's change seemed to have reliably fixed the test in Python 3.5  by dint of running the tests 5 times and not seeing any intermittent failures. I had access to Ben's editor via a VSCode Liveshare and so I was able to highlight bits of code I was talking about as I tried to follow what was going on, so then I tried some very minor refactorings but didn't really add anything, apart from speed replacing the several instances of `getCookie` throughout the test file.  It was crying out to be DRYed up into a single location, but in the interests of time and ensuring our pull request would be simple we demurred.  

Ben went further suggesting putting in a PR to just fix the main branch errors first before trying again with a PR for his new functionality.  An approach that took into consideration the person who would be reviewing the PR and potentially merging it.  This met with double thumbs up approval from me, and that approach bore fruit the following day with a [PR](https://github.com/maxcountryman/flask-seasurf/pull/88) being merged in, and Ben followed it up with a second [PR](https://github.com/maxcountryman/flask-seasurf/pull/89) that added the new CSRF skipping functionality that was able to go green and was also merged in the following day. 

As the week before we'd started the pairing session using a google hangout link shared via Slack, but in contrast to the pairing session with Bryan we'd not used github pong at all, and in fact not committed anything via git as part of the session itself; but that had all worked just fine.   I added an [issue](https://github.com/maxcountryman/flask-seasurf/issues/87) to the repo about the need to DRY out the `getCookie` method and the maintainer gave that a thumbs up and flagged as `help wanted` and `good first issue` so we're all set up for a follow up session.
