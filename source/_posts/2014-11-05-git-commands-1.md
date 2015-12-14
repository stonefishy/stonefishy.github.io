---
layout: post
title: "Git Commands (1)"
description: ""
category: Git

---
###Configure
- Sets the name you want attached to your commit transactions:   
`$ git config --global user.name "[name]"`   
  
- Sets the email you want attached to your commit transactions:   
`$ git config --global user.email "[email]"`   

- Enable git color for command line output:   
`$ git config --global color.ui true`

<!--more-->

###Create Repositories
- Create a new repository with the specifical name, it will create a specified name directory with git:   
`$ git init [project-name]`   

- Init current directory to git repository:   
`$ git init`   

- Downloads a project by repository url:   
`$ git clone [url]`   

- add local repository to remote repository:   
`$ git remote add [remote-name] [remote-repository-url]`

###Make Changes
- List all currently new or modified or deleted files:   
`$ git status`   

- Show all differences of each modified file before staged:   
`$ git diff`   

- Show differences of specified file before staged:   
`$ git diff [file]`

- Show differences of file between staging and the last file version:   
`$ git diff --staged`   

- Add all new or modified files to snapshot for commit:    
`$ git add .` or `$ git add --all`   

- Add specified file to stage for commit:   
`$ git add [file]`   

- Discard the changes of specified file in working directory:   
`$ git checkout [file]`   

- remove the file which has been deleted from local from stage:   
`$ git rm [file]`   

- Removes the file from version control but preserves the file locally:   
`$ git rm --cached [file]`

- Change the file name and prepare it for commit:   
`$ git mv [file-original] [file-renamed]`   

- Unstage the file, but preserve its content:   
`$ git reset [file]`   

- Commit files which was newed or modified or deleted:   
`$ git commit -m"[descriptive message]"`