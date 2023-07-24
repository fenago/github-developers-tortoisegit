<img align="right" src="./logo-small.png">

# Lab: Working Locally and Branching with Git

In this lab, we will cover the following recipes:

- Managing your local branches
- Branches with remotes
- Forcing a merge commit
- Orphan branches

#### Introduction
If you are developing a small application in a big corporation as a developer, or you are trying to wrap your head around an open source project from GitHub, you have already been using branches with Git.

Most of the time, you may have just been working on a local development or master branch, and so didn't care so much about other branches.

In this lab, we will show you different branch types and how to work with them.

#### Pre-reqs:
- Google Chrome (Recommended)

#### Lab Environment
There is no requirement for any setup.

**Important:** Instructions for this lab are written in such a way that it also shows expected output from the git cli. Only run commands that start with **$** as shown below.


Managing your local branches
----------------------------

Suppose you just have your local Git repository, and, at the moment, you
have no intention of sharing the code with others; you can, however,
easily share the knowledge you have while working with a repository with
one or more remotes. Local branches with no remotes work exactly in this
fashion. As you can see in the examples, we are cloning a repository,
and thus we have a remote.

Let's start by creating a few local branches.

### Getting ready

Use the following command to clone the `jgit` repository to
match:

```
$ git clone https://git.eclipse.org/r/jgit/jgit 
$ cd jgit 
```

### How to do it...

Perform the following steps to manage your local branches:

1.  Whenever you start working on a bug fix or a new feature in your
    project, you should create a branch. You can do so using the
    following code:

```
$ git branch newBugFix 
$ git branch 
* master 
 newBugFix
```

2.  The `newBugFix` branch points to the current
    `HEAD` you were on at the time of the creation. You can
    see the `HEAD` with `git log -1`:

```
$ git log -1 newBugFix --format=format:%H 
25fe20b2dbb20cac8aa43c5ad64494ef8ea64ffc
```

3.  If you want to add a description to the branch, you can do this with
    the `--edit-description` option for the git branch
    command:

```
$ git branch --edit-description newBugFix
```

4.  The previous command will open an editor where you can type in a
    description:

```
Refactoring the Hydro controller 

The hydro controller code is currently horrible needs to be refactored.  
```

5.  Press `i` to get into insert mode in Vim. To save and exit press `esc` key then `:wq`. Close the editor and the message will be saved.

### How it works...

Git stores the information in the local `git config` file;
this also means that you cannot push this information to a remote
repository.

To retrieve the description for the branch, you can use the
`--get` flag for the `git config` command:

```
$ git config --get branch.newBugFix.description 

Refactoring the Hydro controller 

The hydro controller code is currently horrible and needs to be refactored.  
```

### Note

Remember to perform a checkout of `newBugFix` before you start
working on it. This must be done with the Git checkout of
`newBugFix`. If you are in a hurry, you can create and
checkout a new branch in a single command. Just give the option
`-b` to `checkout`.

The branch information is stored as a file in
`.git/refs/heads/newBugFix` 

```
$ cat .git/refs/heads/newBugFix 
25fe20b2dbb20cac8aa43c5ad64494ef8ea64ffc 
```

Note that it is the same commit hash we retrieved with the
`git log` command.

### There's more...

Perhaps you want to create specific branches from specific commit
hashes. The first thought might be to check out the commit, and then
create a branch; however, it is much easier to use the
`git branch` command to create the branches without checking
out the commits:

1.  If you need a branch from a specific commit hash, you can create it
    with the `git branch` command as follows:

```
$ git branch anotherBugFix 979e346 
$ git log -1 anotherBugFix --format=format:%h 
979e346 

$ git log -1 anotherBugFix --format=format:%H 
979e3467112618cc787e161097986212eaaa4533 
```

2.  As you can see, the abbreviated commit hash is shown when you use
    `%h`, and the full commit hash is shown when you use
    `%H`. You can see that the abbreviated commit hash is the
    same as the one used to create the branch. Most of the time, you
    want to create and start working on the branch immediately:

```
$ git checkout -b lastBugFix 979e346 
Switched to a new branch 'lastBugFix' 
```

3.  Git switches to the new branch immediately after it creates the
    branch. The
    `lastBugFix` branch is checked out and another
    `BugFix` branch is at the same commit hash:


4.  You can add `-v` to the `git branch` command

```
$ git branch -v 

  anotherBugFix 979e346 Interactive Rebase: Do actions if  
* lastBugFix    979e346 Interactive Rebase: Do actions if  
  master        25fe20b Add missing package import for jg 
  newBugFix     25fe20b Add missing package import for jg
```

