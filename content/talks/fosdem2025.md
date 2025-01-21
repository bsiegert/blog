---
title: "Tracking Bulk Builds in pkgsrc: from Cloud to NetBSD Native"
where: "FOSDEM 2025"
# TODO: fix date after the conference
date: 2025-01-01T15:30:00+01:00
slides: "https://docs.google.com/presentation/d/e/2PACX-1vQUUgMIhsGKuFq5T3xj12Tio_FdRF5Y1XmnHSTPDcGorKYBtvKS-fLDjywDsyUEL_Ou2CCRIGWiaBOy/pub?start=false&loop=false&delayms=60000"
description: >
    pkgsrc, the NetBSD package collection, includes the pbulk tool to build all
    (or a subset of) packages. Bulk build reports are an invaluable resource
    for pkgsrc developers to find build breakage and its causes.

    Since 2014, I have maintained a web app called Bulk Tracker (written in Go)
    to visualize bulk build results along different dimensions such as package
    name, platform and compiler. Originally written as a "serverless" App
    Engine application, it has recently been completely rewritten to run
    natively on NetBSD, on a server owned by the project.

    This talk will show how bulk build data is useful and some typical use
    cases the app supports. It will also be about the journey out of the cloud
    and from a document datastore to SQLite.
---

