---
title: Pairing with Bryan Prevention Point Django
author: Sam Joseph
---

[![Bryan and Sam Pairing on some Django code](https://img.youtube.com/vi/P_opPE1UI_c/maxresdefault.jpg)](https://www.youtube.com/watch?v=P_opPE1UI_c)

So Bryan and I finally managed to sit down to code together on a CodeForPhilly project.  It was really exciting to hear about how CodeForPhilly was managing all of these different open source non-profit projects.  Bryan was attending meetups in Philadelphia and while the CypherPhilly project had not yet gotten to the point of being able to hand out issues for us to work on, the PreventionPoint project had.  Bryan had been to a meetup specifically about this project.  [Prevention Point Philadelphia](http://ppponline.org/) is a private nonprofit organization providing harm reduction services to Philadelphia and the surrounding area. Its mission is to help end deaths from overdoses in Philadelphia.  

As it stands Prevention Point manages the data from their programs in separate Excel/Google spreadsheets, disparate Electronic Health Records (EHRs), and partner data systems. This means that they cannot see all the activities associated with an individual program participant, and also makes it impossible for them to do meaningful analyses that monitor program health and evaluate efforts.  CodeForPhilly is supporting the creation of a [unified reporting system](https://codeforphilly.org/projects/prevention_point_unified_reporting_system), migrating all of the disparate data sources into one system, and making a UI that allows Prevention Point to access all participant data in one place.  The goal being to increase the ease with which program coordinators can evaluate and monitor activities.

Bryan and I talked about how the organisation was using spreadsheets to track people coming into their reception and scheduling appointments.  The person coming into the PreventionPoint offices would usually be someone already in the system and coming in to pick up a prescription or make an appointment to see a behavioural specialist.  Hearing about the idea of creating a custom system from scratch in Python/Django I was initially concerned about whether the non-profit in question would be able to ensure maintainance and support for a custom system in the long term, but Bryan explained that CodeForPhilly provided a framework that meant that the system would be supported and maintained independently of how long the initial developers were volunteering.  CodeForPhilly is a subsidiary of CodeForAmerica which means that there is at least some structure and funding in place to ensure that the non-profits would not be left in the lurch.  This is of course exactly the kind of framework that we at AgileVentures were trying to achieve on a global scale, but it seems that the hyper-local focus of groups like CodeForPhilly with their location specific meetups was working very effectively.

I had a lot of questions, and Bryan very patiently explained the background and the need for an API to support a react(?) front end that would be built to support receptionists managing folks coming to the waiting room, usually on a walk in basis rather than for pre-set appointments.  We sketched out the process for and what API endpoints might be needed:

1) someone comes in to the PreventionPoint office for STEP service: gives name

2) receptionist looks them up by name (assume they exist)

```
  /step/api/participants --> list json
  /step/api/participants?name=xyz --> list json
```

3) person says what service they want

```
  /step/api/services --> list json
  /step/api/services?name=xyz --> list json
```

4) receptionist indicates that the person wants that service
  --> that goes into a queue that other system users can see (e.g. doctors)

```
  POST /step/api/participants/:id/services/:id --> adjust db and confirm
  ---> join table (appointment model?)
```

We discussed how a person might ask for more than one service, and that could turn into multiple appointments.  We also started with the assumption that the person would be in the system, and that they would be arriving to pick up a prescription or maybe see a behavioural specialist.

Ultimately after discussing all this and working it all out, we didn't have so much time for coding, but we managed to put something in place with the following few changes to `preventionpoint/urls.py`:

```py
from step.participants import views as participant_views
router.register(r'participants', participant_views.ParticipantViewSet)
```

`step/participants/serializers.py`:

```py
from step.models import Participant
from rest_framework import serializers

 class ParticipantSerializer(serializers.HyperlinkedModelSerializer):
    class Meta:
        model = Participant
        fields = ('first_name', 'last_name', 'gender', 'race')
```      

and `step/participants/views.py`:

```py
from rest_framework import viewsets
from step.models import Participant
from step.participants.serializers import ParticipantSerializer

 class ParticipantViewSet(viewsets.ModelViewSet):
    """
    API endpoint that allows UDS to be viewed or edited
    """
    queryset = Participant.objects.all()
    serializer_class = ParticipantSerializer
```

The model for a Participant was already in place so it was relatively straightforward to get an endpoint in place that relied on a serializer to produce the JSON to represent the "Participants".  On this occasion Bryan and I used the liveshare functionality as part of vscode and we were able to divide and conquer working on different files in parallel.  I'm not sure if we were faster that way as I was grappling with Django supported APIs for the first time, but in fairly short order we had a participants API endpoint:

![output from /api/participants/](https://dl.dropbox.com/s/wf71p56aslb4ecp/Screenshot%202019-05-08%2009.41.34.png?dl=0)

We got in a [pull request](https://github.com/CodeForPhilly/prevention-point/pull/21) that has subsequently been merged in and I'm looking forward to another session on this project! Bryan and colleagues are having regular sessions with PreventionPoint and it will be interesting to see how the app evolves.

