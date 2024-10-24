---
title: Exploring History
teaching: 25
exercises: 0
questions:
- "How can I identify old versions of files?"
- "How do I review my changes?"
- "How can I recover old versions of files?"
objectives:
- "Explain what the HEAD of a repository is and how to use it."
- "Identify and use Git commit numbers."
- "Compare various versions of tracked files."
- "Restore old versions of files."
keypoints:
- "`git diff` displays differences between commits."
- "`git checkout` recovers old versions of files."
---

As we saw in earlier lessons, we can refer to commits by their
identifiers.  You can refer to the _most recent commit_ of the working
directory by using the identifier `HEAD`.

We've been adding small changes at a time to our recipe files, so it's easy to track our
progress by looking at the `HEAD`s.  Let's create a new recipe, `mushroomsoup.md` in the same way
we created `peasoup.md`.

First, create the file and add some headings, committing the change afterwards.

~~~
$ nano mushroomsoup.md
$ cat mushroomsoup.md
~~~
{: .language-bash}

~~~
# Ingredients
# Instructions
~~~

~~~
$ git add mushroomsoup.md
$ git commit -m "Create a template for recipe"
~~~
{: .language-bash}

Next, add some ingredients to the recipe. Commit the change to the repository.

~~~
$ nano mushroomsoup.md
$ cat mushroomsoup.md
~~~
{: .language-bash}

~~~
# Ingredients
- onions
- garlic
- chestnut mushrooms
# Instructions
~~~

~~~
$ git add mushroomsoup.md
$ git commit -m "Add basic ingredients for mushroom soup"
~~~
{: .language-bash}

We decide to change one of the ingredients:

~~~
$ nano mushroomsoup.md
$ cat mushroomsoup.md
~~~
{: .language-bash}

~~~
# Ingredients
- onions
- garlic
- button mushrooms
# Instructions
~~~
{: .output}

~~~
$ git add mushroomsoup.md
$ git commit -m "Change mushroom type"
~~~
{: .language-bash}

Now let's make a change that turns out to be something of a mistake.

~~~
$ nano mushroomsoup.md
$ cat mushroomsoup.md
~~~
{: .language-bash}

~~~
# Ingredients
- onions
- garlic
- button mushrooms
# Instructions
An ill-considered change
~~~
{: .output}

Now, let's see what the differences are between our current version and the HEAD of the repository.

~~~
$ git diff HEAD mushroomsoup.md
~~~
{: .language-bash}

~~~
diff --git a/mushroomsoup.md b/mushroomsoup.md
index b36abfd..0848c8d 100644
--- a/mushroomsoup.md
+++ b/mushroomsoup.md
@@ -3,3 +3,4 @@
 - garlic
 - button mushrooms
 # Instructions
+An ill-considered change
~~~
{: .output}

Git shows us the difference (_diff_) between the current version of the file
and the most recently-committed version. The output is the same as what you
would get if you leave out `HEAD` (try it). The real goodness in all this is
when you can refer to previous commits. We do that by adding `~1` 
(where "~" is "tilde", pronounced [**til**-d*uh*]) to refer to the commit one
before `HEAD`.

~~~
$ git diff HEAD~1 mushroomsoup.md
~~~
{: .language-bash}

If we want to see the differences between older commits we can use `git diff`
again, but with the notation `HEAD~1`, `HEAD~2`, and so on, to refer to them:

~~~
$ git diff HEAD~2 mushroomsoup.md
~~~
{: .language-bash}

~~~
diff --git a/mushroomsoup.md b/mushroomsoup.md
index df0654a..b36abfd 100644
--- a/mushroomsoup.md
+++ b/mushroomsoup.md
@@ -1,2 +1,6 @@
 # Ingredients
+- onions
+- garlic
+- button mushrooms
 # Instructions
+An ill-considered change
~~~
{: .output}

We could also use `git show` which shows us what changes we made at an older commit as 
well as the commit message, rather than the _differences_ between a commit and our 
working directory that we see by using `git diff`.

~~~
$ git show HEAD~2 mushroomsoup.md
~~~
{: .language-bash}

~~~
commit f22b25e3233b4645dabd0d81e651fe074bd8e73b
Author: Alfredo Linguini <a.linguini@ratatouille.fr>
Date:   Thu Aug 22 10:07:21 2013 -0400

    Create a template for recipe

