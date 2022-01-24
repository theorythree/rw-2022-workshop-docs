# S3-12: Clients Index

I’d like to one more thing with clients before we move on to something else, so let’s start up a work session and I’ll provide the goal when you come back.

---

### Start your work session

Using the startup steps here: [[Workflow: Starting and Ending Your Work Session]]

---

### Goal

The goal of this session would be to create a test, a route, update our ClientController, and create a dummy view for a clients index page.

This page will show a listing of ALL our clients in the database and provide a link for the user to click on to be taken to the client detail page we created in the pervious session.

As always, we’ll start with a test.

1. Open the ClientTest.php file

> While we’re here, let’s do a little project code cleanup. Laravel comes with some example tests in the Feature folder and in the Unit folder, let’s delete them. They’re both called ExampleTest.php, so be sure to only delete those two files.

![Screen Shot 2021-12-16 at 4.25.51 PM.png](S3-12:%20Clients%20Index.assets/Screen%20Shot%202021-12-16%20at%204.25.51%20PM.png)

> You can delete them by right clicking on them and selecting `delete` from the popup menu

![Screen Shot 2021-12-16 at 4.28.30 PM.png](S3-12:%20Clients%20Index.assets/Screen%20Shot%202021-12-16%20at%204.28.30%20PM.png)

![Screen Shot 2021-12-16 at 4.27.36 PM.png](S3-12:%20Clients%20Index.assets/Screen%20Shot%202021-12-16%20at%204.27.36%20PM.png)

With that little business out of the way, let’s get back to our ClientTest

We want to make a simple test that creates a couple of clients and then we can request the client index page and make sure they show up.

2. Create a new test method called `test_user_can_view_client_index_page()`
3. Put this method after the `test_user_can_view_a_client()` method in the ClientTest file

```php
public function test_user_can_create_a_client() { ... }
public function test_user_can_view_a_client()

public function test_user_can_view_client_index_page() { ... }

public function test_user_can_update_a_client() { ... }
...
```

![Screen Shot 2021-12-16 at 4.36.00 PM.png](S3-12:%20Clients%20Index.assets/Screen%20Shot%202021-12-16%20at%204.36.00%20PM.png)

Our test needs to create a bunch of clients. We could create them all one at a time like we did in our previous test, but there’s actually a better way.

We can use a class Laravel has for just this use case. It’s called Factory, and Laravel already built us one for users. Let’s take a quick peek.

4. Open the user factory file /database/factories/UserFactory.php

![Screen Shot 2021-12-16 at 4.40.12 PM.png](S3-12:%20Clients%20Index.assets/Screen%20Shot%202021-12-16%20at%204.40.12%20PM.png)

This is a special class which has one responsibility to build up batches (or single records) of test data for us to use in our tests. The one we’re currently looking at, UserFactory is meant to work with the User model specifically. It’s also using faker to automatically generate some of the data for us.

