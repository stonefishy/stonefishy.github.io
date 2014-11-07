---
layout: post
title: "Visual git guide (reset command)"
description: ""
category: Tools
tags: [Git]
---
{% include JB/setup %}
#Visual git guide (reset command)
---

**Note:** the original website address in [here](http://marklodato.github.io/visual-git-guide/index-en.html?no-svg)

###Reset command
**The reset command moves the current branch to another position**, and optionally updates the stage and the working directory. It also is used to copy files from the history to the stage without touching the working directory.   

If a commit is given with no filenames, the current branch is moved to that commit, and then the stage is updated to match this commit. If --hard is given, the working directory is also updated. If --soft is given, neither is updated.   

![reset-commit](/assets/images/reset-commit.png)

If a commit is not given, it defaults to HEAD. In this case, the branch is not moved, but the stage (and optionally the working directory, if --hard is given) are reset to the contents of the last commit.   

![reset](/assets/images/reset.png)

If a filename (and/or -p) is given, then the command works similarly to checkout with a filename, except only the stage (and not the working directory) is updated. (You may also specify the commit from which to take files, rather than HEAD.)

![reset-files](/assets/images/reset-files.png)