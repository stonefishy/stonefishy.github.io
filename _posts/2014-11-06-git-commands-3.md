---
layout: post
title: "Git commands (3)"
description: ""
category: Tools
tags: [Git]
---
{% include JB/setup %}
# Git Commands (3)
---

###Redo Commits
- Undo all commits after [commit], and preserve changes locally:   
`$ git reset [commit]`   

- Discard all history and changes back to the specified commit:   
`$ git reset --hard [commit]`

###Save Fragments
- Temporarily stores all modified **tracked** files:   
`$ git stash`

- Retore the most recently stashed files:   
`$ git stash pop`

- List all stashed changesets:   
`$ git stash list`

- Discard the most recently stashed changeset:   
`$ git stash drop`   

###Synchronize Changes
- Download all history from the repostory bookmark:   
`$ git fetch [bookmark]`

- Combine bookmark's branch into current branch:    
`$ git merge [bookmark]/[branch]`

- Upload all local branch commits to Github:   
`$ git push [alias] [branch]`

- Downloads bookmark history and incorporates changes:   
`$ git pull`