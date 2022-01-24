# S4-03: Projects - Feature Branch

Well hello there. When we last left off, we had decided to work on the project features of our application and we decided to use a feature branch to have clean separation of effort in our version control. Here’s a list of tasks we need to complete from end to end for this series.

1. > Create a feature branch for our project features
2. > Create a Model, Controller, Database Migration, Test files, and View files
3. > Write our initial suite of tests for projects
4. > Create our routes for projects
5. > Update our controller CRUD code
6. > Update our migration file to build up a projects table in the database
7. > Discuss Model relationships and implement our first relationship between clients and projects
8. > Finish our ProjectFactory file
9. > Update our dummy views for projects
10. > Run our tests and fix any issues we have
11. > Commit our finished feature, then merge it into main branch

That’s a lot of work, but don’t worry, you’ve already done most of this stuff and I’ll break it up into manageable sessions to keep things simple.

---

Start your work session using the steps outlined here:

[[Workflow: Starting and Ending Your Work Session]]

---

### Create a feature branch for our project features

1. Open your project in Visual Studio Code (if you haven't already done so)
2. Open the terminal pane and type the following commands to create a feature branch (replace my name with yours in this command)

```Bash
$ git pull
$ git checkout dan/projects
```

![Screen Shot 2021-12-17 at 4.55.22 PM.png](S4-03:%20Projects%20-%20Feature%20Branch.assets/Screen%20Shot%202021-12-17%20at%204.55.22%20PM.png)

You’re now working in your feature branch (notice I always do a pull to make sure I get the latest changes for the branch I am currently on.

> At this point in time the feature branch is exactly the same as the main branch it was made from. Any change we make from here on out will be made on the feature branch and not main. Only after we merge the branch back to main will they once again be in sync.

### Create a Model, Controller, Database Migration, Test files, Factory, and View files

Seems like a lot of work, but we can create three of those files in one artisan command.

```Bash
$ sail artisan make:model Project -mc --resource
```

> This command is quite powerful. It makes a model named `Project`, the `-m` creates a migration which will assume a database table name of projects, the `c` makes the controller which will be called `ProjectController` and finally we flag that with the `—resource` which will stub out the CRUD methods in the controller.

Let’s make our test file next

```Bash
$ sail artisan make:test ProjectTest
```

![Screen Shot 2021-12-17 at 5.06.48 PM.png](S4-03:%20Projects%20-%20Feature%20Branch.assets/Screen%20Shot%202021-12-17%20at%205.06.48%20PM.png)

Next let’s create a Project Factory so we can build up projects faster in our tests.

```Bash
$ sail artisan make:factory ProjectFactory
```

![Screen Shot 2021-12-17 at 5.38.26 PM.png](S4-03:%20Projects%20-%20Feature%20Branch.assets/Screen%20Shot%202021-12-17%20at%205.38.26%20PM.png)

Finally, for our views, we’re going to create some empty blade templates, artisan won't help us here, so we'll just have to do them the old fashioned way.

Create a folder called `projects` in the `/resources/views/` folder. You can do this in your operating system, or just use Visual Studio Code.

![Screen Shot 2021-12-17 at 5.11.30 PM.png](S4-03:%20Projects%20-%20Feature%20Branch.assets/Screen%20Shot%202021-12-17%20at%205.11.30%20PM.png)

![Screen Shot 2021-12-17 at 5.11.36 PM.png](S4-03:%20Projects%20-%20Feature%20Branch.assets/Screen%20Shot%202021-12-17%20at%205.11.36%20PM.png)

![Screen Shot 2021-12-17 at 5.12.59 PM.png](S4-03:%20Projects%20-%20Feature%20Branch.assets/Screen%20Shot%202021-12-17%20at%205.12.59%20PM.png)

Next create the following files in the projects directory you just created. Use the new file icon (which is just to the left of the new folder icon).

- > create.blade.php
- > edit.blade.php
- > index.blade.php
- > show.blade.php

![Screen Shot 2021-12-17 at 5.15.43 PM.png](S4-03:%20Projects%20-%20Feature%20Branch.assets/Screen%20Shot%202021-12-17%20at%205.15.43%20PM.png)

### Write our initial suite of tests for projects

We’re going to reference our tests from ClientTest for this step.

1. Open `ClientTest` (tests/feature/ClientTest.php)
2. Also open `ProjectTest` (tests/feature/ProjectTest.php)
3. Click and drag the ProjectTest tab to the right side of the window until you see a the the box appear to the right indicating a horizontal split.

[s4-03-01.mp4](S4-03:%20Projects%20-%20Feature%20Branch.assets/s4-03-01.mp4)

Now we can reference the ClientTest code while we work on the ProjectTest code.

![Screen Shot 2021-12-17 at 5.31.45 PM.png](S4-03:%20Projects%20-%20Feature%20Branch.assets/Screen%20Shot%202021-12-17%20at%205.31.45%20PM.png)

Starting at the top of the file add our model to the ProjectTest file

```php
use App\Models\Project;
```

Now add the RefreshDatabase line to the ProjectTest class body

```php
use RefreshDatabase;
```

Delete the test_example() method and its comment that was stubbed out for us when Laravel created the file for us.

![Screen Shot 2021-12-17 at 5.35.33 PM.png](S4-03:%20Projects%20-%20Feature%20Branch.assets/Screen%20Shot%202021-12-17%20at%205.35.33%20PM.png)

Next we’re going to refactor each of these methods to work with projects instead of clients. A simple rename of everything should work just fine, and then we’ll make some adjustments to the tests to work with projects in a second pass.

> Note, I do not suggest you simply copy and paste the code from one file into the other. This is bad practice, because you may mistakenly forget to change something in the new test file that could lead to a test passing or failing when it shouldn’t.

Let’s go through and create each testing method for ProjectTest while referencing the ClientTest method.

```php
public function test_user_can_create_a_project()
{
  $response = $this->post('/projects', ['client_id' => 1,'name' => 'My Project']);
  $this->assertTrue(Project::all()->count() == 1);
}
```

> After renaming, this method, we had to change the data passed to the database, our projects have client_id and no `code` field. I faked a client_id and left our description and budget because they are optional. No need to go overboard and actually create a client for this test.

```php
public function test_user_can_view_a_project()
{ 
  $response = Project::factory()->create();
  $projects = Project::all();
    
  $this->assertTrue($projects->count() == 1);
    
  $project = $projects->first();  
  $response = $this->get('/projects/'.$project->id);
  $response->assertStatus(200);
  $response->assertSee($project->name);
}
```

> I decided to use a `Factory` to create our `project` since the purpose of the test is to test if a user can see a project, and we are not testing the `post` method of our controller like we are in the first test.

> I’m also checking to see the project name since we don’t have a code for project (although that sounds like a good idea to add).

```php
public function test_user_can_view_project_index_page() 
{
  $projects = Project::factory(3)->create();
    
  $this->assertTrue($projects->count() == 3);
    
  $response = $this->get('/projects');
  $response->assertStatus(200);

  $firstProject = $projects->first();
  $lastProject = $projects->last();

  $response->assertSee($firstProject->name);
  $response->assertSee($lastProject->name);
}
```

> For the project index test, I’m using the same approach as the client index test. I’m using a factory to create three projects, then I’m verifying we can access the projects route, and finally I’m looking at the index page and checking to see if I can see the first and last project names on the page.

```php
public function test_user_can_update_a_project()
{
  $response = $this->post('/projects', ['client_id' => 1,'name' => 'My Project']);
  $projects = Project::all();
    
  $this->assertTrue($projects->count() == 1);

  $project = $projects->first();
  $this->assertTrue($project->name == $projects->first()->name);

  $response = $this->put('/projects/'.$project->id, ['client_id' => 1,'name' => 'My Updated Project']);
  $response->assertStatus(200);

  $projects = Project::all();
  $this->assertTrue($projects->first()->name == 'My Updated Project');
}
```

> For the update test method, I’m again choosing not to use a factory, because I want to test the actual name change of a project. This test creates a project, verifies it’s in the database and named what I originally set it to, then the second part of the test is to see if I can make an edit, then finally, I verify my edit is shown on the project detail page.

> Also worth pointing out, I’m using the `put()` method here to make a `put request`, which is what we need to do for all our edit request.

```php
public function test_user_can_delete_a_project()
{
  $response = Project::factory()->create();
  $projects = Project::all();
    
  $this->assertTrue($projects->count() == 1);

  $project = $projects->first();
  $this->assertTrue($project->name == $projects->first()->name);

  $response = $this->delete('/projects/'.$project->id);
  $response->assertStatus(200);

  $projects = Project::all();
  $this->assertTrue($projects->count() == 0);    
}
```

> For the delete test method, I am choosing to use a factory, because I want to test the delete method and I really don’t care what the project details are.

> Also worth pointing out, I’m using the `delete()` method here to make a `delete request`, which is what we need to do for all our delete request.

At this point, we’re done with our test methods for projects. If we were to run our tests they will all fail. You can run the tests if you wish.

```Bash
$ sail artisan test
```

### Time to commit our code

Now would be a good time for us to make a commit to our code. We just added our first set of tests and they’re failing, so we won’t actually push up our changes because we’re going to continue our work, but since we’re on a feature branch, we can, and will, be pushing up incomplete code. Think of it as a backup. We just have to be sure when we’re done with the work of this feature, all of our tests pass before we merge our branch back into the main branch.

I really don’t want to commit ALL of our code files, only the ProjectTest file we worked on. I do this for two reasons. One, there’s no value in checking in the other files because they’re empty and I can always regenerate them if I need to. Secondly, Visual Studio Code colors the new files we added green and it can be handy to remind us that we still need to finish up those green files. As we commit our other files, they’ll go back to their standard color.

1. Check our changes by doing a git status

```Bash
$ git status
```

![Screen Shot 2021-12-18 at 11.30.31 AM.png](S4-03:%20Projects%20-%20Feature%20Branch.assets/Screen%20Shot%202021-12-18%20at%2011.30.31%20AM.png)

In the world of Git, red means that the changes are untracked, meaning they’re not currently included and cannot be committed until they are added.

As I said, I only want to commit our test file, so we’re going to add just it to our commit by using the add command, along with the file name.

2. Add just the ProjectTest file to our commit

```Bash
$ git add tests/Feature/ProjectTest.php
```

3. Now do another git status

```Bash
$ git status
```

![Screen Shot 2021-12-18 at 11.33.36 AM.png](S4-03:%20Projects%20-%20Feature%20Branch.assets/Screen%20Shot%202021-12-18%20at%2011.33.36%20AM.png)

> You’ll see the file we added to our commit is now green and the other files remain untracked as they were before. This is what we want.

4. Now commit the ProjectTest file with our standard commit command and message.

```Bash
$ git commit -m "adds initial tests for projects"
```

![Screen Shot 2021-12-18 at 11.36.23 AM.png](S4-03:%20Projects%20-%20Feature%20Branch.assets/Screen%20Shot%202021-12-18%20at%2011.36.23%20AM.png)

Now do another git status to verify our test file is committed and no longer shows up in our list of untracked files.

```Bash
$ git status
```

![Screen Shot 2021-12-18 at 11.39.04 AM.png](S4-03:%20Projects%20-%20Feature%20Branch.assets/Screen%20Shot%202021-12-18%20at%2011.39.04%20AM.png)

Excellent all is well. As I said, I’m not ready to push these changes up just yet, but at this point I could if I wanted to back up my work.

