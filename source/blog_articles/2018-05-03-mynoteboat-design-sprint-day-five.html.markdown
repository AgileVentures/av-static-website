Gosh, what a ten days.  We managed to complete day five of the mynoteboat design sprint.  However I had pushed myself really hard while fighting off high fevers and it took me the weekend and pretty much the whole of the following week to get back to normal.  And even now I feel a little run down.  After spending the entire weekend in bed doing very little, I did my usual ill working from bed the following week.  Not ideal, but after having a week off for half-term and a week away from the rest of AV for the design sprint, I felt like I needed to run mobs and join scrums.  I didn't push myself in the mornings and let myself off blogging all week.  By Friday I was back at my desk and managing to finish up a last few things on the Anthony Powell society membership database.   So what about the last day of the design sprint I hear you cry?

We had an app ready, and the client checked in around 10am asking for a link to the prototype.  Federico was still finishing off some changes to the formatting of the interactive boat forms.   I didn't think the client was starting testing till after lunch, and thought we'd be having a chat and a run through first, but when the client joined our hangout at around 11am he was already at the yachting club with two very senior French sailors.  It was panic stations for a moment as the client said the app wasn't working on his phone and he needed to show it on his iPad, but there were problems there too.  I don't think we'd ever got the boat image to display on iOS, having spent the entire week focusing on android.  Ultimately it would become clear that the screen size of the clients phone was about half the size that we thought it was.  We knew he was running an Xperia on Android 4.4.2, but we'd just looked up Xperia in the genymotion virtual machines and all the Xperia's has been 1280x1440.  Actually it wasn't until the following week that I found out that the client had a Sony Xperia D2105, which has a screen size of 800x480.

Anyhow, I was jumping out of my bed in a fever grabbing different iPads from around the house ready to try and panic code a solution so these French sailors could start using the app.  I got back to the bed and the hangout and the client was showing the app running on his phone.  The screen was clearly smaller than we had thought, and he was showing us the interactive boat screen where just the dots were showing, but no boat.  This was exactly the image loading problem we had on iOS anyway, but I knew that we sometimes got a slow image load on Android.  I told the client to tap one of the dots.  The boat appeared, and suddenly he was off and testing with the French sailors and I was scattering un-needed iPads off my bed, and trying to listen in to what the French sailors were saying.  I couldn't really follow it at all.  The client had his laptop on the other side of the room from the French sailors.  We could see the two of them tapping away at the app, and chatting away in French.  The client occasionally helping them out.

Not at all what we had been expecting.  Somehow I had thought we would have a clear hour or so to do a test run with the client on the morning of the Friday.  Of course it had been scheduled the evening before, but we'd worked through the night and morning to get the app in shape.  I'd been delerious with fever.  What I should have been doing was making a clear plan with the client about a test run in the morning and nailing down the testing times for the Friday.  That said the client didn't have strong expectations about running the kind of carefully controlled usability studies that a classic design sprint can provide.  He was really interested in seeing a non-trivial prototype that actually ran on the phone, which is what we produced.

The client wrapped up the session with the French sailors and told us he'd give us a summary soon.  Federico joined the hangout with the completed updated forms, and we were left hanging.  We didn't know if there was going to be another testing session starting soon.  We hung out and eventually broke for lunch.  After lunch we started another hangout still not knowing what was going to happen.  The client had spoken about the availability of three different sailors.  The client joined the hangout from his phone driving back from the yacht club.  It turned out that other testers hadn't been available and the client was planning to do other bits of testing the following he week.  The client seemed happy despite some of the shortcomings of the app on his phone.  As he sped through the French landscape he gave us an overview of the feedback from the sailors and told us he was pretty happy.  We took notes and then conducted our own mini-retro on the design sprint technical challenges once the client had to go:

Feedback
--------

* Testers were experienced sailors who thought the app was more for casual sailors
* One of tester’s wife complained about apps when log in is too long and you don’t really benefit from it quickly. (P-Jean)
* Design should be more “suggestive” !

* Maintenance form pages
  * One tester unclear about what they should do
  * Another tester was clear about how to make selections
  * Scroll issues ← fixed
    * On my screen, I can’t validate “essai du moteur”, as if there was no “validate” (P-Jean)
  * → maybe needs clearer call to action
  * → maybe needs to call out that “condition of boat part” needs to be selected
  * “Fréquence” box should be clearer (P-Jean)
* Interactive Boat maintenance
  * Testers liked this / instinctive
  * Would like to add image of their own boat (P-Jean’s statement)
  * Wish a table version so that we can select monthly maintenance, yearly, etc. (P-Jean)


Technical Challenges
--------------------

Sam
* Looking good on multiple devices
  * Can SVG absolute positioning be made to look good on many screens
  * Can SVG use relative positioning?
* Acceptance testing workable?
* Data management
  * Don’t need network in the first instance
  * Do we need redux to ensure all screens stay up to date?
  * All data has to be recalculated dynamically when you open the app to ensure readiness level is accurately represented
* App stability
  * Zmago reported occasional crashes
  * Expo crashes with some frequency on my emulator
* Slow image loading ...

Nico
* Not so familar with React - not sure if it’s the right choice
* Had to use phone for development as emulator got laggy

Federico
* Big gain to develop on real phones
* Development heavy on laptop with emulators and that affected hangout communication


So overall it was a little frustrating since Federico's fixes might have lead to a better testing experience if we'd been able to coordinate them with the client and have a proper run through before the first usability test.  It would also have been nice to have an over the shoulder camera angle on the test itself.  Still the client was happy.  In the following week I got the app working specifically for the screen size on the client's phone and delivered that update.  Mikael helped test on his own phone of the same size, and by the end of the week we had all the scrolling and visibility fixes the client wanted.  That lead to the client and myself exchanging work schedules and revised budgets from me with the idea of building the full app by the start of the summer.  Could this be AgileVenture's first really big paid project.  We shall see ...


