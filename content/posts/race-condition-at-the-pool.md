---
title: "Race Condition at the Pool"
date: 2018-11-20T20:10:09+01:00
---
Recently, I stumbled upon an odd race condition, at the local public pool of
all places. The following workflow, which should be standard, does not work:

1. Buy a 10-entry ticket and pay with debit card.
2. Immediately try to redeem one entry to, well, go for a swim.

The freshly printed card will be declined, and you have to ask for help. When
you leave (because this is Switzerland and everyone is honest, right!?), you
hand in the card at the cash desk, and it is perfectly fine.

They tell me that this is a common issue, and they have it a lot.

The only explanation I can find for this behavior is that the system handling
the tickets only declares the ticket as valid once it has fully cleared the
card transaction &ndash; perhaps to reduce the risk of fraud. This fits with
the observation that paying with cash does not trigger this issue. Also, I
note that the ticket itself is apparently only a record number in a database.

So this is how fraud prevention can annoy the hell out of your customers :)
