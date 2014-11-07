---
layout: post
title: "Visual git guide (rebase command)"
description: ""
category: Tools
tags: [Git]
---
{% include JB/setup %}
#Visual git guide (rebase command)
---

**Note:** the original website address in [here](http://marklodato.github.io/visual-git-guide/index-en.html?no-svg)

###Rebase command
A rebase is an alternative to a merge for combining multiple branches. Whereas a merge creates a single commit with two parents, leaving a non-linear history, a rebase replays the commits from the current branch onto another, leaving a linear history. *In essence, this is an automated way of performing several cherry-picks in a row*.

![rebase](/assets/images/rebase.png)

The above command takes all the commits that exist in topic but not in master (namely 169a6 and 2c33a), replays them onto master, and then moves the branch head to the new tip. Note that the old commits will be garbage collected if they are no longer referenced.    

To limit how far back to go, use the --onto option. The following command replays onto master the most recent commits on the current branch since 169a6 (exclusive), namely 2c33a.   

![rebase-onto](/assets/images/rebase-onto.png)

There is also `git rebase --interactive`, which allows one to do more complicated things than simply replaying commits, namely dropping, reordering, modifying, and squashing commits. There is no obvious picture to draw for this; see git-rebase(1) for more details.