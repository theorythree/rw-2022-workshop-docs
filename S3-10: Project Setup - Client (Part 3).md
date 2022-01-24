# S3-10: Project Setup - Client (Part 3)

When we last left off, we had just finished up the client update and delete tests and initial functionality in our project. We paused right before finishing up our view test for clients. The reason for this is views require a bit more work to setup and test.

Now that we’ve had our rest, we’re ready to being wrapping up our work.

---

Start your work session using the steps outlined here:

[[Workflow: Starting and Ending Your Work Session]]

---

Let’s kick things off by running our tests to remind ourselves where we left off.

```Bash
$ sail artisan test
```

![Screen Shot 2021-12-14 at 7.02.53 PM.png](S3-10:%20Project%20Setup%20-%20Client%20(Part%203).assets/Screen%20Shot%202021-12-14%20at%207.02.53%20PM.png)

Ahh yes, we still have that view test to finish up. Our warning is telling us we have no assertions for our test.

### A quick word about views

Views are templates that we create in our project. They can show a combination of static and dynamic content. The dynamic content can come from our database as will be the case for this session. You may remember that all of our view templates are stored in /resources/views and they all have a blade.php extension.

Blade is the templating language we’re going to be using, so when we get to that part of the session, I’ll give you a proper introduction.

We’re not going to spend a lot of time with the construction of our views in this portion of the workshop. This is because we have a whole series which focuses on our front-end of our project. For now we’ll create some ugly placeholder views to keep us moving along.

### The goal for this session

You may remember I like to start off each session with a clearly defined set of goals.

1. Finish the `user_can_view_a_client()`
2. Create a route to handle the client view request
3. Update the `show()` method of our ClientController to return a view to the user when they make the their request
4. Create a placeholder view template to display client details

> By now, you should start to feel somewhat comfortable with setting up the test, route, and controller portion of this session so we're going to pick up the pace a bit for those parts and slow things back down when we discuss constructing views.

Let’s work on our test first.

Our test should do the following:

1. > Create a new client
2. > Verify we have one, and only one, record in the clients table
3. > Make a get request to the clients show() route (/clients/{id})
4. > Verify we get a status code of 200 on that response
5. > Verify the view is showing some client information
1. Create a new client

We’ve done this in our other tests, and so we’ll just copy and paste the lines responsible for creating a new client in our test database.

Copy these two lines from another test method in our ClientTest file and paste them into the `test_user_can_view_a_client()` method.

```php
$response = $this->post('/clients', ['name' => 'ABC Company', 'code' => 'ABCCO']);
$clients = Client::all();
```

2. Verify we have one, and only, one record in the clients table

Again, we’ve done this before, so copy the following line and put it after the last line in our `test_user_can_view_a_client()` method.

```php
$this->assertTrue($clients->count() == 1);
```

3. Make a get request to the clients show() route (/clients/{id})

Hmm, we haven’t done this before have we? Well we’re going to use a new test method which will help us make a get request of any url on our project. As luck would have it the method name is `get()`, so we can call this method just like we did the post() method. We’re also going to store that method in a variable called `$response`.

For this call, we also want to show one specific client in our clients collection, so I’m going to capture that in a variable called $client.

Add these lines at the end of the method right after our assertTrue() method.

```php
$client = $clients->first();  
$response = $this->get('/clients/'.$client->id);
```

4. Verify we get a status code of 200 on that response

We’ve done this too. We just have to add an assertStatus(200) on the $response to verify it’s 200.

Add this line after our get() request.

```php
$response->assertStatus(200);
```

Let’s run our test real quick:

```Bash
$ sail artisan test
```

![Screen Shot 2021-12-15 at 5.46.00 PM.png](S3-10:%20Project%20Setup%20-%20Client%20(Part%203).assets/Screen%20Shot%202021-12-15%20at%205.46.00%20PM.png)

We get a fail, this is because our route isn’t created yet. We’re going to need to create a route before this part of our test will pass.

At this point, we could update our route and test again, then finish the fifth item on our list for our `test_user_can_view_a_client()`. But since we only have one more thing to test, we're going to add it then move on to the route and controller work.

We need to see if some user information exists on the view template after making this get request. If all goes well, we’d see something like the client name or code. There is a testing method which does just this kind of thing for us. It will parse through a view and check the page for any content we provide. The test method is called `assertSee()` and it takes only one argument the string we’re looking for on the page. In our case, we'll test against the client code.

The line looks like this:

```php
$response->assertSee($client->code);
```

For review the entire `test_user_can_view_a_client()` method will look like this;

