# Introduction to Git
Git is a powerful and widely-used distributed version control system that has transformed the way developers manage and collaborate on software projects. Created by Linus Torvalds in 2005 (cuz he was sick of greedy corps lol), Git allows teams to track changes in their codebase, manage multiple versions of their projects, and facilitate collaboration among developers, regardless of their geographical location.
At its core, Git provides a robust framework for recording changes to files, enabling users to revert to previous versions, compare changes over time, and work concurrently on different features or bug fixes through branching. Unlike traditional centralised version control systems, Git operates on a distributed model, meaning that every developer has a complete copy of the repository, including its entire history. This feature not only enhances performance but also allows for offline work, empowering developers to commit changes locally before pushing them to a shared repository.
Git's flexibility and efficiency make it an essential tool for modern software development. Its ability to handle large projects with numerous contributors while maintaining a clear history of changes has made it the go-to choice for developers worldwide. As you dive deeper into Git, you will discover its myriad features, including staging, merging, and conflict resolution, which further enhance collaboration and streamline the development process. Understanding Git is crucial for any developer looking to improve their workflow and contribute effectively to team projects.

In this note i’ll get in details about Version Control Systems and git, since you know… i kinda really need to understand this shit.
One thing to note is that modern version control like Git have not only just the latest **snapshots** (your latest code), but the whole repository mirrored, with all the changes, with all the versions of the code, so when people work in a project, every post is a full backup with the *version database*. And you can setup different types of collaborations with different group by setting up **workflows** or **branches**. 

### Snapshots
Earlier I mentioned *Snapshots*, they’re what git uses to, like a picture of what your files look like with a reference to the snapshot. this is more efficient as it’s not storing the file again, So You can think of your project history in Git as a stream of these snapshots. Each commit represents a moment in time where you can see the state of your project. This allows you to easily navigate back to any previous state of your project by looking at these snapshots.

### Local Operations
And everything is local, the entire history of the project is there with you, so all operations are blazing fast, so no need to check a remote server and rely on it. you can work and *commit* **offline** until you get to upload later.

### Checksum
Git uses checksum , When you store files in Git, it calculates a checksum for each file and uses that checksum to refer to the file's contents. This checksum is like a digital fingerprint that ensures the integrity of your files. If a file's contents change, even slightly, the checksum will be different. This allows Git to quickly detect any changes or corruptions in your files. Checksums are an essential part of Git's internal workings, ensuring the reliability and consistency of your project's history over time. Git stores everything in its database using hashed content (SHA-1), which looks something like:

	24b9da6552252987aa493b52f8696cd6d3b00373

Git is also very secure, and can rarely do something that is undoable, like deleting stuff.
### States
Git files has three main states: *committed, modified, staged*
1. **Staged**: the file is safely stared in the local database
2. **Modified**: there is some modified change that has not been committed
3. **Staged**: the modified file has been marked and ready to go into the next commit snapshot
