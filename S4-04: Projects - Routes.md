# S4-04: Projects - Routes

At this point, let’s do a quick review of the work we’ve done for our project features in our application.

1. > ~~Create a feature branch for our project features~~
2. > ~~Create a Model, Controller, Database Migration, Test files, and View files~~
3. > ~~Write our initial suite of tests for projects~~
4. > Create our routes for projects
5. > Update our controller CRUD code
6. > Update our migration file to build up a projects table in the database
7. > Discuss Model relationships and implement our first relationship between clients and projects
8. > Finish our ProjectFactory file
9. > Update our dummy views for projects
10. > Run our tests and fix any issues we have
11. > Commit our finished feature, then merge it into main branch

### Create our routes for projects

Up next we get to create some routes, so easy, but just to make things interesting, I’ll show you some handy tips about routes along the way.

Open your web.php routes file. You may remember it’s located in the `/routes` directory.

![Screen Shot 2021-12-18 at 11.45.09 AM.png](S4-04:%20Projects%20-%20Routes.assets/Screen%20Shot%202021-12-18%20at%2011.45.09%20AM.png)

You can see we have all our client routes listed.

These routes are included in our route list and we can see a full list of all our project route rules by running the following command in the terminal.

```Bash
$ sail artisan route:list
```

![Screen Shot 2021-12-18 at 11.54.58 AM.png](S4-04:%20Projects%20-%20Routes.assets/Screen%20Shot%202021-12-18%20at%2011.54.58%20AM.png)

> You can see I just minimized the file explorer to make the table appear a bit more presentable. If you look closely, you can see the client route rules we created in our web.php routes file along with some others as well.

We may explore this entire table in more detail later, but for now, I want you to look at three columns.

1. Method - this is the method of our route test, ignore the HEAD part, and see that all the methods we declared using post(), get(), and put() are listed with the proper method in this column for each of our route rules.
2. URI - this is the url of the route rule that gets tested. Pretty handy.
3. Action - this is action the rule will perform if that route particular route rule is matched, in our case, we’re passing that request on to a controller ClientController and telling that controller to execute a particular method. Quite helpful.

As we start to develop more and more of our project features we’re going to be adding a bunch of these route rules, so this table is going to get much larger in size.

The route table is handy for debugging our route rules, we can verify that the correct method, URI, controller, and methods are all set.

Let’s go back to our web.php routes file now

![Screen Shot 2021-12-18 at 12.06.44 PM.png](S4-04:%20Projects%20-%20Routes.assets/Screen%20Shot%202021-12-18%20at%2012.06.44%20PM.png)

That’s all pretty well and good, but it’s kind of a hassle to type out all these routes, after all, they’re all standard routes for clients, and we’re going to be adding a bunch of these routes.

> We have five standard route rules for clients, if we do some quick math and we consider five routes for each of our models and we have 12 models, that’s 60 route rules we have to create. Eek. That’s a lot of route rules to type out and potentially get wrong.

Luckily Laravel offers up a handy shortcut that allows us to setup ALL our standard routes for our controller.

> Naming conventions are important here, so this is why it was important for us to follow the naming convention when we named our controller and the methods used inside that controller.

You might remember way back when we created our Client controller and our Project controller, I had us add the —resource tag to the artisan command to setup those methods.

Laravel offers a handy method that lets us declare all our standard resource routes for a particular resource controller by using the keyword `resource` instead of the request method (`post()`, `put()`, `delete()`, etc).

Let’s use that resource route shortcut for our project route rules. Here’s what it looks like:

```php
Route::resource('/projects','App\Http\Controllers\ProjectController');
```

You can see the resource() method is used. It takes the same URI argument, but all we have to do is specify the controller (ProjectController) and the methods all get assigned as expected.

Let’s verify this claim by running our route list command again

```Bash
$ sail artisan route:list
```

![Screen Shot 2021-12-18 at 12.16.25 PM.png](S4-04:%20Projects%20-%20Routes.assets/Screen%20Shot%202021-12-18%20at%2012.16.25%20PM.png)

You’ll notice a full set of resource routes for our `projects`. In fact we even have some routes that we didn’t define for clients which we will need later, so our math was wrong it’s `7 routes X 12 models = 84` total route rules.

While we’re here, let’s take a peek at that name column, because there seems to be some vales in there too.

Naming a route is a very very useful feature of Laravel. It is a much safer way for us to reference a route in our code. You can think of it as an alias that allows us to reference a particular URL without actually hard-coding the url manually. This means, for example, if we decided to switch all our projects routes from /projects to just /project we can do that in our route rule and all the places in our code that use the alias instead of the hard-coding method, would still work just like they did before.

To use this handy dandy alias we just need to use the route() method and pass the name we want to use. Here’s an example of the projects create route:

```php
route('projects.create')
```

Wherever we use that in our code it will result in the following path:

```php
projects/create
```

Let’s head back to our web.php route file for one last thought and task.

![Screen Shot 2021-12-18 at 12.27.29 PM.png](S4-04:%20Projects%20-%20Routes.assets/Screen%20Shot%202021-12-18%20at%2012.27.29%20PM.png)

Now that you learned something new, you may be tempted to apply this better approach to the client routes. Seems like a pretty good idea right? Wrong.

Remember, we’re working on a project feature branch, and our work must be constrained to just the work we need to do to complete our work. It’s true, the routes for clients need to be fixed, but that’s not our focus now. Doing so could mean we cause an issue for another developer who happened to be relying on the client routes.

So what do we do in this case, just forget about it an hope for the best? No.

We’re going to add a comment to our route file to remind us that we probably should refactor our client routes. The comment we’re going to add is also going to use the keyword TODO, which is standard practice. In fact you can install a plugin in Visual Studio Code (and other IDEs) that looks for these TODO comments and lists them for you.

Here’s the comment we want to add above the client route rules:

```php
// TODO: replace the client routes with a single resource route
```

![Screen Shot 2021-12-18 at 12.34.38 PM.png](S4-04:%20Projects%20-%20Routes.assets/Screen%20Shot%202021-12-18%20at%2012.34.38%20PM.png)

You’ll notice since I have the todo plugin installed, the word TODO is highlighted. The TODO plugin I use is shown with the other icons in the left bar and looks like a tree with a checkmark in it. I can click on that tree and see all my TODOs throughout the entire project.

![Screen Shot 2021-12-18 at 12.35.58 PM.png](S4-04:%20Projects%20-%20Routes.assets/Screen%20Shot%202021-12-18%20at%2012.35.58%20PM.png)

On the left, I have a complete list of ALL TODOs (only one in this case). When I’m done with a todo, I just delete the comment to remove it from the list.

> Before you run off and start installing this or other extensions to your VS Code editor, let’s wrap up our work.

We should commit our route changes.

```Bash
$ git status
```

![Screen Shot 2021-12-18 at 12.59.24 PM.png](S4-04:%20Projects%20-%20Routes.assets/Screen%20Shot%202021-12-18%20at%2012.59.24%20PM.png)

```Bash
$ git add routes/web.php
$ git status
```

![Screen Shot 2021-12-18 at 11.33.36 AM.png](S4-04:%20Projects%20-%20Routes.assets/Screen%20Shot%202021-12-18%20at%2011.33.36%20AM.png)

```Bash
$ git commit -m "adds project resource routes to web routes"
```

> Note: we’re still not pushing this one up to the repo quite yet. So unless you’re wrapping up your work session, keep this commit local for now.

