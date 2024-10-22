progress by looking at the `HEAD`s.  Let's create a new recipe, `mushroomsoup.md` in the same way
$ nano mushroomsoup.md
$ cat mushroomsoup.md
$ git add mushroomsoup.md
$ nano mushroomsoup.md
$ cat mushroomsoup.md
- garlic
- chestnut mushrooms
$ git add mushroomsoup.md
$ git commit -m "Add basic ingredients for mushroom soup"
We decide to change one of the ingredients:
$ nano mushroomsoup.md
$ cat mushroomsoup.md
- garlic
- button mushrooms
$ git add mushroomsoup.md
$ git commit -m "Change mushroom type"
$ nano mushroomsoup.md
$ cat mushroomsoup.md
- garlic
- button mushrooms
Now, let's see what the differences are between our current version and the HEAD of the repository.
$ git diff HEAD mushroomsoup.md
diff --git a/mushroomsoup.md b/mushroomsoup.md
--- a/mushroomsoup.md
+++ b/mushroomsoup.md
@@ -3,3 +3,4 @@
 - garlic
 - button mushrooms
$ git diff HEAD~1 mushroomsoup.md
$ git diff HEAD~2 mushroomsoup.md
diff --git a/mushroomsoup.md b/mushroomsoup.md
--- a/mushroomsoup.md
+++ b/mushroomsoup.md
@@ -1,2 +1,6 @@
+- garlic
+- button mushrooms
$ git show HEAD~2 mushroomsoup.md
diff --git a/mushroomsoup.md b/mushroomsoup.md
+++ b/mushroomsoup.md
$ git diff f22b25e3233b4645dabd0d81e651fe074bd8e73b mushroomsoup.md
diff --git a/mushroomsoup.md b/mushroomsoup.md
--- a/mushroomsoup.md
+++ b/mushroomsoup.md
@@ -1,2 +1,6 @@
+- garlic
+- button mushrooms
$ git diff f22b25e mushroomsoup.md
diff --git a/mushroomsoup.md b/mushroomsoup.md
--- a/mushroomsoup.md
+++ b/mushroomsoup.md
@@ -1,2 +1,6 @@
+- garlic
+- button mushrooms
`mushroomsoup.md` (the "ill-considered change").
    modified:   mushroomsoup.md
$ git checkout HEAD mushroomsoup.md
$ cat mushroomsoup.md
- garlic
- chestnut mushrooms
$ git checkout f22b25e mushroomsoup.md
$ cat mushroomsoup.md
    modified:   mushroomsoup.md
$ git checkout HEAD mushroomsoup.md
> $ git checkout f22b25e mushroomsoup.md
> to revert `mushroomsoup.md` to its state after the commit `f22b25e`. But be careful!
> If you forget `mushroomsoup.md` in the previous command.
> Consider this command: `git diff HEAD~9 mushroomsoup.md`. What do you predict this command
> Try another command, `git diff [ID] mushroomsoup.md`, where [ID] is replaced with
> Make a change to `mushroomsoup.md`, add that change, and use `git checkout` to see if
> You would like to find a commit that modifies some specific text in `mushroomsoup.md`.
> When you type `git log`, a very long list appears.
> e.g., `git diff mushroomsoup.md`. We can apply a similar idea here.
> $ git log mushroomsoup.md
> Imagine that some commit messages are very ambiguous, e.g., `update files`.
> $ git log --patch mushroomsoup.md
> $ git log --patch HEAD~3 *.md