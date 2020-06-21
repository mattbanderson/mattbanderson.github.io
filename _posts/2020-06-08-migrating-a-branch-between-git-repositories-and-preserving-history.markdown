---

title: Migrating a Branch Between Git Repositories and Preserving History
date: '2020-06-21 17:35:00'
---

# Goal: Change Repositories But Keep Git History

Recently, I found myself working on a user guide and some example code for a project. 
As the guide neared completion, we decided that it made more sense to include it as part of another repository. 
Moving it to the other repo would also simplify the process of adding it to the CI build process. 
The guide was in a long-lived branch that several people had contributed to over a period of severals months.
I preferred to retain the commit history so that future contributors could see how it had evolved over time rather than simply moving the files and adding them to the new repository as a single commit.

# Step 1: Prepare a Clean Copy of the Source Repository

This step involves deleting and re-cloning the source repository.
Ensure that any and all changes you wish to retain have been pushed to a remote prior to proceeding.

Remove the existing local copy of the source repo:
```sh
rm -rf ${SOURCE_REPO}  # e.g. rm -rf ./source-repo
```

Next, clone the branch to be moved and change to that directory:

```sh
git clone --branch ${SOURCE_REPO_BRANCH} --origin ${SOURCE_REMOTE} --progress -v ${GIT_REMOTE_URL}/${SOURCE_REPO}.git  # e.g. git clone --branch feature-branch --origin origin --progress -v git@github.com:organization/source-repo.git
cd ${SOURCE_REPO}  # e.g. cd ./source-repo
```

Delete the remote to eliminate any possibility of accidentally pushing to it:
```sh
git remote rm ${SOURCE_REMOTE}  # e.g. git remote rm origin
```

# Step 2: Filter Contents of the Source Repository

[Git-filer-repo](https://github.com/newren/git-filter-repo) is a tool for re-writing Git history.
It is a single-file python script, so all you have to do to use it is download the file and copy it into your $PATH.

With our source repository cloned, use `git filter-repo` to keep only the specific directory:
```sh
git filter-repo --path ${PATH_TO_DIRECTORY_TO_KEEP}  # e.g. git filter-repo --path ./path-to-keep
```

This command will extract the history of the specified directory.
Any commits that only touched paths outside this directory will be removed.

Lastly, add the changes we made using `git filter-repo` and commit them:
```sh
git add .
git commit -m "Filtering history"
```

# Step 3: Merge History Into the Destination Repository

Again, before proceeding, it would be prudent to ensure that any and all local changes have been pushed to a remote.
Change directory to the destination repository location:
```sh
cd /path/to/${DESTINATION_REPO}  # e.g. cd /path/to/destination-repo
```

Add the source repo as a remote in the destination repo:
```sh
git remote add ${SOURCE_REPO} /path/to/${SOURCE_REPO}  # e.g. git remote add source-repo /path/to/source-repo
```

Ensure you are on the branch into which you will merge the history from the source repo.
Pull from the source repo into the destination repo:
```sh 
git branch  # check to make sure you are on the right branch
git checkout ${DESTINATION_BRANCH}  # if needed; e.g. git checkout destination-branch
git pull ${SOURCE_REPO} ${SOURCE_REPO_BRANCH} --allow-unrelated-histories  # e.g. git pull source-repo feature-branch --allow-unrelated-histories
```

If an error occurs that says `unknown option 'allow-unrelated-histories'`, [update](https://git-scm.com/downloads) your Git version.
The `--allow-unrelated-histories` option was added in Git 2.9, and many operating systems/distros ship with an older version of Git installed.

Remove the source repo as a remote and review the new history:
```sh
git remote rm ${SOURCE_REPO}  # e.g. git remote rm source-repo
git log
```

If you are not happy with the updated history, you can `git reset` to the last commit prior to adding the new history or delete the local copy of the destination repo, re-clone it, and run the previous commands again with different options.

If you are satisfied with updated history, push it to the destination repo's original remote:
```sh
git push ${DESTINATION_REMOTE} ${DESTINATION_BRANCH}  # e.g git push origin destination-branch
```

&#x1F389; &ndash; You have successfully migrated a branch from one Git repository to another!

















