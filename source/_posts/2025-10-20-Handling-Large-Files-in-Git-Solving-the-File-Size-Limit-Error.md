---
title: Handling Large Files in Git - Solving the 'File Size Limit' Error
date: 2025-10-20 10:40:47
categories: Tools
tags: [Git, GitHub]
---

Recently, we're migrating the repository from `bitbucket` to `github`, while pushing changes to my GitHub repository, I encountered the following error:

```
remote: error: File mock-data/OTAMockDataGenerator/project.log is 113.39 MB; this exceeds GitHub's file size limit of 100.00 MB
remote: error: File mock-data/OTADataGenerator/project.log is 113.37 MB; this exceeds GitHub's file size limit of 100.00 MB
```

{% image /assets/images/tools/github-file-size-limit-error.png %}

GitHub restricts file uploads to 100 MB. In my case, these `.log` files were not essential for version control, but they were already committed. Here’s how I resolved the issue and some best practices for handling large files in Git.

---

## 1. Removing Large Files from History with `git filter-repo`

If we've already committed large files, simply deleting them and pushing won’t work—they remain in our repository history. To completely remove them, use [`git filter-repo`](https://github.com/newren/git-filter-repo):

**Step-by-step:**

1. **Install git-filter-repo**  
	On Windows, use [Python pip](https://pypi.org/project/git-filter-repo/):  
	```
	pip install git-filter-repo
	```

2. **Remove the unwanted files from history:**  
	```
	git filter-repo --path mock-data/OTAMockDataGenerator/project.log --path mock-data/OTADataGenerator/project.log --invert-paths
	```
	This command deletes the specified files from all commits.

3. **Force-push the cleaned history:**  
	```
	git push --force
	```
	**Warning:** Force-pushing rewrites history. Coordinate with collaborators before proceeding.

4. **Add `.log` files to `.gitignore`**  
	Prevent future accidental commits:
	```
	echo "*.log" >> .gitignore
	git add .gitignore
	git commit -m "Ignore log files"
	git push
	```

---

## 2. Managing Large Files with Git Large File Storage (LFS)

Alternative way, if you need to track large files (e.g., datasets, binaries), consider [Git LFS](https://git-lfs.github.com/):

**Setup:**

1. **Install Git LFS:**  
	Download and install from [git-lfs.github.com](https://git-lfs.github.com/).

2. **Track large file types:**  
	```
	git lfs track "*.log"
	git add .gitattributes
	git add mock-data/OTAMockDataGenerator/project.log
	git add mock-data/OTADataGenerator/project.log
	git commit -m "Track log files with LFS"
	git push
	```

**Note:**  
Git LFS stores large files outside the main repository, keeping your repo lightweight. However, GitHub LFS has its own storage limits and may incur costs for very large datasets.

---

## Summary

- Use `git filter-repo` to clean up large, unnecessary files from your repo history.
- Add file patterns to `.gitignore` to prevent future issues.
- For essential large files, use Git LFS.

By following these steps, we can keep our repository clean, efficient, and compliant with GitHub’s file size policies.
