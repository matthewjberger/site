---
title: Nuking Your Git Repo
header:
  teaser: "https://farm5.staticflickr.com/4076/4940499208_b79b77fb0a_z.jpg"
categories: 
  - Github
tags:
  - github
---

If you've worked with Git and wanted to scrap what you've been working on or just resync a local repo that's FUBAR, you've probably tried the [nuclear option](https://xkcd.com/1597/) of deleting and re-cloning your repo.

Alright.

Now imagine, a github repository with a few submodules where a recursive clone is needed.

Sure, the cloning time is a bit longer but that's still not so bad.

Now imagine, a github repository with a few submodules as well as build files generated (by cmake, for instance), stored locally, and ignored by git. Nuking this would require you to first copy out all your build files to a backup directory that's outside the repo folder. 

For example, in the [game engine I am working on](https://github.com/matthewjberger/Iceberg3D/) I am using [multiple github repos as dependencies](https://github.com/matthewjberger/Iceberg3D/tree/bb568d03bc4a2c1d89691032eb67982d9880a6f7/Iceberg3D/dependencies) in the form of git submodules. I chose to compile the libraries from source because I want them statically linked into my engine. There is a one time cost for this however where you recursively clone the project and submodules, then run cmake to generate the build files. I like to keep them in the same directory as the git repo. If I nuked the repository by deleting and re-cloning, I'd have to do all of this over again or have to generate my build files somewhere outside of my cloned repo directory (which is inconvenient).


### > git nuke master

__My solution was to place this alias in my gitconfig:__

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