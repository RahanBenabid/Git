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

```
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

this will pop the editor you chose 

[1]:	https://github.com/RahanBenabid/Learning-Backend.git