# S4-11: Projects - Merge Back To Main

One thing left, look at that!

1. > ~~Create a feature branch for our project features~~
2. > ~~Create a Model, Controller, Database Migration, Test files, and View files~~
3. > ~~Write our initial suite of tests for projects~~
4. > ~~Create our routes for projects~~
5. > ~~Update our controller CRUD code~~
6. > ~~Update our migration file to build up a projects table in the database~~
7. > ~~Discuss Model relationships and implement our first relationship between clients and projects~~
8. > ~~Finish our ProjectFactory file~~
9. > ~~Update our dummy views for projects~~
10. > ~~Run our tests and fix any issues we have~~
11. > Commit our finished feature, then merge it into main branch

Now that we’ve finished and tested all of our changes, we need to incorporate our work back into the main branch.

We do this by merging branches.

You can think of this process of syncing up all the changes that have happened on the main branch and our feature branch since it was created.

To do this, we follow this procedure:

1. On your feature branch do a status check to make sure all your changes are committed

```Bash
$ git status
```

![Screen Shot 2021-12-19 at 5.36.44 PM.png](S4-11:%20Projects%20-%20Merge%20Back%20To%20Main.assets/Screen%20Shot%202021-12-19%20at%205.36.44%20PM.png)

2. Then switch to the main branch and do a pull and status check there too

```Bash
$ git checkout main
$ git status
$ git pull
```

![Screen Shot 2021-12-19 at 5.38.48 PM.png](S4-11:%20Projects%20-%20Merge%20Back%20To%20Main.assets/Screen%20Shot%202021-12-19%20at%205.38.48%20PM.png)

3. Merge your feature branch into the main branch

> Remember to replace “dan” with whatever you called your feature branch

```Bash
$ git merge dan/projects
```

You’ll see all of the changed files merged into main listed in the output.

4. Do another status check

```Bash
$ git status
```

![Screen Shot 2021-12-19 at 5.46.45 PM.png](S4-11:%20Projects%20-%20Merge%20Back%20To%20Main.assets/Screen%20Shot%202021-12-19%20at%205.46.45%20PM.png)

5. Push your changes up to GitHub

```Bash
$ git push
```

![Screen Shot 2021-12-19 at 5.47.48 PM.png](S4-11:%20Projects%20-%20Merge%20Back%20To%20Main.assets/Screen%20Shot%202021-12-19%20at%205.47.48%20PM.png)

Congratulations, your changes are now a part of the main branch and your code has been pushed up to the repository.

