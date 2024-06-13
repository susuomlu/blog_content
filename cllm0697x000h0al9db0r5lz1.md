---
title: "Working with a Remote GitHub repository"
datePublished: Tue Aug 22 2023 07:47:02 GMT+0000 (Coordinated Universal Time)
cuid: cllm0697x000h0al9db0r5lz1
slug: working-with-a-remote-github-repository
cover: https://cdn.hashnode.com/res/hashnode/image/stock/unsplash/wnShDP37vB4/upload/9acec8ff798bb8ca14a2c3d3f5eba371.jpeg
tags: github, git

---

# Why do we use Remote repositories?

Remote repositories allow you to:

* Back up your project.
    
* Access a Git project from multiple computers.
    
* Collaborate with others on different projects.
    

# Working with a Remote repository

There are three steps to this process:

* Create the remote repository on GitHub.
    
* Add a connection to the remote repository in the local repository.
    
* Upload (or push) data from the local repository to the remote repository.
    

## Create a Remote repository

Go to https://github.com and create an account. Then you can create a new repository.

In Create a new repository page, you need to give some information:

* Repository name
    
* Description
    
* Accessibility (Public or Private )
    

and click on the Create repository button shown in the screenshot below.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1692587324991/e00a3b02-7fcf-4efb-a16d-1702f58e5fec.png align="center")

## Git clone

Git allows you to get a copy of the GitHub repository for your own local machine and have your own local repository. In order to perform this task Git provides us with the `git clone` command.

You can get the URL by going to your Quick Setup page.

Or if you have an established repo, you can find it in the Code section.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1692588190358/30c01ba8-e8be-4857-9532-9d902a34d20f.png align="center")

In your local machine, open the command prompt and navigate to any folder or directory where you would like to have your local repository set, and type the following command:

```bash
$ git clone https://github.com/'your-username'/'your-repo'
```

You will have a copy of the remote repository on your local machine.

## Git Push

Now let’s create a simple `index.html` file and save it in our working directory.

Then use `git add` command to add the file from the working directory to the staging area. Use `git commit` command to save the changes from the staging area into our local repository. You can read about `git add` and `git commit` [here](https://blog.nguyenducchinh.com/github-making-your-first-commit).

```bash
$ git status
On branch main

No commits yet

Changes to be committed:
  (use "git rm --cached <file>..." to unstage)
        new file:   index.html

$ git commit -m "remote repo"
[main (root-commit) efc93b3] remote repo
 1 file changed, 19 insertions(+)
 create mode 100644 index.html
```

Now, you can use git push to push to your remote repository.

```bash
$ git push
info: please complete authentication in your browser...
Enumerating objects: 3, done.
Counting objects: 100% (3/3), done.
Delta compression using up to 8 threads
Compressing objects: 100% (2/2), done.
Writing objects: 100% (3/3), 447 bytes | 447.00 KiB/s, done.
Total 3 (delta 0), reused 0 (delta 0), pack-reused 0
To https://github.com/susuomlu/git-begin.git
 * [new branch]      main -> main
```

Now check back your GitHub repository, you can see the newly uploaded file.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1692686887062/7b20ae03-58e6-4159-85b3-52d7ae79183a.png align="center")

## Git Pull

Let’s update the `index.html` file present in the GitHub repository.

Open GitHub repository page, open `index.html` and click edit as shown in the screenshot below.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1692687345172/5e86a4c7-93d0-47b4-9494-dd20af0f3000.png align="center")

Choose Commit changes and write the commit message, as shown in the screenshot below.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1692687705789/6ff98099-3e31-4b3c-b9a4-7cc972a4cc99.png align="center")

Now the GitHub repository contains the latest updated version of `index.html` file. However, the local repository is now outdated.

In order to get this new version into our local repository, the `git pull` command is used.

```bash
$ git pull
remote: Enumerating objects: 5, done.
remote: Counting objects: 100% (5/5), done.
remote: Compressing objects: 100% (2/2), done.
remote: Total 3 (delta 1), reused 0 (delta 0), pack-reused 0
Unpacking objects: 100% (3/3), 677 bytes | 35.00 KiB/s, done.
From https://github.com/susuomlu/git-begin
   efc93b3..1b054c7  main       -> origin/main
Updating efc93b3..1b054c7
Fast-forward
 index.html | 1 +
 1 file changed, 1 insertions(+)
```

Refresh `index.html` file in our local machine and you will see the updated version.

## Git Merge and Merge Conflict

Merge conflict happens when developers are working on the same file and on the same lines of code.

When this happens Git does not know how to fix the issue and throws a merge conflict message and it is up to the developer to resolve such a situation.

In your local machine, open `index.html` and make changes to it.

After it do `git add` and `git commit` do save the changes.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1692688180060/f2a24fc6-0661-4e44-86da-0967fc4aac1c.png align="center")

Open the GitHub repository page, update `index.html` file at the same line and commit the changes.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1692688351181/9dbad7ee-6732-4660-8034-5615dc50f9a4.png align="center")

Now if we pull the code from GitHub. Since changes were made to the same file and to the same line, Git will throw a merge conflict message as shown below.

```bash
$ git pull
remote: Enumerating objects: 5, done.
remote: Counting objects: 100% (5/5), done.
remote: Compressing objects: 100% (2/2), done.
remote: Total 3 (delta 1), reused 0 (delta 0), pack-reused 0
Unpacking objects: 100% (3/3), 682 bytes | 22.00 KiB/s, done.
From https://github.com/susuomlu/git-begin
   1b054c7..14e1e2c  main       -> origin/main
Auto-merging index.html
CONFLICT (content): Merge conflict in index.html
Automatic merge failed; fix conflicts and then commit the result.
```

Let’s open `index.html` file in our local machine. You can see Git input some lines in it.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1692689794039/bcd3a017-24fe-4957-bd5d-851187ec2ac5.png align="center")

In order to resolve this issue, one of the developers has to delete all the merge conflict from `index.html` file and commit it again.

In this case, I decided to delete my own modification and let the

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1692689722120/ed105d62-c959-4ef5-bce7-210b4a322e9f.png align="center")

*Note: The shortcut of* `git add` + `git commit` *is:* `git commit –am “commit message”`

```bash
$ git commit -am "merge resolve"
[main 8efe397] merge resolve
$ git pull
Already up to date.
```

This is the final for my series talking about the basic of how to work with Git and GitHub.

We went through how to work with a local repository, how to branch and merge, and finally how to work with a remote repository.

For older posts regarding this topic you can refer to:

[\[GitHub\] Making your first commit](https://blog.nguyenducchinh.com/github-making-your-first-commit)

[\[GitHub\] Branching and Merging](https://blog.nguyenducchinh.com/github-branching-and-merging)