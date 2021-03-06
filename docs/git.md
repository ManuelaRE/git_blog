# 5 scenarios where git commands will help you keep your commits Atomic.

I recently graduated from a bootcamp and started my first Developer job. Just like many before me, the first week was a bit overwhelming with what seemed like an avalanche of information.
This article is a reflection on my first week as a Junior Software Developer and a few new Git commands I learned in order to make Atomic commits easier to manage.

If you haven't heard before about Atomic commits you can do more reading
[here](https://www.freshconsulting.com/atomic-commits/) and [here](https://dev.to/cbillowes/why-i-create-atomic-commits-in-git-kfi). In essence Atomic commits revolve around one independent task or one fix.

Some of the main benefits of saving your work this way are:

- **track your history**. With git log you can easily see the history of your commits. I would also add here that it is important to document each change with a commit message that tells what you've done and why.
- **easy to review**. If your code is focused on only one atomic change then your reviewer should be able to follow and understand your code more easily.
- **easy to revert**. If you've introduced a new bug in your code it's easy to revert your change and fix the issue.

In order to keep a clean working tree and adhere to the Atomic principles here are 5 scenarios that can happen on everyday basis and how you can easily get passed them.

## 1. You've just committed your work and realized you forgot to add something

Start with a `git log` to check it's indeed the last commit you want to change.

```
commit 2df06c898ba035a0c0878e4f78032b0bd676f3ad (HEAD -> master)
Date:   Sat Feb 13 14:50:27 2021 +0000

    Frontend: Created layout for homepage

    This is a base on which we will develop the homepage and add
    new content.

```

Make your changes, add them to the staging area with `git add.` and them commit using
`git commit --amend`. This will allow you to override your last commit with your new additions.
If you do a `git log` now you can see you still have only on commit.

```
commit 5f7037f6e7e243e178a58b119e56c0b3337cd408 (HEAD -> master)
Date:   Sat Feb 13 14:50:27 2021 +0000

    Frontend: Created layout for homepage

    This is a base on which we will develop the homepage and add
    new content.
```

##2. You've worked on two things at the same time but you want to commit them separately.

You could use `git add <file>` but there is a more powerful option.
Your key here is `git add --patch` or simply `git add -p`.
When you pass this option to add, instead of immediately adding all the changes in the file to the index, it goes through each change and asks you what you want to do, and looks like this:

```
diff --git a/styles.css b/styles.css
index 23d8732..470d5dc 100644
--- a/styles.css
+++ b/styles.css
@@ -2,4 +2,5 @@
     margin: 0;
     padding: 0;
     box-sizing: border-box;
+    background-color: aquamarine;
 }
(1/1) Stage this hunk [y,n,q,a,d,e,?]? y
```

## 3. You want to merge together two commits you realized are part of the same Atomic commit.

Start by doing a `git log`. In the example below we want to merge `f2662c5` into `5f7037f` (the last commit into the first one).

```
commit f2662c51cf2f14ccb33907267f1d823ff6b8a678 (HEAD -> master)
Date:   Sat Feb 13 16:36:03 2021 +0000

    Frontend: added style link to Index.html

    This is in order to be able to see the style changes.

commit f84f936e22a0028ad00d3c33771518f23ac192b2
Date:   Sat Feb 13 15:29:42 2021 +0000

    Frontend: Set up style sheet with default styling

    This is a base on which the rest of the homepage will be styled
    upon.

commit 5f7037f6e7e243e178a58b119e56c0b3337cd408
Date:   Sat Feb 13 14:50:27 2021 +0000

    Frontend: Created layout for homepage

    This is a base on which we will develop the homepage and add
    new content.
```

To achieve this start start by typing: `git rebase -i Head~2`.
What this means is: the last commit we want to retain as-is has an index position of 2 (Git uses zero-based indexing) so we can pass in HEAD~2 to our interactive rebase command.

This will open an interactive panel where we look for our commit `f2662c5` and change the word `pick` to `f` and move it right under the commit you want to merge into e.g `5f7037f`.
Save using `:wq` command.

```
pick f84f936 Frontend: Set up style sheet with default styling
f f2662c5 Frontend: added style link to Index.html

# Rebase 5f7037f..f2662c5 onto 5f7037f (2 commands)
#
# Commands:
# p, pick <commit> = use commit
# r, reword <commit> = use commit, but edit the commit message
# e, edit <commit> = use commit, but stop for amending
# s, squash <commit> = use commit, but meld into previous commit
# f, fixup <commit> = like "squash", but discard this commit's log message
# x, exec <command> = run command (the rest of the line) using shell
# b, break = stop here (continue rebase later with 'git rebase --continue')
# d, drop <commit> = remove commit
# l, label <label> = label current HEAD with a name
# t, reset <label> = reset HEAD to a label
# m, merge [-C <commit> | -c <commit>] <label> [# <oneline>]
# .       create a merge commit using the original merge commit's
# .       message (or the oneline, if no original merge commit was
# .       specified). Use -c <commit> to reword the commit message.
#
# These lines can be re-ordered; they are executed from top to bottom.
#
# If you remove a line here THAT COMMIT WILL BE LOST.
#
# However, if you remove everything, the rebase will be aborted.
```

If you check your git log now you will see that commit `f2662c5` is no longer there.

## 4. Your code has been reviewed and you need to make some changes to your commit.
To modify a commit that is farther back in your history your best bet is the the rebase again.
Let's say you need to change something on commit `ed57576`.

```
commit e7433f8b9ff28eb819b8740c6692ffb975b426d4 (HEAD -> feature-button, origin/feature-button)
Date:   Sun Feb 14 11:53:53 2021 +0000

    Frontend: changed border radius value for the button

commit ed57576bf379fd7132eb2338b0be34b71f4a94f2 (main)
Date:   Sun Feb 14 11:55:38 2021 +0000

    Frontend: added border specifications

commit a429de8e24c1a5a05c7c4b24c599b637a4fce715 (origin/main)
Date:   Sat Feb 13 17:15:56 2021 +0000

    Frontend: Added button on landing page
```

In this case you can do a `rebase -i Head~1` and in the interactive panel change `pick` for `e` to edit your commit.

```
e e7433f8 Frontend: changed border radius value for the button

# Rebase ed57576..e7433f8 onto ed57576 (1 command)
#
# Commands:
# p, pick <commit> = use commit
# r, reword <commit> = use commit, but edit the commit message
# e, edit <commit> = use commit, but stop for amending
# s, squash <commit> = use commit, but meld into previous commit
# f, fixup <commit> = like "squash", but discard this commit's log message
# x, exec <command> = run command (the rest of the line) using shell
# b, break = stop here (continue rebase later with 'git rebase --continue')
# d, drop <commit> = remove commit
# l, label <label> = label current HEAD with a name
# t, reset <label> = reset HEAD to a label
# m, merge [-C <commit> | -c <commit>] <label> [# <oneline>]
# .       create a merge commit using the original merge commit's
# .       message (or the oneline, if no original merge commit was
# .       specified). Use -c <commit> to reword the commit message.
#
# These lines can be re-ordered; they are executed from top to bottom.
#
# If you remove a line here THAT COMMIT WILL BE LOST.
```

 After you save with `:wq` you can go ahead and make your changes followed by:

`git add .`
`git commit --amend`
`git rebase --continue`

If you want to push the branch to the remote repository you now need to force it in order to replace the current version. for that use: `git push -f origin <feature-name>`.




## 5. Master has changed while you were working on your branch.
One way to integrate your branch updates into the master is through a merge. Another way is to perform a master to branch rebase. The benefit of the latter is the clean, linear, non-branched commit history that elutes.

Start by getting the latest version of master:
`git checkout master`
`git pull`

Checkout into your branch and perform the rebase

`git checkout <branch name>`
`git rebase master`

If there are any conflicts you a prompt will ask you to choose what version you would like to keep.

```
button {
Accept Current Changes | Accept Incoming Change | Accept both Changes
<<< HEAD (Current Change)
    border-radius: 2%;
    border: 2px solid red;
    background-color: green;
=======
    border-radius: 5%;
    background-color:green;
>>> 376b235 (Frontend: changed border radius value for the button)(Incoming Change)
}
```

After solving all the merge conflicts you can add the amended files to the rebase and continue the process:
`git add .`
`git rebase --continue`
`git commit`


Git is powerful! So much so that there are many commands you don't even know they exist. I found that the scenario approach works best for me in learning how to harness the power of Git.
I recommend [these](https://gitexercises.fracz.com/exercise/master) exercises if you want practice and learn more.




