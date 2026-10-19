---
title: "jj on NFS is not fun"
date: 2026-10-19T08:32:02Z
---

You may have seen my relentless stanning of the `jj` version control system,
e.g. in my recent [FrOSCon 2026 talk](/talks/froscon2026/). I think `jj` is
super fun to use! But not on all setups.

In fact, I recently ran into the worst type of setup for it. My
[pkgsrc](https://pkgsrc.org/) repo was located on a home server, on spinning
rust -- this is bad already because of operation latency. But then I imported
it via NFS, over Wi-Fi, onto my laptop, and the experience was miserable.

You see, `jj` has the "magic" behavior that you are always *editing* a commit.
That is, every file change you make, every non-ignore file you create, will
end up in that commit. The way that this is implemented is through snapshots:
say you run `jj log` or `jj st`. Before anything else happens, `jj` will
snapshot the entire repository, by scanning it for changes. On a fast SSD, you
might see a "snapshotting" line fly by, but with this NFS setup, snapshots
take more than a minute. And if you do a mutating change, `jj` might need to
snapshot *again* at the end of the command.

You can get around this on a per-operation basis by adding the
`--ignore-working-copy` flag. For instance, `jj log --ignore-working-copy`
will just show the log and not update the current commit. You can even do
certain mutating commands this way. For instance, to "check out" the latest
`main` branch, you can run

```
jj new main --ignore-working-copy
```

However, there is a problem now: none of the files in your working copy have
been updated, because you explicitly told `jj` not to. This is called a "stale
workspace". You can get out of this situation (without a snapshot!) by running

```
jj workspace update-stale
```

This will actually reconcile the working copy with the operations in the
oplog. Gentle reminder, you can inspect the operations that you did by running

```
jj op log
```

This is also the basis for `jj undo`.

## Is this useful?

If you write scripts that do multiple `jj` operations in sequence, the
`--ignore-working-copy` flag may give you a nice speed boost. Just add the
flag to every operation that does not need an updated commit state and
reconcile with `jj workspace update-stale` in the end.

Speaking of workspaces, this is probably a better way of fixing the slow
operations: create a new workspace on local disk and work in that.