diff --git a/mushroomsoup.md b/mushroomsoup.md
new file mode 100644
index 0000000..df0654a
--- /dev/null
+++ b/mushroomsoup.md
@@ -0,0 +1,2 @@
+# Ingredients
+# Instructions
~~~
{: .output}

In this way,
we can build up a chain of commits.
The most recent end of the chain is referred to as `HEAD`;
we can refer to previous commits using the `~` notation,
so `HEAD~1`
means "the previous commit",
while `HEAD~123` goes back 123 commits from where we are now.

We can also refer to commits using
those long strings of digits and letters
that `git log` displays.
These are unique IDs for the changes,
and "unique" really does mean unique:
every change to any set of files on any computer
has a unique 40-character identifier.
Our first commit was given the ID
`f22b25e3233b4645dabd0d81e651fe074bd8e73b`,
so let's try this (your ID will be different):

~~~
$ git diff f22b25e3233b4645dabd0d81e651fe074bd8e73b mushroomsoup.md
~~~
{: .language-bash}

~~~
diff --git a/mushroomsoup.md b/mushroomsoup.md
index df0654a..93a3e13 100644
--- a/mushroomsoup.md
+++ b/mushroomsoup.md
@@ -1,2 +1,6 @@
 # Ingredients
+- onions
+- garlic
+- button mushrooms
 # Instructions
+An ill-considered change
~~~
{: .output}

That's the right answer, but because typing out random 40-character strings is annoying,
Git lets us use just the first few characters (typically seven for normal size projects):

~~~
$ git diff f22b25e mushroomsoup.md
~~~
{: .language-bash}

~~~
diff --git a/mushroomsoup.md b/mushroomsoup.md
index df0654a..93a3e13 100644
--- a/mushroomsoup.md
+++ b/mushroomsoup.md
@@ -1,2 +1,6 @@
 # Ingredients
+- onions
+- garlic
+- button mushrooms
 # Instructions
+An ill-considered change
~~~
{: .output}

All right! So we can save changes to files and see what we've changed.
Now, can we restore older versions?
Let's suppose we change our mind about the last update to
`mushroomsoup.md` (the "ill-considered change").

`git status` now tells us that the file has been changed,
but those changes haven't been staged (we haven't yet used the `git commit`
command):

~~~
$ git status
~~~
{: .language-bash}

~~~
On branch main
Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git checkout -- <file>..." to discard changes in working directory)
    modified:   mushroomsoup.md

no changes added to commit (use "git add" and/or "git commit -a")
~~~
{: .output}

We can put things back the way they were by using `git checkout`:

~~~
$ git checkout HEAD mushroomsoup.md
$ cat mushroomsoup.md
~~~
{: .language-bash}

~~~
# Ingredients
- onions
- garlic
- chestnut mushrooms
# Instructions
~~~
{: .output}

As you might guess from its name,
`git checkout` checks out (i.e. restores) an old version of a file.
In this case,
we're telling Git that we want to recover the version of the file recorded in `HEAD`,
which is the last saved commit.
If we want to go back even further,
we can use a commit identifier instead:

~~~
$ git checkout f22b25e mushroomsoup.md
~~~
{: .language-bash}

~~~
$ cat mushroomsoup.md
~~~
{: .language-bash}

~~~
# Ingredients
# Instructions
~~~
{: .output}

~~~
$ git status
~~~
{: .language-bash}

~~~
On branch main
Changes to be committed:
  (use "git reset HEAD <file>..." to unstage)
    modified:   mushroomsoup.md

~~~
{: .output}

Notice that the changes are currently in the staging area.
Again, we can put things back the way they were
by using `git checkout`:

~~~
$ git checkout HEAD mushroomsoup.md
~~~
{: .language-bash}

