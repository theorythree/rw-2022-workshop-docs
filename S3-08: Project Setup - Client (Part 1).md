# S3-08: Project Setup - Client (Part 1)

Now that our testing is all setup, and we have our first commit under our belts, we're going to pick one of our Models (other than User) and build a test for it. Along the way we’ll create the routes, Model, and Controller so that our test passes.

> The reason we don’t use User as our first test is that Laravel already created some of that model, and supporting code for us when the project was create and I want to avoid the confusion of what Laravel created and the work that we will be doing.

Let’s start with something simple. The `Client` model seems like a good place to start.

Remember in our project plan we had the following details for `Client` :

- > Client - Attributes
   - > id (int | unique)
   - > name (string)
   - > code (string)
   - > address (text | optional)
   - > phone (string | optional)
   - > created_at (date time)
   - > updated_at (date time)

> Client - Relationships

   - > can have multiple users working for them
   - > can have multiple projects
   - > can appear on multiple time entries
   - > can have multiple estimates
   - > can have multiple invoices

> Client - Controller Type

   - > Clients (CRUD)

But before we do any work, let’s make sure we follow that workflow we’ve been talking oh so much about and make sure we have pulled down any changes someone may have made on our project code. ::Yes we even do this step when we are 100% certain no one has contributed to our project, because it's good practice.::

1. Open the Tempo project in Visual Studio Code, then open up a terminal and type the following command.

```Bash
$ git pull
```

![Screen Shot 2021-12-12 at 12.45.17 PM.png](S3-08:%20Project%20Setup%20-%20Client%20(Part%201).assets/Screen%20Shot%202021-12-12%20at%2012.45.17%20PM.png)

We’re already up to date, so we can begin our work. Let’s go!

We’re going to build our test around these details about `Client`.

Laravel makes creating models, controllers, and tests files easy. We’re going to use a command called `artisan` which will make our file and save it to the appropriate directory in our project. Since we're using `Docker`, we're going to prefix the `artisan` command with the `sail` command that we’ve been using.

> Using the sail (proxy) command, is like telling `Sail` to issue whatever proceeds it on the Docker container and not any software that may or may not be installed elsewhere on your computer. In this case, we’re telling `Sail` to issue the `artisan` command for us.

1. Issue the following command in the Terminal pane of Visual Studio Code

```Bash
$ sail artisan make:test ClientTest
```

> If we were not using Docker, we would issue the same command as such: `$ php artisan make:test ClientTest`. The result would be the same, but the command would rely on your version of PHP and supporting technologies. So if you're following along without a Docker contained project, just replace `sail` henceforth with `php` and you'll be all set.

![Screen Shot 2021-12-08 at 5.09.22 AM.png](S3-08:%20Project%20Setup%20-%20Client%20(Part%201).assets/Screen%20Shot%202021-12-08%20at%205.09.22%20AM.png)

You’ll notice a new file called `ClientTest` was created in the `tests/Feature` directory.

2. Open the ClientTest file

![Screen Shot 2021-12-08 at 5.11.17 AM.png](S3-08:%20Project%20Setup%20-%20Client%20(Part%201).assets/Screen%20Shot%202021-12-08%20at%205.11.17%20AM.png)

Our Laravel project comes with a stubbed out test for us to use to make sure our testing setup is working correctly. Let’s run this test to be sure everything is okay.

3. Run the following command in the terminal

```Bash
$ sail artisan test
```

You should see that three tests were run and they should have all passed. This means our project is now properly setup for testing.

![Screen Shot 2021-12-08 at 5.16.58 AM.png](S3-08:%20Project%20Setup%20-%20Client%20(Part%201).assets/Screen%20Shot%202021-12-08%20at%205.16.58%20AM.png)

> You may notice we’re moving a bit faster. From this point forward, I am going to assume you know how to open and close the Terminal window in Visual Studio. You may also notice that the Terminal pane in my screenshot takes over the entire right side of the window. I did this by clicking on the `^` right next to the `X` in the upper-right corner of the terminal window pane.

