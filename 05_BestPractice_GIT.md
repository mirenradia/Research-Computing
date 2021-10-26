# Good practice for coding

When writing code to solve science problems it is very easy to aim to do \*just\* enough to get the answer you need.  It always feels like the fastest thing to do at the time, and at the time it may well be.  Unfortunately what usually happens is the problem you started solving presents new problems and they present new problems and the code you started with then gets added to, and added to, and added to, to solve all these extra problems.  The result is that your original 'quick and dirty' code becomes your 'long, slow, un-readable, fragile mess' code.  You then find that you have to spent more and more time debugging it and trying to remember exactly what all the different parts do.

The other problem is that you may find that you code is in fact quite useful and you may want to share it with collaborators or students but unfortunately as it's a 'long, slow, un-readable, fragile mess' no-one else can understand any of it, let alone make it work, and they end up having to write their own 'long, slow, un-readable, fragile messes' to duplicate yours.

This is certainly the experience I had with my own code which is now over 50,000 lines long and comprises 29 different executables of which only about 12 still work.  So, with with the benefit of hindsight, the goal of this section is to help you to write code that is readable and maintainable.  And, once you have solved what you need to solve, can be shared with the world with your head held high.

The first thing I need to teach you is the fundamental rule of writing code:

- You will often do very very stupid things

My worst was typing `rm *` then, when going for the shift to add `Out` to remove my output files, instead bumped `enter` irreversibly deleting my entire code base. On a smaller scale you will often find that when coding you will sometimes convert working code to broken code and you won't know why and you won't be able to get it back to how it was (this will happen a lot).

Therefore the most important part of good coding practice is:

## Version Control

In its simplest form version control takes snapshots of your code at different stages and saves it so you can wind back to earlier versions if a problem arises.  It also allows you to branch your code so you can try new approaches while leaving the original version safe.  Finally it allows multiple people to work on a given code at the same time and for them to resolve the conflicts that arise when they edit the same files.

### Git

In this course we will introduce you to `git` (there are others but git is an industry standard and used widely, ~70%). git is a distributed and decentralised version control system.

-   You can run it locally on your desktop just for yourself (You will want to make sure this is a machine that has a backup service)
-   Or you can upload you repository to a git hosting site like github, bitbucket and gitlab.  the UIS also has git repositories.  This makes it easy for a team to work on the code and also gives you an off site backup.
- Git will scale to just about any size you can think of (sometimes needs a small extra effort)
- Using git opens door to industrial best practices and peer-to-peer software ideas

One thing to consider when choosing a hosting site is data protection.  If you are working with sensitive data you may want your repository to be private. This can be done on all the above options.  Github now allows private repositories on free accounts for everyone. Bitbucket also allows unlimited private repositories for small teams. Gitlab allows local hosting  https://git.maths.cam.ac.uk/users/sign_in and the UIS service is naturally cambridge only https://git.uis.cam.ac.uk/x/maths/.

Before we get into the detail lets see it works in simple cases

### Creating a repository

One you have some code in a directory that works you will want to create a git repository to keep track of changes (you can of course do this before you start a project).  You create a git instance from the terminal window with: 

```
cd directory_with_project
git init
```

Now if you type `ls -a` you will see a hidden directory called '.git'. This is you repository.  At the moment there are no files in it so let's add some:

```
git add 'list of files and directories'
```

Note as a general rule you should only add files you have actually typed.  So not: compiled executables, libraries, object files, \*.pyc, editor backup files or data.  This command doesn't actually add them to the repository it just tells git that these are files we need to keep track of. We can check what has happened with:

```
git status
```

