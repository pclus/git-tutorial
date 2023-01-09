# Tutorial on git and github

For initial steps you can also check, for instance, [this tutorial](https://www.freecodecamp.org/news/git-and-github-for-beginners/).


## Create and initialize a repository:

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

### Avoid tracking certain files with .gitignore

Usually there are going to be files that you do not want to track (i.e., large datafiles, compile builds, temporary files, ...).
To avoid tracking these files, you can add them in the `.gitignore` file on your repo.
You can use astherisks to create patterns. For instance:

- `*.dat` will ignore all files of the `dat`type, also those in subfolders.
- `!network.dat` will add an exception to the pattern for specific matching filenames.

### Tokens

Github now works with tokens instead of passwords.
In your github page go to the menu from your profile picture -> Settings (at the end).
Then, in the menus on the left go to *Developer settings* (also at the end).
Then go to *Personal Access Tokens*.

At the current date (Wed 04 Jan 2023 09:27:26 AM CET) there are two options, but one
is in a Beta phase, so we use the *Tokens (classic)*.
When generating a new token, specify the scope, specially if you are using a private repo.
Save your token locally, since you'll need it anytime you want to push changes.

## Basic commands:

### Working remotely: `push`and `pull`

```
git push <remote_name> <branch name>
git pull <remote_name> <branch name>
```

Usually `<remote_name>` will be `origin`and `<branch_name>` will be `main`.
The command `pull` is a combination of `fetch` and `merge`.

### Cloning

Let's say that you have a repository called `<repo>` in github.
In order to clone this repo in a new machine (or new directory in the same machine)
just do:

```
git clone <url to repo> <local directory estination>
```
where `<url to repo>` can be obtained from the `<> Code` icon in the github repo page.

For example, to clone repo to your current folder do:

```
git clone https://github.com/pclus/repo.git .
```

Notice that if the repo is private it will ask for a "password", a.k.a., a token.

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

### Where am I?

- `git branch` will list all branches and mark the one you are currently in.
- `git status` will show the list of commited/uncommited changes in the branch.
- `git log` shows the history of commits and branchings. Remember that `HEAD` is the pointer to the current branch
- `git log --graph --decorate --oneline` useful to visualize branches and commits. 

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
