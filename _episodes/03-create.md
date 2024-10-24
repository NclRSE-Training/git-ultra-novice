---
title: Creating a Repository
teaching: 10
exercises: 10
questions:
- "Where does Git store information?"
objectives:
- "Create a local Git repository."
- "Describe the purpose of the `.git` directory."
keypoints:
- "`git init` initializes a repository."
- "Git stores all of its repository data in the `.git` directory."
---

Once Git is configured,
we can start using it.

We will help Alfredo with his new project, create a repository with all his recipes.

First, let's create a directory in `Desktop` folder for our work and then move into that directory:

~~~
$ cd ~/Desktop
$ mkdir recipes
$ cd recipes
~~~
{: .language-bash}

Then we tell Git to make `recipes` a [repository]({{ page.root }}{% link reference.md %}#repository)—a place where Git can store versions of our files:

~~~
$ git init
~~~
{: .language-bash}

It is important to note that `git init` will create a repository that
includes subdirectories and their files---there is no need to create
separate repositories nested within the `recipes` repository, whether
subdirectories are present from the beginning or added later. Also, note
that the creation of the `recipes` directory and its initialization as a
repository are completely separate processes.

If we use `ls` to show the directory's contents,
it appears that nothing has changed:

~~~
$ ls
~~~
{: .language-bash}

But if we add the `-a` flag to show everything,
we can see that Git has created a hidden directory within `recipes` called `.git`:

~~~
$ ls -a
~~~
{: .language-bash}

~~~
.	..	.git
~~~
{: .output}

Git uses this special subdirectory to store all the information about the project, 
including all files and sub-directories located within the project's directory.
If we ever delete the `.git` subdirectory,
we will lose the project's history.

We can check that everything is set up correctly
by asking Git to tell us the status of our project:

~~~
$ git status
~~~
{: .language-bash}
~~~
On branch main

Initial commit

nothing to commit (create/copy files and use "git add" to track)
~~~
{: .output}

If you are using a different version of `git`, the exact
wording of the output might be slightly different.

> ## Places to Create Git Repositories
>
> Along with tracking information about recipes (the project we have already created),
> Alfredo would also like to track information about cocktails.
> Despite Jimmy's concerns, Alfredo creates a `cocktails` project inside his `recipes`
> project with the following sequence of commands:
>
> ~~~
> $ cd ~/Desktop    # return to Desktop directory
> $ cd recipes      # go into recipes directory, which is already a Git repository
> $ ls -a           # ensure the .git subdirectory is still present in the recipes directory
> $ mkdir cocktails # make a sub-directory recipes/cocktails
> $ cd cocktails    # go into cocktails subdirectory
> $ git init        # make the cocktails subdirectory a Git repository
> $ ls -a           # ensure the .git subdirectory is present indicating we have created a new Git repository
> ~~~
> {: .language-bash}
>
> Is the `git init` command, run inside the `cocktails` subdirectory, required for
> tracking files stored in the `cocktails` subdirectory?
>
> > ## Solution
> >
> > No. Alfredo does not need to make the `cocktails` subdirectory a Git repository
> > because the `recipes` repository will track all files, sub-directories, and
> > subdirectory files under the `recipes` directory.  Thus, in order to track
> > all information about cocktails, Alfredo only needed to add the `cocktails` subdirectory
> > to the `recipes` directory.
> >
> > Additionally, Git repositories can interfere with each other if they are "nested":
> > the outer repository will try to version-control
> > the inner repository. Therefore, it's best to create each new Git
> > repository in a separate directory. To be sure that there is no conflicting
> > repository in the directory, check the output of `git status`. If it looks
> > like the following, you are good to go to create a new repository as shown
> > above:
> >
> > ~~~
> > $ git status
> > ~~~
> > {: .language-bash}
> > ~~~
> > fatal: Not a git repository (or any of the parent directories): .git
> > ~~~
> > {: .output}
> {: .solution}
{: .challenge}
> ## Correcting `git init` Mistakes
> Jimmy explains to Alfredo how a nested repository is redundant and may cause confusion
> down the road. Alfredo would like to remove the nested repository. How can Alfredo undo
> his last `git init` in the `cocktails` subdirectory?
>
> > ## Solution -- USE WITH CAUTION!
> >
> > ### Background
> > Removing files from a git repository needs to be done with caution. To remove files from the working tree and not from your working directory, use
> > ~~~
> > $ rm filename
> > ~~~
> > {: .language-bash}
> > 
> > The file being removed has to be in sync with the branch head with no updates. If there are updates, the file can be removed by force by using the `-f` option. Similarly a directory can be removed from git using `rm -r dirname` or `rm -rf dirname`.
> >
> > ### Solution
> > Git keeps all of its files in the `.git` directory.
> > To recover from this little mistake, Alfredo can just remove the `.git`
> > folder in the cocktails subdirectory by running the following command from inside the `recipes` directory:
> >
> > ~~~
> > $ rm -rf cocktails/.git
> > ~~~
> > {: .language-bash}
> >
> > But be careful! Running this command in the wrong directory, will remove
> > the entire Git history of a project you might want to keep. Therefore, always check your current directory using the
> > command `pwd`.
> {: .solution}
{: .challenge}
