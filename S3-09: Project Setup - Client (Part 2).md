# S3-09: Project Setup - Client (Part 2)

When we last left off, we had a passing test called ClientTest that tested if a user could create a client in our test database. (whoah, that’s a lot of uses of the word test).

We’re ready to pick back up where we left off and start another working session.

---

### Starting your work session

Every time (yes every time) we start up a work session, we make sure to perform these steps.

1. Ensure Docker is running
2. Open the project in Visual Studio Code
3. Issue the sail up command (if it’s not already running) in a terminal window

```Bash
$ sail up
```

4. Ensure our project is running in the browser by visiting localhost
5. Check the status of our local code to make sure we have no uncommitted changes

```Bash
$ git status
```

6. If we have any uncommitted changes, either commit them or revert them before we proceed
7. Check to make sure our local copy of code is up to date

```Bash
$ git pull
```

8. Run our tests to make sure they are all still passing

```Bash
$ sail artisan test
```

9. Fix any failed tests and commit those changes
10. Set a goal for the work you wish to accomplish in this session
11. Begin your work

---

> From now on you can refer to the steps to startup your and end your work session here:  [[Workflow: Starting and Ending Your Work Session]]

---

### Step 10: Our Goal

Assuming you performed steps 1-9 above, let’s set our goal.

The Goal of this session is to finish up the initial work around implementing our Client into our project. Specifically we’re going to finish the Read, Update and Delete methods to manage clients in our project, by creating tests, updating our routes, model, and controller for the client. That’s a lot of work, so we’re going to break everything up into manageable chunks.

---

### Start with tests

We’re going to continue our TDD approach by starting with our ClientTest file.

1. Open ClientTest in Visual Studio

![Screen Shot 2021-12-12 at 2.21.17 PM.png](S3-09:%20Project%20Setup%20-%20Client%20(Part%202).assets/Screen%20Shot%202021-12-12%20at%202.21.17%20PM.png)

> We're going to stub out all of the tests we want to write today right away. For us, as our goal stated, we want to test the read, update, and delete methods in out ClientController.

2. Create the following test methods in our ClientTest file and add them below the existing test method we already have in that file.

```php
public function test_user_can_view_a_client()
{
}

public function test_user_can_update_a_client()
{
}

public function test_user_can_delete_a_client()
{
}
```

Open up a new terminal window in Visual Studio Code so we can keep our Docker services running in one and run our tests in another.

3. Open the terminal in Visual Studio Code and click on the `+` button next to the 1: bash dropdown menu.

![Screen Shot 2021-12-12 at 2.26.45 PM.png](S3-09:%20Project%20Setup%20-%20Client%20(Part%202).assets/Screen%20Shot%202021-12-12%20at%202.26.45%20PM.png)

4. Run the tests to verify they are all still passing

![Screen Shot 2021-12-12 at 2.32.58 PM.png](S3-09:%20Project%20Setup%20-%20Client%20(Part%202).assets/Screen%20Shot%202021-12-12%20at%202.32.58%20PM.png)

5. Hide the terminal windows by clicking on the `X` in the upper-right corner of the terminal window pane.

Because the view test will require us actually setting up some views, we’re going to focus on the other two tests first.

Testing if a user can update a client is going to be quite similar to the first test we made. We just need to create a client, then test if a user can make an edit. We can use fake data along the way.

Let’s begin.

1. Update the `test_user_can_update_a_client()` method with the code we used to create a client.

```php
public function test_user_can_update_a_client()
{
  $response = $this->post('/clients', ['name' => 'ABC Company', 'code' => 'ABCCO']);
  $this->assertTrue(Client::all()->count() == 1);
}
```

> You may be wondering why we just didn’t call `test_user_can_create_a_client()` test method in the `test_user_can_update_a_client()` to save ourselves from the hassle of retyping the same code. This is because we always want our tests to work in isolation from one another and we don’t want this particular test to be dependent on another test.

> Remember, our database will be built up and torn down before each of these testing methods, so we want to make sure we have the proper setup for this specific test.

2. Run your tests

```php
$ sail artisan test
```

![Screen Shot 2021-12-12 at 2.43.54 PM.png](S3-09:%20Project%20Setup%20-%20Client%20(Part%202).assets/Screen%20Shot%202021-12-12%20at%202.43.54%20PM.png)

You’ll see some assertion warnings, but they apply to our other test. How annoying. We don’t want to see them every time we run our test for the work we’re doing in the `test_user_can_update_a_client()` testing method. They might distract us. If only there was a way to run just a single test at a time, we could avoid this confusion.

