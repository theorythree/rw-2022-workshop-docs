# S7-04: CRUD for Project Controller

1. > ~~Refactor TimeEntryController~~
2. > ~~Complete TimeEntryController CRUD methods~~
3. > Update and Complete CRUD methods in ProjectController and ClientController
4. > Complete and Cross off all project TODOs
5. > Remove any unwanted or unused code

Let’s run through each of the methods in each controller and see if we can make any improvements. We’ll start with the ProjectController in this episode and then replicate what we’ve learned for the ClientController in an upcoming episode.

Open App/Http/Controllers/ProjectController.php

![Screen Shot 2022-01-05 at 3.10.15 PM.png](S7-04:%20CRUD%20for%20Project%20Controller.assets/Screen%20Shot%202022-01-05%20at%203.10.15%20PM.png)

### Index()

This looks good for now, when we add companies, we’ll need to filter these projects by company.

### Create()

The create method should just return a view, so let’s update that method now.

```php
public function create()
{
  return view('projects.create');
}
```

Skip down to the edit() method

### Edit()

We just need to return a view with the project that is being edited, thanks to route model binding, this is a snap.

```php
public function edit(Project $project)
{
  return view('projects.edit',compact('project'));
}
```

Our CRUD methods for the ProjectController and ClientController are looking pretty good at this point. I want to add validation to our `store()` and `update()` methods, just like we did for the TimeEntryController.

To do that, we need to do two things:

1. > Add a Request class for each
2. > Use our validated values for our store() and update() methods

Use the Artisan command to create the ProjectRequest file

```Bash
$ sail artisan make:request ProjectRequest
```

![Screen Shot 2022-01-05 at 3.15.49 PM.png](S7-04:%20CRUD%20for%20Project%20Controller.assets/Screen%20Shot%202022-01-05%20at%203.15.49%20PM.png)

Open App/Http/Requests/ProjectRequest.php

![Screen Shot 2022-01-05 at 4.28.00 PM.png](S7-04:%20CRUD%20for%20Project%20Controller.assets/Screen%20Shot%202022-01-05%20at%204.28.00%20PM.png)

We’re going to need to validate that the user is logged in, so include `Auth` in the header.

```php
use Auth;
```

And put that auth into use in the `authorize()` method.

```php
public function authorize()
{
    return auth()->check();
}
```

And for the `rules()` method, we’ll add all of our columns in the projects database table.

```php
public function rules()
{
  return [
    'client_id' => ['required', 'numeric'],
    'name' => ['required', 'string'],
    'description' => ['nullable'],
    'budget' => ['nullable','numeric'],
  ];
}
```

You can see we’re adding all columns, including the nullable ones, that way we can add rules to them when the values are provided.

![Screen Shot 2022-01-05 at 4.32.22 PM.png](S7-04:%20CRUD%20for%20Project%20Controller.assets/Screen%20Shot%202022-01-05%20at%204.32.22%20PM.png)

Now let’s use this new class in our ProjectController.

Open App/Http/Controllers/ProjectController.php

![Screen Shot 2022-01-05 at 4.33.12 PM.png](S7-04:%20CRUD%20for%20Project%20Controller.assets/Screen%20Shot%202022-01-05%20at%204.33.12%20PM.png)

Include the new ProjectRequest in the header.

```php
use App\Http\Requests\ProjectRequest;
```

And let’s put that request class into use in the store() method.

```php
public function store(ProjectRequest $request) { ... }
```

And let’s replace the method body with our create one liner like we did in tTimeEntryController.

```php
public function store(ProjectRequest $request)
{
  $project = Project::create($request->validated());
}
```

And again, same strategy for our update() method.

```php
public function update(ProjectRequest $request, Project $project)
{
  $project->update($request->validated());
}
```

Great, now remember, we have to allow these columns for mass assignment in our Project model.

Open App/Models/Project.php

![Screen Shot 2022-01-05 at 4.40.03 PM.png](S7-04:%20CRUD%20for%20Project%20Controller.assets/Screen%20Shot%202022-01-05%20at%204.40.03%20PM.png)

Add the protected $fillable array of columns names we want to authorize for mass assignment.

```php
protected $fillable = ['client_id','name','description','budget'];
```

