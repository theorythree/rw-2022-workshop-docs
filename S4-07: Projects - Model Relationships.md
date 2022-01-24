# S4-07: Projects - Model Relationships

Okay we are making some good progress, so it’s time to do another review of our todo list:

1. > ~~Create a feature branch for our project features~~
2. > ~~Create a Model, Controller, Database Migration, Test files, and View files~~
3. > ~~Write our initial suite of tests for projects~~
4. > ~~Create our routes for projects~~
5. > ~~Update our controller CRUD code~~
6. > ~~Update our migration file to build up a projects table in the database~~
7. > Discuss Model relationships and implement our first relationship between clients and projects
8. > Finish our ProjectFactory file
9. > Update our dummy views for projects
10. > Run our tests and fix any issues we have
11. > Commit our finished feature, then merge it into main branch

It’s time to talk about our relationships. Specifically how our Models relate to one another. We discussed the different kinds of relationships and we even mapped them out in our project plan way back in a previous series.

The relationship we’re interested in right now is the one between our clients and our projects, or to put it in more technical terms, our Client model and our Project model.

Here’s what we came up with, I’ve highlighted the relationships we care about.

---

> Client

   - > can have multiple users working for them
   - > can have multiple projects
   - > can appear on multiple time entries
   - > can have multiple estimates
   - > can have multiple invoices

> Project

   - > can have multiple users working on a project
   - > belongs to a client

> you can checkout the full list of our model relationships in our project plan:  [[S3-01: Project Planning (Models)]]

---

But how exactly do we set that up in Laravel? Well, as it turns out, it’s pretty simple. It all happens in the model class files, so let’s walk through that now.

### Client to Project Relationship

1. Open the Client model (app/Models/Client.php) - right now it’s nearly empty

![Screen Shot 2021-12-19 at 11.28.40 AM.png](S4-07:%20Projects%20-%20Model%20Relationships.assets/Screen%20Shot%202021-12-19%20at%2011.28.40%20AM.png)

   > Our single Client has many projects, so that’s what we’re going to describe for this side of the relationship.

2. Add the following relationship method to the Client model:

```php
public function projects() 
{
  return $this->hasMany(Project::class);
}
```

   > The name of the method should match the model (lower camel case), and it will be plural if we’re referring to a multiple occurrences of that model. So since, a `client` has multiple `projects`, we name the method `projects()` instead of just `project()`.

   To put this relationship into more descriptive terms, we will have a bunch of clients, and each of those clients will have one or more projects.

---

   It might look something like this in our database:

   1. > (Client) ABC Company
      1. > (Project) Corporate Website
      2. > (Project) Mobile App
   2. > (Client) XYZ Company
      1. > (Project) Web Design Project

---

   ### Example of Client to Projects in use

   Here’s a made up example of how that might be used in our code:

```php
$client = Client::where('id',$id)->first();

echo $client->name." Projects:<br />";

foreach ($client->projects as $project) {
  echo "Project Name: ".$project->name."<br />";
  echo "Budget: $".$project->budget."<br />";
}
```

   > For this example, I’m fetching a single client, then echoing out the name of that particular client. Then, I’m accessing all of the projects that belong to this particular client by accessing the relationship we just setup ($client->projects)

   > Now you can see why we named that method plural projects() not project(), because it more accurately describes what it’s doing, it’s giving us multiple projects, not just one.

   > Also: notice with relationship accessors functions, we do NOT include the open and close parentheses `()` in the call.

   That’s it for the Client side of the relationship, now let’s setup the Project side.

---

   ### Project to Client Relationship

1. Open the Project model (app/Models/Project.php) - right now it’s nearly empty as well

![Screen Shot 2021-12-19 at 11.41.38 AM.png](S4-07:%20Projects%20-%20Model%20Relationships.assets/Screen%20Shot%202021-12-19%20at%2011.41.38%20AM.png)

2. Add the following relationship method to the Project Model

```php
public function client()
{
  return $this->belongsTo(Client::class);
}
```

   > This time, the relationship we’re establishing is from the Project model to the Client model. Again, the name of the method should match the model we’re establishing a relationship with, in this case a `client`. It’s also single tense because all projects belong to just one `client`.

   > You can see the method names `belongsTo()` and `hasMany()` make it more clearly understandable how the relationship looks from that particular model.

   ### Example of Project to Client in use

   Again, here’s a made up example of how we might use this relationship in our code.

```php
$projects = Project::all();

foreach($projects as $project) {
  echo "Project Name: ".$project->name." | ";
  echo "Client: ".$project->client->name."<br />";
}
```

   > In this example, I’m fetching all the projects, and then I loop through them using a `foreach()` loop. I’m accessing the project name in the first line `$project->name`, and that should make sense, but if you notice I’m using the relationship to access the name of the client from the project itself `$project->client->name`, that’s using the relationship to access a property on the client that the project belongs to.

### Relationship Advice

I strongly suggest you get in the habit of doing both sides of a relationship at the same time. This is why we did both the `Client` and `Project` model updates in this session, even though we’re supposed to be focusing on just projects, it's all part of the feature we're building.

### Final Thoughts

We’re going to be setting up more of these relationships, but for now, I just want you to see how powerful they can be in our code. We can set them up between any combination of models in our project, and we will.

### Finish Up This Session

As we have in the previous sessions, we’re going to check in our changes. In this case, we want to add two files app/Models/Client.php and app/Models/Client.php, we’ll make it clear in our commit why both files are included.

```Bash
$ git status
```

![Screen Shot 2021-12-19 at 12.50.19 PM.png](S4-07:%20Projects%20-%20Model%20Relationships.assets/Screen%20Shot%202021-12-19%20at%2012.50.19%20PM.png)

```Bash
$ git add app/Models/Client.php
$ git add app/Models/Project.php
$ git status
```

![Screen Shot 2021-12-19 at 12.51.49 PM.png](S4-07:%20Projects%20-%20Model%20Relationships.assets/Screen%20Shot%202021-12-19%20at%2012.51.49%20PM.png)

```Bash
$ git commit -m "Adds relationship between Client and Project models"
```

Done and done. Let’s move on to the next session…

