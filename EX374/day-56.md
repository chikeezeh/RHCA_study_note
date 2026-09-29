#### More Git Drills

##### Branching Practice

Branching allows you to make changes to the code base safely, without affecting the main branch. The new branch can be tested first and make sure nothing breaks, before merging it back into the original main branch. 

Use `git branch` to list the available branches.

```shell
[ansible@tower ansible-lab (main)]$ git branch
* main
  newfeature
```
use `git switch <branch name>` to switch to a branch. 

When you switch to a `branch1` and make a change but didn't commit it in `branch1` and switched to `branch2`, that change will show up in `branch2` as well. To prevent issues with merge conflict, commit the change in `branch1` first, before switching to `branch2` and the change should only be reflected in `branch1`. See the flow below. 

```shell
[ansible@tower ansible-lab (main)]$ git switch newfeature
Switched to branch 'newfeature'
[ansible@tower ansible-lab (newfeature)]$ touch anotherfeature
[ansible@tower ansible-lab (newfeature)]$ git status
On branch newfeature
Untracked files:
  (use "git add <file>..." to include in what will be committed)
        anotherfeature

nothing added to commit but untracked files present (use "git add" to track)
[ansible@tower ansible-lab (newfeature)]$ git switch main
Switched to branch 'main'
Your branch is up to date with 'origin/main'.
[ansible@tower ansible-lab (main)]$ ll
total 8
-rw-r--r--. 1 ansible ansible  0 Sep 29 08:33 anotherfeature
-rw-r--r--. 1 ansible ansible  0 Sep 29 05:59 featurefile
-rw-r--r--. 1 ansible ansible  0 Sep 28 15:31 firstfile
-rw-r--r--. 1 ansible ansible 13 Sep 28 15:25 README.md
-rw-r--r--. 1 ansible ansible  1 Sep 28 16:02 remotefile
-rw-r--r--. 1 ansible ansible  0 Sep 29 05:47 secondfile
[ansible@tower ansible-lab (main)]$ git switch newfeature
Switched to branch 'newfeature'
[ansible@tower ansible-lab (newfeature)]$ git add .
[ansible@tower ansible-lab (newfeature)]$ git commit -m "another feature added"
[newfeature 3bd18d2] another feature added
 1 file changed, 0 insertions(+), 0 deletions(-)
 create mode 100644 anotherfeature
[ansible@tower ansible-lab (newfeature)]$ ll
total 8
-rw-r--r--. 1 ansible ansible  0 Sep 29 08:33 anotherfeature
-rw-r--r--. 1 ansible ansible  0 Sep 29 05:59 featurefile
-rw-r--r--. 1 ansible ansible  0 Sep 28 15:31 firstfile
-rw-r--r--. 1 ansible ansible 13 Sep 28 15:25 README.md
-rw-r--r--. 1 ansible ansible  1 Sep 28 16:02 remotefile
-rw-r--r--. 1 ansible ansible  0 Sep 29 05:47 secondfile
[ansible@tower ansible-lab (newfeature)]$ git switch main
Switched to branch 'main'
Your branch is up to date with 'origin/main'.
[ansible@tower ansible-lab (main)]$
[ansible@tower ansible-lab (main)]$ git status
On branch main
Your branch is up to date with 'origin/main'.

nothing to commit, working tree clean
[ansible@tower ansible-lab (main)]$ ll
total 8
-rw-r--r--. 1 ansible ansible  0 Sep 29 05:59 featurefile
-rw-r--r--. 1 ansible ansible  0 Sep 28 15:31 firstfile
-rw-r--r--. 1 ansible ansible 13 Sep 28 15:25 README.md
-rw-r--r--. 1 ansible ansible  1 Sep 28 16:02 remotefile
-rw-r--r--. 1 ansible ansible  0 Sep 29 05:47 secondfile
[ansible@tower ansible-lab (main)]$

```

As seen above, the `anotherfeature` file is only present in the `newfeature` branch, to get that change in `main` branch, we first switch to `main` branch, and run `git merge newfeature`. See output below:

```shell
[ansible@tower ansible-lab (main)]$ git merge newfeature
Updating f3bc0d8..3bd18d2
Fast-forward
 anotherfeature | 0
 1 file changed, 0 insertions(+), 0 deletions(-)
 create mode 100644 anotherfeature
[ansible@tower ansible-lab (main)]$ ll
total 8
-rw-r--r--. 1 ansible ansible  0 Sep 29 08:42 anotherfeature
-rw-r--r--. 1 ansible ansible  0 Sep 29 05:59 featurefile
-rw-r--r--. 1 ansible ansible  0 Sep 28 15:31 firstfile
-rw-r--r--. 1 ansible ansible 13 Sep 28 15:25 README.md
-rw-r--r--. 1 ansible ansible  1 Sep 28 16:02 remotefile
-rw-r--r--. 1 ansible ansible  0 Sep 29 05:47 secondfile
```

###### Branching Practice Questions
1. Create a new branch named feature-update. Create the branch and switch to it. Ans: `git switch -c feature-update`
2. Make a file named config.txt in the cloned repository, add sample configuration details, and commit the file with the message "Add initial config".
Ans:
`vim config.txt` add the sample configuration, then commit with `git add .` followed by `git commit -m "Add initial config"`. 
3. Modify the config.txt file to add a new configuration port=8080. Update the file and commit with the message "Add port configuration".
Ans: Edit the file to add the new config. Then, `git add .`, followed by `git commit -m " Add port configuration"`. 
4. Push your changes from the feature-update branch to the remote repository.
Ans: Use `git push origin feature-update`, this will move it to the remote repository, I merged it with the `main` branch using a pull request in the remote UI. 
5. Check the history of commits in the repository. Display a concise log of the last 3 commits.
Ans: 
```shell
[ansible@tower ansible-lab (main)]$ git log -n3 --oneline
035bb43 (HEAD -> main, origin/main, origin/HEAD) Merge pull request #1 from chikeezeh/feature-update
5d68f43 (origin/feature-update, feature-update) Add port configuration
610dd4e Add initial config
```
