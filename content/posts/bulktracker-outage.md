---
title: "The BulkTracker Outage"
date: 2022-01-08T17:12:44+01:00
categories:
- cloud
---

I have been running the [BulkTracker] web app for keeping track of [pkgsrc]
bulk package build results since about 2015.

After running without problems since the start (!!), the BulkTracker app had its
first outage in November of 2021. It turns out that the function that renders
the home page returns a 500 if it gets an error from Datastore. The error that
was returned was:

```
rpc error: code = ResourceExhausted desc = Quota exceeded.
```

After [complaining on Twitter], I fixed the handler to return at least a
degraded home page in case of Datastore errors. But why was there an error in
the first place? According to the "Quotas" page on the Google Cloud Console,
there are no Datastore-related quotas that can be exhausted -- essentially, you
can use as much as you need as long as you pay for it. I fixed the handler to
return at least a degraded home page in case of Datastore errors.

But why was there an error in the first place?  I filed an issue
([bsiegert/BulkTracker#26]) and paused ingestion for the time being. 

After several days, I ended up finding a **daily spending limit** for App
Engine.  This setting is deprecated and thus kind of hidden in the UI. It turns
out that the storage costs were eating almost the entire daily budget! As soon
as one build was ingested in a day, the spending limit would be reached, and
Cloud Datastore just fails *all* requests at that point. Not a good failure
mode, if you ask me!

After deleting the spending limit, I watched the situation for a day, then I
tentatively re-enabled ingestion of new bulk builds. It turns out that solved
it! I have since deleted a bunch of old builds, so that there is not as much stored
data.

[BulkTracker]: https://bulktracker.appspot.com/
[pkgsrc]: https://pkgsrc.org/
[complaining on Twitter]: https://twitter.com/bentsukun/status/1466492927643987976
[bsiegert/BulkTracker#26]: https://github.com/bsiegert/BulkTracker/issues/26


Unfortunately, I never implemented
automatic expiration of results, so they have been accumulating ever since.
I built a small tool that deletes the detailed data for the *n* oldest builds
in the database, and I notice it would run fairly slowly: deleting a set of
20,000 records would take about 3 minutes. This seems like a lot. The next post
in this series will talk about how I made this faster.

## Background: Datastore

As an App Engine application, using the App Engine datastore (a NoSQL database)
was a logical choice at the time. A SQL database may have been more appropriate
for the workload, as I later found out, but the pricing for Google Cloud SQL is
prohibitive for a small application.

Small side note: Through my job I learned how App Engine Datastore was actually
implemented, and it was *horrifying*.

Over the years, App Engine Datastore became first Cloud Datastore -- an offering
independent from App Engine that can be used with any Cloud application -- then
something called "Cloud Firestore in Datastore Mode". Firestore is the Firebase
NoSQL database; its API is very different from the Datastore one, but there is a
shim layer that provides the Datastore API on top of it. The migration was
transparent and flawlessly done by the Google Cloud folks -- kudos.

## Some Statistics

Most of the space in the BulkTracker datastore is used by indices, since all
queries must use an index. So in early December, before I started deleting old
data, the storage contained:

 * data for **8800 bulk builds**
 * the oldest build was from June 2015
 * total size of the data itself was about **30 GiB** in **165,000,000 records**
 * total index size was another whopping **300 GiB**
