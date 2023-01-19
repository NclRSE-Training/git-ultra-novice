---
title: Branches
teaching: 20
exercises: 0
questions:
- "What are branches?"
- "How can I work in parallel using branches?"
objectives:
- "Understand why branches are useful for:"
- "*working on separate tasks in the same repository concurrently*"
- "*trying multiple solutions to a problem*"
- "*check-pointing versions of code*"
- "Merge branches back into the main branch"
keypoints:
- "Branches are useful for developing new features while ensuring that the main
working base of code is not disturbed"
- "Branches are quick to create and to delete"
- "Creating a branch, adding a new feature in that branch, merging it with the main branch,
and then deleting the branch is a basic engineering workflow when working with#
Git"
- "Or: \"branch early and often\""
---

So far we've always been working in a straight timeline.
However, there are times when we might want to keep
our main work safe from experimental changes we are working on.
To do this we can use branches to work on separate tasks in parallel
without changing our current branch, `main`.

You may not have noticed before, but the first branch made is called `main`.
This is the default branch created when initializing a repository and
is often considered to be the "clean" or "working" version of a
repository's code.

We can see what branches exist in a repository by typing

~~~
$ git branch
~~~
{: .bash}

~~~
* main
~~~
{: .output}


The '*' indicates which branch we are currently on.

In this lesson, Alfredo wants to create two new categories of recipes, one 
for starters and one for desserts. However, he does not want to work on the
main branch because it is stable and he wants to keep it safe.

First let's make the `starters` branch. We use the same `git branch` command but
add the name we want to give our new branch.

~~~
$ git branch starters
~~~
{: .bash}

We can now check our work with the `git branch` command:

~~~
$ git branch
~~~
{: .bash}

~~~
* main
  starters
~~~
{: .output}

We can see that we created the `starters` branch but we are still in the main
branch. We can also see this in the output of the `git status` command.

~~~
$ git status
~~~
{: .bash}

~~~
On branch main
nothing to commit, working directory clean
~~~
{: .output}

To switch to the new branch we can use the `checkout` command
we learned earlier and check our work with `git branch`.

~~~
$ git checkout starters
$ git branch
~~~
{: .bash}

~~~
  main
* starters
~~~
{: .output}

We have previously used the `checkout` command to checkout a file from a
specific commit using commit hashes, or `HEAD` and the filename
(`git checkout HEAD <file>`). The `checkout` command can also be used to
checkout an entire previous version of the repository, updating all files in
the repository to match the state of a desired commit.

Branches allow us to do this using a human-readable name rather than memorizing
a commit hash. This name also typically gives purpose to the set of changes in
that branch. When we use the command `git checkout <branch_name>`, we are using
a nickname to checkout a version of the repository that matches the most recent
commit in that branch (a.k.a. the `HEAD` of that branch).

You can use `git log` and `ls` to see that the history and 
files are the same as our `main` branch. This will be true until
some changes are committed to our new branch.

Let's create a starter recipe for olives. Use the command
`touch olives.md` to create an empty file (but imagine we spent hours
working on this recipe!).

~~~
$ touch olives.md
~~~
{: .bash}

Now we can add and commit the file to our branch.

~~~
$ git add olives.md
$ git commit -m "Wrote olives recipe"
~~~
{: .bash}

~~~
[starters x792csa1] Wrote olives recipe
 1 file changed, 1 insertion(+)
 create mode 100644 olives.md
~~~
{: .output}

Lets check our work!

~~~
$ ls
$ git log --oneline
~~~
{: .bash}

As expected, we see our commit in the log. Now let's switch back to the `main`
branch.

~~~
$ git checkout main
$ git branch
~~~
{: .bash}

~~~
* main
  starters
~~~
{: .output}

Now that we've confirmed we are in the `main` branch again, let's explore the
repository a bit more. Confirm that `olives.md` and our last commit aren't
in `main`:

~~~
$ ls
$ git log --oneline
~~~
{: .bash}

We no longer see the file `olives.md` and our latest commit doesn't
appear in this branch's history. Where has it disappeared to? I hasn't vanished;
all of our hard work remains in the `starters` branch. We can confirm this by
moving back to that branch.

~~~
$ git checkout starters
$ git branch
~~~
{: .bash}

~~~
  main
* starters
~~~
{: .output}

~~~
$ ls
$ git log --oneline
~~~
{: .bash}

And we see that our `olives.md` file and respective commit have been
preserved in the `starters` branch.

Now we can repeat the process in a branch called `desserts`.

First we must checkout the `main` branch again. New branches will
include the entire history up to the current commit, and we'd like
to keep the two branches independent.

~~~
$ git checkout main
$ git branch
~~~
{: .bash}

~~~
* main
  starters
~~~
{: .output}

This time let's create and switch to the `desserts` branch
in one command. We can do so by adding the `-b` flag to checkout.

~~~
$ git checkout -b desserts
$ git branch
~~~
{: .bash}

~~~
* desserts
  main
  starters
~~~
{: .output}


We can use `ls` and `git log` to see that this branch is 
the same as our current `main` branch.

Now we can make `apple_pie.md` and add and commit it.
Again imagine instead of `touch`ing the file we worked 
on it for many hours.

~~~
$ touch apple_pie.md
$ git add apple_pie.md
$ git commit –m “Wrote recipe for apple pie”
~~~
{: .bash}

~~~
[bashdev 2n779ds] Wrote recipe for classic tiramisu
 1 file changed, 1 insertion(+)
 create mode 100644 apple_pie.md
~~~
{: .output}

Lets check our work again before we switch back to the main branch.
~~~
$ ls
$ git log --oneline
~~~
{: .bash}

Alfredo decides his olives recipe needs more work before it is ready. But
he is happy with the apple pie recipe, so let's *merge* this version into our
`main` branch so we can use it. Merging brings the changes
from a different branch into the current branch.

First we must switch to the branch we're merging changes **into**, `main`.

~~~
$ git checkout main
$ git branch
~~~
{: .bash}

~~~
  desserts
* main
  starters
~~~
{: .output}

Now we can `merge` **from** the `desserts` branch **into** our current branch
(`main`). In English, this command could be stated as "`git`, please
`merge` the changes in the `desserts` branch into the branch
I am currently in".

~~~
$ git merge desserts
~~~
{: .bash}

~~~
Updating 12687f6..x792csa1
Fast-forward
 apple_pie.md | 0
 1 file changed, 0 insertions(+), 0 deletions(-)
 create mode 100644 apple_pie.md
~~~
{: .output}

Now that we've merged `desserts` into `main`, these changes
exist in both branches. This could be confusing in the future if we
stumble upon the `desserts` branch again. We would therefore like to
delete our old branches to avoid such future confusion.
We can do so by adding the `-d` flag to the `git branch` command.

~~~
git branch -d desserts
~~~
{: .bash}

~~~
Deleted branch desserts (was x792csa1).
~~~
{: .output}

Alfredo decides he doesn't want to keep the starters branch after all, and
deletes it.

~~~
$ git branch -d starters
~~~
{: .bash}

~~~
error: The branch 'starters' is not fully merged.
If you are sure you want to delete it, run 'git branch -D starters'.
~~~
{: .output}

Git recognises that we've never merged the changes from the `starters` branch
into `main`, and warns us that deleting it will cause us to lose the
development that we have done in that branch. Git tells us to use the `-D` flag
instead if we are absolutely sure we want to delete this branch and the work
contained in it.

Alfredo decides that he really does want to delete this branch, so we go ahead
and do so.

~~~
git branch -D starters
~~~
{: .bash}

~~~
Deleted branch starters (was 2n779ds).
~~~
{: .output}
