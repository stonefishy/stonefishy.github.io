---
layout: post
title: "Visual git guide (commit command)"
description: ""
category: Tools
tags: [Git]
---
{% include JB/setup %}
#Visual git guide (commit command)
---

**Note:** the original website address in [here](http://marklodato.github.io/visual-git-guide/index-en.html?no-svg)

###Commit command
When you commit, git creates a new commit object using the files from the stage and sets the parent to the current commit. It then points the current branch to this new commit. In the image below, the current branch is *master*. Before the command was run, *master* pointed to ed489. Afterward, a new commit, f0cec, was created, with parent ed489, and then *master* was moved to the new commit.   

![commit-master](/assets/images/commit-master.png)

This same process happens even when the current branch is an ancestor of another. Below, a commit occurs on branch *maint*, which was an ancestor of *master*, resulting in 1800b. Afterward, *maint* is no longer an ancestor of *master*. To join the two histories, a **merge** (or **rebase**) will be necessary.   

![commit-maint](/assets/images/commit-maint.png)

Sometimes a mistake is made in a commit, but this is easy to correct with `git commit --amend`. When you use this command, git creates a new commit with the same parent as the current commit. (*The old commit will be discarded if nothing else references it.*)   

![commit-amend](/assets/images/commit-amend.png)

When **HEAD** is detached, commits work like normal, except no named branch gets updated. (*You can think of this as an anonymous branch.*)   

![commit-detached](/assets/images/commit-detached.png)