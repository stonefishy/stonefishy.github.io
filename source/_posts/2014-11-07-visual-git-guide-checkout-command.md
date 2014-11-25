---
layout: post
title: "visual git guide (checkout command)"
description: ""
category: Git

---

**Note:** the original website address in [here](http://marklodato.github.io/visual-git-guide/index-en.html?no-svg)

###Checkout


The checkout command is used to copy files from the history (or stage) to the working directory, and to optionally switch branches.   

When a filename (and/or -p) is given, git copies those files from the given commit to the stage and the working directory. For example, git checkout HEAD~ foo.c copies the file foo.c from the commit called HEAD~ (the parent of the current commit) to the working directory, and also stages it. (*If no commit name is given, files are copied from the stage.*) **Note that the current branch is not changed.**   

![checkout-files](/assets/images/checkout-files.png)

<!--more-->

When a filename is not given but the reference is a (local) branch, HEAD is moved to that branch (that is, we "switch to" that branch), and then the stage and working directory are set to match the contents of that commit. Any file that exists in the new commit (a47c3 below) is copied; any file that exists in the old commit (ed489) but not in the new one is deleted; and any file that exists in neither is ignored.   

![checkout-branch](/assets/images/checkout-branch.png)

When a filename is not given and the reference is not a (local) branch — say, it is a tag, a remote branch, a SHA-1 ID, or something like master~3 — we get an anonymous branch, called a detached HEAD. This is useful for jumping around the history. Say you want to compile version 1.6.6.1 of git. You can git checkout v1.6.6.1 (which is a tag, not a branch), compile, install, and then switch back to another branch, say git checkout master. However, committing works slightly differently with a detached HEAD; this is covered below.   

![checkout-detached](/assets/images/checkout-detached.png)

Once you check out something else, say master, the commit is (presumably) no longer referenced by anything else, and gets lost. Note that after the command, there is nothing referencing 2eecb.       

![checkout-after-detached](/assets/images/checkout-after-detached.png)   

If, on the other hand, you want to save this state, you can create a new named branch using `git checkout -b name`.   

![checkout-b-detached](/assets/images/checkout-b-detached.png)