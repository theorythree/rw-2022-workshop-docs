# S4-09: Projects - Views

It’s looking like all grunt work from this point forward. Here’s our updated todo list for this series thus far.

1. > ~~Create a feature branch for our project features~~
2. > ~~Create a Model, Controller, Database Migration, Test files, and View files~~
3. > ~~Write our initial suite of tests for projects~~
4. > ~~Create our routes for projects~~
5. > ~~Update our controller CRUD code~~
6. > ~~Update our migration file to build up a projects table in the database~~
7. > ~~Discuss Model relationships and implement our first relationship between clients and projects~~
8. > ~~Finish our ProjectFactory file~~
9. > Update our dummy views for projects
10. > Run our tests and fix any issues we have
11. > Commit our finished feature, then merge it into main branch

As I’ve said many many times before, I like me some ugly views when we’re building the back-end of an application first. I want to put the very bare minimum into my view code because I know we’re going to be creating real view and doing anything more is just a waste of our time at this point.

We get to make four ugly views today, and they all live in the resources/views/projects/ directory.

![Screen Shot 2021-12-19 at 2.20.21 PM.png](S4-09:%20Projects%20-%20Views.assets/Screen%20Shot%202021-12-19%20at%202.20.21%20PM.png)

We’re also going to be adding some code to our ProjectController to handle the new, and still ugly, views as well. So strap in… and here we go!

Let’s start at the top with create.blade.php

1. Open create.blade.php

![Screen Shot 2021-12-19 at 2.23.20 PM.png](S4-09:%20Projects%20-%20Views.assets/Screen%20Shot%202021-12-19%20at%202.23.20%20PM.png)

You might remember this view is going to be what the user sees when they are creating a new project. So since all our projects belong to a client, we can expect that the ProjectController is going to give us a client object so we can insert that into the project form so it will end up in the `client_id` column of the projects table.

We’re not going to worry about that now, and I’m also going to tell you another thing we’re not going to do as well, ::we are NOT going to fake a client id:: as we did in our testing. Faking data in our code is a no no, in fact it’s a triple no no (which ends up being like six no’s). Doing so will surly end in a major bug in our code, and one that might go undetected for some time until we write a test for it.

Instead, we’re going to assume the controller is doing its job and passing us the client record, and if it’s doing that, we can expect it to use the same naming convention we are in our template, so the name of the variable will be `$client`. Single tense, nothing fancy, just `$client`.

Okay off to code.

I’m using an extension to VS Code that autocompletes a basic HTML template for me, I just have to type `html:5` + `tab` and then I get the following:

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <meta http-equiv="X-UA-Compatible" content="ie=edge">
  <title>Document</title>
</head>
<body>
  
</body>
</html>
```

If you didn’t install this extension when we discussed it, you can do that now, or you can simply copy and paste the code above.

I’m going to update the title, then add a form that includes all the fields we need, here’s my code for the form, again, we’re going ugly here, so I’m not going to assume you know basic HTML. I'll point out the notable parts as we go.

Here’s the full view code with the updates and the form included.

```html
<!DOCTYPE html>
<html lang="en">

<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <meta http-equiv="X-UA-Compatible" content="ie=edge">
  <title>Create a Project for {{ $client->name }}</title>
</head>

<body>
  <form action="{{ route('projects.store') }}" method="POST">
    <input type="hidden" name="client_id" value="{{ $client->id }}" />
    <input type="text" name="name" id="name" placeholder="Name of the Project" required><br />
    <input type="text" name="budget" id="budget" placeholder="Budget in US Dollars"><br />
    <textarea name="description" id="description" cols="30" rows="10"
      placeholder="Project Description"></textarea><br />
    <input type="submit" value="Submit">
  </form>
</body>

</html>
```

![Screen Shot 2021-12-19 at 3.11.10 PM.png](S4-09:%20Projects%20-%20Views.assets/Screen%20Shot%202021-12-19%20at%203.11.10%20PM.png)

I already mentioned we’re expecting a client to be passed to this view from our ProjectController, and you can see that in action on `lines 8`, and `13` in the screenshot. I’m just using the name of the client in the title, so we can make sure we are entering a project for the correct client.

And I’m going to need that client id when we add the project to our database.

You’ll also notice, I’m using the route named products.store, because that’s the method in the controller that’s going to be doing the storing of the project in the database.

Let’s move on to the edit.blade.php

1. Open edit.blade.php

![Screen Shot 2021-12-19 at 2.52.02 PM.png](S4-09:%20Projects%20-%20Views.assets/Screen%20Shot%202021-12-19%20at%202.52.02%20PM.png)

2. Copy and paste the contents of create.blade.php into edit.blade.php

Create views and edit views are nearly identical, with one really notable example. We are expecting a project to be passed to this template, it’s the project that is being edited, so we should expect to be using a variable called `$project` (again, single tense and named after the Model).

Because we’re getting a project, I going to put our model relationships into use here for the client information. No need to expect the ProjectController to pass two variables, when one will do just fine.

Here’s my refactored code:

```html
<!DOCTYPE html>
<html lang="en">

