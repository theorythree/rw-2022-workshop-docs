# S4-05: Projects - Controller

Oh, hello there. When we last left off, we just finished our routes and learned a handy way to define all our resource routes in one line. You may also have been down the rabbit hole with theming and installing some extensions to VS Code. If so, welcome back.

As a matter of review, here’s an updated checklist

1. > ~~Create a feature branch for our project features~~
2. > ~~Create a Model, Controller, Database Migration, Test files, and View files~~
3. > ~~Write our initial suite of tests for projects~~
4. > ~~Create our routes for projects~~
5. > Update our controller CRUD code
6. > Update our migration file to build up a projects table in the database
7. > Discuss Model relationships and implement our first relationship between clients and projects
8. > Finish our ProjectFactory file
9. > Update our dummy views for projects
10. > Run our tests and fix any issues we have
11. > Commit our finished feature, then merge it into main branch

I love checking off todo list items, don’t you?

### Update our controller CRUD code

We are going to use the split screen feature of VS Code by opening ClientController and ProjectController, then clicking on the ClientController tab and dragging it to the upper right side of the window.

> You may have noticed that you have a couple of split options when you do this. The box that appears when you do your dragging will let you know what the final placement will be. You can do multiple tabs in vertical and horizontal splits, so play around if you want. If you mess up, just close the tab or drag it back next to the other tag to start again.

![Screen Shot 2021-12-18 at 4.15.46 PM.png](S4-05:%20Projects%20-%20Controller.assets/Screen%20Shot%202021-12-18%20at%204.15.46%20PM.png)

So in this split setup, we’ll be working on the left and referring to the code on the right to get a sense of what we want to do.

Again, I’ll take these one-by-one so you can follow along.

```php
public function index()
{
  $projects = Project::all();
  return view('projects.index',compact('projects'));
}
```

The index() method is just doing what we did with clients. It pulls all the records and stores them in a variable called $projects, then returns the projects/index.blade.php view, along with the projects data.

```php
public function store(Request $request)
{
  $project = new Project();
  $project->client_id = $request->input('client_id');
  $project->name = $request->input('name');
  $project->save();
}
```

This method is very similar to the store() method of our ClientController. We’re creating a new project from the Project model, and taking the request values and storing them in the project object before finally saving them to the database.

> Note: I strongly urge you to name your variables in the way I am, avoid using generics like $item, and if you copy/paste never leave the variables named from another controller.

> Also, you may be asking how I know what to provide in the input() for this controller. Where did I get those names? Well, those are the names as they will appear in the database column. Those names should also match the name provided in the name of the form element as well. For example if we had a form the text input would look like this for name:

```html
<input type="text" name="name" value="" placeholder="Project Name" required>
```

For the client_id, we wouldn’t expect our user to know the client ID off the top of their head, instead it’s more likely we’d set that for the user and include it in a hidden field, so it would look something like this:

```html
<input type="hidden" name="client_id" value="{{ $client->id }}">
```

```php
public function show(Project $project)
{
  return view('projects.show',compact('project'));
}
```

Simple one liner here. All we’re doing is using `Route Model Binding` in the `show()` method arguments, returning a view (`projects/show.blade.php`) expressed as `projects.show`, and then taking the `$project variable`, compacting it, and passing it to that view.

> Note: when I setup my controller, it already provided the `Project $project` in the method parameters using `Route Model Binding` that we discussed previously. But double check your controller it might be different and if it is, make yours match mine.

```php
public function update(Request $request, Project $project)
{
  $project->client_id = $request->input('client_id');
  $project->name = $request->input('name');
  $project->save();
}
```

The update() method is nearly identical to the store() method. It also takes a Request, but it uses the same Route Model Binding feature to allow us to access the project using $project without having to look it up.

```php
public function destroy(Project $project)
{
  $project->delete();
}
```

The destroy() method is a simple one liner too, it uses Route Model Binding as well, and deletes the project that was passed to it.

Thats it for our controller update, let’s commit that code now.

```Bash
$ git status
$ git add app/Http/Controllers/ProjectController.php
$ git commit -m "Adds initial ProjectController code for index(), store(), show(), update(), and delete() methods"
```