5.  With `-v`, you can see the abbreviated commit hash for
    each branch, and with `-vv`, you can also see that the
    master branch tracks the origin/master branch:

```
$ git branch -vv 

  anotherBugFix 979e346 Interactive Rebase: Do actions if e 
* lastBugFix    979e346 Interactive Rebase: Do actions if e 
  master        25fe20b [origin/master] Add missing package  
  newBugFix     25fe20b Add missing package import for g 
```

Branches with remotes
---------------------

* * * * *

At some point, it is very likely that you have cloned somebody's
repository. This means that you have an associated remote. The remote is
usually called origin because it is where the source originated from.

While working with Git and remotes, you will get some benefits from Git.

We can start with git status and see what we get while working with the
remote.

### Getting ready

Follow these steps:

1.  We will start by checking out a local branch that tracks a remote
    branch:

```
$ git checkout -b remoteBugFix --track origin/stable-3.2 
Branch remoteBugFix set up to track remote branch stable-3.2 from origin. 
Switched to a new branch 'remoteBugFix'
```

2.  The previous command creates and checks out the
    `remoteBugFix` branch that will track the
    `origin/stable-3.2` branch. Therefore, for instance,
    executing `git status` will automatically show how
    different your branch is from `origin/stable-3.2`, and it
    will also show whether your branch's `HEAD` can be fast
    forwarded to the `HEAD` of the remote branch or not.
3.  To provide an example of how the previous step works, we need to do
    some manual work that will simulate this situation. First, we find a
    commit:

```
$ git log -10 origin/stable-3.2 --oneline 
f839d383e (HEAD -> remoteBugFix, origin/stable-3.2) Prepare post 3.2.0 builds
699900c30 (tag: v3.2.0.201312181205-r) JGit v3.2.0.201312181205-r
0ff691cdb Revert "Fix for core.autocrlf=input resulting in modified file..."
1def0a125 Fix for core.autocrlf=input resulting in modified file and unsmudge
0ce61caef Canonicalize worktree path in BaseRepositoryBuilder if set via config
be7942f2b Add missing @since tags for new public methods in Config
ea04d2329 Don't use API exception in RebaseTodoLine
3a063a0ed Merge "Fix aborting rebase with detached head" into stable-3.2
e90438c0e Fix aborting rebase with detached head
2e0d17885 Add recursive variant of Config.getNames() methods 
```

4.  The command will list the last 10 commits on the
    `stable-3.2` branch from the remote origin. The
    `--oneline` option will show the abbreviated commit hash
    and the commit subject. For this recipe, we will be using the
    following commit:

```
$ git reset --hard 2e0d178 
HEAD is now at 2e0d178 Add recursive variant of Config.getNames() methods 
```

5.  This will reset the `remoteBugFix` branch to the
    `2e0d178` commit hash. We are now ready to continue using
    the free benefits of Git when we have a remote tracking branch.

We are resetting to a commit that is accessible from the
`origin/stable-3.2` remote tracking branch; this is done to
simulate that we have performed a Git fetch and new commits were
downloaded for the`origin/stable-3.2` branch.

### How to do it...

Here, we will try a few commands that assist you when you have a remote
tracking branch:

1.  Start by executing `git status` :

```
$ git status 

On branch remoteBugFix 
Your branch is behind 'origin/stable-3.2' by 9 commits, and can be fast-forwarded. 
  (use "git pull" to update your local branch) 

  nothing to commit, working directory clean 
```

Git is very descriptive when you have a tracking branch and you use
`git status` .

As you can see from the message, you can use`git pull` to
update your local branch, which we will try in the next example. The
message says it can be fast-forwarded. It simply means that Git can
advance the `HEAD` without merging. Now, we will just perform
the merge:

### Note

The`git pull` command is just a`git fetch` command and
then a`git merge` command with the remote tracking branch.

```
$ git merge origin/stable-3.2 

Updating 2e0d178..f839d38 
Fast-forward 
 .../org/eclipse/jgit/api/RebaseCommandTest.java    | 213 +++++++++++ 
 .../src/org/eclipse/jgit/api/RebaseCommand.java    |  31 +-- 
 .../jgit/errors/IllegalTodoFileModification.java   |  59 ++++++ 
 .../eclipse/jgit/lib/BaseRepositoryBuilder.java    |   2 +- 
 .../src/org/eclipse/jgit/lib/Config.java           |   2 +
 .../src/org/eclipse/jgit/lib/RebaseTodoLine.java   |  16 +- 
 6 files changed, 266 insertions(+), 57 deletions(-) 
 create mode 100644 org.eclipse.jgit/src/org/eclipse/jgit/errors/IllegalTodoFileModification.java
```