Luckily there’s a way we can do just that. All we have to do is pass a `filter` tag to our testing command. Try this one:

```php
$ sail artisan test --filter=test_user_can_update_a_client
```

![Screen Shot 2021-12-12 at 2.48.40 PM.png](S3-09:%20Project%20Setup%20-%20Client%20(Part%202).assets/Screen%20Shot%202021-12-12%20at%202.48.40%20PM.png)

Cool, now our test passes. But we’re not done, we still need the test to test an update. Let’s do that now.

We have to first get the actual client record we just created. We can do that by performing a query on our database.

3. Fetch the newly created record by adding the following line of code after the last assertion in the `test_user_can_update_a_client()` method.

```php
$client = Client::all();
```

![Screen Shot 2021-12-12 at 2.52.17 PM.png](S3-09:%20Project%20Setup%20-%20Client%20(Part%202).assets/Screen%20Shot%202021-12-12%20at%202.52.17%20PM.png)

> Wait just one second. Doesn’t that line just get all of the clients in the database? And isn’t that the same thing we’re trying to asset in line 26? Yup, we can do better. We don’t need to query the database twice for the same thing. Let’s first query the database, then assert our count is 1 after we do that.

Here’s an updated method for you to ponder.

```php
public function test_user_can_update_a_client()
{
  $response = $this->post('/clients', ['name' => 'ABC Company', 'code' => 'ABCCO']);
  $clients = Client::all();
  
  $this->assertTrue($clients->count() == 1);

}
```

> Notice I renamed the variable from $client to $clients, this is because the all() method will assume it’s returning a collection of records and not just one. This is still what we want to test on, because we still want to assert that we only have one record in the entire table. If it fails it will be because we have zero or more than one client in the clients table. In both cases we want this test to fail so we can see what’s going on.

Now that we know we have a client in the database, we just need to grab the first one (in our collection of one client) because that’s the record we want to edit for our test.

4. Add the following line to fetch the first client in our test database after the assertion line.

```php
$client = $clients->first();
```

![Screen Shot 2021-12-14 at 1.49.55 PM.png](S3-09:%20Project%20Setup%20-%20Client%20(Part%202).assets/Screen%20Shot%202021-12-14%20at%201.49.55%20PM.png)

The test we’re writing is to update a client, I want to update the name of the client for this test, so I want to make sure before we edit the record, that we have a specific name.

5. Add this assertion line at the bottom of our test method.

```php
$this->assertTrue($client->name == $clients->first()->name);
```

We just made an assertion so now it a good time to run our test again.

```Bash
$ sail artisan test --filter=test_user_can_update_a_client
```

![Screen Shot 2021-12-14 at 1.54.24 PM.png](S3-09:%20Project%20Setup%20-%20Client%20(Part%202).assets/Screen%20Shot%202021-12-14%20at%201.54.24%20PM.png)

Up next, we’re going to make a change to our client record. To do that we’re going to need to use the put() method which is the method used for updating records.

Just like the post() method above, which created our record, I want to capture that response in a $response variable and provide the details of our edit.

```php
$response = $this->put('/clients/'.$client->id, ['name' => 'ABC Company Updated', 'code' => 'ABCCO']);
```

> You’ll also notice I changed the name from `ABC Company` to `ABC Company Updated`.

![Screen Shot 2021-12-14 at 2.00.56 PM.png](S3-09:%20Project%20Setup%20-%20Client%20(Part%202).assets/Screen%20Shot%202021-12-14%20at%202.00.56%20PM.png)

> You’ll notice something slightly different in this method. We’re including the `$client->id` in the `put()` method’s url argument. This is because we have to specify what record we want to update, even though we only have one record in the database, the `put()` method requires the `id` of the record to be passed along with the details.

Next we want to make sure our update response is okay. To do that, we’re going to use a new testing method called `assertStatus()`. This method will check any http response code we’re looking for. The okay response is always 200, so the assert line you need to add after our put() method line will be:

```php
$response->assertStatus(200);
```

Run our test again

```other
$ sail artisan test --filter=test_user_can_update_a_client
```

And with that we get another fail (which we should expect)

![Screen Shot 2021-12-14 at 3.02.34 PM.png](S3-09:%20Project%20Setup%20-%20Client%20(Part%202).assets/Screen%20Shot%202021-12-14%20at%203.02.34%20PM.png)

