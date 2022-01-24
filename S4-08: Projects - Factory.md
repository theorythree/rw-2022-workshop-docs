# S4-08: Projects - Factory

We are in the home stretch on this one, and boy oh boy I can’t wait to show you our updated todo list:

1. > ~~Create a feature branch for our project features~~
2. > ~~Create a Model, Controller, Database Migration, Test files, and View files~~
3. > ~~Write our initial suite of tests for projects~~
4. > ~~Create our routes for projects~~
5. > ~~Update our controller CRUD code~~
6. > ~~Update our migration file to build up a projects table in the database~~
7. > ~~Discuss Model relationships and implement our first relationship between clients and projects~~
8. > Finish our ProjectFactory file
9. > Update our dummy views for projects
10. > Run our tests and fix any issues we have
11. > Commit our finished feature, then merge it into main branch

We’re going to wrap up our factory file for our projects. We’re already using the factory in our tests, and so we should probably take a peek at our project feature test ProjectTest.

1. Open tests/Feature/ProjectTest.php

![Screen Shot 2021-12-19 at 12.59.31 PM.png](S4-08:%20Projects%20-%20Factory.assets/Screen%20Shot%202021-12-19%20at%2012.59.31%20PM.png)

I’m interested in building our factory to only provide the required fields right now, later on in the workshop, we’ll come back and add the other fields. So the line of code I’m going to refer to is Line 16 in my screenshot above.

```php
$response = $this->post('/projects', ['client_id' => 1,'name' => 'My Project']);
```

From that line, I can see that we just need a client id and a name to build up a project, so let’s do that now.

> I could have also looked at our project database table, or project plan to see which fields were required, both of those are equally useful.

2. Open database/factories/ProjectFactory.php
3. Also open database/factories/ClientFactory.php for reference

![Screen Shot 2021-12-19 at 1.05.12 PM.png](S4-08:%20Projects%20-%20Factory.assets/Screen%20Shot%202021-12-19%20at%201.05.12%20PM.png)

You can see, we only need to return an array with our key value pairs. In the case of projects, we just need a client id and a name of our project.

Here’s what I came up with.

4. Add this to your definition() method

```php
return [
  "client_id" => 1,
  "name" => Str::random(10)." Project",
];
```

For `client_id`, I’m just putting a 1 for the id, this assumes we have at least one client in the clients database table, and since our tests aren’t testing the relationship at this point, and since we can always override this id in our tests, it’ll do just fine for now.

For `name`, we were using Faker to fake a name for our `company`, unfortunately, Faker doesn’t have an option for a project name. I could manually provide a value, but I know we will eventually want to create a bunch of these projects for testing and I don't want to have them be all the same name, nor do I want to manually provide a list of names.

So, instead, I’m using the string randomizer to come up with a string with ten random characters, then I'm appending it with the word `Project`, so all our project names will be different, but  have the word project in them so they kind of make sense.

5. Don’t forget to add the use Str line at the top of your code for that randomizer to work.

```php
use Illuminate\Support\Str;
```

When you’re all done your completed Factor will look like this:

![Screen Shot 2021-12-19 at 1.20.47 PM.png](S4-08:%20Projects%20-%20Factory.assets/Screen%20Shot%202021-12-19%20at%201.20.47%20PM.png)

Well, since this is a short one, let’s go ahead and add the other fields, why not?

We just have to add description and budget. Faker can help us out with both.

6. Add the following two lines of code after the "name" in the return array.

```php
"description" => $this->faker->text(200),
"budget" => $this->faker->randomFloat(2,100,10000),
```

You can see, I’m just using Faker to generate 200 characters of random text and I’m also generating a random float using randomFloat(), which takes three numbers, the number of decimal places (we need two, i.e. 0.00), the minimum number 100 (that means we will have budgets of at least $100), and finally the max number (our budgets will not exceed $10,000).

---

> You can view a full list of faker functions on their GitHub page: [https://github.com/fzaninotto/Faker#fakerproviderlorem](https://github.com/fzaninotto/Faker#fakerproviderlorem) if you're interested in learning more about Faker.

---

### Run run run our tests

Okay, it’s been an almost unforgivable amount of time since we’ve run our last test, and now is a good time to do so. We know we’re going to get some fails, but we also should get some passes too. Here’s what I got when I ran my tests on the project at this point.

```Bash
sail artisan test
```

![Screen Shot 2021-12-19 at 1.45.07 PM.png](S4-08:%20Projects%20-%20Factory.assets/Screen%20Shot%202021-12-19%20at%201.45.07%20PM.png)

This is what I was expecting. You can see I had 2 failed and 8 passed tests. The two that failed were user can view a project and user view project index page, both of which relate to the views, which we have not finished yet.

### What if I have another test fail too?

Now, if you see something different, you have two choices, run your tests and try to do some debugging on your own, or checkout my troubleshooting guide on the topic to help you find your way  ([[Troubleshooting: Unexpected Test Failures]]).

1. Assuming all is well, let’s check in our code:

```Bash
$ git status
```

![Screen Shot 2021-12-19 at 2.00.13 PM.png](S4-08:%20Projects%20-%20Factory.assets/Screen%20Shot%202021-12-19%20at%202.00.13%20PM.png)

2. And add commit just the ProjectFactor file we worked on.

```Bash
$ git add database/factories/ProjectFactory.php
$ git status
```

![Screen Shot 2021-12-19 at 2.02.43 PM.png](S4-08:%20Projects%20-%20Factory.assets/Screen%20Shot%202021-12-19%20at%202.02.43%20PM.png)

3. Finally, do your commit

```Bash
$ git commit -m "Adds factory for projects"
```

