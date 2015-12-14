---
layout: post
title: "Visual git guide (merge command)
"
description: ""
category: Git
---

**Note:** the original website address in [here](http://marklodato.github.io/visual-git-guide/index-en.html?no-svg)

###Merge command
A merge creates a new commit that incorporates changes from other commits. Before merging, the stage must match the current commit. The trivial case is if the other commit is an ancestor of the current commit, in which case nothing is done. The next most simple is if the current commit is an ancestor of the other commit. This results in a fast-forward merge. The reference is simply moved, and then the new commit is checked out.  

<!--more--> 

![merge-ff](/assets/images/merge-ff.png)

Otherwise, a "real" merge must occur. You can choose other strategies, but the default is to perform a "recursive" merge, which basically takes the current commit (ed489 below), the other commit (33104), and their common ancestor (b325c), and performs a three-way merge. The result is saved to the working directory and the stage, and then a commit occurs, with an extra parent (33104) for the new commit.   

![merge](/assets/images/merge.png)

