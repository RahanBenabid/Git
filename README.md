# Introduction to Git
In this note i’ll get in details about Version Control Systems and git, since you know… i kinda really need to understand this shit.
One thing to note is that modern version control like Git have not only just the latest **snapshots** (your latest code), but the whole repository mirrored, with all the changes, with all the versions of the code, so when people work in a project, every post is a full backup with the *version database*. And you can setup different types of collaborations with different group by setting up **workflows** or **branches**. 

### Snapshots
Earlier I mentioned *Snapshots*, they’re what git uses to, like a picture of what your files look like with a reference to the snapshot. this is more efficient as it’s not storing the file again, So You can think of your project history in Git as a stream of these snapshots. Each commit represents a moment in time where you can see the state of your project. This allows you to easily navigate back to any previous state of your project by looking at these snapshots.

### Local Operations
And everything is local, the entire history of the project is there with you, so all operations are blazing fast, so no need to check a remote server and rely on it. you can work and *commit* **offline** until you get to upload later.

### Checksum
Git uses checksum , When you store files in Git, it calculates a checksum for each file and uses that checksum to refer to the file's contents. This checksum is like a digital fingerprint that ensures the integrity of your files. If a file's contents change, even slightly, the checksum will be different. This allows Git to quickly detect any changes or corruptions in your files. Checksums are an essential part of Git's internal workings, ensuring the reliability and consistency of your project's history over time. Git stores everything in its database using hashed content (SHA-1), which looks something like:

```swift
24b9da6552252987aa493b52f8696cd6d3b00373
```

Git is also very secure, and can rarely do something that is undoable, like deleting stuff.
### States
Git files has three main states: *committed, modified, staged*
1. **Staged**: the file is safely stared in the local database
2. **Modified**: there is some modified change that has not been committed
3. **Staged**: the modified file has been marked and ready to go into the next commit snapshot

Here is the Git folders:
- the `/.git` directory is where the *metadata* and *object database* are stored, this is what is copied when a repo is cloned in your computer 
- the **Working** directory, is the local directory on your computer where your project files are stored. This is where you can view, edit, and manage your files before they are committed to the Git repository.
- the staging area, contained (generally) in the `/.git` directory, contains info about the next commit, also called `index`, 

```swift
+---------------------+
| Working Directory   |
| (Modify Files)      |
+----------+----------+
           |
           v
+---------------------+
| Staging Area        |
| (Stage Files)       |
+----------+----------+
           |
           v
+---------------------+
| Git Repository      |
| (Commit Changes)    |
+---------------------+
```

to put it simple if the file is in the `./git` directory then it’s ** committed**, if it’s in the staging area, it’s staged, if it’s modified but in neither places, it’s modified.
You can have a bunch of infos about yourself in the `.gitconfig` file in your system’s root directory, it contains a bunch of infos, to display them, just write

```bash
git config --list
```

# Git Basics

To initialise your project, you can either take and existing project, and then import it into git, or, clone an existing Git repo from another server, here is how you do it respectively:

```bash
git init
```

this one will create the `/.git` subdirectory, If your project includes large files or binary assets, they are stored as blobs in the `.git` directory, increasing its size. and to start version controlling your files, you just need to add them to the staging area, then commit them, let’s for example stage then commit all the `.c` files in your already existing project

```bash
git add *.c
git add LISCENCE
git commit -m 'first commit/first project version'
```

Now you have a new repo with tracked files, you can, also, clone an exiting repo using

```bash
git clone url optional_name
```

