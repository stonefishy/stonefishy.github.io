---
layout: post
title: "Git Commands (2)"
description: ""
category: Tools
tags: [Git]
---
{% include JB/setup %}
#Git commands (2)
---

###Group Changes
- List all local branches in the current repository:   
`$ git branch`   

- Create a new branch with specified name:   
`$ git branch [branch-new]`   

- Switch to the specified branch and updates the working directory:   
`$ git checkout [branch-name]`

- Combine the specified branch to the current branch:   
`$ git merge [branch-name]`

- Delete the specified branch:   
`$ git branch -d [branch-name]`

<!--break-->

###Suppress Tracking
- A text file named `.gitigore` suppresses accidental versioning of files and paths matching the specified patterns, for example:   
`*.log temp-* dir/*`   
will ignore log file and started with temp- files and all files in dir directory.   

- List all ignore files in this project:   
`$ git ls-files --other --ignored --exclude-standard`   

###Review History
- List all version history for the current branch:   
`$ git log`

- List viewsion history for a single file, including rename:   
`$ git log --follow [file]`

- Show content differences between two branchs:   
`$ git diff [first-branch] [second-branch]`

- Output metadata and content changes of the specified name:   
`$ git show [commit]`