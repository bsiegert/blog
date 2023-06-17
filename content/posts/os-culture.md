---
title: "Operating Systems, Transit and Cultural Influences"
date: 2023-06-17T11:50:15+02:00
categories:
 - personal
---

All of the dominant commercial operating systems in desktop and mobile
computing — macOS, iOS, Windows, Android, Chrome OS — are made in the US,
on the West Coast. Except for Microsoft, they are heavily concentrated in
the SF Bay Area. (Note that I am not talking about free software such as
GNU/Linux, BSD, etc., which is probably more diverse regarding the location
of their developers.)

# Flight Tracking

All of these OSes are very eager to support you with flight tickets and
boarding passes. For instance, you can add your boarding pass to Apple Wallet,
or Google Wallet, and it will automatically surface when you are at the
airport. Very convenient!

I have a flight to Tallinn, Estonia, in a few weeks. Ever since the flight
appeared in my airline’s mobile app, my iPhone has been sending me reminders
— *daily*, sometimes even multiple times a day — that it has found data about
a flight, added it to my calendar and will be tracking it. Similarly, on iOS,
you can pull up the OS search field and enter a flight number to get instant
tracking of that flight. The OS will even remember it for a while.

# Culture?

Here is the thing though: Try to enter, say, "ICE 79" into the same search
field, and you get — *nothing.*

**This made me realize how much cultural influence bleeds into the features
that they provide.** It’s not like these companies do not care about public
transit. Both mobile Wallet apps have good support for things line the OMNY
pass in New York City and the Clipper card in the Bay area. It’s just that
long-distance, inter-city travel by train is all but nonexistent in the
US. A few people take Amtrak, but they are not worth building support for. 
In the US, people just fly or drive. Rail, transit in general, is something 
you might use for your commute and that’s it.

Now, there are some technical hurdles to providing similarly good support for
trains. For one, the data formats are not as standardized as in air traffic.
But that could be changed. Google and Apple do have transit teams. They did
make most transit agencies output feeds in GTFS format for real-time updates,
plans of which metro line goes where and whatnot. 

So if there was a push for a standardized way of supporting inter-city rail
tickets, it would probably succeed. But it doesn’t look as if this is a
priority for any of them.
