---
title: "GitHub Branching and Merging"
datePublished: Mon Aug 14 2023 08:09:10 GMT+0000 (Coordinated Universal Time)
cuid: cllalfwhl000508mq6loa4p9b
slug: github-branching-and-merging
cover: https://cdn.hashnode.com/res/hashnode/image/stock/unsplash/jH_-L1C_o6Q/upload/b2f62b75fd21f8976a6656562b29cf8f.jpeg
tags: github, git

---

# What are Git Branches?

Suppose I'm writing an online novel. The published chapters are the main branch.

I don’t want to publish to the official line until my editor has reviewed and approved it.

So, I can make a secondary branch, work on that branch, and then submit it to be reviewed by my editor. Once they’ve approved the work I can combine that branch into the main branch.

If I hire an assistant, we can work on our own secondary branches. Then, only the work on a secondary branch that has been approved by both the other author and the editor will be combined with the main branch.

A branch represents a line of development. Branches in Git are movable pointers to commits.

New commits are recorded in the history of the current branch, which is called a fork in the history of the project. Every time you commit, the master branch pointer moves forward automatically.

A Git project can have multiple branches (or lines of development). Each of these branches is a standalone version of the project.

# Why do we use Branches?

Branches are a powerful tool for managing your code in Git. They allow you to work on different aspects of your project without interfering with each other and allow multiple people to work on the same project at the same time.

You can also use branches to create pull requests on platforms like GitHub or GitLab, where other developers can review and approve your code before merging it to the main branch.

# Working on a Branch

Let's go back to the original project we have in the previous post. I modified the `colors.txt` file and commit it.

```bash
❯ git status
On branch main
Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git restore <file>..." to discard changes in working directory)
        modified:   colors.txt

no changes added to commit (use "git add" and/or "git commit -a")
```

Git tells us that `colors.txt` is a modified file. It is now listed in the git status output. The colors.txt file is not staged for commit; in other words, it has not been added to the staging area.

Let's stage it and commit it.

```bash
❯ git add -A

❯ git commit -m "begin"
[main 2314fbc] modified colors
 1 file changed, 1 insertion(+)

❯ git log
commit 2314fbc16b12a978e455eb852c400425e389aa8a (HEAD -> main)
Author: Nguyen Duc Chinh <susuomlu@gmail.com>
Date:   Mon Aug 14 13:14:54 2023 +0700
  begin

commit acdeedbd9540d6a4918d81e7926096a380839dd2
Author: Nguyen Duc Chinh <susuomlu@gmail.com>
Date:   Sat Aug 12 15:00:47 2023 +0700
  first commit
```

You just made a new commit on a branch, this case is the main branch.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1691994875943/0795a128-eb96-477b-9677-894b351a0dd4.png align="center")

The HEAD indicates which branch you are on by referencing in .git &gt; refs &gt; heads. You can also know which branch you are on using `git branch`.

```bash
❯ git branch
* main
```

The \* symbol indicates which branch you are in.

# Create a Branch

You can use the command `git branch <name>` to create a new branch with the given name. This will not switch you to the new branch but only create it.

```bash
❯ git branch chapter#1

❯ git branch
  chapter#1
* main
```

To switch to the new branch, you can use `git checkout <name>` or `git switch <name>`.

```bash
❯ git checkout chapter#1
Switched to branch 'chapter#1'

❯ git branch
* chapter#1
  main
```

If I edit `colors.txt` again, then do `git commit -m "new branch"` it, the brach is now separated.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1691998741539/ae61ee87-bd5d-4f51-978c-c6ad36174455.png align="center")

Now the HEAD is in another branch called chapter#1.

# What is Git Merge?

Merge is a way of combining the changes from two different branches in Git. When you merge, you take the latest commits from one branch and apply them to another branch. This way, you can keep your code up to date and avoid conflicts. Merging is usually done when you want to integrate new features or bug fixes into your main branch.

You can merge using the `git merge` command, or use a graphical tool like GitHub Desktop or GitKraken.

There are two types of merges:

* Fast-forward merges
    
* Three-way merges
    

## Fast-forward Merge

Let’s assume I work on my book `colors.txt` and I add commits C and D to the chapter#1 branch.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1691999218289/24643b8b-3e7c-4059-9e38-6fb82694ca55.png align="center")

When I merge using the `git merge` command, a fast-forward merge would occur.

In this case, we can say the branches have not diverged. During the fast-forward merge, the main branch pointer simply moves forward to point to the commit that the chapter#1 branch points to, which is D.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1691999443587/ab6c4699-3135-403b-8c7e-545e9584c287.png align="center")

## Three-way Merge

Now suppose I decide to make a chapter#8 branch to work on chapter 8 of my book `colors.txt`, and I make commits E and F.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1691999953213/4e1bb581-6ad1-41e6-b8ec-196cacfceae1.png align="center")

If I merge the chapter#8 branch into the main branch, it can’t be a Fast-forward merge because there is no way to just move the branch pointer forward to combine these two development histories.

Instead, a merge commit will be created to tie the two development histories together. This is Three-way Merge.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1692000213086/dfd64bff-1745-4798-ac92-cb82577a2631.png align="center")

In this topic, I introduced the concept of branches as different lines of development and showed they are movable pointers to different commits.

We also learn what merging is, know the two types of merges, and explained how the type of merge that will be carried out depends on the development histories of the branches involved in the merge.

In the next topic, we will learn about Pushing and Pulling from a Remote Repository.