4. Close the terminal pane and return to the ClientTest file so we can edit it.
5. Remove the test_example() method and it’s comment above so we can create our own test case.

![Screen Shot 2021-12-08 at 5.26.37 AM.png](S3-08:%20Project%20Setup%20-%20Client%20(Part%201).assets/Screen%20Shot%202021-12-08%20at%205.26.37%20AM.png)

### What do we want to test?

Let’s take a quick moment and discuss what we actually want to test. I’d like to test against the controller functionality. In other words, we want a set of tests that test `creating`, `updating`, `editing`, and `deleting` a client.

### Let's write our first test

All test functions start with the key word `test`. Since we’re going to be writing a lot of these tests, the  names should be named as descriptively as possible. I also want to use the proper naming conventions for routing names, controller names, and model names  that we briefly discussed.

I want to test the creating functionality of our Client controller (which we have not even created yet). I’m going to name the test function `test_user_can_create_a_client()`  this seems pretty descriptive, this test should check whether or not we can create a new client in the clients database table. Simple.

> We will always use test_ as the first word of all our tests. Doing to will tell the testing software that this is a test method and it should run it.

1. Create a new test case called `test_user_can_create_a_client()` in the ClientTest file using the following code.

```php
public function test_user_can_create_a_client()
{
    
}
```

   > We’re going to use Snake Case for all our testing methods. Because our method names are going to be long, Snake Case allows for more readable method names. You could also use Lower Camel Case and your tests would run just fine as long as test is the first word of your method name.

   Your file should look exactly like this:

![Screen Shot 2021-12-08 at 5.40.58 AM.png](S3-08:%20Project%20Setup%20-%20Client%20(Part%201).assets/Screen%20Shot%202021-12-08%20at%205.40.58%20AM.png)

The body of our method still needs to be created, so let’s do that now.

For each test we write, we have some setup, then we must include an `assertion statement`. In our case, we’re going to create a client, store them in the database, and assert that indeed this record exists in the database.

I like to write these steps out in comments as I work. These comments help remind me of the work I need to do to write my test. Here’s an example:

![Screen Shot 2021-12-08 at 5.45.29 AM.png](S3-08:%20Project%20Setup%20-%20Client%20(Part%201).assets/Screen%20Shot%202021-12-08%20at%205.45.29%20AM.png)

To create a client, we need to do two steps in this file.

1. Import our Client model into our code
2. Create a new Client model by posting some data to our Client controller

Let’s get started

1. Add the following line just below the other use statements (line 8 in my screenshot). This line is responsible for adding the Client model (you can tell just by the file path).

```php
use App\Models\Client;
```

![Screen Shot 2021-12-08 at 5.50.56 AM.png](S3-08:%20Project%20Setup%20-%20Client%20(Part%201).assets/Screen%20Shot%202021-12-08%20at%205.50.56%20AM.png)

Let’s run our tests to see if adding these new lines of code will affect our test results (hint, it will)

```other
$ sail artisan test
```

![Screen Shot 2021-12-08 at 5.53.53 AM.png](S3-08:%20Project%20Setup%20-%20Client%20(Part%201).assets/Screen%20Shot%202021-12-08%20at%205.53.53%20AM.png)

You’ll notice that we got a warning (yellow) on our ClientTest. The warning is telling us that the test we just created, doesn’t actually test anything. It’s all setup and no assertions have been made yet. Let’s fix that with a quick test that will pass.

1. Update our `test_user_can_create_a_client()` method by Inserting `$this->assertTrue(true);` in the method body. (line 14 in my screenshot)

![Screen Shot 2021-12-08 at 5.56.51 AM.png](S3-08:%20Project%20Setup%20-%20Client%20(Part%201).assets/Screen%20Shot%202021-12-08%20at%205.56.51%20AM.png)

2. Rerun our test

```other
$ sail artisan test
```

You should see all green again.

![Screen Shot 2021-12-08 at 5.59.42 AM.png](S3-08:%20Project%20Setup%20-%20Client%20(Part%201).assets/Screen%20Shot%202021-12-08%20at%205.59.42%20AM.png)