The `url` could be something like [https://github.com/Username/Reponame.git][1] This will not only copy the existing working directory, but all the data the server has, with all the versions of the files and all the history, all *by default*. So if your version gets corrupted or the server goes down, you can use any clone of that repo to restore it (minus some server-side hooks which idk what they are), note that git ignores **untracked** files, so you need to add them first so that it would be able to determine if they have been modified or not

```bash
Untracked     Unmodified     Modified       Staged
   |              |              |             |
   |              |              |             |
   |---> Add the file ------------------------>|
   |              |              |             |
   |              |--> Edit ---->|             |
   |              |              |             |
   |<- Remove <---|              |             |
   |              |              |             |
   |              |              |--> Stage -->|
   |              |              |             |
   |              |              |             |
   |              |<--------- Commit ----------|
```

#### Check the project state
The untracked files will not be included in the next commits until you explicitly tell git to do so …the command `git status` lets you see the state of your files in the project, here’s the output of this project (in french lol)

```bash
$ git status
Sur la branche main
Votre branche est à jour avec 'origin/main'.

Modifications qui ne seront pas validées :
  (utilisez "git add <fichier>..." pour mettre à jour ce qui sera validé)
  (utilisez "git restore <fichier>..." pour annuler les modifications dans le répertoire de travail)
	modifié :         README.md

aucune modification n\'a été ajoutée à la validation (utilisez "git add" ou "git commit -a")
```

In case there wouldn’t be modifications, this would show up instead

```bash
$ git status
On branch master
nothing to commit, working directory clean
```

So any modification would result in the first state being shown instead, which means git sees you have a file *you didn’t have in the previous snapshot (commit)*, now let’s create an new file which will be untracked and then start tracking it

```bash
$ touch new_file.txt
$ git add new_file
```

Now, if you commit, the version you had at the point of running `git add` is what will be included in the next snapshot, in other hands: `git add` is used for **tracking new files**, **staging existing files** and we’ll see later, **marking merge-conflicted files as resolved**.
After staging, you end up modifying a file you already staged, it will be **marked as both staged and unstaged**, so to solve that, you need to run `git add <file>` again so that you commit the latest changes you made. Also here’s a better way to check

```bash
# for a more readable output
$ git status -s
M README. md	# modified files
MM Rakefile		# staged and modified again
A lib/git.rb	# new files that have been added
M lib/simplegit.rb	
?? LICENSE.txt	# not tracked
```

#### Ignoring Files
Some projects have large files or system/temporary files that can be disposed of (don’t need to be tracked), the perfect example is the `/node_modules` folder, for that you need to add it to the list inside of the `.gitignore` file inside your project. the file had a bunch of patterns

```bash
$ cat .gitignore
*.[oa]
*~
```

this tell Git to ignore any files ending in “.o” or “.a”. here is a more complete `.gitignore` file:

```bash
# a comment - this is ignored
*.a # no .a files
!lib.a # but do track lib.a, even though you're ignoring .a files above
/TODO # only ignore the root TODO file, not subdir/TODO
build/ # ignore all files in the build/ directory
doc/*.txt # ignore doc/notes.txt, but not doc/server/arch.txt
```

too see the changes just type

````bash
$ git diff

diff --git a/README.md b/README.md
index 032451a..1813333 100644
--- a/README.md
+++ b/README.md
@@ -93,10 +93,11 @@ Untracked     Unmodified     Modified       Staged
    |              |<--------- Commit ----------|
 ```

+#### Check the project state
 The untracked files will not be included in the next commits until you explicitly tell git to do so …the command `git status` lets you see the state of your files in the project, here’s the output of this project (in french lol)

-```bash
-git status
+```
+$ git status
 Sur la branche main
 Votre branche est à jour avec 'origin/main'.

@@ -105,7 +106,55 @@ Modifications qui ne seront pas validées :
   (utilisez "git restore <fichier>..." pour annuler les modifications dans le répertoire de travail)
        modifié :         README.md

-aucune modification n'a été ajoutée à la validation (utilisez "git add" ou "git commit -a")
+aucune modification n\'a été ajoutée à la validation (utilisez "git add" ou "git commit -a")
+```
+
+In case there wouldn’t be modifications, this would show up instead
+
+```bash
+$ git status
+On branch master
+nothing to commit, working directory clean
+```
+
+So any modification would result in the first state being shown instead, which means git sees you have a file *
````

> this however only shows changes that are unstaged, not all the changes made since the last commit, so if you stage then modify again, it can get confusing
and to see what you staged and what will go to your next commit

```bash
git diff --staged

# same long output as the last command
```

Now, after all this BS, you should be ready to commit, because any file you didn’t stage/track will not be included in the commit. so check twice by running `git status` or `git status -s` again, now let’s commit

```bash
git commit
```

this will pop the editor you chose, *vim* in my case, and will have a text, where `# lines` are ignored, and you can write your commit message there, Git will commit after you exit the editor, alternatively and for a faster experience, just write the commit message after the `-m` flag

```bash
$ git commit -m "commit directly from the terminal"
[main 3a66df9] commit directly from the terminal
 1 file changed, 12 insertions(+), 1 deletion(-)
```

this gives you a couple of outputs:
- the branch
- the `SHA-1` checksum, $3a66df9$ in our case
- the files that have been changed, added, deleted
- the lines deletions, insertions
For a simpler use, using the `-a` flag after the `git commit` will stage all the files being tracked, so you can skip the `git add` part

#### Removing/Renaming Files
To remove a file, like seen in the graph above, you have to remove it from your staging area, and then commit, so removing the file irl is optional, but Git tracks removing files nonetheless

```bash
$ rm .DS_Store
$ git status -s
D DS_Store # notices it has been removed

$ git rm .DS_Store
```

in the next commit, the file will be gone from your snapshot, if you want something to stop being tracked and still exist in your hard drive

```bash
git rm --cached README
```

you can rename files in git using the `mv` command

```bash
$ git mv file_from file_to

$ git mv README.md README
$ git status
On branch master
Changes to be committed:
	(use "git reset HEAD <file>..." to unstage)
		renamed: README.md -> README
```

so longer alternative to that command is these 3 commands

```bash
mv README.md README
git rm README.md
git add README
```

#### View History
using the `git log` command, you can see the commit history for your project

```bash
$ git log

commit 3a66df9c3d63db9b5c2ad40d1d3867de9d5b2e0a (HEAD -> main)
Author: RahanBenabid <rahannadime@gmail.com>
Date:   Thu Aug 15 20:51:54 2024 +0100

    commit directly from the terminal

commit 8f2c54300893427b85cb7fb74ac08f707f3c99b1
Author: RahanBenabid <rahannadime@gmail.com>
Date:   Thu Aug 15 20:50:20 2024 +0100

    testing the commit thingy with VIM... yay

commit c76f10014dbf2ce98a7523bbf666aaeab6193cfa (origin/main)
Author: RahanBenabid <rahannadime@gmail.com>
Date:   Wed Aug 14 00:23:35 2024 +0100

    small bonus notes

commit 528b760e7b3c604099949d424f73596cc8f75c9d
Author: RahanBenabid <rahannadime@gmail.com>
Date:   Tue Aug 13 19:35:04 2024 +0100

    updated the .gitignore

commit 3dd941d73350b8c31b526db413af98ea33938b38
Author: RahanBenabid <rahannadime@gmail.com>
Date:   Tue Aug 13 01:40:58 2024 +0100

    git intro

commit c5f3785c72aa74c8fd2bbdaa170d42d762c77d9d
Author: RahanBenabid <rahannadime@gmail.com>
Date:   Sun Aug 11 11:57:49 2024 +0100

    Some key Git aspects

commit 6eec0d3db0e8086e47cd77d6b42959c81008fce8
```

This logs them in chronological order, and adding the `-p` flag, you can see the changes that have been made, for better readability, it’s best to use the `--pretty` flag

```bash
$ git log --pretty=oneline

3a66df9c3d63db9b5c2ad40d1d3867de9d5b2e0a (HEAD -> main) commit directly from the terminal
8f2c54300893427b85cb7fb74ac08f707f3c99b1 testing the commit thingy with VIM... yay
c76f10014dbf2ce98a7523bbf666aaeab6193cfa (origin/main) small bonus notes
528b760e7b3c604099949d424f73596cc8f75c9d updated the .gitignore
3dd941d73350b8c31b526db413af98ea33938b38 git intro
c5f3785c72aa74c8fd2bbdaa170d42d762c77d9d Some key Git aspects
6eec0d3db0e8086e47cd77d6b42959c81008fce8 first commit, intro to Git
```


| Option | Description of Output                           |
| ------ | ----------------------------------------------- |
| `%H`   | Commit hash                                     |
| `%h`   | Abbreviated commit hash                         |
| `%T`   | Tree hash                                       |
| `%t`   | Abbreviated tree hash                           |
| `%P`   | Parent hash                                     |
| `%p`   | Abbreviated parent hash                         |
| `%an`  | Author name                                     |
| `%ae`  | Author e-mail                                   |
| `%ad`  | Author date (format respects the –date= option) |
| `%ar`  | Author date, relative                           |
| `%cn`  | Committer name                                  |
| `%ce`  | Committer e-mail                                |
| `%cd`  | Committer date                                  |
| `%cr`  | Committer date, relative                        |
| `%s`   | Subject                                         |



The difference between the author and committer, is that the author is the one who wrote the work, and the committer is the one who last applied the work, so both persons get credit, you can even turn it into a file tree

```bash
$ git log --pretty=format:"%h %s" --graph

* 4f1d57c Delete, update acronyms/create, see categories
* b4995ff Added bootstrap, users
* 362f6e4 Added Leaf to the project
* af51b37 Creating categories/adding categories to acronyms
* d997cbc Acronyms can be deleted and edited in the app
* 84adb9e didnt stage everything
* e23fa19 added the ability to visit the Acronyms
* 1375fdb modified some files and added comments
* ba2cdf3 Added a whole iOS interface
* 5ed32bb Added tests to the Project
* 1c0012f added queries
* b36202d Added a Pivot model
* b899489 Added a new Categories model
* b6ba04e Added Parent-Sibling relationship between the 2 models
* b951f59 fixed a small annoying bug that took me 2 hours
* 05f5d1f Added the User Model
* 3ab7001 More controller
* 950d909 fixed the controller, made the code more organized
* 32839f6 added a controller
*   4dab4f0 Merge branch 'main' of https://github.com/RahanBenabid/TILApp
|\
| * b672bed added CRUD operations
* | 6d208ff Im so done with life
* | 5edeb4d working with fork, hope it works
* | 59274b8 added CRUD operations
|/
* 55258b4 added CRUD operations
* 4792af1 changed from postgre to sqlite
* 2499cfd added a REAMDE
* be5a3fa Generate Vapor project.
```

#### Undoing 
Here is how you can undo so however, you can’t undo anything, but here, for example, in case you commit early

```bash
git commit -amend
```

if you run this after your last commit without modification, it will only **change your commit message**,  but in this case, you end up modifying your last commit

```bash
git commit -m 'initial commit'
git add forgotten_file
git commit --amend
```

you can also undo your *staging*

```bash
git reset HEAD README.md
```

what about unundoing? you wanna revert to your file before you modified it, like the previous snapshot

```bash
git checkout -- README.md
```

it’s a dangerous command, since any changes made to it will be **GONE**



[1]:	https://github.com/RahanBenabid/Learning-Backend.git