<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <meta http-equiv="X-UA-Compatible" content="ie=edge">
  <title>Update a Project for {{ $project->client->name }}</title>
</head>

<body>
  <form action="{{ route('projects.update', $project->id) }}">
    @method('put')
    <input type="text" name="name" id="name" value="{{ $project->name }}" placeholder="Name of the Project" required>
    <input type="text" name="budget" id="budget" value="{{ $project->budget }}" placeholder="Budget in US Dollars">
    <textarea name="description" id="description" cols="30" rows="10" placeholder="Project Description">
      {{ $project->description }}
    </textarea>
    <input type="submit" value="Submit">
  </form>
</body>

</html>
```

![Screen Shot 2021-12-19 at 3.14.09 PM.png](S4-09:%20Projects%20-%20Views.assets/Screen%20Shot%202021-12-19%20at%203.14.09%20PM.png)

A couple of noteworthy things in this file, first we’re now using the project relationship to access the client name in the title on line 8 of my screenshot.

For the form action, we’re using the projects.route and passing the `project id` as well. This is because that route expects the `id` of the project record it’s supposed to edit. You might remember our `test_user_can_update_a_project()` test method we expressed this with a put() method with a url of `/projects/'.$project->id`.

`Line 13` is how we tell Laravel this is supposed to be a PUT request, and not a typical POST request, which the form will attempt by default (even if we put PUT as the value in the method attribute of the form).

The rest of the form is simply echoing out the values for the project into the value attribute for their related form fields.

Next up is the index.blade.php file

3. Open the index.blade.php file

![Screen Shot 2021-12-19 at 3.21.54 PM.png](S4-09:%20Projects%20-%20Views.assets/Screen%20Shot%202021-12-19%20at%203.21.54%20PM.png)

Remember, this template is going to show a list of projects, and each list item will be clickable to take the user to the show page for more details. We made one of these for the clients.

Use the stubbed out HTML template code again, you can copy and paste this code if you cannot generate the snippet:

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <meta http-equiv="X-UA-Compatible" content="ie=edge">
  <title>Document</title>
</head>
<body>
  
</body>
</html>
```

And here’s my updated code for that one:

```html
<!DOCTYPE html>
<html lang="en">

<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <meta http-equiv="X-UA-Compatible" content="ie=edge">
  <title>Project List</title>
</head>

<body>

  @foreach($projects as $project)
  <p>
    Name: {{ $project->name }} | Client: {{ $project->client->name }} | Budget: ${{ $project->budget }}
    <a href="{{ route('projects.show',$project->id) }}">View Project</a>
  </p>
  @endforeach

</body>

</html>
```

![Screen Shot 2021-12-19 at 3.28.16 PM.png](S4-09:%20Projects%20-%20Views.assets/Screen%20Shot%202021-12-19%20at%203.28.16%20PM.png)

There’s nothing fancy going on with this one, just looping through the projects and I’m using the named route on `line 16`.

For the show view, let’s do this all in one go, I’ll use the same HTML template and put in the code for our reference.

```html
<!DOCTYPE html>
<html lang="en">

<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <meta http-equiv="X-UA-Compatible" content="ie=edge">
  <title>{{ $project->name }}</title>
</head>

<body>
  <a href="{{ route('projects.index') }}">Back to Project List</a>
  <h1>{{ $project->name }}</h1>
  <p>Client: {{ $project->client->name }} | Budget: ${{ $project->budget }}</p>

  <h2>Project Description</h2>
  <p>{{ $project->description }}</p>

</body>

</html>
```

![Screen Shot 2021-12-19 at 3.41.12 PM.png](S4-09:%20Projects%20-%20Views.assets/Screen%20Shot%202021-12-19%20at%203.41.12%20PM.png)

This all looks good to me, let’s commit it and go on to the next one!

```Bash
$ git status
```

![Screen Shot 2021-12-19 at 3.42.22 PM.png](S4-09:%20Projects%20-%20Views.assets/Screen%20Shot%202021-12-19%20at%203.42.22%20PM.png)

Just one change, the contents of that directory, so we can add them all and commit.

```Bash
$ git add .
$ git commit -m "Adds initial views for projects"
```

Ugly work my friend, we’ll see you in the next one!

