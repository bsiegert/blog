---
title: "Developing pkgsrc with git"
date: 2025-03-09T17:13:42+01:00
categories:
  - NetBSD
  - pkgsrc
---

I stopped developing pkgsrc with CVS.

Quick bit of background: NetBSD is still using CVS as its version control system. The decision to move to something else has been taken long ago, but the switch has not happened as of today.

Working with CVS is painful for many reasons. For instance, there is no way to see your local changes without waiting several minutes for a `cvs up -n`. A full tree update (`cvs up`) churns for quite a while before it even starts updating any files.

I met Taylor (riastradh@) last year, and he told me about his git-based workflow. I must say I have become a convert! I use the [GitHub mirror](https://github.com/NetBSD/pkgsrc) as my main source tree. Yes, it adds GitHub (and thus Microsoft) as an intermediary, but I don't mind. Below, I am going to describe my workflow and play through an example.

## My setup

I have two pkgsrc source trees:

- a CVS checkout at `~/pkgsrc-cvs`, based on the writable repo and only used for writing
- a git checkout at `~/pkgsrc`

The git checkout is relatively quick when skipping history:

```shell
$ git clone --depth 1 https://github.com/NetBSD/pkgsrc
```

The first thing is to create a `local/${hostname}` branch. Every time I rebase this branch onto the `trunk`, I run `pkgrrxx -u` to update all packages to that state. This gives me a stable base from which to work. For work on packages, I create a new branch (e.g. `update/${pkgname}`) and delete it when done. Or I do the change directly in the `local` branch if I am lazy :)

For committing to CVS, I use the [`git-cvsexportcommit`](https://git-scm.com/docs/git-cvsexportcommit) script from the [devel/git-perlscripts](https://pkgsrc.se/devel/git-perlscripts) package.

## Worked example: a package update

Here, I am updating the [net/gh](https://pkgsrc.se/net/gh) package (the GitHub CLI) to a new version. Once I have made my changes, I commit:

```
$ git commit -a
[local/fedorakumori 668ebe1fa] gh: update to 2.68.1
 3 files changed, 473 insertions(+), 509 deletions(-)
```

Note the commit hash; we will use it for exporting the commit.

```
$ cd ~/pkgsrc-cvs
$ GIT_DIR=~/pkgsrc/.git git cvsexportcommit -v -c 668ebe1fa
Applying to CVS commit 668ebe1fa32d778ce19fe81bc3529f15488a048d from parent 7fa1acd873b88c6b36796133c7b692023a611f61
Checking if patch will apply
Applying
error: patch failed: net/gh/Makefile:2
error: net/gh/Makefile: patch does not apply
error: patch failed: net/gh/distinfo:1
error: net/gh/distinfo: patch does not apply
error: patch failed: net/gh/go-modules.mk:1
error: net/gh/go-modules.mk: patch does not apply
cannot patch at /usr/pkg/libexec/git-core/git-cvsexportcommit line 338.
```

There has been some other commit in pkgsrc since my last sync. To resolve, let's first switch over to a new branch, based on the `trunk`:


```
$ cd ~/pkgsrc
$ git checkout trunk
$ git pull
$ git checkout -b update/gh
$ git cherry-pick 668ebe1fa
Auto-merging net/gh/Makefile
CONFLICT (content): Merge conflict in net/gh/Makefile
Auto-merging net/gh/distinfo
CONFLICT (content): Merge conflict in net/gh/distinfo
Auto-merging net/gh/go-modules.mk
CONFLICT (content): Merge conflict in net/gh/go-modules.mk
error: could not apply 668ebe1fa... gh: update to 2.68.1
hint: After resolving the conflicts, mark them with
hint: "git add/rm <pathspec>", then run
hint: "git cherry-pick --continue".
hint: You can instead skip this commit with "git cherry-pick --skip".
hint: To abort and get back to the state before "git cherry-pick",
hint: run "git cherry-pick --abort".
hint: Disable this message with "git config set advice.mergeConflict false"
$ cd net/gh
$ git status
On branch update/gh
You are currently cherry-picking commit 668ebe1fa.
  (fix conflicts and run "git cherry-pick --continue")
  (use "git cherry-pick --skip" to skip this patch)
  (use "git cherry-pick --abort" to cancel the cherry-pick operation)

Unmerged paths:
  (use "git add <file>..." to mark resolution)
        both modified:   Makefile
        both modified:   distinfo
        both modified:   go-modules.mk

no changes added to commit (use "git add" and/or "git commit -a")
```

Now I open the three files in my editor and merge, thanks to my years-long routine of doing this. Confusingly, the messages above show that there are two ways to continue the cherrypick: either with `git commit` or with `git cherry-pick --continue`. As far as I can tell, they do the exact same thing.

So, after resolving the conflict and running `git commit -a`, I have a commit with a new hash:

```
$ git log | head -1
commit 2b51308c74912f41974d2267f58bb35987bde7e8
```

Of course, it is a good idea to build the package again at this point and verify that it still works.

Let's commit this one to CVS now. You don't need to copy the whole hash, just the first 6-8 characters.

```
$ cd ~/pkgsrc-cvs
$ GIT_DIR=~/pkgsrc/.git git cvsexportcommit -v -c 2b51308c7
Applying to CVS commit 2b51308c74912f41974d2267f58bb35987bde7e8 from parent 44a7067c8ab30c8ec0e58668ec86ed0edaaba1de
Checking if patch will apply
Applying
Patch applied successfully. Adding new files and directories to CVS
Commit to CVS
Patch title (first comment line): gh: update to 2.68.1
Autocommit
  cvs commit -F .msg 'net/gh/Makefile' 'net/gh/distinfo' 'net/gh/go-modules.mk'
/cvsroot/pkgsrc/net/gh/Makefile,v  <--  net/gh/Makefile
new revision: 1.90; previous revision: 1.89
/cvsroot/pkgsrc/net/gh/distinfo,v  <--  net/gh/distinfo
new revision: 1.45; previous revision: 1.44
/cvsroot/pkgsrc/net/gh/go-modules.mk,v  <--  net/gh/go-modules.mk
new revision: 1.39; previous revision: 1.38
Committed successfully to CVS
```

The change is now live in CVS. The normal thing to do now is to add to the changelog. I do this directly in CVS for simplicity.

```
$ cd ~/pkgsrc-cvs/net/gh
$ bmake changes-entry && bmake changes-entry-commit
=> Updating CHANGES-2025 and TODO
P CHANGES-2025
=> Adding the change
=> Committing the change
/cvsroot/pkgsrc/doc/CHANGES-2025,v  <--  CHANGES-2025
new revision: 1.2125; previous revision: 1.2124
```

This works well for cross-cutting changes, like revbumps, too.

## Why

In the beginning, I alluded to some of the pain points with CVS. But this also enables me to do some new things. In CVS, working copies are expensive, so you use the same one each time. It has happened to me more than once that I committed something I did not intend to because I had the change lying around in my tree, uncommitted.

> Uncommitted changes are liabilities.

In a DVCS tree, you can commit everything right away and upstream when you are ready to. This means that you write change descriptions while you still remember what you did :) For a cross-cutting or independent change, you can just "reset" your tree to a clean state in a few seconds. This is a great thing.