This will tell us that there are no files committed to branch master and a list of new files to be included. If you are working on some code that creates a bunch of executables and output files (stuff you shouldn't track by git), the output might get cluttered by them (marked as "untracked") making it harder to see the important stuff. To avoid that, you should create a file called `.gitignore` (make sure you can see "hidden files") and add to it all the extensions you would like to exclude from tracking, you can also add folders to it.  Now if we are happy with this we can commit them to the repository with:

```
git commit -m 'Descriptive message here saying what you have done'
```

If you omit the -m git brings up an editor where you can write a much longer note **which you should probably do most of the time** as what makes sense to you now won't make sense to you in 2 years time. Git will complain the first time you do this as name and email are not known. To set them use:

```
git config --global user.name "Wilberforce Velociraptor Norton"
git config --global user.email wvn99@cam.ac.uk
```

You may also want to specify the editor that git opens for typing commit messages using:

```
git config --global core.editor name_of_editor
```

When you write you commit messages do not get in the habit of writing ones like this:

![](Plots/git_commit.png)

Most importantly don't just say what you have done but say why you did it.  You **will** forget so make sure you have enough detail for someone new to the project to understand what changed.  Here is the standard style guide for commit messages:

```
Summarize changes in around 50 characters or less

More detailed explanatory text, if necessary. Wrap it to about 72
characters or so. In some contexts, the first line is treated as the
subject of the commit and the rest of the text as the body. The
blank line separating the summary from the body is critical (unless
you omit the body entirely); various tools like `log`, `shortlog`
and `rebase` can get confused if you run the two together.

Explain the problem that this commit is solving. Focus on why you
are making this change as opposed to how (the code explains that).
Are there side effects or other unintuitive consequences of this
change? Here's the place to explain them.

Further paragraphs come after blank lines.

 - Bullet points are okay, too

 - Typically a hyphen or asterisk is used for the bullet, preceded
   by a single space, with blank lines in between, but conventions
   vary here

If you use an issue tracker, put references to them at the bottom,
like this:

Resolves: #123
See also: #456, #789
```

If you have made a mistake with your commit, like forgetting a file, you can fix this using `--amend` eq:

```
git commit -m 'initial commit'
git add forgotten_file
git commit --amend
```

Ideally, you should always add files manually one by one to know exactly what you are going to commit, but sometimes you might have been working on too many files (for example, you changed a global variable name in a big c project) or maybe you are just sure your `.gitignore` file is configured correctly and you are working on your own repository anyway, so it won't be too bad if you accidentally add something wrong to it, then you can just use an "add all" command to save typing time: `git add -A`.  


You can check the history of the project with:

```
git log
```

This is the basic cycle for one person using git.  You edit some code, once you are happy with it you add then commit the changes, then edit some more. You should think of git as having three areas that it keeps track of.

1. Working directory
2. Staging area
3. Committed

Files start in 1, the working directory where you can edit/create them. When you are happy with your work you use `git add` to tag that are ready for being committed. Now they are in 2, staging.  Once all the files you want are in staging you take a snapshot of them with `git commit` which makes a copy and hides it in the `.git` folder.  Now you go back to editing which moves the files back to 1.  Again, this should only really be for your code you typed yourself. If you to add large data sets then the repository can become very large and checking what had changed between commits becomes much more difficult.  These should be stored with your usual backups (which you should also definitely have). 

If you find that you have made a terrible mistake in a file but haven't committed it yet, you can return to the last committed version of that file with:

```
git checkout -- 'filename'
```

This will revert to the last committed version of that file, this will work even if you deleted the file in question.  

If you want all changes removed use `git checkout .` (note if you are not in the root folder it will only remove changes from folders down from where you are).  

All these command have multiple options you can add using `-` just like in `BASH`.  To learn more about any of the commands use:

```
git help 'command'
```

Git keeps track of all your commits with something called a Directed Acyclic Graph (DAG).  This is just in essence a list of all commits.  

![](Plots/GIT_DAG_1.png)

Here we see that we have made 3 commits (C0, C1, C2) and the label `master` points to the last one, C2.

Now let's consider the option where you have broken you code but didn't notice until you had already committed it. Now you want to back to an earlier version. You do this with:

```
git reset HEAD~
```

This will wind you back one commit (`HEAD~n` winds back n commits). It comes with three options, `--soft`, `--mixed`, and `--hard`.  The `--soft` option means that we go back to just before the commit.  `--mixed` mean go back to before the commit and undo all the `add`'s. `--hard` means go back to just after the last commit, before anything was edited. Commits after will this still exist for a while (30 days) so nothing is lost immediately but will eventually get cleaned up, to go back we need to do `git reset HEAD@{1}` which means reset to the previous place `HEAD` pointed to (`HEAD@{n}` goes to the place head pointed to n steps ago). Here is what it looks like after a reset

![](Plots/GIT_DAG_2.png)

Now we see that all that's changed is we are now pointing to C1.

You should only use this if you genuinely want to undo changes.  If you just want to look at the previous commit you should use `git checkout @{1}`, then `git checkout master` will return you to where you were.  To go anywhere in the DAG you just need to use the SHA-1 (Secure Hash Algorithm) hash of the commit as the label, this is the long string after the word commit when using `git log` then use `git checkout 'SHA-1'`. Git will understand any unique shortening of this so you don't have to type it all.  To get a list of shortened codes use `git log --abbrev-commit` instead.

There is a complication that this command leaves you in a 'detached' state which means that `HEAD` no longer points to `master` so if you try to commit the commit won't belong on any branch.  You can avoid this by creating a branch after checkout or at the time of checkout by using `git checkout -b branchname SHA-1`.

This can all be a bit confusing to navigate so there are many GUI clients which help visualise the DAG.  Part of the standard install will be gitk (on mac you will have to do install it yourself) or you can use a third party option: https://git-scm.com/downloads/guis/, many of which allow you to manage all of git rather than just view it.  

You should note that checking out other commits would delete any uncommitted changes in you current space. Luckily by default git refuses to perform checkout to different branch if it would result in lose of uncommitted changes, you can override this by using `-f`. If you want to go look at another commit when you are in the middle of something that isn't ready to be committed but don't want to lose it then you can save you progress with:

```
git stash
```

This takes all you edits and saves them, you are now safe to checkout any other commits.  You can view your current stashes with `git stash list` and bring them back with `git stash apply`.  You can even do this somewhere different from where you stashed it.

If you just want to compare two different commits you don't need to check them out at all.  You just use:

```
git diff 'commit1' 'commit2'
```

diff can also compare individual files or even branches.

The next thing you might want to do is try developing a new feature while keeping the original production code as it is for safe keeping.  To do this you create a `branch`:

```
git branch 'sensible and descriptive branch name usually with "-" or "_" instead of spaces'
```

This creates the branch but doesn't actually move you to it, you will still be on master. You do this with the `checkout` command. To switch between branches you type:

```
git checkout master # switch to main
git checkout branch # switch to new
```

Or you can add the `-b` option to the branch command which creates and switches to the new branch.  After creating the branch the DAG looks like this (assuming we went back to C2 beforehand):

![](Plots/GIT_DAG_3.png)

So switching between master and branch does nothing.  Suppose you continue to work on the branch and make two more commits.  The DAG will look like this:

![](Plots/GIT_DAG_4.png)

At any time you are free to edit and commit to both master or branch and git will keep everything separate.  Switching between the branch and master will cause git to move change all the files in your directories to those associated with the last commit on each.  If we are happy with our new feature we may want to add it to the master version.  To do this we would perform a `merge`:

```
git checkout master
git merge branch
```

This changes back to the last commit on the master branch and merges it with the last commit on the new branch.  As there are no places where the graph splits between the two we can just move the label master to point to the commit C4

![](Plots/GIT_DAG_4a.png)

This is known as a 'fast-forward' commit.  After it we can then delete the branch with `git branch -d 'branch name'`

Now suppose instead of this you had switched back to master and made one more commit.  Now the DAG would look like this:

![](Plots/GIT_DAG_5.png)

So `HEAD` on points to C5 on master and C4 on branch. Now what happens when you try to merge? If we use the same commands as before we would change to the master then merge the commit the branch points to, C4, with the one master points to, C5, to make a new one C6

![](Plots/GIT_DAG_6.png)

This may not be straight forward. The same file may have been edited differently in C5 and C4 so git will be unsure what to do when merging.  When this happens you get the message: "Automatic merge failed; fix conflicts and then commit the result."  To see what happened you can use `git status` which will list the files which have problems.  If you then open one of these files you will see sections which conflict have been marked with code that looks like this:

```
<<<<<<< HEAD:filename.py
'the code in the file on master'
=======
'the code in the file on branch'
>>>>>>> branch:filename.py
```

You then have to go in and replace each of these sections with what you think the code should really say.  Once you have done this you should `add` the file to staging.  Once all files have been `add`ed then you should run `git status` to check all conflicts are gone then `git commit` to complete the merge.  After this we are free to delete the branch with `git branch -d 'branch name'`.

Unfortunately git will only raise issues if two commits have edited the same file.  This doesn't mean that you code will still work.  You could have made an <b>evil merge</b>.  This occurs in the following example when you try to merge the following two commits: 
    
- Added extra argument to function in branch1
- Used function somewhere new in branch2

The merge is fine as the files edited in each commit were different but the code is now broken!  This is generally avoided by merging branches together then running tests before merging the changes back to master.


### Git on remote repositories

So far we have only looked at what happens locally when we have a single user, now we will look at the case when you are writing code collaboratively.  Here git is essential for managing conflicts associated with multiple people working on the same files simultaneously.  To do this, generally put a `master` version of the git repository somewhere everyone in the collaboration can see, either a local server or a git hosting service like github, https://github.com, or https://bitbucket.org.

Once the master repository is somewhere everyone can see we need to make a local copy for us to work on (ideally no-one should work on the master but this is perfectly possible, you can have a system with no central repository and you just send changes to each other directly but this can be tricky).  To create our local repository use:

```
git clone 'repository to clone'
```

This creates a local version of the repository for us to work on. You can actually create a empty repository with a default readme file on github and clone that when you are starting a new project. Now we have two commands to manage interaction with the remote repository, `fetch` and `push`.

Unless you have just cloned the repository the first thing you should do before starting work is:

```
git fetch origin
```

This gets a new copy of the remote repository (we can leave out `origin` as it defaults to this anyway). Next we have to merge it with ours using `git merge origin/master`, checking for conflicts.  Once these are resolved, we are ready to start with everything up to date. Ideally, you should look what you are going to merge to avoid weird accidents, but in practice, it's often convenient to replace those two commands by one - `pull`. So you can just type `git pull origin master` and it will perform a fetch and merge for you.


Now we follow all the same steps as above edit->add->commit.  Once we are done with our changes we merge them with the remote repository with:

```
git push origin master
```

Provided no-one has edited the same files while we were working on, this will merge our changes with the remote repository.  If it fails because of conflicts then we will have to do a `fetch` and `merge` (or `pull`) then try again. 

### Forks

Sometimes you might want to work on a project based on someone's else public code, but not actually be a part of their development team. For example, you might want a copy of your professor's repository, but will your own personal notes/solutions added. For this, github has an option to "fork the repository". You can then add a second remote address of your branch with 

```
git remote add MYREMOTENAME MYFORKADDRESS
```

This will allow you to get all the updated notes with 

```
git pull origin master
```

and add your own stuff to your fork with

```
git push MYREMOTENAME master
```

### Git style guides

So that is the basic commands for using GIT, but what should your workflow look like?  Some people just use git like this:

![](Plots/git_cartoon.png)

But it can pay to think about the workflow model you want to use, here we will look at a few.

The simplest is to have a branch master and a branch development. You always work on development until you are happy with the code then do a fastforward merge to bring master up to development then continue editing on development. Do not use this unless your project is really small and you are working alone, otherwise you might get lost in your changes.

The second is Trunk based workflow.  Here everyone works mostly on master, using branches for short tests.  Once you have a stable version you all like, you branch it out as a 'release' of the code which saves it for eternity.  This works for small numbers of users who seldom work on the code at the same time.  For larger numbers you may mostly work on a branch then only commit back to master after checking for evil merges.  Often in large groups only senior developers would be able to commit to master. 

![](Plots/GIT_DAG_8.png)

The third is git flow.  Here each person works on a development branch, branching out tests for any new features.  Once each person has a stable version, they merge their code back to master which everyone else has to pull to their branch. Actually, it's considered a bad practice to ever make commits to the master branch directly, instead, a "pull request" should be opened after pushing your changes into a development/test branch. This is what you should get used to if you ever envision yourself working as a programmer. 

![](Plots/GIT_DAG_9.png)
 

### Exercises

1. Make a directory and create a git repository in it.
2. Create 3 files A.txt, B.txt, C.txt with some text in them, add them to git then commit
3. Add the text "I'm in master" to C.txt then commit
4. Checkout the original commit and create a branch. switch to it then create 2 more files D.txt, E.txt (again with some text) Add them then commit
5. Switch between the master and branch and see how they change
6. Switch to the branch and add the text "I'm in branch" to C.txt
7. Merge the branch and master and resolve the conflict
8. Delete the branch, use gitk (if installed) to view the repository

### Group Exercises

9. For this we will write a collaborative story together simultaneously. When I say go, clone the remote repository, IRC_course_git,  add your contribution to the story (as much as you want to add it can be to continue the story, change names or places of delete stuff you don't like) to the file "story.txt" then push it back to the master before trying to add some more (due to conflicts this will be a race to get your additions in before someone else changes it).  The goal is to try and write the best story possible!


10. The goal of the previous exercise was to practice basic commands and have fun, but this is absolutely not how you should work on a real project. Here is a more "real life" scenario: split into groups of 3-4 people and write a program that can sort an array of 20 by various methods. Each person should be responsible for writing one sorting function and someone has to take the role of writing the code for generating the arrays, calling the sort functions and displaying the results. First, create a github repository (one person should do it, and then "invite collaborators"). Everyone should be working on their separate branch, pushing results to development. Once the code in the development branch works, open the "pull request" to merge it to the master branch that the owner of the repository will have to approve. 


There is a lot more you can do with git. You should also consider using it for tasks beyond just coding, eg for writing collaborative papers in latex. To learn more this is pretty useful: https://git-scm.com/doc  






