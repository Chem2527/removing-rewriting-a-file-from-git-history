# Documentation for Removing a File from Git History
## Command Overview
The command executed was:
```bash
git rev-list --objects --all | grep <FILENAME>

```
**Command Breakdown:**
```bash
git rev-list --objects --all

	git rev-list: Lists all commits in the repository.
	--objects: Includes the SHA-1 hashes of objects (blobs, trees) related to the commits.
	--all: Ensures all references (branches, tags, etc.) are checked.

	grep <FILENAME>
	Filters the output to include only the lines that contain the string  <FILENAME>. This is helpful to locate the file in the history.
```
## Output Explanation
```bash
af3d5f597830edb8876f4350d1507bc61d677e52 <FILEPATH>/<FILENAME>
	af3d5f597830edb8876f4350d1507bc61d677e52: This is the SHA-1 hash of the object corresponding to the file cb_2014_us_zcta510_500k.csv. It uniquely identifies the file in the repository’s history.
src/assets/postcode-data/us/<FILENAME>: This is the path of the file in the repository.
```

## What Does the Output Mean?
```bash
The file <FILENAME> was added and committed to the repository at some point in its history. The SHA-1 hash identifies the version of that file in the Git history. The file may have been deleted in later commits, but its data still exists in the repository’s history.
```
## Steps to Remove the File from Git History
If you want to remove the file from your Git history (e.g., for security reasons or migration issues), follow these steps:
```bash
Step 1: Backup Your Repository
Always back up your repository before modifying history:
git clone --mirror <your-repo-url> repo-backup
This will create a backup clone of your repository.
Step 2: Identify All References to the Object
Find all references to the object using:
git rev-list --all --objects | grep af3d5f597830edb8876f4350d1507bc61d677e52
This helps in verifying where the object is being referenced.
Step 3: Remove the File from History
```

## Option 1: Using git filter-repo (Recommended)
For an efficient solution, use git filter-repo:
```bash
git filter-repo --path  <FILEPATH>/<FILENAME> --invert-paths
```
This removes the file from all commits.

## Option 2: Using git filter-branch (Deprecated) If git filter-repo is unavailable, you can use git filter-branch:
```bash
git filter-branch --force --index-filter \
"git rm --cached --ignore-unmatch  <FILEPATH>/<FILENAME>" \
--prune-empty --tag-name-filter cat -- --all
```
•	Explanation: This command rewrites commit history and removes the file from all commits. The --prune-empty option ensures empty commits are removed.
## Step 4: Clean Up Orphaned Objects
Run Git’s garbage collection to remove orphaned objects:
```bash
rm -rf .git/refs/original/
git reflog expire --expire=now --all
git gc --prune=now
git gc --aggressive --prune=now
```
## Step 5: Force Push to Remote
Since history has been rewritten, a force push is required to update the remote repository:
```bash
git push origin --force --all
git push origin --force --tags
```
 Warning: This action will overwrite history in the remote repository and may affect collaborators. Communicate with your team before proceeding.