![Screen Shot 2022-01-05 at 4.41.10 PM.png](S7-04:%20CRUD%20for%20Project%20Controller.assets/Screen%20Shot%202022-01-05%20at%204.41.10%20PM.png)

Great! I’m sensing the color red, let’s run our project tests to see why I’m feeling this way.

```Bash
$ sail artisan test --filter=ProjectTest
```

![Screen Shot 2022-01-05 at 4.46.59 PM.png](S7-04:%20CRUD%20for%20Project%20Controller.assets/Screen%20Shot%202022-01-05%20at%204.46.59%20PM.png)

Oh boy, what’s the deal with that?

Open up tests/Feature/ProjectTest.php

![Screen Shot 2022-01-05 at 4.47.53 PM.png](S7-04:%20CRUD%20for%20Project%20Controller.assets/Screen%20Shot%202022-01-05%20at%204.47.53%20PM.png)

Okay, there’s your problem. When we set these tests up just anyone (even a guest) could create a project and our new ProjectRequest is verifying that only a logged in user can create or update a project, that’s why our tests are failing.

Here’s that line in the Project Request for your reference:

```php
public function authorize()
{
  return auth()->check();
}
```

Okay so only and logged in user is authorized, we know how to fix that, we just need to create one in our tests.

Open tests/Feature/ProjectTest.php

![Screen Shot 2022-01-05 at 4.47.53 PM.png](S7-04:%20CRUD%20for%20Project%20Controller.assets/Screen%20Shot%202022-01-05%20at%204.47.53%20PM%20(2).png)

So let’s setup a user using the setup() method like we did in our other tests.

We’re going to use the user and role models, so include them in the header.

```php
use App\Models\User;
use App\Models\Role;
```

And now let’s create that setup() method so our tests can use the same user.

```php
public function setUp(): void
{
  parent::setUp();
  $this->artisan('db:seed --class=RoleSeeder');
  $this->user = User::factory()->create();
  $this->user->roles()->attach(Role::IS_USER);
  $this->owner = User::factory()->create();
  $this->owner->roles()->attach(Role::IS_OWNER);
}
```

Nothing fancy here, same code as we’ve used in our other tests. I’m setting up two users, one an owner and the other is a plain old user.

We want to enforce some of our rules around project creation. Remember, we want users to be able to see projects (that’s how they’ll add time to a project), but we don’t want them to be able to actually create a project. So we should write some tests to enforce this rule of ours.

I’m going to repurpose `the test_user_can_create_a_project()` method and make it for an owner instead.

That’s just a name change and a quick update on the post() method to use the actingAs() method.

```php
public function test_owner_can_create_a_project()
{
  $response = $this->actingAs($this->owner)->post('/projects', ['client_id' => 1,'name' => 'My Project']);
  $this->assertTrue(Project::all()->count() == 1);
}
```

Let’s run our test to see if we get a pass on the newly repurposed test method

```Bash
$ sail artisan test --filter=ProjectTest
```

![Screen Shot 2022-01-05 at 5.02.22 PM.png](S7-04:%20CRUD%20for%20Project%20Controller.assets/Screen%20Shot%202022-01-05%20at%205.02.22%20PM.png)

Green is good. But let’s not get to happy, we need to write a test that checks that a user cannot add a project… and I bet you can guess how that’s going to turn out.

```php
public function test_user_can_not_create_a_project()
{
  $response = $this->actingAs($this->user)->post('/projects', ['client_id' => 1,'name' => 'My Project']);
  $this->assertTrue(Project::all()->count() == 0);
}
```

This runs the same post request, but it’s being done by a user, not an owner, and so we expect to see zero records in the database.

```Bash
$ sail artisan test --filter=ProjectTest
```

![Screen Shot 2022-01-05 at 5.05.12 PM.png](S7-04:%20CRUD%20for%20Project%20Controller.assets/Screen%20Shot%202022-01-05%20at%205.05.12%20PM.png)

Annnnd we get a fail. That’s because a user can, in fact, create a project. Not cool.

So we have something to fix, our failed test will remind us of that fact, so let’s move on to the next test.

Take a peek at the `test_user_can_view_a_project()` method

