# S4-10: Projects - Testing

We are in the home stretch on this one, and boy oh boy I can’t wait to show you our updated todo list:

1. > ~~Create a feature branch for our project features~~
2. > ~~Create a Model, Controller, Database Migration, Test files, and View files~~
3. > ~~Write our initial suite of tests for projects~~
4. > ~~Create our routes for projects~~
5. > ~~Update our controller CRUD code~~
6. > ~~Update our migration file to build up a projects table in the database~~
7. > ~~Discuss Model relationships and implement our first relationship between clients and projects~~
8. > ~~Finish our ProjectFactory file~~
9. > ~~Update our dummy views for projects~~
10. > Run our tests and fix any issues we have
11. > Commit our finished feature, then merge it into main branch

We’re all done with our work, well, at least we think we are. We won’t know for sure until we run our tests and see if we have any issues. Let’s do that now.

```Bash
$ sail artisan test
```

![Screen Shot 2021-12-19 at 3.51.30 PM.png](S4-10:%20Projects%20-%20Testing.assets/Screen%20Shot%202021-12-19%20at%203.51.30%20PM.png)

Whoa there, what a mess… how are we supposed to make sense of any of that?

We had this kind of error happen to us before, and believe it or not, we do get some useful error output, but it’s a bit difficult to find. Try scrolling to the top

![Screen Shot 2021-12-19 at 3.53.27 PM.png](S4-10:%20Projects%20-%20Testing.assets/Screen%20Shot%202021-12-19%20at%203.53.27%20PM.png)

We can see we have two failing tests and at the top of the monstrous error message we see Expected response status code 200 but received 500.

The reason for all this output is those particular tests are testing views, and views are handled by the Laravel Exception Handler. It does a stack trace and builds up that lovely error page we saw earlier in the workshop. But it’s not so go for our tests.

Luckily there’s a quick fix for that.

1. Open the ProjectTest file

![Screen Shot 2021-12-19 at 4.53.41 PM.png](S4-10:%20Projects%20-%20Testing.assets/Screen%20Shot%202021-12-19%20at%204.53.41%20PM.png)

In the two failing tests (`test_user_can_view_a_project()` and `test_user_can_view_project_index_page()`)add the following line of code at the top of the method body:

```php
$this->withoutExceptionHandling();
```

![Screen Shot 2021-12-19 at 4.55.38 PM.png](S4-10:%20Projects%20-%20Testing.assets/Screen%20Shot%202021-12-19%20at%204.55.38%20PM.png)

You can see the updates on `line 22` and line `37` of my screenshot.

This will tell Laravel that we don’t want it to pretty up our error output and give us just the error please.

Run the tests again:

```other
$ sail artisan test
```

![Screen Shot 2021-12-19 at 4.58.07 PM.png](S4-10:%20Projects%20-%20Testing.assets/Screen%20Shot%202021-12-19%20at%204.58.07%20PM.png)

This time we get a much more manageable error message. We can see that our first error is coming from the show.blade.php file line 14 on mine, your line may be different. And the second error is coming from the index.blade.php file (line 15 on my version of that file).

The error messages are the same, trying to get property “name” on null. In both cases this is because we’re trying to access a client name accessed through the project relationship, and there is no client.

If you haven’t figured it out yet, it’s because we have an issue in our test. Let’s go to our test file and see why this is happening.

1. Open tests/Feature/ProjectTest.php

Let’s turn our attention to the first failing test method, `test_user_can_view_a_project()`

```php
public function test_user_can_view_a_project()
{ 
  $this->withoutExceptionHandling();

  $response = Project::factory()->create();
  $projects = Project::all();
    
  $this->assertTrue($projects->count() == 1);
    
  $project = $projects->first();  
  $response = $this->get('/projects/'.$project->id);
  $response->assertStatus(200);
  $response->assertSee($project->name);
}
```

You can see on the second line of code in that method, we’re using a factory to create a project. If we open our ProjectFactory file we can see how that’s working currently:

![Screen Shot 2021-12-19 at 5.07.21 PM.png](S4-10:%20Projects%20-%20Testing.assets/Screen%20Shot%202021-12-19%20at%205.07.21%20PM.png)

For the client_id we’re hard-coding a value of 1, so we can insert that into the database because it’s a required field. But back in our tests, we don’t actually make a client record at all, so when those tests run we’re coming up empty when we try to access the client.

We can fix that by using the ClientFactory in our ProjectTest and building a project with the id of the client.

This is what that looks like:

You can see, I first included the Client model to our test file on `line 9`:

![Screen Shot 2021-12-19 at 5.14.32 PM.png](S4-10:%20Projects%20-%20Testing.assets/Screen%20Shot%202021-12-19%20at%205.14.32%20PM.png)

```php
use App\Models\Client;
```

Then, I created a Client on line 25 using the ClientFactory

```php
$client = Client::factory()->create();
```

Then on line 27, I added an array to the factory’s create() method and specified a client_id set to the client I just created.

Factories allow us to override values like this, so we can tell a factory to create something and then provide one of the values we want to set and it will take care of the rest.

```php
$response = Project::factory()->create(['client_id' => $client]);
```

We’ll do the same trick on the `test_user_can_view_project_index_page()` method, but let’s first run our tests to see if our fix worked.

```Bash
$ sail artisan test
```

![Screen Shot 2021-12-19 at 5.19.08 PM.png](S4-10:%20Projects%20-%20Testing.assets/Screen%20Shot%202021-12-19%20at%205.19.08%20PM.png)

We’re down to one failed test, so looks like our fix worked.

Now we can do the same fix on the `test_user_can_view_project_index_page()` method.

```php
$client = Client::factory()->create();
$projects = Project::factory(3)->create(['client_id' => $client]);
```

![Screen Shot 2021-12-19 at 5.22.28 PM.png](S4-10:%20Projects%20-%20Testing.assets/Screen%20Shot%202021-12-19%20at%205.22.28%20PM.png)

That should do it, run the tests again to see if we get all green this time.

```Bash
$ sail artisan test
```

![Screen Shot 2021-12-19 at 5.24.36 PM.png](S4-10:%20Projects%20-%20Testing.assets/Screen%20Shot%202021-12-19%20at%205.24.36%20PM.png)

And we do! Life is good, wrap this one up and commit your code.

```Bash
$ git status
$ git add .
$ git commit -am "fixes project test by creating a client and assigning it to a project"
```

---

### What if I have another test fail too?

If you're still having unexpected tests failures, checkout my troubleshooting guide on the topic. [[Troubleshooting: Unexpected Test Failures]]

