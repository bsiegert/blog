---
title: "Leaving AWS"
date: 2017-12-03T22:16:42+01:00
tags: [NetBSD, Cloud]
---

Today, I deleted my Amazon AWS account.

{{< figure src="/img/close-aws-account.png" title="And done!" width="100%" >}}

I had been on AWS since about 2011. My usage was mainly for two things:

1. Saving large amounts of files (build logs and such) on S3;
2. Running NetBSD VMs on EC2.

EC2 is based on Xen, and NetBSD runs really well in PV (paravirtualized) mode on Xen. However, [XSA-240](https://xenbits.xen.org/xsa/advisory-240.html) means that a malicious PV guest may crash (or even otherwise exploit) the hypervisor, with the recommended fix being to not run untrusted PV guests. Over night, Amazon disabled PV, making NetBSD VMs useless.

In general, EC2 has been moving away from Xen. The newer instance types already no longer supported PV; there are two higher-performance paravirtualized modes (PVH and PVHVM) that are preferred these days, and that NetBSD does not support. The newest machine types use a custom hypervisor based on KVM.

The way the PV change was rolled out highlighted another long-standing EC2 problem: instances would continue running until the server they ran on got rebooted, at which point they were migrated to a random machine. If the target machine had PV disabled, the VM simply did not come up again. I have had the same type of issue in the past, where your VM randomly landed on a "good" or a "bad" machine and did not come up on the bad one. There is no way (AFAIK) to constrain to a certain subset of servers, e.g. running a certain hypervisor version.

Also, of course, there was no warning or announcement, just that VMs stopped working all of a sudden. A bunch of people were completely caught by surprise when their service became unavailable. I hope you have monitoring!?

## The Alternative

Which brings me to where I did take my workloads: Google Cloud Platform. 

(This has nothing to do whatsoever with who my employer is. I pay for my GCP usage with my own money.)

These days, NetBSD (8+) runs great on Google Compute Engine. There is a script (that I created) to stage instances at https://github.com/google/netbsd-gce, though there are no official NetBSD images around. My S3 usage works equally well using Google Cloud Storage. And I have always been a fan of App Engine, particularly because of its great Go support. https://bulktracker.appspot.com/ runs on App Engine.

## Conclusion

My general impression is: Features are roughly on par, prices on GCP are a bit cheaper, and the Google Cloud SDK and command-line tools are better. So rather than let old, unusable VM images continue to rot and pay Amazon 2$ a month for that bit of storage, I let go of that AWS account. Bye, Amazon.