You can see a full list of the things Faker can fake here: [https://github.com/fzaninotto/Faker](https://github.com/fzaninotto/Faker)

Let’s create our `ClientFactory` file and use Faker to make some dummy data for our clients and use that factory in our `test_user_can_view_client_index_page()` test method.

Laravel has an artisan command to make Faker files, and they must be named the same as our model with the word Faker after it. Therefore, our Faker file will be called `ClientFactory`.

In the Visual Studio terminal issue the following command.

```Bash
$ sail artisan make:factory ClientFactory
```

![Screen Shot 2021-12-16 at 4.48.24 PM.png](S3-12:%20Clients%20Index.assets/Screen%20Shot%202021-12-16%20at%204.48.24%20PM.png)

> Artisan builds up a file and stores it in the appropriate directory /database/factories/

5. Open the ClientFactory file so we can create a factory for the tests of our clients

![Screen Shot 2021-12-16 at 4.50.15 PM.png](S3-12:%20Clients%20Index.assets/Screen%20Shot%202021-12-16%20at%204.50.15%20PM.png)

We can use the UserFactory as a guide to create ours.

Our return should match the database columns in our database (at least the required ones for sure).

- > name
- > code
- > phone
- > address

Our factory `return` array will look like this:

```php
return [
  "name" => $this->faker->company(),
  "code" => Str::random(5),
  "phone" => $this->faker->phoneNumber(),
  "address" => $this->faker->streetAddress.', '.$this->faker->city.', '.$this->faker->stateAbbr.' '.$this->faker->postcode(),
];
```

Note, I’m using the string random() method which will produce a random string of any length we pass to it, so be sure to include it at the top of your code, the line is the same from the UserFactory:

```php
use Illuminate\Support\Str;
```

Here’s the full file for your reference:

![Screen Shot 2021-12-16 at 5.02.53 PM.png](S3-12:%20Clients%20Index.assets/Screen%20Shot%202021-12-16%20at%205.02.53%20PM.png)

6. That's all we need for our factory, so let's head back to our ClientTest file and use the factory to create multiple clients with one line of code.
7. Create multiple clients in our test database using the ClientFactory we just created.

```php
$clients = Client::factory(3)->create();
```

You can see the usage is quite simple. We start with our model, Client, then call the factory() method and pass the number of clients we want (we’ll start with just three for now), then we issue the create() command to create the clients. Pretty simple, we didn’t even need to tell our test method where to find our factory file, it just knows.

We haven’t made an assertion quite yet, but we can run our test to make sure we don’t have any errors with our Factory or Faker.

```Bash
$ sail artisan test
```

![Screen Shot 2021-12-16 at 5.16.22 PM.png](S3-12:%20Clients%20Index.assets/Screen%20Shot%202021-12-16%20at%205.16.22%20PM.png)

As expected, our test isn’t asserting anything. We can test to make sure we have three records, by repeating the tests we have in our previous test methods.

8. Add these lines under our first line:

```php
$clients = Client::all();
    
$this->assertTrue($clients->count() == 3);
```

![Screen Shot 2021-12-16 at 5.18.31 PM.png](S3-12:%20Clients%20Index.assets/Screen%20Shot%202021-12-16%20at%205.18.31%20PM.png)

> Notice, I collapsed the other methods so we can focus on the test we’re working on. I do this quite a bit to avoid distraction.

All these lines of code do is get all the clients from the database and assert that we have the same number. Pretty straight forward.

Let’s run our test now

```Bash
$ sail artisan test
```

![Screen Shot 2021-12-16 at 5.20.34 PM.png](S3-12:%20Clients%20Index.assets/Screen%20Shot%202021-12-16%20at%205.20.34%20PM.png)

All green, but not done!

Now that we have three clients built up in our test, we want to visit the clients page and look for some client information.

9. Add a get request to the url /clients and check for a response of 200 by putting these two lines of code after our `assertStatus()` line.

```php
$response = $this->get('/clients');
$response->assertStatus(200);
```

10. Run the tests

```Bash
$ sail artisan test
```

![Screen Shot 2021-12-16 at 5.25.12 PM.png](S3-12:%20Clients%20Index.assets/Screen%20Shot%202021-12-16%20at%205.25.12%20PM.png)

This was expected, we don’t have a route and we have not updated our controller or created a view. Let’s do all of that in succession before we run another test.

> At this point, we do not have to run tests at every step along the way. We know now that we have to make a route, then update our controller, then create a view. We’ve done this before and we can run our test at any time to verify all is well if we have any doubts.

11. Open the web.php route file and add a new route under the other routes

```php
Route::get('/clients','App\Http\Controllers\ClientController@index');
```

![Screen Shot 2021-12-16 at 5.29.52 PM.png](S3-12:%20Clients%20Index.assets/Screen%20Shot%202021-12-16%20at%205.29.52%20PM.png)

12. Open the ClientController file and find the index() method that has been stubbed out for us.

![Screen Shot 2021-12-16 at 5.31.07 PM.png](S3-12:%20Clients%20Index.assets/Screen%20Shot%202021-12-16%20at%205.31.07%20PM.png)

All we’re going to do here is find all the client records and pass them to a view we’re going to create.

13. Add a query to fetch all the clients and store them in a variable called `$clients`

   We’ve done this query in our test methods a couple of times.

```php
$clients = Client::all();
```

After that, we can return our view and compact the $clients variable and pass it to the view to do something with it.

```php
return view('clients.index',compact('clients'));
```

This is nearly identical to the `show()` method return, we’re just updating the view and changing `client` to `clients` for the variable name.

The whole index() method should look like this when you’re done:

```php
public function index()
{
  $clients = Client::all();
  return view('clients.index',compact('clients'));
}
```

Our controller is returning a view clients.index, which should be a file with the full path named: `/resources/views/clients/index.blade.php`. We do not have that file, so let’s create it file now.

![Screen Shot 2021-12-16 at 5.39.33 PM.png](S3-12:%20Clients%20Index.assets/Screen%20Shot%202021-12-16%20at%205.39.33%20PM.png)

![Screen Shot 2021-12-16 at 5.39.53 PM.png](S3-12:%20Clients%20Index.assets/Screen%20Shot%202021-12-16%20at%205.39.53%20PM.png)

![Screen Shot 2021-12-16 at 5.39.59 PM.png](S3-12:%20Clients%20Index.assets/Screen%20Shot%202021-12-16%20at%205.39.59%20PM.png)

14. Copy and paste the contents of the `show.blade.php` file into the `index.blade.php` file so we can modify it to work for our needs.

![Screen Shot 2021-12-16 at 5.41.26 PM.png](S3-12:%20Clients%20Index.assets/Screen%20Shot%202021-12-16%20at%205.41.26%20PM.png)

15. Starting at the top, let’s change the title to: `Client Index Screen`

So the rest of the code was written for one client, but our controller is going to be passing us a collection of clients in a variable called $clients, so what do we do?

We need to loop through each of the `clients` stored in `$clients` variable and output the information.

We can do this with a `foreach loop`. Because we’re in a Blade template the syntax looks something like this:

```php
@foreach($clients as $client)
...
@endforeach
```

> Note, that’s not a typo on my part, there are no open `{` or closing `}` brackets for the foreach method in Blade code.

This works just like any other foreach loop in PHP, it will take each of the clients stored in the $clients variable and assign it to $client so we can access the individual client information. In fact, our code already uses a variable called $client. Another reason to adhere to standard variable names so we can predict what may come.

16. Wrap the tow lines of code for the client in the foreach() method above.
17. Do a small amount of reformatting to make each client appear on one line

The full page will look like this when you’re done:

```php
<html>
  <head>
    <title>Client Index Screen</title>
  </head>
  <body>
    @foreach($clients as $client)
      <p>Name: {{ $client->name }} | Code: {{ $client->code }}</p>
    @endforeach
  </body>
</html>
```

![Screen Shot 2021-12-16 at 5.53.17 PM.png](S3-12:%20Clients%20Index.assets/Screen%20Shot%202021-12-16%20at%205.53.17%20PM.png)

18. Run our tests to see that oh lovely color green!

```Bash
$ sail artisan test
```

![Screen Shot 2021-12-16 at 5.54.35 PM.png](S3-12:%20Clients%20Index.assets/Screen%20Shot%202021-12-16%20at%205.54.35%20PM.png)

Now, I’m not quite satisfied with our test. It’s passing, but it’s not actually checking the content that is displayed on the page. Let’s fix that now.

19. In the `test_user_can_view_client_index_page()` test method inClientTest let's check for the first and last client to make sure some of their data is displayed.
20. Add these four lines to the bottom of the test_user_can_view_client_index_page()

```php
$firstClient = $clients->first();
$lastClient = $clients->last();

$response->assertSee($firstClient->code);
$response->assertSee($lastClient->code);
```

You can see I’m just capturing first and last records using the first() and last() method on the clients collection. Then I added two assertions to check for their codes whatever our factory set them to when they were created.

21. Go for another green by running the tests

```Bash
$ sail artisan test
```

![Screen Shot 2021-12-16 at 6.02.31 PM.png](S3-12:%20Clients%20Index.assets/Screen%20Shot%202021-12-16%20at%206.02.31%20PM.png)

You can also check the fruits of our labor by going to the following web address in your browser:

`localhost/clients`

![Screen Shot 2021-12-16 at 6.04.19 PM.png](S3-12:%20Clients%20Index.assets/Screen%20Shot%202021-12-16%20at%206.04.19%20PM.png)

There’s just one more thing I wanted to do on this page, now that I take a look at it. I wanted to link each client listing to the client detail page.

Let’s finish strong by doing that now.

22. Open the /resources/views/clients/index.blade.php file and adding a link to the listing.

Here’s the link code:

```html
<a href="/clients/{{ $client->id }}">View</a>
```

And here’s how it all looks together in the file:

![Screen Shot 2021-12-16 at 6.07.58 PM.png](S3-12:%20Clients%20Index.assets/Screen%20Shot%202021-12-16%20at%206.07.58%20PM.png)

You can see I just added the {{ $client->id }} after the /clients/ in the href.

23. Refresh your browser

![Screen Shot 2021-12-16 at 6.09.37 PM.png](S3-12:%20Clients%20Index.assets/Screen%20Shot%202021-12-16%20at%206.09.37%20PM.png)

24. And lastly, click on the `View` link to see if it works.

![Screen Shot 2021-12-16 at 6.10.17 PM.png](S3-12:%20Clients%20Index.assets/Screen%20Shot%202021-12-16%20at%206.10.17%20PM.png)

With passing tests, we’re safe to end our work session following the steps in [[Workflow: Starting and Ending Your Work Session]]

Great work today!

