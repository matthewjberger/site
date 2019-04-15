---
title: Nuking Your Git Repo
header:
  image: "/images/roses.jpg"
categories: 
  - Github
tags:
  - github
published: true
---

When working with git, it's desirable sometimes to clear all of your changes and begin working at the last commit by taking the [nuclear option](https://xkcd.com/1597/) of deleting and re-cloning your repo.

There is an easier way to do this, however...

### > git nuke master

Place this alias in your gitconfig:

```bash
# Has the same effect as deleting the entire repo and re-cloning
# Except it doesn't delete files in the .gitignore, to be safe
nuke = "!sh -c \"git checkout $1 && git stash -u && git fetch --all && git reset --hard origin/$1  && git clean -df && git submodule update --init --recursive\" -"
```

##### Usage:

  _git nuke branch-name_

For example:

    git nuke master

## Breakdown

### Shell commands as git aliases
First, the odd syntax of running shell commands with parameters via git aliases:

```bash
"!sh -c \"git command $1\" -"
```

_Note: the inner quotations must be escaped with backslashes._

To run a shell command through a git alias, you simply put an exclamation point before the command name.

[From the gitconfig manual](https://git-scm.com/docs/git-config):

> If the alias expansion is prefixed with an exclamation point, it will be treated as a shell command.

In the nuke alias we use this to our advantage by running the sh program with the -c switch, the command will be given as a string, and the hyphen at the end will send it to standard input.

The symbol $1 is interpreted as the first parameter passed in, and likewise later parameters are handled by their position prefixed by a dollar sign. (First parameter: $1, Second parameter: $2, etc.)

Now that we can run shell commands with parameters from git aliases we can string together a chain of useful git commands.

### > git nuke master
First, we want to checkout the branch that was passed in as a parameter:

    git checkout $1 
    
Then we want to save local modifications away and revert the working directory to match the HEAD commit, including untracked files with the -u switch:

    git stash -u

Next, we want to get all current upstream refs (this also conveniently allows us to know about all current upstream branches):

    git fetch --all

Next, we want to throw away all of our uncommitted changes and reset our git repo to the latest commit of the branch we specified:

    git reset --hard origin/$1

Next, we want to discard the stashed changes from before, forcing deletion of the stash with the -f flag and allowing deletion of directories that were stashed with the -d flag:

    git clean -df

Finally we ensure that our git submodules are up to date with the current branch:

    git submodule update --init --recursive


And with that, your git repo's local branch is now completely synced with the upstream branch, and your build files have been preserved.