The error we’re getting is a 404, that means we have no matched route for our request. But we know how to fix that don’t we. We just need to write a route rule in our web.php routes file.

6. Open the `web.php` route file and add the following route rule to the file.

```php
Route::put('/clients/{client}','App\Http\Controllers\ClientController@update');
```

![Screen Shot 2021-12-14 at 3.05.41 PM.png](S3-09:%20Project%20Setup%20-%20Client%20(Part%202).assets/Screen%20Shot%202021-12-14%20at%203.05.41%20PM.png)

We’re using the standard route `/clients/{client}` where the `{client}` is a placeholder for the client we’re going to be updating. By using the put() method the router knows this is an update of an existing record and just needs to be told which controller and method will be handling the request (in this case the `ClientController` and the `@update` method), respectively.

7. Running our test fixes our test, and it now should pass.

```Bash
$ sail artisan test --filter=test_user_can_update_a_client
```

![Screen Shot 2021-12-14 at 3.10.25 PM.png](S3-09:%20Project%20Setup%20-%20Client%20(Part%202).assets/Screen%20Shot%202021-12-14%20at%203.10.25%20PM.png)

But we’re not quite done yet are we? All we did was make a put request and the controller said it go it, did we actually make a change? Probably not, let’s finish our test.

To do that, we want to assert that the record actually gets updated.

Let’s fetch all the clients and check that there’s one in there with the updated name (hint, it’s not going to be there).

Add these two lines to the bottom of our test_user_can_update_a_client() method.

```php
$clients = Client::all();
$this->assertTrue($clients->first()->name == 'ABC Company Updated');
```

The first line will do another fetch of all the records in the `clients` table and verify that the first one matches the updated client name of `ABC Company Updated`.

8. Run the test again

```Bash
$ sail artisan test --filter=test_user_can_update_a_client
```

![Screen Shot 2021-12-14 at 3.15.14 PM.png](S3-09:%20Project%20Setup%20-%20Client%20(Part%202).assets/Screen%20Shot%202021-12-14%20at%203.15.14%20PM.png)

As you can see, our assertion failed, we do not have a name match. This is because our ClientController didn’t make the change, let’s finish that work up now.

1. Open the ClientController file and look for the update() method.

Let’s take a quick look at the update() method. It looks something like this:

```php
public function update(Request $request, $id)
{
    //
}
```

It’s empty, which is why our test is failing, but I wanted to direct your attention to something else. Notice the arguments that this method is taking? It’s taking two arguments a `Request` (which will contain our request data in a variable named `$request` and an `id` which is named `$id`).

We’ll be able to take that `$id` and query the `clients` database table to find the record we want to edit. But there’s actually a better way. We can use a feature of Laravel called `Route Model Binding`. Because it’s safe to assume we want will always want to look up the client from the id passed, Laravel can do this for us automatically and just pass us the client we’re interested in to save us some time.

To use this feature, we just need to replace the $id argument with an actual client object. Here’s what that looks like.

```php
public function update(Request $request, Client $client) { ... }
```

By doing so, we’re casting the `$client` variable to be of type Client (our model) and Laravel will see we’re asking for it to look up the actual client and pass that to our method instead of just the id.

Now we can refer to the client in our method and just do the update and save.

The code for an update is nearly identical to our create() method, we just remove the one line that was responsible for creating a new client and the rest stays the same.

```php
public function update(Request $request, Client $client)
{
  $client->name = $request->input('name');
  $client->code = $request->input('code');
  $client->save();
}
```

2. Add the lines above, save your code and run your test.

```Bash
$ sail artisan test --filter=test_user_can_update_a_client
```

![Screen Shot 2021-12-14 at 3.29.40 PM.png](S3-09:%20Project%20Setup%20-%20Client%20(Part%202).assets/Screen%20Shot%202021-12-14%20at%203.29.40%20PM.png)

This time our test passes. Great.

One final step would be to run ALL of our tests to make sure we still get the expected results. To do that we just remove the —flag part of our test.

```Bash
$ sail artisan test
```

![Screen Shot 2021-12-14 at 3.32.13 PM.png](S3-09:%20Project%20Setup%20-%20Client%20(Part%202).assets/Screen%20Shot%202021-12-14%20at%203.32.13%20PM.png)

As you can see, we get the two warnings we were getting before because we’re running all of our tests.

Let’s make short work of the `test_user_can_delete_a_client()` method by copying our `test_user_can_update_a_client(`) method and repurposing it to do a delete.