This test now passes without a warning because we updated our test to pass by asserting that true equals true. Not a very helpful test, but that’s okay, we just ran our first testing cycle. We ran the test, got a warning, and fixed that warning.

We know true equals true, so let’s make our test achieve its purpose of testing the creation of a client in the database.

1. Remove the `$this->assertTrue(true)` line of code in our file

![Screen Shot 2021-12-08 at 5.50.56 AM.png](S3-08:%20Project%20Setup%20-%20Client%20(Part%201).assets/Screen%20Shot%202021-12-08%20at%205.50.56%20AM%20(2).png)

Back to our checklist

1. ~~Import our Client model into our code~~
2. Create a new Client model by posting some data to our Client controller

Posting data with tests is very easy. We just have to create a `post` request and pass along the `URL` we want to post to and the data array we want to submit. The post request will return a response which we will store in a variable called `$response`. You can think of this as filling out a form and submitting it, but without the need for the actual form.

Add the following line to the `test_user_can_create_a_client()` test body.

```php
// create a new client
$response = $this->post('/clients', ['name' => 'ABC Company', 'code' => 'ABCCO']);
```

This is just some dummy data that we can use to create a client. Our clients database table requires only two things; name and code, so that’s all we’re providing now.

You may notice that we’re using the `$this→` to call the `post()` method. `$this→` will always refer to the class that contains that line of code. In our case our class name is `ClientTest`, but it extends another class called `TestCase`, that class contains the `post()` method.

The whole function will look like this:

![Screen Shot 2021-12-08 at 6.18.39 AM.png](S3-08:%20Project%20Setup%20-%20Client%20(Part%201).assets/Screen%20Shot%202021-12-08%20at%206.18.39%20AM.png)

3. Run our tests again and we should get our first real error.

```other
$ sail artisan test
```

![Screen Shot 2021-12-08 at 6.19.39 AM.png](S3-08:%20Project%20Setup%20-%20Client%20(Part%201).assets/Screen%20Shot%202021-12-08%20at%206.19.39%20AM.png)

From this point forward, our test results will tell us what to do. This is what it means to be test-driven.

If we look at our error message, it says our error is in the ClientTest file (we knew that), and that the Class `“App\Models\Client`” could not be found. That’s because we haven’t created it. Let’s do that now.

1. Issue the following artisan command to create a new Model
```php
$ sail artisan make:model Client
```

We’re telling artisan to make us a model called `Client`. Remember, we want to keep this singular. You can see that the message returned is `Model created successfully` and the file was created in the appropriate directory `app/Models/` , which is the same file path we referred to in our test file.

![Screen Shot 2021-12-08 at 6.26.11 AM.png](S3-08:%20Project%20Setup%20-%20Client%20(Part%201).assets/Screen%20Shot%202021-12-08%20at%206.26.11%20AM.png)

3. Let’s run our tests again

```other
$ sail artisan test
```

![Screen Shot 2021-12-08 at 6.28.52 AM.png](S3-08:%20Project%20Setup%20-%20Client%20(Part%201).assets/Screen%20Shot%202021-12-08%20at%206.28.52%20AM.png)

Okay, back to that same warning we got before. This time we’re not going to fake our assertion.

Moving on to the next step, `save that client to the database,` that step is actually going to be handled by our controller, which we have not created yet. When we do, our test won’t have to do anything to make the actual save happen.

1. Add a new assertion that will check for one client record in our database.

```php
// assert that the client exists in the database
$this->assertTrue(Client::all()->count() == 1);
```

2. Run our tests

```other
$ sail artisan test
```

![Screen Shot 2021-12-08 at 7.18.44 AM.png](S3-08:%20Project%20Setup%20-%20Client%20(Part%201).assets/Screen%20Shot%202021-12-08%20at%207.18.44%20AM.png)

Great, we expected that this test would fail and it did just that. The error message we got was: `SQLSTATE[HY000]: General error: 1 no such table: clients (SQL: select * from "clients")`

This seems to be telling us there’s something wrong with the the database. There’s no table called `clients`. We can expect that because we haven’t created a database table called `clients` yet.

