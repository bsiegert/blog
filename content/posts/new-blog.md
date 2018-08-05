---
title: "New Blog!"
date: 2018-01-18T19:44:52+01:00
categories:
 - Cloud
---

My new year's resolution for 2018 has been to blog more. So I decided to create
an actual blog!

It started with me closing my Amazon AWS account and writing about it. The
posting was up as a Gist on Github, and I shared that URL. This does give
a useful viewer and the ability to add comments, but it is hardly discoverable.
Anyway, the post was somewhat widely circulated (the provocative title certainly
helped) and even made it to
[Reddit](https://www.reddit.com/r/BSD/comments/7hjy1x/leaving_aws/).

After deciding to create a real blog, I started looking into how to do that:

<blockquote class="twitter-tweet" data-lang="de">
<p lang="en" dir="ltr">Thinking about creating an actual blog (again). Blogger, GitHub pages, write something myself or ...?<br>Any tips?</p>
&mdash; Photos of Kernel Panics (@bentsukun) 
<a href="https://twitter.com/bentsukun/status/947436110271086593?ref_src=twsrc%5Etfw">31. Dezember 2017</a>
</blockquote>

<script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>

Now I finally have something up and running. The address is simple enough, and
based on my Twitter handle:

> **https://bentsukun.ch/**

In true Open Source tradition, the source code for the site is visible online
at https://github.com/bsiegert/blog.

For now, I am quite happy with this setup.

## The setup

* Hugo with the purehugo theme,
* Firebase Hosting,
* domain at HostTech.


The site itself is created with the excellent [Hugo](https://gohugo.io), which
has the added advantage of being written in Go :) The installation is as easy
as

```shell
go get github.com/gohugoio/hugo
```

or by using the [www/hugo](http://pkgsrc.se/www/hugo) package in pkgsrc. Hugo
ends up creating a fully static web site, so simple static hosting is enough.

Google Cloud Storage supports hosting a static web site -- that's great, right?
Well, not quite. There is no support for HTTPS in plain GCS. If you want HTTPS,
apparently you need:

1. The GCS bucket that holds the files,
2. A Cloud Load Balancer instance in front
3. Cloud CDN for delivering content from the edge.

Or, you can use [Firebase Hosting](https://firebase.google.com/docs/hosting/),
also by Google. This is what I am doing. A simple ```firebase deploy``` uploads
all the static files, and an SSL certificate is included in the offer.

I tried using Google Domains to register the domain but that service is not
available in Switzerland, unfortunately. [hosttech.ch](https://hosttech.ch/)
to the rescue! It took me a while to figure out that (a) a DNS zone for the
domain is included and (b) how to add entries to it.

To get Firebase Hosting to serve directly on the domain, it is necessary to add
a `google-site-verification` TXT record into the DNS for it. And that's it.
