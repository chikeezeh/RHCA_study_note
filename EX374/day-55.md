#### Git drills

Practicing the required git skills needed for the exam. 

Created a private git repository on `Github` to practice pushing to a remote repository. 

##### Exam requirements (Understand and use Git).
- Clone a Git repository
- Create, modify and push files in a Git repository

###### Cloning a git repository

Since I am using a private repository on Github, a personal access token is required, make sure to select read/write as part of the contents in permissions, and for repository access, select your private repo as shown in the image below. 

![git token](images/gittoken.jpg)

For https cloning, use `git clone <url>`, you will be prompted for username, enter that, then for the password, enter the personal access token you generated above.

For ssh cloning, we will need to add a public ssh key to our repository, go to the repository setting as shown below.

![git ssh](images/gitssh.jpg)

Then click on deploy keys, use the guide to create a key. 

![git deploy](images/gitdeploy.jpg)

Same as https cloning, use `git clone <ssh url>` to clone the remote repository.


###### Create, modify and push files in a Git repository

To be able to modify a repository, you need an `user.email` and `user.name`, use the following commands to configure it.

```shell
Author identity unknown

*** Please tell me who you are.

Run

  git config --global user.email "you@example.com"
  git config --global user.name "Your Name"

```
Create a new file; `touch newfile`

Stage the file; `git add .`

Commit the file; `git commit -m "commit message"`

Push the change to the remote repo; `git push origin main`

I added a new file on the remote server, then using `git fetch origin`, my git repo knows there is a new file, but it doesn't have it yet. 

```shell
[ansible@tower ansible-lab (main)]$ git fetch origin
remote: Enumerating objects: 4, done.
remote: Counting objects: 100% (4/4), done.
remote: Compressing objects: 100% (2/2), done.
remote: Total 3 (delta 0), reused 0 (delta 0), pack-reused 0 (from 0)
Unpacking objects: 100% (3/3), 950 bytes | 950.00 KiB/s, done.
From github.com:chikeezeh/ansible-lab
   9169fec..90fc48b  main       -> origin/main
[ansible@tower ansible-lab (main)]$ ll
total 4
-rw-r--r--. 1 ansible ansible  0 Sep 28 15:31 firstfile
-rw-r--r--. 1 ansible ansible 13 Sep 28 15:25 README.md
[ansible@tower ansible-lab (main)]$ git status
On branch main
Your branch is behind 'origin/main' by 1 commit, and can be fast-forwarded.
  (use "git pull" to update your local branch)

nothing to commit, working tree clean
```

Use `git pull` to get the latest remote change. 

Add the following to .bashrc to be able to see the current git branch, this is handy when switching branches.

```shell
# Extract current Git branch safely
parse_git_branch() {
    git branch 2> /dev/null | sed -e '/^[^*]/d' -e 's/* \(.*\)/ (\1)/'
}

# Prompt: [user@host directory (branch)]$
PS1='[\u@\h \W\[\033[32m\]$(parse_git_branch)\[\033[00m\]]\$ '
```