We need to create our `clients` database table. And for that, we’re going to create a `migration file` to automatically create the table with the proper fields.

Laravel has another artisan command to help us create the migration file we need.

```Bash
$ sail artisan make:migration create_clients_table
```

> This command makes a migration file called `create_clients_table`. There are a whole set of artisan commands, the ones we need to make our files usually start with `make`, then `:` and the kind of file we want to make, followed by the name of the file.

> For this, and all our migrations files, we are using snake case. Which is just a fancy way of saying all lower case and `_` instead of spaces between the words.

![Screen Shot 2021-12-08 at 7.39.58 AM.png](S3-08:%20Project%20Setup%20-%20Client%20(Part%201).assets/Screen%20Shot%202021-12-08%20at%207.39.58%20AM.png)

All our migrations are stored in the `/database/migrations` folder. You’ll see the one we created at the bottom of some migrations that Laravel included in our project when it setup it up for us.

1. Open the new migration file (note, my migration will be named slightly different than yours because the migration files are prepended with a date timestamp).

![Screen Shot 2021-12-08 at 7.40.26 AM.png](S3-08:%20Project%20Setup%20-%20Client%20(Part%201).assets/Screen%20Shot%202021-12-08%20at%207.40.26%20AM.png)

> All migration files have two methods; one for creating (or updating) a database table called `up()` and another for reverting our changes to the table called `down()`.

> You’ll notice that Laravel already put some of our attributes in the `up()` method.

```php
public function up()
{
  Schema::create('clients', function (Blueprint $table) {
    $table->id();
    $table->timestamps();
  });
}
```

And the `down()` method seems to have something already in it too.

```php
public function down()
{
  Schema::dropIfExists('clients');
}
```

In fact the `down()` method is already done for us, it will drop the table when the teardown of our database occurs. Let's focus on the `up()` method, which actually creates our database table.

The two lines of code we’re interested in are in the up() method.

```php
$table->id();
$table->timestamps();
```

The first one is will setup our `id` column. We want all of our tables to have it.

The second one will actually setup our two date columns `created_at` and `updated_at`. They will both be date timestamps. We want to keep that line as well.

Let’s review the attributes we planned discussed earlier, we should have columns for each of the attributes we planned out.

- > ~~id (int | unique)~~
- > name (string)
- > code (string)
- > address (text | optional)
- > phone (string | optional)
- > ~~created_at (date time)~~
- > ~~updated_at (date time)~~

It looks like all we have to do is create the `name`, `code`, `address` ,and `phone` columns. Let’s do that now.

The syntax we’re going to use is the similar to the existing lines. We’re going to use the `$table→` to setup each column field.  We'll follow that by the `type` of data the column in our database will store. I have that indicated in parentheses (i.e. string). Then we will pass the column name to the method that matches our data type. Finally, we will append the command with any options we might want to pass along as well.

So the new code we want to add to our migration file will look like this:

```php
$table->string('name');
$table->string('code');
$table->string('phone')->nullable();
$table->text('address')->nullable();
```

> By default all columns are required, to make them optional, we add the `->nullable()` option to the field. By doing so we're telling our database that it is okay to have a record without a value for this column.

1. Add the new code block to the `up()` method in our migration file
2. Be sure to save your changes

The whole file will look like this when you’re done:

![Screen Shot 2021-12-08 at 10.47.11 AM.png](S3-08:%20Project%20Setup%20-%20Client%20(Part%201).assets/Screen%20Shot%202021-12-08%20at%2010.47.11%20AM.png)

> Please note, the order of these lines of codes matters. When the table is built, the columns will be created in the order they are defined in our file. Meaning the order will be; `id`, `name`, `code`, `phone`, `address`, `created_at`, `updated_at`.

> I always keep the id column as the first column, and the date columns as the last two. I then prefer to have any IDs after my id column, then strings, then text columns, and I like the `created_at` and `updated_at` columns to always be my last two columns.

> You’re database will work just fine regardless of the order of the columns, however, having this strategy to keep your database tables all organized in the same way will ensure that you can find the information you’re looking for more easily.