> ## Don't Lose Your HEAD
>
> Above we used
>
> ~~~
> $ git checkout f22b25e mushroomsoup.md
> ~~~
> {: .language-bash}
>
> to revert `mushroomsoup.md` to its state after the commit `f22b25e`. But be careful!
> The command `checkout` has other important functionalities and Git will misunderstand
> your intentions if you are not accurate with your typing.
> If you forget `mushroomsoup.md` in the previous command.
>
> ~~~
> $ git checkout f22b25e
> ~~~
> {: .language-bash}
>
> ~~~
> Note: checking out 'f22b25e'.
>
> You will be in what's known as a "detached HEAD" state. You can look around,
> make experimental changes and commit them, and you can discard any commits you
> make in this state without impacting any branches by performing another checkout.
>
> If you want to create a new branch to retain commits you create, you may do so
> (now or later) by using -b with the checkout command again. Example:
>
>  git checkout -b <new-branch-name>
>
> HEAD is now at f22b25e Create a template for recipe
> ~~~
> {: .error}
>
> The "detached HEAD" is like "look, but don't touch" here,
> so you shouldn't make any changes in this state.
> After investigating your repo's past state, reattach your `HEAD` with `git checkout main`.
{: .callout}

It's important to remember that
we must use the commit number that identifies the state of the repository
*before* the change we're trying to undo.
A common mistake is to use the number of
the commit in which we made the change we're trying to discard.
In the example below, we want to retrieve the state from before the most
recent commit (`HEAD~1`), which is commit `f22b25e`:

![Git Checkout](../fig/git-checkout.svg)

So, to put it all together, here's how Git works in cartoon form:

![https://figshare.com/articles/How_Git_works_a_cartoon/1328266](../fig/git_staging.svg)

> ## Simplifying the Common Case
>
> If you read the output of `git status` carefully,
> you'll see that it includes this hint:
>
> ~~~
> (use "git checkout -- <file>..." to discard changes in working directory)
> ~~~
> {: .language-bash}
>
> As it says,
> `git checkout` without a version identifier restores files to the state saved in `HEAD`.
> The double dash `--` is needed to separate the names of the files being recovered
> from the command itself:
> without it,
> Git would try to use the name of the file as the commit identifier.
{: .callout}

The fact that files can be reverted one by one
tends to change the way people organize their work.
If everything is in one large document,
it's hard (but not impossible) to undo changes to the introduction
without also undoing changes made later to the conclusion.
If, on the other hand, the introduction and conclusion are stored in separate files,
moving backward and forward in time becomes much easier.

> ## Recovering Older Versions of a File
>
> Jennifer has made changes to the Python script that she has been working on for weeks, and the
> modifications she made this morning "broke" the script and it no longer runs. She has spent
> ~ 1hr trying to fix it, with no luck...
>
> Luckily, she has been keeping track of her project's versions using Git! Which commands below will
> let her recover the last committed version of her Python script called
> `data_cruncher.py`?
>
> 1. `$ git checkout HEAD`
>
> 2. `$ git checkout HEAD data_cruncher.py`
>
> 3. `$ git checkout HEAD~1 data_cruncher.py`
>
> 4. `$ git checkout <unique ID of last commit> data_cruncher.py`
>
> 5. Both 2 and 4
>
>
> > ## Solution
> >
> > The answer is (5)-Both 2 and 4. 
> > 
> > The `checkout` command restores files from the repository, overwriting the files in your working 
> > directory. Answers 2 and 4 both restore the *latest* version *in the repository* of the file 
> > `data_cruncher.py`. Answer 2 uses `HEAD` to indicate the *latest*, whereas answer 4 uses the 
> > unique ID of the last commit, which is what `HEAD` means. 
> > 
> > Answer 3 gets the version of `data_cruncher.py` from the commit *before* `HEAD`, which is NOT 
> > what we wanted.
> > 
> > Answer 1 can be dangerous! Without a filename, `git checkout` will restore **all files** 
> > in the current directory (and all directories below it) to their state at the commit specified. 
> > This command will restore `data_cruncher.py` to the latest commit version, but it will also 
> > restore *any other files that are changed* to that version, erasing any changes you may 
> > have made to those files!
> > As discussed above, you are left in a *detached* `HEAD` state, and you don't want to be there.
> {: .solution}
{: .challenge}

> ## Reverting a Commit
>
> Jennifer is collaborating on her Python script with her colleagues and
> realizes her last commit to the project's repository contained an error and
> she wants to undo it. `git revert [erroneous commit ID]` will create a new 
> commit that reverses Jennifer's erroneous commit. Therefore `git revert` is
> different to `git checkout [commit ID]` because `git checkout` returns the
> files within the local repository to a previous state, whereas `git revert`
> reverses changes committed to the local and project repositories.  
> Below are the right steps and explanations for Jennifer to use `git revert`,
> what is the missing command?
>
> 1. `________ # Look at the git history of the project to find the commit ID`
>
> 2. Copy the ID (the first few characters of the ID, e.g. 0b1d055).
>
> 3. `git revert [commit ID]`
>
> 4. Type in the new commit message.
>
> 5. Save and close
{: .challenge}

> ## Understanding Workflow and History
>
> What is the output of the last command in
>
> ~~~
> $ cd recipes
> $ echo "I like tomatoes, therefore I like ketchup" > ketchup.md
> $ git add ketchup.md
> $ echo "ketchup enhances pasta dishes" > ketchup.md
> $ git commit -m "my opinions about the red sauce"
> $ git checkout HEAD ketchup.md
> $ cat ketchup.md # this will print the content of ketchup.md on screen
> ~~~
> {: .language-bash}
>
> 1. ~~~
>    ketchup enchances pasta dishes
>    ~~~
>    {: .output}
> 2. ~~~
>    I like tomatoes, therefore I like ketchup
>    ~~~
>    {: .output}
> 3. ~~~
>    I like tomatoes, therefore I like ketchup
>    ketchup enhances pasta dishes
>    ~~~
>    {: .output}
> 4. ~~~
>    Error because you have changed ketchup.md without committing the changes
>    ~~~
>    {: .output}
>
> > ## Solution
> >
> > The answer is 2. 
> > 
> > The changes to the file from the second `echo` command are only applied to the working copy, 
> > The command `git add ketchup.md` places the current version of `ketchup.md` into the staging area.
> > not the version in the staging area.
> >
> > So, when `git commit -m "my opinions about the red sauce"` is executed,
> > the version of `ketchup.md` committed to the repository is the one from the staging area and
> > has only one line.
> >  
> >  At this time, the working copy still has the second line (and 
> >  
> >  `git status` will show that the file is modified). However, `git checkout HEAD ketchup.md`
> >  replaces the working copy with the most recently committed version of `ketchup.md`.
> >  So, `cat ketchup.md` will output
> >  ~~~
> >  I like tomatos, therefore I like ketchup
> > ~~~
> > {: .output}
> {: .solution}
{: .challenge}

> ## Checking Understanding of `git diff`
>
> Consider this command: `git diff HEAD~9 mushroomsoup.md`. What do you predict this command
> will do if you execute it? What happens when you do execute it? Why?
>
> Try another command, `git diff [ID] mushroomsoup.md`, where [ID] is replaced with
> the unique identifier for your most recent commit. What do you think will happen,
> and what does happen?
{: .challenge}

> ## Getting Rid of Staged Changes
>
> `git checkout` can be used to restore a previous commit when unstaged changes have
> been made, but will it also work for changes that have been staged but not committed?
> Make a change to `mushroomsoup.md`, add that change, and use `git checkout` to see if
> you can remove your change.
{: .challenge}

> ## Explore and Summarize Histories
>
> Exploring history is an important part of Git, and often it is a challenge to find
> the right commit ID, especially if the commit is from several months ago.
> Imagine the `recipes` project has more than 50 files.
> You would like to find a commit that modifies some specific text in `mushroomsoup.md`.
> When you type `git log`, a very long list appears.
> How can you narrow down the search?
>
> Recall that the `git diff` command allows us to explore one specific file,
> e.g., `git diff mushroomsoup.md`. We can apply a similar idea here.
>
> ~~~
> $ git log mushroomsoup.md
> ~~~
> {: .language-bash}
>
> Imagine that some commit messages are very ambiguous, e.g., `update files`.
> How can you search through these files?
>
> Both `git diff` and `git log` are very useful and they summarize a different part of the history 
> for you.
> Is it possible to combine both? Let's try the following:
>
> ~~~
> $ git log --patch mushroomsoup.md
> ~~~
> {: .language-bash}
>
> You should get a long list of output, and you should be able to see both commit messages and 
> the difference between each commit.
>
> Question: What does the following command do?
>
> ~~~
> $ git log --patch HEAD~3 *.md
> ~~~
> {: .language-bash}
{: .challenge}