1. Copy the contents of `test_user_can_update_a_client(`) into  `test_user_can_delete_a_client()`

![Screen Shot 2021-12-14 at 3.35.16 PM.png](S3-09:%20Project%20Setup%20-%20Client%20(Part%202).assets/Screen%20Shot%202021-12-14%20at%203.35.16%20PM.png)

We still want to do everything up until the put() method is called. We’re going to want to change that to the delete() method and we’re also not going to need to pass any other data since we’re not changing anything to the client we’re deleting.

2. Update the `test_user_can_delete_a_client()` method with the following code:

```php
$response = $this->delete('/clients/'.$client->id);
$response->assertStatus(200);
```

> Notice we still want to check for a status of 200 on this one too.

3. Take a look at the final line of code of our test.

```php
$this->assertTrue($clients->count() == 1);
```

This is no longer correct. We want to make sure that there are zero records in the database after we attempt to delete the only record we created earlier in our test.

4. Change the `assertTrue()` line to check for a zero count.

```php
$this->assertTrue($clients->count() == 0);
```

5. Now let’s run our test (just the delete test for now)

```Bash
$ sail artisan test --filter=test_user_can_delete_a_client
```

![Screen Shot 2021-12-14 at 3.45.56 PM.png](S3-09:%20Project%20Setup%20-%20Client%20(Part%202).assets/Screen%20Shot%202021-12-14%20at%203.45.56%20PM.png)

Our test fails because our controller hasn’t deleted the record, and we’re still getting a count of 1 back from the clients database table. We know this because we have not added a route and updated our ClientController file yet. Let’s do both now.

6. In the web.php routes file add the delete route (it looks a lot like the update() route we made earlier).
```php
Route::delete('/clients/{client}','App\Http\Controllers\ClientController@destroy');
```

![Screen Shot 2021-12-14 at 3.50.42 PM.png](S3-09:%20Project%20Setup%20-%20Client%20(Part%202).assets/Screen%20Shot%202021-12-14%20at%203.50.42%20PM.png)

8. Now add the code to delete the client in the `ClientController` `destroy()` method. We’re going to use `Route Model Binding` to delete the client so change replace the $id argument with Client $client like we did for the update() method.

```php
public function destroy(Client $client) { ... }
```

Now deleting a client is a one line command.

```php
$client->delete();
```

The entire destroy method will look like this:

```php
public function destroy(Client $client)
{
  $client->delete();
}
```

![Screen Shot 2021-12-14 at 3.56.30 PM.png](S3-09:%20Project%20Setup%20-%20Client%20(Part%202).assets/Screen%20Shot%202021-12-14%20at%203.56.30%20PM.png)

That should finish up our destroy() method and our test should now pass.

9. Run the test

```php
$ sail artisan test --filter=test_user_can_delete_a_client
```

![Screen Shot 2021-12-14 at 3.58.01 PM.png](S3-09:%20Project%20Setup%20-%20Client%20(Part%202).assets/Screen%20Shot%202021-12-14%20at%203.58.01%20PM.png)

10. Now run all our tests to see only one with a warning.

```php
$ sail artisan test
```

![Screen Shot 2021-12-14 at 3.59.04 PM.png](S3-09:%20Project%20Setup%20-%20Client%20(Part%202).assets/Screen%20Shot%202021-12-14%20at%203.59.04%20PM.png)

Great work. This is a good place for us to commit and push up our code before we move on to the view test.

1. Commit your changes

```Bash
$ git status
```

2. Confirm that the only files we intended to modify are listed. If not, do not proceed with committing.

> If you do have some files that do not belong to your commit, check out the [[Trouble Shooting: Git]] guide to help you fix the issue before you continue with your commit.

![Screen Shot 2021-12-14 at 4.03.35 PM.png](S3-09:%20Project%20Setup%20-%20Client%20(Part%202).assets/Screen%20Shot%202021-12-14%20at%204.03.35%20PM.png)

3. Add your changed files to the commit

```Bash
$ git add .
```

4. finally commit your changes with a descriptive commit message and push the changes up.

```Bash
$ git commit -m "adds new tests, routes, and controller updates for upddating and deleting clients"
```

5. Finally, push up our changes

```Bash
$ git push
```

![Screen Shot 2021-12-14 at 4.07.56 PM.png](S3-09:%20Project%20Setup%20-%20Client%20(Part%202).assets/Screen%20Shot%202021-12-14%20at%204.07.56%20PM.png)

Excellent. You’re ready to move on to the third, and last part of this series.