3. Run our test again

```other
$ sail artisan test
```

![Screen Shot 2021-12-08 at 8.56.27 AM.png](S3-08:%20Project%20Setup%20-%20Client%20(Part%201).assets/Screen%20Shot%202021-12-08%20at%208.56.27%20AM.png)

Our test still fails, and with the same error message as before. I expected as much. The reason is that our testing class isn’t actually creating the database quite yet. We need to add one line of code in our class to make that process happen.

Add this line just after the class open bracket `{` and before our first test case `test_user_can_create_client()`. You'll see this line on line 12 of my screenshot.

```php
use RefreshDatabase;
```

![Screen Shot 2021-12-08 at 9.01.56 AM.png](S3-08:%20Project%20Setup%20-%20Client%20(Part%201).assets/Screen%20Shot%202021-12-08%20at%209.01.56%20AM.png)

> `RefreshDatabase` is a class that helps us out by doing some very important setup for our tests. What it does is run all of our migration files (including the ones put there by default) which builds up our database tables and columns. The database will be completely empty for our tests. We’ll add the data we need to the database for that particular test, then, after our test has run, it will completely tear down and destroy the database and all of the data will be gone as well. This is so we always start every test with a fresh database with no content in it.

By using `RefreshDatabase` we should have a database created, let’s run our test again to see what the next error we get.

```other
$ sail artisan test
```

![Screen Shot 2021-12-08 at 9.10.21 AM.png](S3-08:%20Project%20Setup%20-%20Client%20(Part%201).assets/Screen%20Shot%202021-12-08%20at%209.10.21%20AM.png)

Great, now we’re getting somewhere. Take a peek at the latest error message: `Failed asserting that false is true.` The red arrow (line 22 on my screenshot) shows that the assertion we made is not passing. False isn't true. So let's look at the assertion line to explain why this error is happening.

```php
// assert that the client exists in the database
$this->assertTrue(Client::all()->count() == 1);
```

Everything in the `assertTrue()` is the test itself. It has to prove out to be true when it’s resolved. We’re using our `Client` model to fetch all the records from the clients table in our database `Client::all()`, then we’re getting the count `->count()` and saying that should equal one record `== 1` .

So the reason we’re not testing true is because there isn’t one record in the database. So we just learned that RefreshDatabase is building up our database properly because we are trying to fetch a record from the new table we created. So the problem is that we haven’t actually saved the information to the database and therefore we’re probably returning a zero count.

We can do a quick test to verify that we’re getting zero by changing our assertion line to this:

```php
// assert that the client exists in the database
$this->assertTrue(Client::all()->count() == 0);
```

> Notice we now are checking for a zero count, not a count of one. Let’s run our test.

```other
$ sail artisan test
```

![Screen Shot 2021-12-08 at 9.22.18 AM.png](S3-08:%20Project%20Setup%20-%20Client%20(Part%201).assets/Screen%20Shot%202021-12-08%20at%209.22.18%20AM.png)

Wow! We got our first passed test. Hold on there, we’ve just written our test to pass to test an assumption, but this test isn’t actually done because it doesn’t do what it says it does. It doesn’t actually create a client. So before we forget, let’s revert our changes and rerun our test to get back to a failed state.

1. Change the code back to 1

```php
// assert that the client exists in the database
$this->assertTrue(Client::all()->count() == 1);
```

2. Run our tests again

```other
$ sail artisan test
```

![Screen Shot 2021-12-08 at 9.25.18 AM.png](S3-08:%20Project%20Setup%20-%20Client%20(Part%201).assets/Screen%20Shot%202021-12-08%20at%209.25.18%20AM.png)

Okay that’s more like it, our test should fail at this point because we haven’t finished our work. Let’s press on!

We know our test is failing because the record is not actually getting saved to the database, so how do we do we save our record to the database?

The actual saving is going to happen in our controller which, using our naming convention, should be called `ClientController` which we haven’t made quite yet. Let’s do that now.

1. Issue the following command in the terminal

