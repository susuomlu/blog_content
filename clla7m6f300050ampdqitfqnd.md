---
title: "[GitHub] Making your first commit"
datePublished: Mon Aug 14 2023 01:42:08 GMT+0000 (Coordinated Universal Time)
cuid: clla7m6f300050ampdqitfqnd
slug: github-making-your-first-commit
cover: https://cdn.hashnode.com/res/hashnode/image/stock/unsplash/4vMfb8srdTQ/upload/90167ff7cebfaf5ba6aa42aa41c9d938.jpeg
tags: github, git

---

# *What are Git and GitHub?*

Git is a distributed version control system that tracks changes in any set of computer files. It is usually used for coordinating work among programmers who are collaboratively developing source code during software development.

GitHub is a cloud-based service for software development and version control using Git.

In summary, Git is a tool that developers install locally to manage source code while GitHub is an online service to which developers who use Git can connect and upload or download resources.

# *Repositories*

A repository (also known as a repo) is how we refer to a project version controlled by Git.

There are two types of repositories:

* Local repositories are repositories that are stored on a computer.
    
* Remote repositories are repositories that are hosted on a hosting service.
    

There are many company that provides hosting for projects using Git. The main hosting services are GitHub, GitLab, and BitBucket.

# *Working Directory*

The working directory contains the files and directories in the project directory that represent one version of a project.

It is where you add, edit, and delete files and directories.

Suppose the Book project that I am working on has 100 chapters, and I have 100 text files, one for each chapter: `chapter_one.txt`, `chapter_two.txt`, and so on.

To add each of these chapter files to my project, I would create these files in the working directory.

If I wanted to make any changes to the content of those chapters, I would start by editing the files in the working directory.

And finally, if I decided I wanted to remove an entire chapter of my book, I would delete the corresponding file in the working directory.

# *Local Repositories*

To turn a directory into a Git repository you have to initialize it.

When you initialize a repository, the .git directory is automatically created inside the project

directory.

To initialize a Git repository, you use the

```bash
❯ git init -b <branch-name> <project-name>
```

command. Your current directory must be the project directory you want to turn into a repository when you execute this command.

For example:

```bash
❯ git init -b main git-begin
```

will create the current directory as a Git directory with the branch name `main`.

```bash
 Directory: C:\Users\nguyenducchinh\git-begin
    Mode                 LastWriteTime         Length Name
    ----                 -------------         ------ ----
    da-h--         8/12/2023   2:07 PM                .git
```

Now you successfully init a Git local repository. This is what it currently looks like:

![](https://telescopecdn.ams3.digitaloceanspaces.com/images/34366265653835622d316266362d343331312d616464332d353564306538313363653735/587b900540f97734da6ce1ef85af93ea867bac9f0d80df12e7a233521a293041.webp align="left")

Within the .git repository, there are two important areas we want to explore further: the staging area and the commit history.

```bash
Directory: C:\Users\nguyenducchinh\git-begin\.git
    Mode                 LastWriteTime         Length Name
    ----                 -------------         ------ ----
    d-----         8/12/2023   2:07 PM                hooks
    d-----         8/12/2023   2:07 PM                info
    d-----         8/12/2023   2:07 PM                objects
    d-----         8/12/2023   2:07 PM                refs
    -a----         8/12/2023   2:07 PM            130 config
    -a----         8/12/2023   2:07 PM             73 description
    -a----         8/12/2023   2:07 PM             21 HEAD
```

We’ll take a look at those next and also discuss the concept of a commit in a little more detail.

### *Staging area*

The staging area is similar to a rough draft space. It is where you can add and remove files when you are preparing what you want to include in the next saved version of your project (your next commit). The staging area is represented by a file in the .git directory called index.

![](https://telescopecdn.ams3.digitaloceanspaces.com/images/34366265653835622d316266362d343331312d616464332d353564306538313363653735/851fee595c75b4aea0956e93a2b31a422be08857d83cadc50b327d0cb42b1906.webp align="left")

### *Commit History*

The commit history is where you can think of your commits existing. It is represented by the objects directory inside the .git directory.

Now that we have a complete Git diagram showing the most important areas when working with Git, let’s add the first file to the project and use a text editor to edit it.

![](https://telescopecdn.ams3.digitaloceanspaces.com/images/34366265653835622d316266362d343331312d616464332d353564306538313363653735/2b15e073dfba00570cb757e6b1a8a24d65ee31426a609600fa831cad174e7510.webp align="left")

## *Making a Commit*

What is a commit? A commit represents one version of a project. Every time you want to save a new version of a project, you can make a commit.

Committing is important because it allows you to back up your work. A common saying in the world of Git is “commit early, commit often”. Once you’ve made a commit, you’ll be able to go back and look at that commit to see what your project looked like at that point in time and avoid the frustration of losing unsaved work.

We will create one file, called colors.txt, in our working directory. This is now what our project looks like:

![](https://telescopecdn.ams3.digitaloceanspaces.com/images/34366265653835622d316266362d343331312d616464332d353564306538313363653735/8c66bde6e7123ac58dc3cc2b313282fa4ef8122d7edfe3918dd10ca6a1ea99e0.webp align="left")

Making a commit is a two-step process:

* Add all the files you want to include in the next commit to the staging area.
    
* Make a commit with a commit message.
    

```bash
❯ git status
On branch main
No commits yet

Untracked files:
(use "git add <file>..." to include in what will be committed)
        colors.txt

nothing to commit (create/copy files and use "git add" to track)
```

The ”git status” output informs you:

* No commits history yet.
    
* The colors.txt file is an untracked file.
    
* Git gives you the instructions that you need to add the untracked file to the staging area: use "git add &lt;file&gt;..." to include in what will be committed.
    

### *Add file to the Staging area*

To add files to the staging area, you use the `git add` command. If you only want to add individual files that you have edited to the staging area, then you can pass in the filename or filenames to the git add command as arguments. To add all the files you have edited or changed in your working directory, you can use the git add command with the -A option (which stands for “all”).

```bash
❯ git add -A

❯ git status

On branch main
No commits yet
Changes to be committed:
    (use "git rm --cached <file>..." to unstage)
        new file:   colors.txt
```

As mentioned in above, the staging area allows you to choose which updated files (or changes) will be included in your next commit. You can see the file colors.txt will be in the next commit.

This is what our project looks like:

![](https://telescopecdn.ams3.digitaloceanspaces.com/images/34366265653835622d316266362d343331312d616464332d353564306538313363653735/81ca09b871c25d20535038706fbc2691b1449effa7b2c42b37714c1dfbd82103.webp align="left")

The `git add` command does not move a file from the working directory to the staging area, it creates a copy of the file from the working directory into the staging area.

With the colors.txt file in the staging area, we are now ready to make a commit with a commit message

### *Make a Commit*

To make a commit, you will use the `git commit` command and pass in the `-m` option (which stands for “message”). The message should usually be a brief description of the changes you made in this version of the project.

```bash
❯ git commit -m "first commit"
 [main (root-commit) a513742] first commit
   1 file changed, 0 insertions(+), 0 deletions(-)
   create mode 100644 colors.txt
```

Now that we have made the first commit in the repository, let’s take a look at the information you can find about this commit in the commit history. Let see `git log`

```bash
❯ git log

commit a51374292e065da20b52ea4f9c377134cd5e0761 (HEAD -> main)
Author: Nguyen Duc Chinh <susuomlu@gmail.com>\
Date:   Sat Aug 12 15:00:47 2023 +0700
  first commit
```

The `git log` output shows:

* The full commit hash of the commit.
    
* The author of commit.
    
* The date and time at which the commit was made.
    
* The commit message, which in this case is ”first commit”.
    

This is what our project looks like:

![](https://telescopecdn.ams3.digitaloceanspaces.com/images/34366265653835622d316266362d343331312d616464332d353564306538313363653735/9c0ab2008642ad7619af46ca1e6d13323178ef03cbd52d314415004d928ee137.webp align="left")

In this topic, I introduced the different areas you will interact with when working with Git: the working directory, the staging area, the commit history, and the local repository.

We also learn the two steps of making a commit: adding files to the staging area and making a commit with a commit message and making a first commit in the repository.

In the next topic, we will learn about Git Braches and Merge.