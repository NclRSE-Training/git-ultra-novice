---
title: Conflicts
teaching: 15
exercises: 0
questions:
- "What do I do when my changes conflict with someone else's?"
objectives:
- "Explain what conflicts are and when they can occur."
- "Resolve conflicts resulting from a merge."
keypoints:
- "Conflicts occur when two or more people change the same lines of the same file."
- "The version control system does not allow people to overwrite each other's changes blindly, but highlights conflicts so that they can be resolved."
---

As soon as people can work in parallel, they'll likely step on each other's
toes.  This will even happen with a single person: if we are working on
a piece of software on both our laptop and a server in the lab, we could make
different changes to each copy.  Version control helps us manage these
[conflicts]({{ page.root}}{% link reference.md %}#conflict) by giving us tools to
[resolve]({{ page.root }}{% link reference.md %}#resolve) overlapping changes.

To see how we can resolve conflicts, we must first create one.  The file
`peasoup.md` currently looks like this in both partners' copies of our `recipes`
repository:

~~~
$ cat peasoup.md
~~~
{: .language-bash}

~~~
# Ingredients
- peas
- spring onions
- potato
# Instructions
~~~
{: .output}

Let's add a line to one partner's copy only:

~~~
$ nano peasoup.md
$ cat peasoup.md
~~~
{: .language-bash}

~~~
# Ingredients
- peas
- spring onions
- potato
# Instructions
- put peas into a pan.
~~~
{: .output}

and then push the change to GitHub:

~~~
$ git add peasoup.md
$ git commit -m "First step in the instructions"
~~~
{: .language-bash}

~~~
[main 5ae9631] First step in the instructions
 1 file changed, 1 insertion(+)
~~~
{: .output}

~~~
$ git push origin main
~~~
{: .language-bash}

~~~
Enumerating objects: 5, done.
Counting objects: 100% (5/5), done.
Delta compression using up to 8 threads
Compressing objects: 100% (3/3), done.
Writing objects: 100% (3/3), 331 bytes | 331.00 KiB/s, done.
Total 3 (delta 2), reused 0 (delta 0)
remote: Resolving deltas: 100% (2/2), completed with 2 local objects.
To https://github.com/alflin/recipes.git
   29aba7c..dabb4c8  main -> main
~~~
{: .output}

Now let's have the other partner make a different change to their copy
*without* updating from GitHub:

~~~
$ nano peasoup.md
$ cat peasoup.md
~~~
{: .language-bash}

~~~
# Ingredients
- peas
- spring onions
- potato
# Instructions
- shell the peas
~~~
{: .output}

We can commit the change locally:

~~~
$ git add peasoup.md
$ git commit -m "Add first step"
~~~
{: .language-bash}

~~~
[main 07ebc69] Add first step
 1 file changed, 1 insertion(+)
~~~
{: .output}

but Git won't let us push it to GitHub:

~~~
$ git push origin main
~~~
{: .language-bash}

~~~
To https://github.com/alflin/recipes.git
 ! [rejected]        main -> main (fetch first)
error: failed to push some refs to 'https://github.com/alflin/recipes.git'
hint: Updates were rejected because the remote contains work that you do
hint: not have locally. This is usually caused by another repository pushing
hint: to the same ref. You may want to first integrate the remote changes
hint: (e.g., 'git pull ...') before pushing again.
hint: See the 'Note about fast-forwards' in 'git push --help' for details.
~~~
{: .output}

![The Conflicting Changes](../fig/conflict.svg)

Git rejects the push because it detects that the remote repository has new updates that have not been
incorporated into the local branch.
What we have to do is pull the changes from GitHub,
[merge]({{ page.root }}{% link reference.md %}#merge) them into the copy we're currently working in, and then push that.
Let's start by pulling:

~~~
$ git pull origin main
~~~
{: .language-bash}

~~~
remote: Enumerating objects: 5, done.
remote: Counting objects: 100% (5/5), done.
remote: Compressing objects: 100% (1/1), done.
remote: Total 3 (delta 2), reused 3 (delta 2), pack-reused 0
Unpacking objects: 100% (3/3), done.
From https://github.com/alflin/recipes
 * branch            main     -> FETCH_HEAD
    29aba7c..dabb4c8  main     -> origin/main
Auto-merging peasoup.md
CONFLICT (content): Merge conflict in peasoup.md
Automatic merge failed; fix conflicts and then commit the result.
~~~
{: .output}

The `git pull` command updates the local repository to include those
changes already included in the remote repository.
After the changes from remote branch have been fetched, Git detects that changes made to the local copy 
overlap with those made to the remote repository, and therefore refuses to merge the two versions to
stop us from trampling on our previous work. The conflict is marked in
in the affected file:

~~~
$ cat peasoup.md
~~~
{: .language-bash}

~~~
# Ingredients
- peas
- spring onions
- potato
# Instructions
<<<<<<< HEAD
- shell the peas
=======
- put peas into a pan.
>>>>>>> dabb4c8c450e8475aee9b14b4383acc99f42af1d
~~~
{: .output}

Our change is preceded by `<<<<<<< HEAD`.
Git has then inserted `=======` as a separator between the conflicting changes
and marked the end of the content downloaded from GitHub with `>>>>>>>`.
(The string of letters and digits after that marker
identifies the commit we've just downloaded.)

It is now up to us to edit this file to remove these markers
and reconcile the changes.
We can do anything we want: keep the change made in the local repository, keep
the change made in the remote repository, write something new to replace both,
or get rid of the change entirely.
Let's replace both so that the file looks like this:

~~~
$ cat peasoup.md
~~~
{: .language-bash}

~~~
# Ingredients
- peas
- spring onions
- potato
# Instructions
- shell the peas and put them into a pan.
~~~
{: .output}

To finish merging,
we add `peasoup.md` to the changes being made by the merge
and then commit:

~~~
$ git add peasoup.md
$ git status
~~~
{: .language-bash}

~~~
On branch main
All conflicts fixed but you are still merging.
  (use "git commit" to conclude merge)

Changes to be committed:

	modified:   peasoup.md

~~~
{: .output}

~~~
$ git commit -m "Merge changes from GitHub"
~~~
{: .language-bash}

~~~
[main 2abf2b1] Merge changes from GitHub
~~~
{: .output}

Now we can push our changes to GitHub:

~~~
$ git push origin main
~~~
{: .language-bash}

~~~
Enumerating objects: 10, done.
Counting objects: 100% (10/10), done.
Delta compression using up to 8 threads
Compressing objects: 100% (6/6), done.
Writing objects: 100% (6/6), 645 bytes | 645.00 KiB/s, done.
Total 6 (delta 4), reused 0 (delta 0)
remote: Resolving deltas: 100% (4/4), completed with 2 local objects.
To https://github.com/alflin/recipes.git
   dabb4c8..2abf2b1  main -> main
~~~
{: .output}

Git keeps track of what we've merged with what,
so we don't have to fix things by hand again
when the collaborator who made the first change pulls again:

~~~
$ git pull origin main
~~~
{: .language-bash}

~~~
remote: Enumerating objects: 10, done.
remote: Counting objects: 100% (10/10), done.
remote: Compressing objects: 100% (2/2), done.
remote: Total 6 (delta 4), reused 6 (delta 4), pack-reused 0
Unpacking objects: 100% (6/6), done.
From https://github.com/alflin/recipes
 * branch            main     -> FETCH_HEAD
    dabb4c8..2abf2b1  main     -> origin/main
Updating dabb4c8..2abf2b1
Fast-forward
 peasoup.md | 2 +-
 1 file changed, 1 insertion(+), 1 deletion(-)
~~~
{: .output}

We get the merged file:

~~~
$ cat peasoup.md
~~~
{: .language-bash}

~~~
# Ingredients
- peas
- spring onions
- potato
# Instructions
- shell the peas and put them into a pan.
~~~
{: .output}

We don't need to merge again because Git knows someone has already done that.

Git's ability to resolve conflicts is very useful, but conflict resolution
costs time and effort, and can introduce errors if conflicts are not resolved
correctly. If you find yourself resolving a lot of conflicts in a project,
consider these technical approaches to reducing them:

- Pull from upstream more frequently, especially before starting new work
- Use topic branches to segregate work, merging to main when complete
- Make smaller more atomic commits
- Where logically appropriate, break large files into smaller ones so that it is
  less likely that two authors will alter the same file simultaneously

Conflicts can also be minimized with project management strategies:

- Clarify who is responsible for what areas with your collaborators
- Discuss what order tasks should be carried out in with your collaborators so
  that tasks expected to change the same lines won't be worked on simultaneously
- If the conflicts are stylistic churn (e.g. tabs vs. spaces), establish a
  project convention that is governing and use code style tools (e.g.
  `htmltidy`, `perltidy`, `rubocop`, etc.) to enforce, if necessary

> ## Solving Conflicts that You Create
>
> Clone the repository created by your instructor.
> Add a new file to it,
> and modify an existing file (your instructor will tell you which one).
> When asked by your instructor,
> pull her changes from the repository to create a conflict,
> then resolve it.
{: .challenge}

> ## Conflicts on Non-textual files
>
> What does Git do
> when there is a conflict in an image or some other non-textual file
> that is stored in version control?
>
> > ## Solution
> >
> > Let's try it. Suppose Alfredo takes a picture of his pea soup and
> > calls it `peasoup.jpg`.
> >
> > If you do not have an image file of pea soup available, you can create
> > a dummy binary file like this:
> >
> > ~~~
> > $ head --bytes 1024 /dev/urandom > peasoup.jpg
> > $ ls -lh peasoup.jpg
> > ~~~
> > {: .language-bash}
> >
> > ~~~
> > -rw-r--r-- 1 alflin 57095 1.0K Mar  8 20:24 peasoup.jpg
> > ~~~
> > {: .output}
> >
> > `ls` shows us that this created a 1-kilobyte file. It is full of
> > random bytes read from the special file, `/dev/urandom`.
> >
> > Now, suppose Alfredo adds `peasoup.jpg` to his repository:
> >
> > ~~~
> > $ git add peasoup.jpg
> > $ git commit -m "Add picture of peasoup"
> > ~~~
> > {: .language-bash}
> >
> > ~~~
> > [main 8e4115c] Add picture of peasoup
> >  1 file changed, 0 insertions(+), 0 deletions(-)
> >  create mode 100644 peasoup.jpg
> > ~~~
> > {: .output}
> >
> > Suppose that Jimmy has added a similar picture in the meantime.
> > His is a picture of a pea soup with sour cream, but it is *also* called `peasoup.jpg`.
> > When Alfredo tries to push, he gets a familiar message:
> >
> > ~~~
> > $ git push origin main
> > ~~~
> > {: .language-bash}
> >
> > ~~~
> > To https://github.com/alflin/recipes.git
> >  ! [rejected]        main -> main (fetch first)
> > error: failed to push some refs to 'https://github.com/alflin/recipes.git'
> > hint: Updates were rejected because the remote contains work that you do
> > hint: not have locally. This is usually caused by another repository pushing
> > hint: to the same ref. You may want to first integrate the remote changes
> > hint: (e.g., 'git pull ...') before pushing again.
> > hint: See the 'Note about fast-forwards' in 'git push --help' for details.
> > ~~~
> > {: .output}
> >
> > We've learned that we must pull first and resolve any conflicts:
> >
> > ~~~
> > $ git pull origin main
> > ~~~
> > {: .language-bash}
> >
> > When there is a conflict on an image or other binary file, git prints
> > a message like this:
> >
> > ~~~
> > $ git pull origin main
> > remote: Counting objects: 3, done.
> > remote: Compressing objects: 100% (3/3), done.
> > remote: Total 3 (delta 0), reused 0 (delta 0)
> > Unpacking objects: 100% (3/3), done.
> > From https://github.com/alflin/recipes.git
> >  * branch            main     -> FETCH_HEAD
> >    6a67967..439dc8c  main     -> origin/main
> > warning: Cannot merge binary files: peasoup.jpg (HEAD vs. 439dc8c08869c342438f6dc4a2b615b05b93c76e)
> > Auto-merging peasoup.jpg
> > CONFLICT (add/add): Merge conflict in peasoup.jpg
> > Automatic merge failed; fix conflicts and then commit the result.
> > ~~~
> > {: .output}
> >
> > The conflict message here is mostly the same as it was for `peasoup.md`, but
> > there is one key additional line:
> >
> > ~~~
> > warning: Cannot merge binary files: peasoup.jpg (HEAD vs. 439dc8c08869c342438f6dc4a2b615b05b93c76e)
> > ~~~
> >
> > Git cannot automatically insert conflict markers into an image as it does
> > for text files. So, instead of editing the image file, we must check out
> > the version we want to keep. Then we can add and commit this version.
> >
> > On the key line above, Git has conveniently given us commit identifiers
> > for the two versions of `peasoup.jpg`. Our version is `HEAD`, and Jimmy's
> > version is `439dc8c0...`. If we want to use our version, we can use
> > `git checkout`:
> >
> > ~~~
> > $ git checkout HEAD peasoup.jpg
> > $ git add peasoup.jpg
> > $ git commit -m "Use image of plain pea soup instead of with sour cream"
> > ~~~
> > {: .language-bash}
> >
> > ~~~
> > [main 21032c3] Use image of plain pea soup instead of with sour cream
> > ~~~
> > {: .output}
> >
> > If instead we want to use Jimmy's version, we can use `git checkout` with
> > Jimmy's commit identifier, `439dc8c0`:
> >
> > ~~~
> > $ git checkout 439dc8c0 peasoup.jpg
> > $ git add peasoup.jpg
> > $ git commit -m "Use image of peasoup with sour cream instead of plain pea soup"
> > ~~~
> > {: .language-bash}
> >
> > ~~~
> > [main da21b34] Use image of peasoup with sour cream instead of plain pea soup
> > ~~~
> > {: .output}
> >
> > We can also keep *both* images. The catch is that we cannot keep them
> > under the same name. But, we can check out each version in succession
> > and *rename* it, then add the renamed versions. First, check out each
> > image and rename it:
> >
> > ~~~
> > $ git checkout HEAD peasoup.jpg
> > $ git mv peasoup.jpg peasoup-plain.jpg
> > $ git checkout 439dc8c0 peasoup.jpg
> > $ mv peasoup.jpg peasoup-sourcream.jpg
> > ~~~
> > {: .language-bash}
> >
> > Then, remove the old `peasoup.jpg` and add the two new files:
> >
> > ~~~
> > $ git rm peasoup.jpg
> > $ git add peasoup-plain.jpg
> > $ git add peasoup-sourcream.jpg
> > $ git commit -m "Use two images: plain pea soup and with sour cream"
> > ~~~
> > {: .language-bash}
> >
> > ~~~
> > [main 94ae08c] Use two images: plain pea soup and with sour cream
> >  2 files changed, 0 insertions(+), 0 deletions(-)
> >  create mode 100644 peasoup-sourcream.jpg
> >  rename peasoup.jpg => peasoup-plain.jpg (100%)
> > ~~~
> > {: .output}
> >
> > Now both images of peasoup are checked into the repository, and `peasoup.jpg`
> > no longer exists.
> {: .solution}
{: .challenge}

> ## A Typical Work Session
>
> You sit down at your computer to work on a shared project that is tracked in a
> remote Git repository. During your work session, you take the following
> actions, but not in this order:
>
> - *Make changes* by appending the number `100` to a text file `numbers.txt`
> - *Update remote* repository to match the local repository
> - *Celebrate* your success with beer(s)
> - *Update local* repository to match the remote repository
> - *Stage changes* to be committed
> - *Commit changes* to the local repository
>
> In what order should you perform these actions to minimize the chances of
> conflicts? Put the commands above in order in the *action* column of the table
> below. When you have the order right, see if you can write the corresponding
> commands in the *command* column. A few steps are populated to get you
> started.
>
> |order|action . . . . . . . . . . |command . . . . . . . . . . |
> |-----|---------------------------|----------------------------|
> |1    |                           |                            |
> |2    |                           | `echo 100 >> numbers.txt`  |
> |3    |                           |                            |
> |4    |                           |                            |
> |5    |                           |                            |
> |6    | Celebrate!                | `AFK`                      |
>
> > ## Solution
> >
> > |order|action . . . . . . |command . . . . . . . . . . . . . . . . . . . |
> > |-----|-------------------|----------------------------------------------|
> > |1    | Update local      | `git pull origin main`                     |
> > |2    | Make changes      | `echo 100 >> numbers.txt`                    |
> > |3    | Stage changes     | `git add numbers.txt`                        |
> > |4    | Commit changes    | `git commit -m "Add 100 to numbers.txt"`     |
> > |5    | Update remote     | `git push origin main`                     |
> > |6    | Celebrate!        | `AFK`                                        |
> >
> {: .solution}
{: .challenge}