```php
$ sail artisan make:controller ClientController --resource
```

![Screen Shot 2021-12-08 at 9.33.10 AM.png](S3-08:%20Project%20Setup%20-%20Client%20(Part%201).assets/Screen%20Shot%202021-12-08%20at%209.33.10%20AM.png)

> Notice that Laravel created a new file named ClientController in the `/app/Http/Controllers` folder. We also used the `—resource` flag on this command. That flag tells Laravel to stub out our `CRUD` methods. Let's open the file to see what that looks like.

![Screen Shot 2021-12-08 at 9.35.14 AM.png](S3-08:%20Project%20Setup%20-%20Client%20(Part%201).assets/Screen%20Shot%202021-12-08%20at%209.35.14%20AM.png)

> If we left off the `—resource` Laravel would have created an empty controller. It still would have been named `ClientController`, but it would have none of the functions stubbed out for us. You might do that if, for some reason, you had a controller that wouldn't use these methods or if you wanted to add them yourself manually.

### Take a Quick Look at a Controller

Since this is our first real look at an actual controller, let’s look at each of the functions and explain what they do.

1. `public function index()`  This method would be used to list all the clients in the database.
2. `public function create()`
3. This method would be used to display the new client page (which would contain a form for the user to fill out the client information).
4. `public function store(Request $request)`
5. This method is used to handle the `post request` which has the client information and store it in the database. It could get the client information from the `create` page, or some other source as well.
6. `public function show($id)`
7. This method is used to show a specific client’s details on a client detail page.
8. `public function edit($id)`  This method is used to serve up an edit form for a specific client so a user could change their details.
9. `public function update(Request $request, $id)`  This method is used to handle the `put request` which has the updated client information and store it in the database. Much like the `create` + `store` methods, this works with its counterpart, the `edit` method. The only difference between the two sets, is that one is for creating a record, and the other is for modifying.
10. `public function destroy($id)`  This method will delete a specific client from the database.

