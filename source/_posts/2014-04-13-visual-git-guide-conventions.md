---

title: "Visual git guide (conventions)"
date: 2014-04-13 17:10
categories: Tools
tags: [Git]

---
**Note:** the original website address is [here](http://marklodato.github.io/visual-git-guide/index-en.html?no-svg)

### Conventions
![conventions](/assets/images/legacy/conventions.png)   

<!--more-->

Commits are shown in green as 5-character IDs, and they point to their parents. Branches are shown in orange, and they point to particular commits.The current branch is identified by the special reference HEAD, which is "attached" to that branch. In this image, the five latest commits are shown, with ed489 being the most recent. master (the current branch) points to this commit, while maint (another branch) points to an ancestor of master's commit.