```php
public function test_user_can_view_a_client()
{ 
  $response = $this->post('/clients', ['name' => 'ABC Company', 'code' => 'ABCCO']);
  $clients = Client::all();
  
  $this->assertTrue($clients->count() == 1);
    
  $client = $clients->first();  
  $response = $this->get('/clients/'.$client->id);
  $response->assertStatus(200);
  $response->assertSee($client->code);
}
```

That’s our completed test. Let’s run our tests again to see what we get.

```other
$ sail artisan test
```

![Screen Shot 2021-12-15 at 5.58.11 PM.png](S3-10:%20Project%20Setup%20-%20Client%20(Part%203).assets/Screen%20Shot%202021-12-15%20at%205.58.11%20PM.png)

Same error, but we expected that.

Our todo list is looking like this:

1. > ~~Finish the `user_can_view_a_client()`~~
2. > Create a route to handle the client view request
3. > Update the `show()` method of our ClientController to return a view to the user when they make the their request
4. > Create a placeholder view template to display client details

Let’s tackle number 2 on our to-do list.

2. Create a route to handle the client view request

Open our routes file (`web.php)` and let’s add our route. We want to add a route that is looking for a `get request`, it is going to need a client id passed to it, and it should use the `@show` method on the `ClientController`.

Here’s the line of code you should add at the bottom of your `web.php` routes file.

```php
Route::get('/clients/{client}','App\Http\Controllers\ClientController@show');
```

![Screen Shot 2021-12-15 at 6.03.18 PM.png](S3-10:%20Project%20Setup%20-%20Client%20(Part%203).assets/Screen%20Shot%202021-12-15%20at%206.03.18%20PM.png)

Run your test

```Bash
$ sail artisan test
```

![Screen Shot 2021-12-15 at 6.04.03 PM.png](S3-10:%20Project%20Setup%20-%20Client%20(Part%203).assets/Screen%20Shot%202021-12-15%20at%206.04.03%20PM.png)

Okay cool, looks like our error is now that it cannot see the content we’re asserting that should be on our view. That’s okay, we don’t have the `ClientController` `show()` method done, let’s do that now.

Open up the ClientController file (/app/Http/Controllers/) and look for the show() method.

It should look like this:

```php
public function show($id)
```

We want to make life easy on us, and swap out the `$id` for an actual client using `Route Model Binding` like we did for the other methods in this file.  Replace $id with a variable called $client that is cast as Client.

```php
public function show(Client $client) { ... }
```

Now our show() method will come with the `client object` we want to show.

Showing couldn’t be easier, we just need to return a view, and there’s a method Laravel gives us to do just that. It’s called `view()`. It takes two arguments, one the view we want to serve up, and the data we want to pass to the view to use. There’s a convenience method we’ll use called `compact()`, which takes an array of strings that match the names of the variables we want to pass.

Put together the one and only line we need to add to our `show()` method looks like this:

```php
return view('clients.show',compact('client'));
```

The entire show() method looks like this:

```php
public function show(Client $client)
{
  return view('clients.show',compact('client'));
}
```

Run your test

```Bash
$ sail artisan test
```

![Screen Shot 2021-12-16 at 2.06.17 PM.png](S3-10:%20Project%20Setup%20-%20Client%20(Part%203).assets/Screen%20Shot%202021-12-16%20at%202.06.17%20PM.png)

You can see at the very top of the error message we did not get a 200 response, we got a 500 and the exception was that `clients.view` was not found. This is because we haven't actually created our view yet, so the error is correct.

The expected root folder for all our views is in `/resources/views` and we’re using dot notation for our the name of our view so it needs to be in a folder called `clients` and named `show.blade.php`. The full path will be `/resources/views/clients/show.blade.php.`

Create a folder named `clients` in the `resources/views` folder

You can do so by clicking on the folder icon when hovering over the file inspector while having the `views` folder selected.

![Screen Shot 2021-12-16 at 2.12.24 PM.png](S3-10:%20Project%20Setup%20-%20Client%20(Part%203).assets/Screen%20Shot%202021-12-16%20at%202.12.24%20PM.png)

![Screen Shot 2021-12-16 at 2.14.02 PM.png](S3-10:%20Project%20Setup%20-%20Client%20(Part%203).assets/Screen%20Shot%202021-12-16%20at%202.14.02%20PM.png)

![Screen Shot 2021-12-16 at 2.14.12 PM.png](S3-10:%20Project%20Setup%20-%20Client%20(Part%203).assets/Screen%20Shot%202021-12-16%20at%202.14.12%20PM.png)

While that folder is still selected, click on the `add file` icon to add a new file and name it `show.blade.php`.

![Screen Shot 2021-12-16 at 2.14.53 PM.png](S3-10:%20Project%20Setup%20-%20Client%20(Part%203).assets/Screen%20Shot%202021-12-16%20at%202.14.53%20PM.png)

![Screen Shot 2021-12-16 at 2.14.58 PM.png](S3-10:%20Project%20Setup%20-%20Client%20(Part%203).assets/Screen%20Shot%202021-12-16%20at%202.14.58%20PM.png)

![Screen Shot 2021-12-16 at 2.16.44 PM.png](S3-10:%20Project%20Setup%20-%20Client%20(Part%203).assets/Screen%20Shot%202021-12-16%20at%202.16.44%20PM.png)

---

### A quick word about Blade templates

Blade is the templating language we use in all of our Laravel projects. You can think of it as a really smart HTML file. It uses its own syntax to generate PHP code that gets used in our templates, which, at the end of the parsing process, results in HTML code. The idea is that the people writing the template code may not want to write PHP and Blade make their life easier while still offering quite a bit of power under the hood.

You can do anything in Blade that you can do in PHP, however the syntax is a bit different.

In PHP, for example we might echo a line of text like this:

```php
<?php echo "my line of text"; ?>
```

Where as in Blade that same line would look like this:

```php
{{ "my line of text" }}
```

We can echo anything using Blade syntax, including variables and objects.

```php
{{ $myObject->name }}
```

We can also, inject Blade code in our HTML code as well.

```html
<h1>{{ $myObject->name }}</h1>
```

Conditionals look like this in Blade:

```html
@if ($myVar == 1)
  <h2>Some HTML</h2>
@endif
```

You can see the `{` and `}` brackets are replaced with the `@if` and `@endif` entirely.

We’ll learn much much more about Blade when we build our front-end, but for now this bit of a primer will get us what we need to wrap up our work.

---

### Finish our show template

For now, we’re going to just build a basic (and I do mean basic) HTML template that shows us the client name and code that is passed to it by the ClientController.

If you remember the ClientController is compacting the $client variable, so that’s the name of the object we have to use in our template. We’ll use the `{{` and `}}` brackets to echo some values from our client object.

Here’s the finished template:

```html
<html>
  <head>
    <title>Client Detail Screen</title>
  </head>
  <body>
    <p>Name: {{ $client->name }}</p>
    <p>Code: {{ $client->code }}</p>
  </body>
</html>
```

![Screen Shot 2021-12-16 at 2.51.53 PM.png](S3-10:%20Project%20Setup%20-%20Client%20(Part%203).assets/Screen%20Shot%202021-12-16%20at%202.51.53%20PM.png)

So with this view template in it’s proper place and echoing out the client name and code on the template, our tests should pass:

```other
$ sail artisan test
```

![Screen Shot 2021-12-16 at 2.54.39 PM.png](S3-10:%20Project%20Setup%20-%20Client%20(Part%203).assets/Screen%20Shot%202021-12-16%20at%202.54.39%20PM.png)

If you want to see what a assertSee() test assertion failure looks like, just remove lines 6 and 7 from the show.blade.php template file.

![Screen Shot 2021-12-16 at 2.55.49 PM.png](S3-10:%20Project%20Setup%20-%20Client%20(Part%203).assets/Screen%20Shot%202021-12-16%20at%202.55.49%20PM.png)

Then run your tests again.

```Bash
$ sail artisan test
```

![Screen Shot 2021-12-16 at 2.56.28 PM.png](S3-10:%20Project%20Setup%20-%20Client%20(Part%203).assets/Screen%20Shot%202021-12-16%20at%202.56.28%20PM.png)

You’ll see that the HTML it’s checking does not contain “ABCCO” which is the client code we’re checking on. The error message also outputs the HTML it’s checking against so you can see for yourself why the test failed.

Don’t forget to replace those lines of code, save your work and run your test to verify we’re still all good.

```Bash
$ sail artisan test
```

![Screen Shot 2021-12-16 at 2.59.00 PM.png](S3-10:%20Project%20Setup%20-%20Client%20(Part%203).assets/Screen%20Shot%202021-12-16%20at%202.59.00%20PM.png)

---

### Finish your work session

You can follow the steps outlined in [[Workflow: Starting and Ending Your Work Session]] if you need a refresher.

> I used the following commit message if you are looking for a suggestion: `"Adds test, route, and view for show() method for ClientController"`

---