If you read through those various methods, you could probably guess the one we’re interested in is the `store` method (item #4 above). That's what will actually store the data in the database.

Let’s work on that now.

You can see the store method looks like this:

```php
public function store(Request $request) { ... }
```

> You’ll notice it’s quite common to use the `{ … }` in a code sample, when we are not referring to anything within the method body. This shorthand is used for the entire method, when what we want to focus on is the method name itself.

The `store()` method takes a request of type `Request` and will pass that request using the variable `$request`, which is how we will refer to it within the method.

> It’s the job of our controller (specifically the `store` method in this controller) to take a request and, in this case, store some data in the database.

Let’s build that method up.

Add the following code block to the store() method:

```php
$client = new Client();
$client->name = $request->input('name');
$client->code = $request->input('code');
$client->save();
```

> The code above creates a new client using the Client model. We are accessing a request and looking at the two inputs we setup in our test.

> The `$client->save()` is where the record actually gets saved to the database.

To use the Client model, we have to include it at the top of our controller file along with the other `use statements`, add this line.

```php
use App\Models\Client;
```

Be sure to save the `ClientController` file and your finished file should look like this:

![Screen Shot 2021-12-08 at 10.56.36 AM.png](S3-08:%20Project%20Setup%20-%20Client%20(Part%201).assets/Screen%20Shot%202021-12-08%20at%2010.56.36%20AM.png)

1. Run our tests

```other
$ sail artisan test
```

![Screen Shot 2021-12-08 at 10.58.07 AM.png](S3-08:%20Project%20Setup%20-%20Client%20(Part%201).assets/Screen%20Shot%202021-12-08%20at%2010.58.07%20AM.png)

We’re seeing the same error. Our test still shows that there are no records in the clients table. When you think about it, it kind of makes sense, we haven’t told Laravel how to handle our request yet. In other words, we’re submitting our form, but we haven’t connected our request to our controller.

For that, we’re going to need to setup a `route`. You might remember from our previous session that routes direct requests to the code that will handle the requests. In our case we want the handler to be the `ClientController`, the method to be used to be `store`, and we want it to expect a `post` request on the following url `/clients` (you may remember that route in our test.

1. Open your web routes file `/routes/web.php`

![Screen Shot 2021-12-08 at 11.29.18 AM.png](S3-08:%20Project%20Setup%20-%20Client%20(Part%201).assets/Screen%20Shot%202021-12-08%20at%2011.29.18%20AM.png)

We have just one route, let’s add a new route to handle our client `post` request with the `/clients` url and provide the name of the controller (`ClientController`), the method (`store()`).

2. Add the following code below the existing route (line 20 on my screenshot):

```php
Route::post('/clients','App\Http\Controllers\ClientController@store');
```

![Screen Shot 2021-12-08 at 11.35.30 AM.png](S3-08:%20Project%20Setup%20-%20Client%20(Part%201).assets/Screen%20Shot%202021-12-08%20at%2011.35.30%20AM.png)

Let’s take a closer look at this route command.

We start the command with a facade called `Route::` don’t worry about what facades are, we’ll cover them in detail late on.

The type of request is of type `post`, and is handled by the `post()` method. That method takes two arguments; the `url` of the request, and the `handler` of the request (in our case it routes the request to the `ClientController`). The `@` symbol is used to specify which method to call on our `ClientController`. (in our case the `store()` method is specified).

Our route should now be all setup and we can test this by running our tests.

```other
$ sail artisan test
```

![Screen Shot 2021-12-08 at 11.43.57 AM.png](S3-08:%20Project%20Setup%20-%20Client%20(Part%201).assets/Screen%20Shot%202021-12-08%20at%2011.43.57%20AM.png)

Congratulations, you just completed your first test for Clients using TDD!

Let’s open the ClientTest file and do a little review and clean up of our code.

![Screen Shot 2021-12-08 at 11.46.28 AM.png](S3-08:%20Project%20Setup%20-%20Client%20(Part%201).assets/Screen%20Shot%202021-12-08%20at%2011.46.28%20AM.png)

I removed some unwanted comments from our file and tightened up the spacing. Here’s my final file.

![Screen Shot 2021-12-08 at 11.50.27 AM.png](S3-08:%20Project%20Setup%20-%20Client%20(Part%201).assets/Screen%20Shot%202021-12-08%20at%2011.50.27%20AM.png)

### One thing to mention about TDD

TDD can seem daunting at first, especially to new developers who are still learning the framework and how the various parts connect together. I promise the initial time investment you make in testing first will pay off in the long run. As we continue to write more tests and code, our project will become more and more complicated. Having a good set of tests, written first, will ensure that we won’t break our code accidentally in the future.

I’ll show you some shortcuts to make the entire process more manageable, so stick with it.

> ### Summary of our work

> We setup our first test method, `test_user_can_create_a_client()` . Along the way we setup the Model (`Client`), Controller (`ClientController`), Migration, and Route for our clients. We tested that a client can create a client in the database using a test method called `test_user_can_create_client()`.

---

### Finish our working session

We’ve done quite a bit of work and it would be a shame if we lost it. So let’s commit our changes to GitHub.

I would suggest running your tests before you do any commit. So let's do that now.

```Bash
$ sail artisan test
```

> If you don’t see passing tests for everything, do not commit your changes until you fix them.

From now on, I’m just going to give you the commands to commit your code along with a brief description of what they’re doing. After that, I’ll just give you the commands. Read? Okay let’s go.

1. First, we’re going to check the status to see what’s been changed on our local machine (new files, etc)

```Bash
$ git status
```

2. This is a good time to do a pull to make sure someone else hasn't changed anything while we did our work

```other
$ git pull
```

3. After reviewing the files to make sure we don’t see any surprises (files we didn’t mean to modify, add, or delete) we’ll add the files to this commit.

```Bash
$ git add .
```

4. Then we’ll commit them and provide a simple, but descriptive commit message

```Bash
$ git commit -m "Adds a test, model, controller, and route rule for creating a Client"
```

5. Finally, let’s push them up

```Bash
$ git push
```

After your code is committed, if you’re done working was can turn off our Docker container.

```Bash
$ sail down
```