```php
// REMOVE THIS METHOD:
public function test_user_can_view_a_project()
{ 
  $this->withoutExceptionHandling();

  $client = Client::factory()->create();

  $response = Project::factory()->create(['client_id' => $client]);
  $projects = Project::all();
  
  $this->assertTrue($projects->count() == 1);
  
  $project = $projects->first();  
  $response = $this->get('/projects/'.$project->id);
  $response->assertStatus(200);
  $response->assertSee($project->name);
}
```

This test seems a bit misplaced. We’re not sure if we’re even going to have a projects page, and if we do, we might want to guard against guests only. So let’s remove this test entirely.

Next let’s train our eyeballs to the `test_user_can_view_project_index_page()` method.

```php
// REMOVE THIS METHOD
public function test_user_can_view_project_index_page() 
{
  $this->withoutExceptionHandling();
  
  $client = Client::factory()->create();
  $projects = Project::factory(3)->create(['client_id' => $client]);
  
  $this->assertTrue($projects->count() == 3);
  
  $response = $this->get('/projects');
  $response->assertStatus(200);

  $firstProject = $projects->first();
  $lastProject = $projects->last();

  $response->assertSee($firstProject->name);
  $response->assertSee($lastProject->name);
}
```

This test seems too opinionated as well. I’m not sure I want to test that a user can or cannot see a list of projects. They probably should be able to see this page, but so should the owners, so since we do not have a template built I don’t want to use a render test at all at this point. Let’s remove it completely.

On to the next method: `test_user_can_update_a_project()`

This seems like a useful thing to test. Again, I’d like two tests one for the user and one for the owner. The owner will be allowed to update a project, but I don’t want the user to be able to have that privilege.

```php
public function test_owner_can_update_a_project()
{
  $project = Project::factory()->create(['name' => 'Original Project Name']);
  $this->assertTrue(Project::all()->count() == 1);
  $this->assertDatabaseHas('projects', ['name' => $project->name]);

  $response = $this->actingAs($this->owner)->put('/projects/'.$project->id, ['client_id' => 1,'name' => 'My Updated Project Name']);
  $this->assertDatabaseHas('projects', ['name' => 'My Updated Project Name']);
}
```

```php
public function test_user_can_not_update_a_project()
{
  $project = Project::factory()->create(['name' => 'Original Project Name']);
  $this->assertTrue(Project::all()->count() == 1);
  $this->assertDatabaseHas('projects', ['name' => $project->name]);

  $response = $this->actingAs($this->user)->put('/projects/'.$project->id, ['client_id' => 1,'name' => 'My Updated Project Name']);
  $this->assertDatabaseHas('projects', ['name' => $project->name]);
}
```

You can see the same strategy, we’re building up a project using a factory, ensuring that we define the name of the project, then we attempt an update to the name, and only the owner should see the update in the database.

Finally let’s look at the `test_user_can_delete_a_project()` method.

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

Again with this one we know we do not want a user to delete a project for sure. So we’ll create two test methods, one for user and one for owner. The owner can delete a project and the user should not be able to do so.

```php
public function test_owner_can_delete_a_project()
{
  $project = Project::factory()->create();
  $this->assertTrue(Project::all()->count() == 1);

  $response = $this->actingAs($this->owner)->delete('/projects/'.$project->id);
  $this->assertTrue(Project::all()->count() == 0);    
}
```

```php
public function test_user_can_not_delete_a_project()
{
  $project = Project::factory()->create();
  $this->assertTrue(Project::all()->count() == 1);

  $response = $this->actingAs($this->user)->delete('/projects/'.$project->id);
  $this->assertTrue(Project::all()->count() == 1);    
}
```

Okay, let’s run some tests already.

```Bash
$ sail artisan test --filter=ProjectTest
```

![Screen Shot 2022-01-05 at 5.57.31 PM.png](S7-04:%20CRUD%20for%20Project%20Controller.assets/Screen%20Shot%202022-01-05%20at%205.57.31%20PM.png)

Okay, some fails on the ProjectTest, that’s what we want at this point. In an upcoming episode we will discuss exactly how to lock down the user to prevent them from doing certain things that our owners can.

Let’s add and commit (not push) our code before moving on.

```Bash
$ git status
$ git add .
$ git commit -m "Update: refactors project controller and tests"
```

> 🍎 This is a good time for a short break. Get up and stretch your legs. We’ll see you in five.

