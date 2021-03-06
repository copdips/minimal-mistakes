---
last_modified_at: 2021-03-15 23:50:01
title: "Git Cheat Sheet"
excerpt: "Some personal often forgotten git commands."
tags:
  - git
published: true
# header:
#   teaserlogo:
#   teaser: ''
#   image: ''
#   caption:
gallery:
  - image_path: ''
    url: ''
    title: ''
---

> This is not a complete Git cheat sheet for everyone, this is just a personal cheat sheet for some often forgotten git commands.


## Alias

User level alias

Edit `~/.gitconfig`

```ini
[alias]
        st = status
        br = branch
        co = checkout
        cm = checkout master
        re = restore
        unstage = reset HEAD
        ci = commit
        amend = commit --amend -C HEAD
        sh = show
        df = diff
        last = log -1 HEAD
        lga = log --graph --decorate --oneline --all
        ll = log --graph --all --pretty=format:'%C(auto)%h%Creset <%an>: %s %Creset%C(auto)%d%Creset %C(bold black)(%cr)%Creset' --abbrev-commit --date=relative
```

## Restore

### Restore a file to an old version

```bash
git restore --source [old_commit_hash] [file_name]
```

## Undo


### Discard changes in working directory

```bash
# discard changes to a file in working directory
git checkout <filename or wildcard>

# discard changes to all files in working directory
git checkout .
# or
git checkout *
```

Untracked files cannot be discarded by checkout.
{: .notice--info}


### Unstage from staging area

[StackOverflow: How do I undo git add before commit?](https://stackoverflow.com/questions/348170/how-do-i-undo-git-add-before-commit)

```bash
# unstage a file from staging area
git reset <filename or wildcard>

# unstage all files from staging area
git reset
```

No more need to add `HEAD` like `git reset HEAD <file>` and `git reset HEAD` since git v1.8.2.
{: .notice--info}

Dont use `git rm --cached <filename>` to unstage, it works only for newly created file to remove them from the staging area. But if you specify a existing file, it will delete it from cache, even if it is not staged.
{: .notice--warning}


### Undo commit to working directory

[StackOverflow: How do I undo the most recent local commits in Git?](https://stackoverflow.com/questions/927358/how-do-i-undo-the-most-recent-local-commits-in-git)

> You should readd the files if you want to commit them, as they're in the working directory now, they're unstaged too.

```bash
# Undo last commit to working directory
git reset HEAD~
# same as to
git reset HEAD~1

# Undo last 2 commits to working directory
git reset HEAD~2

# Undo till a special commit to working directory,
# the special commit and every commits before are still committed.
git reset <commit number>

# Unstage a file
git restore --staged <file>
```

`git reset HEAD` will do nothing, as the HEAD is already at the last commit.
{: .notice--info}


### Undo commit to staging area

[StackOverflow: How do I undo the most recent local commits in Git?](https://stackoverflow.com/questions/927358/how-do-i-undo-the-most-recent-local-commits-in-git)

> Often used to just change a commit message, because the uncommitted files have already been staged.

```bash
# Undo last commit to staging area
git reset --soft HEAD~
# same as to
git reset --soft HEAD~1

# Undo the last 2nd commit to staging area,
# and all the after (the last commit in this case) to working directory
git reset --soft HEAD~2

# Undo till a special commit to staging area,
# the special commit and every commits before are still committed.
git reset --soft <commit number>

# If we want to remove (unstage) a file from the current staging area to working directory
git reset HEAD <file>
or
git reset <file>
or
git rm --cached <file>

# We can merge above 2 steps by
git reset
```

`git reset HEAD~1 <file>` will create a delete <file> index in staging area. Normally we don't need this command.
{: .notice--info}

`git reset HEAD` will do nothing, as the HEAD is already at the last commit.
{: .notice--info}

`git reset --hard HEAD~` will undo the last commit (HEAD~) and also delete the changes from the working directory. This is like doing `git reset HEAD~ ; git checkout .` .
If you want to rollback the `reset --hard`, and you have the discarded commit number, you can rollback by `git cherry-pick <commit number>`
{: .notice--warning}git reset HEAD <file>

## Recovering a deleted branch

```bash
git reflog
git branch [branch_name] [commit_hash_that_preceded_the_delete_commit]
```

## Force local branch to the same with remote branch

```bash
git reset --hard upstream/master
or
git checkout -B master origin/master # sometimes this one might not work
```

## Disable host key checking

Sometimes during CICD, we need to use git to do something, if the remote repository is accessed by SSH, the first time when you use git (git clone for example), you need to accept the remote host key. This might be a problem for CICD as it cannot type `Y` for you as you do in an interactive session. To let git to disable the host key checking or precisely accept automatically the remote host key, you need to add the following line in git config:

```bash
> git config --global core.sshcommand 'ssh -i [YouPrivateKeyPath] -o UserKnownHostsFile=/dev/null -o StrictHostKeyChecking=no -F /dev/null'
```

You may need to use `git config --system` to set the config at system level.

## Proxy

Usually, in an enterprise environment, we need to use a proxy to connect to the Internet resources.
And from Powershell, we can ask Powershell to [inherit the IE proxy settings](https://copdips.com/2018/05/setting-up-powershell-gallery-and-nuget-gallery-for-powershell.html#configure-proxy-in-powershell).

With this proxy setting in Powershell, we should be able to use `git clone` to connect to the external www.github.com or to the internally hosted for example [github.your_enterprise_local_domain](github.your_enterprise_local_domain).

But trust me, some enterprises' proxy settings (often for those who use a [.pac](https://en.wikipedia.org/wiki/Proxy_auto-config) file) are so complicated that Powershell cannot use the proxy the same way as IE.

In such case, fortunately, git has its own proxy setting. I think the [official doc](https://git-scm.com/docs/git-config) doesn't explain very well how to set the proxy. But this [gist](https://gist.github.com/evantoli/f8c23a37eb3558ab8765) gives some good examples.

So, normally, you just need to set this config to ask git to use the `$internet_proxy` only for the url github.com, and all the other urls, git won't use the proxy.

```bash
git config --global http.https://github.com.proxy $internet_proxy
```

## GUI

GitForWindows ships with a GUI tool, very cool.

```bash
# start git gui tool
git gui
```

![git-gui](https://github.com/copdips/copdips.github.io/raw/master/_image/blog/2019-06-19-git-cheat-sheet/git-gui.PNG)
