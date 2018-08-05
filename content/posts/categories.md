---
title: "Working categories"
date: 2018-08-05T14:43:49+03:00
---

Vacation is a good time for some housekeeping. So I managed to get categories
for posts working! In the process, I learned a bit about how
[hugo](https://gohugo.io/) works.

Hugo automatically creates so-called *taxonomies* for tags and categories. The
theme I have been using only shows categories in the header itself. And I
managed to *disable* the categories taxonomy in the config file. And I got the
syntax for specifying them wrong.

It turns out that the config file is toml and the front matter of posts is
yaml, for some inexplicable reason. So the correct syntax for categories is a
YAML list:

```yaml
categories:
 - NetBSD
 - Cloud
```

Hugo will then create pages for each category under `/categories`, with a
filtered list of posts. Click a category on a post below to see it.

## Google Analytics disabled

While here, I also got rid of the Google Analytics include (yay data
protection!). However, the whole site remains hosted on Firebase Hosting,
which is a Google product.
