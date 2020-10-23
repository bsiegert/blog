---
title: "Reflections on Ops"
date: 2020-10-23T06:50:32Z
---

I never spent much time toiling in the "sysadmin job" mines, though my first
job was user support and admin. Later, I joined Google SRE and worked with
both world-class and mediocre tools. As a junior SRE, you are mostly a *user*
of these tools, though it is easy to pat yourself on the back and think of
yourself as better than the run-of-the-mill admin because all this automation
is available to you.

The thing is, the good tools did not get so good over night. Instead, they are
the product of literally years of operational experience. I can assure you
that the first generation of Google administration tools and helpers were just
as shitty as anything a "regular" admin would write. Indeed, the notion of an
SRE (Site Reliability Engineer) took years (and executive vision) to appear.

# The Traditional Tooling Ladder

Let's say you are indeed a systems administrator in a smallish shop. NB: I am
assuming a willingness to write some code, which not all admins might have.

The SRE spirit -- to automate yourself out of a job -- can be a win-win (for
you, who can do more interesting things) and for the company (who gains in
efficiency).  The tooling ladder typically goes like this:

 - Shitty scripts, held together with duct tape, to automate the low-hanging
   fruit,
 - Tooling improves over time and gets fancier

Step #1 is crucial already. You have to start somewhere. Step #2 has you add
e.g. monitoring, a web interface, or unify your tooling into a larger whole.
This process typically stops as soon as an equilibrium between the complexity
of your use case and the effort to develop the tooling has been reached.

# Jump-starting

These days, increasingly, instead of going from #1 to #2, increasingly there
is this:

 - Adapt a large, complex tool that has been open-sourced by a large company.
   Start by using a few percent of the features and increase over time.

Is this good or bad? On one hand, this allows you to fast-forward and skip
acquiring all this experience, instead relying on the wisdom of others who
have done this. In a way, this ultimately is what civilization *is*. No one
builds their computer from scratch, starting with a bucket of sand, right?

On the other hand, learning the tool itself (instead of writing it) is now the
rate-determining step. Some folks deploy powerful tools without understanding
them and inadvertently cause security nightmares (such world-readable or even
world-writable storage) because they don't know what they are doing.

This is even more true when these tools come in the form of managed cloud
services. By virtue of being on the internet, misconfigurations are a lot more
problematic (and sometimes expensive!) than in a relatively isolated
on-premise network. Cloud orchestration tools add a whole more blast radius on
top. 

# K8s

Of course, the prime example for a complex tool described above is Kubernetes.

Don't get me wrong, I am a huge fan of cluster orchestration tools -- Borg
within Google has been an absolute game-changer. Managing clusters of 50,000
nodes and thousands of production roles and services has enabled development
to thrive beyond anyone's wildest dreams.

However, if the size of your deployment is three on-prem VMs running one or
two instances of two services, then perhaps you do not need all the complexity
of Kubernetes in the first place. Maybe a classic arrangement of two larger
servers with failover would be sufficient, and you could spent more time on
the actual, you know, workload?

The complexity of the Kubernetes ecosystem has spawned a whole ecosystem of
its own of Kubernetes-explainers, coaches, conferences and so on. It seems to
me, as an outsider, that the dynamics of such a system lead to ever more
complexity over time to sustain all of this.

For instance, there is a trend of adding ever more sidecar processes along
your main service, into the same pod. At Google, we have largely eliminated
sidecar jobs in the same container because they are just so damn
inconvenient.. I can even remember a rewrite of an internal system where
eliminating the sidecar was a major design goal.

# Conclusion

In the end, there is a choice to be made between complex, "web-scale"
deployments and perhaps a more limited, more conventional one. Again, the
trade-offs depend on the scale and complexity of your services. But YAGNI (You
Ain't Gonna Need It) is a powerful principle.

Let me offer this small tale as an example: When the founder of [Pinboard]
bought back the service from the company that was running it, it was losing a
lot of money, since revenue did not match operation costs. The service was
running in the Cloud, as a collection of microservices. He ended up reducing
the cost of running it by more than 90% by essentially moving it onto a single
machine in a colo.

If your use case is small enough, maybe that's all you need?

(Note: I am saying this as someone whose tiny serverless app on GCP now ends
up costing more each month than a small dedicated server would -- sigh.)


[Pinboard]: https://pinboard.in/
