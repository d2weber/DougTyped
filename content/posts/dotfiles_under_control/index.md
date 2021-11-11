+++
title="Dotfiles under control"
date=2021-11-11

[taxonomies]
categories = ["Setup"]
tags = ["dotfiles", "configuration", "git"]
+++


Recently I read a post about storing dotfiles in a git repository and I really
liked some of the ideas. Inspired by the post I came up with a similar, very
simple method for versioning my dotfiles, which I think is worth sharing.

<!-- more -->

I missed autocompletion of git command when using the method in the
aforementioned [post](https://www.atlassian.com/git/tutorials/dotfiles), so I
prefer the method described in this post. What makes it elegant in my eyes:

* Very fast and simple setup
* No additional software required besides git
* Comfortable usage via alias `git dotfiles`

> Note that this method is only meant to work for files within the home
> directory. In theory, it can also be used for system configuration files, but
> keep in mind that it can be dangerous. For example if you break your
> `/etc/fstab`, your system won't boot anymore.


## Why bare?

We will be using a bare git repository to track our configurations. The naive
way to track the files would be to use a normal repository with its root in the
home directory. That approach however has the disadvantage that whenever you
are in a non-git directory within your home directory, git will think it's
working on your configuration repository. This is because whenever you run a
normal git command, git traverses the directory tree until it finds a git
work-tree. This behaviour can be confusing. You can find some explanation on
what bare means by looking for `--bare` in these [git docs](https://www.git-scm.com/docs/git-clone).


## Configuring repository with git alias

To initialize the repository run:

    git init --bare ~/dotfiles.git

It creates a directory `~/dotfiles.git` with a bare git repository, where the
name `dotfiles.git` is arbitrary.

To be able to perform git operations on the configurations repo regardless of
the current working directory, we will be using a git alias:

    git config --global alias.dotfiles '!git --git-dir=$HOME/dotfiles.git/ --work-tree=$HOME'

You can choose the name of the alias (here `dotfiles`) as you like. The
parameters `git-dir` and `work-tree` are set to the respective paths. Because
we are using the `$HOME` environment variable, the preceding `!git` is
required.

We configure our dotfiles-repository to not show untracked files, because most
of the home directories content will not be under version control. Otherwise
the output of git status would be cluttered.

    git dotfiles config --local status.showUntrackedFiles no


And that's all we had to do. Now we can perform git commands like this:

    git dotfiles add .gitconfig
    git dotfiles commit -m 'Add gitconfig'

Since we are using a git alias, all commands (including other git aliases) will
work as if using a normal git command, even autocompletion will be available. I
hope you are exited as me about this simple method!