2.  From the output, you can see it is a fast-forward merge, as Git
    predicted in the output of `git status` .

### There's more...

You can also add a remote to an existing branch, which is very handy
when you realize that you actually wanted a remote tracking branch but
forgot to add the tracking information while creating the branch:

1.  Start by creating a local branch at the `2e0d17` commit:

```
$ git checkout -b remoteBugFix2 2e0d17 
Switched to a new branch 'remoteBugFix2' 
```

2.  The `remoteBugFix2` branch is just a local branch at the
    moment with no tracking information; to set the tracking branch, we
    need to use `--set-upstream-to`   or `-u` as a
    flag to the `git branch` command:

```
$ git branch --set-upstream-to origin/stable-3.2 
Branch remoteBugFix2 set up to track remote branch stable-3.2 from origin. 
```

3.  As you can see from the Git output, we are now tracking the
    `stable-3.2` branch from the origin:

```
$ git status 
On branch remoteBugFix2 
Your branch is behind 'origin/stable-3.2' by 9 commits, and can be fast-forwarded. 
  (use "git pull" to update your local branch) 
nothing to commit, working directory clean 
```

4.  You can see from the Git output that you are nine commits ahead, and
    you can use `git pull` to update the branch. Remember that
    a `git pull` command is just a `git fetch` 
    command, followed by a `git merge` command with the
    upstream branch, which we also call the remote tracking branch:

```
$ git pull 

Updating 2e0d17885..f839d383e
Fast-forward
 org.eclipse.jgit.test/tst/org/eclipse/jgit/api/RebaseCommandTest.java | 213 +++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++----------------
 org.eclipse.jgit/src/org/eclipse/jgit/api/RebaseCommand.java | 31 ++++++++------
 org.eclipse.jgit/src/org/eclipse/jgit/errors/IllegalTodoFileModification.java | 59 +++++++++++++++++++++++++++
 org.eclipse.jgit/src/org/eclipse/jgit/lib/BaseRepositoryBuilder.java | 2 +-
 org.eclipse.jgit/src/org/eclipse/jgit/lib/Config.java | 2 +
 org.eclipse.jgit/src/org/eclipse/jgit/lib/RebaseTodoLine.java | 16 ++++----
 6 files changed, 266 insertions(+), 57 deletions(-)
 create mode 100644 org.eclipse.jgit/src/org/eclipse/jgit/errors/IllegalTodoFileModification.java
```

5.  From the output, you can see that the branch has been fast forwarded
    to the `f839d383e`  commit hash, which is equivalent to
    `origin/stable-3.2` . You can verify this with
    `git log` :

```
$ git log -1 origin/stable-3.2  --format=format:%h 
f839d383e 
```


Forcing a merge commit
----------------------

* * * * *

You might have seen a lot of basic examples of software delivery chains
and branching models before reading this book. It is very likely that
you have been trying to use different strategies and found that none of
them completely support your scenario, which is perfectly fine as long
as the tool can support your specific workflow.

Git supports almost any workflow. We have often encountered a situation
that requires a merge commit while merging a feature, even though it can
be done with a fast-forward merge. Those who requested it often use it
to indicate that you have actually merged in a feature and want to store
the information in the repository.

### Note

Git has fast and easy access to all the commit messages, so the
repository should be used as a journal, and not just a backup of the
source code.

### Getting ready

Start by checking out a local branch `remoteOldbugFix` that
tracks `origin/stable-3.1` :

```
$ git checkout -b remoteOldBugFix --track origin/stable-3.1 
Branch remoteOldBugFix set up to track remote branch stable-3.1 from Switched to a new branch 'remoteOldBugFix' 
```

### How to do it...

The following steps will show you how to force a merge commit:

1.  To force a merge commit, you need to use the `--no-ff` 
    flag; *no-ff* means no fast forward. We will also use
    the `--quiet`  flag to minimize the output and
    `--edit` to allow us to edit the commit message. Unless
    you have a merge conflict, Git will create the merge commit for you
    automatically:

```
$ git merge origin/stable-3.2 --no-ff --edit --quiet 

Auto-merging org.eclipse.jgit.test/tst/org/eclipse/jgit/test/resources/SampleDat 
Removing org.eclipse.jgit.test/tst/org/eclipse/jgit/internal/storage/file/GCTe 
Auto-merging org.eclipse.jgit.packaging/org.eclipse.jgit.target/jgit-4.3.target 
```

2.  The commit message editor will open, and you can write a commit
    message. Closing the editor creates the merge commit and we are
    done.

3.  To verify this, you can reset back to
    `origin/stable-3.1` and perform the merge without
    the`--no-ff` flag:

```
$ git reset --hard  remotes/origin/stable-3.1 
HEAD is now at da6e87b Prepare post 3.1.0 builds 
```

4.  Now, perform the merge with the following command:

```
$ git merge origin/stable-3.2 --quiet
```

5.  You can see the difference. The following screenshot
    shows the fast- forward merge; as you can see, our
    `remoteOldBugFix` branch points to
    `origin/stable-3.2` :

![](data:image/png;base64,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)

6.  The next screenshot shows the merge commit we forced Git to create.
    Our branch`remoteOldBugFix` is ahead of
    `remotes/origin/stable-3.2`, and then we performed the
    commit:

![](data:image/png;base64,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)


Orphan branches
---------------

You are now familiar with Git's data model, the DAG. You have seen that
objects have a parent. When you create a new branch, the commit is its
parent. However, in some situations, it is useful to have a branch with
no parent.

One example would be an instance where you have your code base in two
separate repositories, but, for some reason, you now want to consolidate
it into one. One way is to simply copy the files and add them to one of
the repositories, but the disadvantage is that you will lose the
histories. The second way is to use an orphan branch that can help you
to fetch one repository in another.

### Getting ready

It is actually easy to create an orphan branch. The flag
`--orphan` to `checkout` will do it. It can be
executed as follows:

```
$ git clone https://github.com/fenago/github_helloworld.git
$ cd github_helloworld
$ git checkout --orphan fresh-start
Switched to a new branch 'fresh-start'
```

### How to do it...

1.  We now have a branch with no parent. You can verify it by examining
    the commit log as follows:

```
$ git log
fatal: your current branch 'fresh-start' does not have any commits yet
```

`Fresh start` does not mean that you are starting from
scratch. The files and directories that have been added to the
repository still exist:

```
$ ls
helloWorld.groovy hello_world.c hello_world.php hello_world.pl hello_world.py hello_world.sh

$ git status
On branch fresh-start

No commits yet

Changes to be committed:
 (use "git rm --cached <file>..." to unstage)

 new file: helloWorld.groovy
 new file: hello_world.c
 new file: hello_world.php
 new file: hello_world.pl
 new file: hello_world.py
 new file: hello_world.sh
```

2.  If you need a fresh start, you can delete the files (but remember
    not to delete `.git`) as follows:

```
$ git rm --cached README.md a_sub_directory/readme another-file.txt cat-me.txt hello_world.c
$ rm -rf README.md a_sub_directory another-file.txt cat-me.txt hello_world.c
$ git status
On branch fresh-start

No commits yet

nothing to commit (create/copy files and use "git add" to track)
```

3.  You have a branch with no files and no commits. Moreover, the branch
    does not share any commit history with your `master` 
    branch. You could add another repository and fetch all its commits
    using `git remote add` and `git fetch` . Instead,
    we will simply add a text file to illustrate it as follows:

```
$ echo "This is from an orphan branch." > orphan.txt
$ git add orphan.txt
$ git commit  -m "Orphan"
```

Commit is the only thing in the history that you can verify with the
command `git log` . If you fetch another repository into the
branch, you will see all the commits and, more importantly you will have
a copy of the repository's history. 

4.  Once you have your commits in place on the orphan branch, it is time
    to merge them into your master branch. However, your first attempt
    will fail. For example, check the following:

```
$ git checkout master
$ git merge fresh-start
fatal: refusing to merge unrelated histories
```

5.  As you can see, the orphan branch does not share history with the
    master branch, and git will not allow you to merge the branch. It
    shouldn't come as a surprise, since it is basically what an orphan
    branch is all about. However, you can still merge an orphan branch
    by allowing unrelated histories to be merged:

```
$ git merge fresh-start --allow-unrelated-histories
$ git log -3
commit aa804347c728552f7ce9298a83ab646148078dab (HEAD -> master)
Merge: 13dcada 45d1798
Author: John Doe <john.doe@example.com>
Date: Fri May 11 08:57:45 2018 +0200

Merge branch 'fresh-start'

commit 45d179838f8f9f8fd64c6c7bf96147e09ceadbc2 (fresh-start)
Author: John Doe <john.doe@example.com>
Date: Fri May 11 08:57:22 2018 +0200

Orphan

commit 13dcada077e446d3a05ea9cdbc8ecc261a94e42d (origin/master, origin/HEAD)
Author: John Doe <john.doe@example.com>
Date: Fri Dec 13 12:26:00 2013 +0100

This is the subject line of the commit message

... and more output
```

It is unlikely that you will use orphan branches on a daily basis, but
it is a strong feature to know when you need to reorganize your code
base.

### There's more...

There are more options in the help files for Git. Just run
`git merge --help` or `git branch --help` to see
what other options are available. 
