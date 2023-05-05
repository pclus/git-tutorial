# Tutorial on git and github

For initial steps you can also check, for instance, [this tutorial](https://www.freecodecamp.org/news/git-and-github-for-beginners/).

<!-- vim-markdown-toc GFM -->

* [Create and initialize a repository:](#create-and-initialize-a-repository)
	* [Solving initial conflicts with `rebase`](#solving-initial-conflicts-with-rebase)
	* [Tokens](#tokens)
	* [Avoid tracking certain files with .gitignore](#avoid-tracking-certain-files-with-gitignore)
* [Basic workflow:](#basic-workflow)
	* [Working remotely: `push`and `pull`](#working-remotely-pushand-pull)
	* [Cloning](#cloning)
	* [Branching and merging](#branching-and-merging)
	* [Branching for backups](#branching-for-backups)
	* [Resolve conflicts](#resolve-conflicts)
* [Where am I?](#where-am-i)
* [Working across machines with `ssh`](#working-across-machines-with-ssh)
* [Tricks and tips](#tricks-and-tips)
	* [Bash prompt branch display](#bash-prompt-branch-display)
	* [Avoid prompt for user and token](#avoid-prompt-for-user-and-token)
	* [Globally change default branch name to `main`](#globally-change-default-branch-name-to-main)

<!-- vim-markdown-toc -->


## Create and initialize a repository:

If you are starting an empty project, or a project already in github, just need to clone into your local workspace:

```
git clone https://github.com/pclus/<github-repo-name> . # or any other valid URL
```
Notice the `.` at the end to clone to the current (`pwd`) directory.

Otherwise, if you already have some files in your local machine:

1. Go to your github page and create a new repo.
2. Go to your local project workspace and do:

   `git init` initialize git  
   `git add .` add all elements in folder  
   `git commit -m "Initial commit"` commit changes  

3. So far the local repo and the github repo are not linked yet.
To do so, do:

   `git remote add origin https://github.com/pclus/<github-repo-name>` creates the connection between the two repos.  
   `git branch -M main` changes the main branch name to "main".  
   `git push -u origin main` pushes changes from the local branch to the remote. This will ask for your username and a **token**.

Remember that `origin` is a term to refer to the remote repository.
Notice that `origin/main` is the local copy of the main branch in your device.
See [this](https://stackoverflow.com/questions/18137175/in-git-what-is-the-difference-between-origin-master-vs-origin-master) for a nice explanation.

When all is set, then you can mostly keep going with the commands

```
git add .
git commit -m "Message"
git push -u origin main
```

### Solving initial conflicts with `rebase`

If when creating the repo from github you create a LICENSE or README file,
then there'd be a commit on the remote. It is possible that then git does not allow you to push
without first doing a pull. In addition it won't allow you to pull from origin because
the two branches (remote and local) are, so far, unrelated (no common comit).
This can be solved by simply using `rebase` before the `push` in step 3:

```
git rebase origin/main
git  push -u origin main
```

According to github docs: "The `rebase` command allows you to easily change a series of commits, modifying the history of your repository.
You can reorder, edit, or squash commits together."

### Tokens

Github now works with tokens instead of passwords.
In your github page go to the menu from your profile picture -> Settings (at the end).
Then, in the menus on the left go to *Developer settings* (also at the end).
Then go to *Personal Access Tokens*.

At the current date (Wed 04 Jan 2023 09:27:26 AM CET) there are two options, but one
is in a Beta phase, so we use the *Tokens (classic)*.
When generating a new token, specify the scope, specially if you are using a private repo.
Save your token locally, since you'll need it anytime you want to push changes.


### Avoid tracking certain files with .gitignore

Usually there are going to be files that you do not want to track (i.e., large datafiles, compile builds, temporary files, ...).
To avoid tracking these files, you can add them in the `.gitignore` file on your repo.
You can use astherisks to create patterns. For instance:

- `*.dat` will ignore all files of the `dat`type, also those in subfolders.
- `!network.dat` will add an exception to the pattern for specific matching filenames.

You can add comments in the `.gitignore` file starting a line with `#`:

```
# This is a comment
But # this is not 
```

Files already staged or pushed won't be affected if they are included in a `.gitignore`
later on.
In order to do so: `git rm -r --cached <files-to-unstage>`

Ignored files are not tracked, so they appear in all branches.
For instance, if you ignore files with the format `*.dat`,
and you have a code that generates an output with this format, 
this output will appear in all branches, regardless of the branch
that created the new file (for our purposes this is actually quite convenient!).

## Basic workflow:

### Working remotely: `push`and `pull`

```
git push <remote_name> <(local) branch name>
git pull <remote_name> <(remote) branch name>
```

Usually `<remote_name>` will be `origin`and `<branch_name>` will be `main`.
The command `pull` is a combination of `fetch` and `merge`.

`push` will update the remote branch `<branch name>`. 
If such branch does not exist remotely, it will be created.
For instance, this is what you have to use when you created a new branch
locally and want to updated in the remote.

`pull` instead is a combination of `fetch` and `merge`. 
Therefore, it will synchronize the remote branch named `<branch name>` in
the remote with **your current branch**:

```
git switch branch1
git pull origin branch1 # will synchronize branch1
```

but

```
git switch main
git pull origin branch1 # will merge branch1 to your local main!
```

### Cloning

Let's say that you have a repository called `<repo>` in github.
In order to clone this repo in a new machine (or new directory in the same machine)
just do:

```
git clone <url to repo> <local directory destination>
```
where `<url to repo>` can be obtained from the `<> Code` icon in the github repo page.

For example, to clone repo to your current folder do:

```
git clone https://github.com/pclus/repo.git .
```

Notice that if the repo is private it will ask for a "password", i.e., a token.

### Branching and merging

- `git branch testing̣` creates a new branch named "testing". **Caution!** This does not change the branch you are currently in!
- `git switch testing` moves to the branch "testing".

You can also use `checkout` instead of switch: `git checkout testing` moves to the branch "testing".
To create and switch in one command use `git switch -c <new-branch>`.
`git switch -` switches to the previously used branch.
To remove a branch use `git branch -d <branch-name>`.

See [this](https://git-scm.com/book/en/v2/Git-Branching-Branches-in-a-Nutshell) for more information on branching.
See [this](https://git-scm.com/book/en/v2/Git-Branching-Basic-Branching-and-Merging) for more information on merging.

To merge branch `<new-branch>` to your `main` branch (or any other name):

1. Move to the `main` (or else) branch: `git switch main`
2. Merge with `git merge <new-branch>`
3. (Optional) Remove the useless `<new-branch>` with `git branch -d <new-branch>`

### Branching for backups

A simple workflow to backup progress before continuing is to create a new branch and push it to remote:

```
git branch backup-jan23
git switch backup-jan23
git push origin backup-jan23
git switch main
```

This produces a branching tree like:

```
a - b - c - e     (main)
     \  d         (backup-jan23)
```
There are other ways to backup that avoid create a permanent branch, e.g. check [`bundle`](https://linux.die.net/man/1/git-bundle)

### Resolve conflicts

You edit a file `example.c` from machine A, but forget to commit and push changes.
Then, from machine B, you make further advances on `example.c` that interfere with the previously uncommited changes (i.e., same line editing). 
You add, commit, and push the changes from B.
The next day, when trying to pull `example.c` from A, git will not allow you because the two branches diverged.

It is as easy as follow the instructions from git:

1. `add` and `commit` the changes you made and forgot to commit.
2. Fetch the remote changes: `git fetch origin main`. 
3. Now you can try to merge, `git merge origin/main`, but it will fail and ask you to "fix conflicts".
4. Open your conlicting file (`example.c`) with any editor (i.e., Visual Studio or Vim).
In the file you will see the conflicting lines marked with something like

```
this is not conflicting
<<<<<<< HEAD
this is what's in your local file 
=======
this is 
what
is new in the remote
>>>>>>> branch-B 
```
Edit the file at your convinience and remove the `<<<<<<< HEAD`, `=======`, and `>>>>>>> branch-B` indicators.

5. Everything done, it's a good moment to `add`, `commit`, and `push` (no need to `merge`).

See more about this in the [github docs](https://docs.github.com/en/pull-requests/collaborating-with-pull-requests/addressing-merge-conflicts/resolving-a-merge-conflict-using-the-command-line).

## Where am I?

- `git branch` will list all branches and mark the one you are currently in.
- `git status` will show the list of commited/uncommited changes in the branch.
- `git log` shows the history of commits and branchings. Remember that `HEAD` is the pointer to the current branch
- `git log --graph --decorate --oneline` useful to visualize branches and commits. 
- `git config --get remote.origin.url` or `git remote show origin` will display you the URL for `origin`.
- `git remote set-url origin <url>` changes the `remote origin` to the new URL.

To rescue a version from a previous commit you can use:
```
git reset --hard <commit-before-merge>
```
This removes the commits posterior to the reset point, getting you back on track.
**But** if the commits where pushed remotely, you need to use the `revert` option:

```
git revert -m 1 <merge-commit-hash>
```
The main difference is that `revert` creates a new commit that you can synchronize with the other machines, whereas with `reset` you will
be behind when trying to `push` your changes.

## Working across machines with `ssh`

Let's say you have two computers *local* and *remote* and that you can connect
from the first to the second via `ssh` (here we do not discuss how to setup the ssh).
It might be useful to use `git` in projects that only concern these two machines (no github involved).

1. Create your new repository from the *remote*:

```
git init 
git add .			# optional
git commit -m "Initial commit"  # optional
git branch -M main		# recommended
```
2. Clone the repository from the *local* machine:

```
git clone ssh://<username>@<address>/path/to/project .
```

3. When working from *local* you cannot push to the main branch.
You should force it, modifying the default behavior. Or push to a different branch and then merging (which might be problematic if you forget).

You might want to use `git init --bare` if the remote is only for storing, not working.
An option is to create an additional directory in the remote machine that contains the `bare` repository (i.e., only to push and pull from, not to work with).
Then you can have both, your local and remote pushing and pulling to the bare repository (as is if it was github).
To be precise:

1. Create a directory in your remote machine called, for instance, `mkdir ~/BareRepos/project`
2. From there, `git init --bare`.
3. Go to either of your workspaces (remote or local), and proceed as normal

```
git init
git add .
git commit -m "Initial commit"
git remote add origin ssh://<uname>@<address>/~/BareRepos/project # from local
git remote add origin ~/BareRepos/project # from remote
git push origin main
```

## Tricks and tips

### Bash prompt branch display

In order to display the name of the current branch in the terminal prompt edit your `.bashrc` file
to include the function:

```
parse_git_branch() {
     git branch 2> /dev/null | sed -e '/^[^*]/d' -e 's/* \(.*\)/ [\1]/'
}
```
And then edit the PS1 variable, for instance adding: `\[\033[01;33m\]$(parse_git_branch)\[\033[00m\]` to your previous setup.
In my case I use:

```
PS1='${debian_chroot:+($debian_chroot)}\[\033[01;31m\]\u\[\033[00m\]:\[\033[01;32m\]\W\[\033[00m\]\[\033[01;33m\]$(parse_git_branch)\[\033[00m\]\$ '
```

The name of the branch will be displayied in square brackets, i.e.,  [main].
You can change the bracketting for instance from `[\1]` to `branch name: \1 !!` in the `parse_git_branch` function.

### Avoid prompt for user and token

One option is to set git through `shh`.
Another is to install `gh`.
Follow the [instructions here](https://docs.github.com/en/get-started/getting-started-with-git/caching-your-github-credentials-in-git)
and then load, from a terminal `gh auth login` and follow the instructions.

### Globally change default branch name to `main`

```
git config --global init.defaultBranch main
```
