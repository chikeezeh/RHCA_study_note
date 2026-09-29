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
