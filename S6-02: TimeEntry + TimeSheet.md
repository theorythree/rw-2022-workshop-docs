# S6-02: TimeEntry + TimeSheet

---

### Start your work session

Using the startup steps here: [[Workflow: Starting and Ending Your Work Session]]

---

Let’s start this session with safety in mind and create a new feature branch to perform our work.

```other
$ git checkout main
$ git pull
$ git checkout -b dan/timesheet
```

With that out of the way, we can create our files. We’re going to need two sets of models, controllers, and migrations. We can do all that with two commands.

```other
$ sail artisan make:model TimeSheet -mc --resource
$ sail artisan make:model TimeEntry -mc --resource
```

![Screen Shot 2021-12-29 at 4.40.56 PM.png](S6-02:%20TimeEntry%20+%20TimeSheet.assets/Screen%20Shot%202021-12-29%20at%204.40.56%20PM.png)

And let’s make some tests too:

```other
$ sail artisan make:test TimeEntryTest
$ sail artisan make:test TimeSheetTest
```

![Screen Shot 2021-12-29 at 4.42.25 PM.png](S6-02:%20TimeEntry%20+%20TimeSheet.assets/Screen%20Shot%202021-12-29%20at%204.42.25%20PM.png)

And of course we’ll need some factories to use in those tests as well.

```other
$ sail artisan make:factory TimeEntryFactory
$ sail artisan make:factory TimeSheetFactory
```

![Screen Shot 2021-12-29 at 4.46.55 PM.png](S6-02:%20TimeEntry%20+%20TimeSheet.assets/Screen%20Shot%202021-12-29%20at%204.46.55%20PM.png)

### Where to begin?

I like to start with migrations first, even before tests. This is because our tests depend on migrations to run and by doing so it can help us with what our first few tests might be.

Open the `database/migrations/xxxx_xx_xx_xxxxxx_create_time_sheets_table.php` file

![Screen Shot 2021-12-29 at 4.56.10 PM.png](S6-02:%20TimeEntry%20+%20TimeSheet.assets/Screen%20Shot%202021-12-29%20at%204.56.10%20PM.png)

Time sheets belong to users, and not clients or projects because a single user may work on multiple projects from difference clients in one day. So our database table is going to be pretty simple.

Add the new table columns to the up() method of the file.

```php
Schema::create('time_sheets', function (Blueprint $table) {
  $table->id();
  $table->unsignedInteger('user_id');
  $table->date('date');
  $table->timestamps();
});
```

You can think of timesheets as a collection of time entries. It serves no other function than to provide a way for us to pull all of the time entries for a particular user for a particular date. We could have even just used the time entries themselves and looked them up by date, but I decided to create a timesheet because I wanted to leave room for some possible features in the future.

Now we can move on to the time entry migration file.

Open the `database/migrations/xxxx_xx_xx_xxxxxx_create_time_entries_table.php` file

![Screen Shot 2021-12-29 at 5.05.02 PM.png](S6-02:%20TimeEntry%20+%20TimeSheet.assets/Screen%20Shot%202021-12-29%20at%205.05.02%20PM.png)

> Did you notice Laravel correctly translated entry into entries (the plural of entry) when generating this file? Pretty cool, right?

A time entry is going to have to belong to a project, timesheet, and a user, without those three pieces of information a time entry would be useless. Duration, invoice and invoiced will be optional. It may seem odd to make duration optional, but I want to allow a user to setup an entry then add time to it afterwards, so it’s valid because there’s a use case for it.

```php
Schema::create('time_entries', function (Blueprint $table) {
    $table->id();
    $table->unsignedInteger('invoice_id')->nullable();
    $table->unsignedInteger('project_id');
    $table->unsignedInteger('time_sheet_id');
    $table->unsignedInteger('user_id');
    $table->integer('duration')->default(0)->nullable();
    $table->boolean('invoiced')->default(false);
    $table->timestamps();
});
```

A couple of things to point out here. One, I’m using a combination of nullable() and default() in some of these. Here’s why. For invoice_id, we know invoices will be generated after time entries, so there’s a very real possibility it may be nullable. I don’t want to provide a default value here, I want the field to be empty and null to indicate that this entry has no invoice assigned to it.

The project_id, time_sheet_id, and user_id are required, which is the default behavior when you do not specify anything in the schema create() method. They are all unsigned integers because we will never have a negative id value in our database.

The duration is NOT an unsigned integer because I want to be able to provide negative time values in this field. Why you may ask? Well negative values can act as credits. So imagine you’re calculating up all your time and you want to credit the client for some of that time, you might provide a negative value of time which will be multiplied by an hourly rate and it would calculate to a negative dollar amount (i.e. -$150). I do provide a default value of 0, but I’m also going to allow for null values just incase the user deletes the zero and submits an edit to the time entry.

Invoiced also has a default value and it’s set to false. I did not want to allow null on this field because it will make it easier to test and query against if we only have to check for true and false and not true, false, or null.

This brings up a good point about storage of data in the database. `Null`, refers to no value at all, don’t confuse that with `false`, `0`, `“false”`, or an empty string `“”`. All of those are, in fact values and it’s stored as such in the database table. `False` is also a value, in fact you’ll see soon enough that our booleans are stored in the database as `tinyints` with values of `0` or `1` (`false` or `true`). This is to save space in the database, the field type is a single bite in size with a `0` or `1` value, which requires less storage than the words `true` or `false`. So I could have replaced the `default(false)` with `default(0)` and it would have worked. I chose not to, because I believe false is more readable.

Another point of confusion I see with new developers is they will incorrectly store true/false values in the database as strings and not booleans. The word “true” and “false” are stored in the database as words. This is a bad idea, not just for the storage inefficiencies I mentioned, but it will cause confusion in their code.

For example, the following if statements will both test true.

```php
// invoiced stored in the database as string "false"
if ($timeEntry->invoiced) { ... }

// invoiced stored in the database as string "true"
if ($timeEntry->invoiced) { ... }
```

The reason for this is that the string “false” is in fact a value, so is the word “true”, so both pass the conditional statement.

### What's next, should we run our migrations?

We could do that, but remember we only really set theses up so we could write some tests, besides we may have an error in our migration files and wouldn’t it be great to discover that through a well-written test instead? I think so.

Let’s start with the TimeEntryTest

Open tests/Feature/TimeEntryTest.php

![Screen Shot 2021-12-29 at 7.10.29 PM.png](S6-02:%20TimeEntry%20+%20TimeSheet.assets/Screen%20Shot%202021-12-29%20at%207.10.29%20PM.png)

In order to test time entries, we’re going to need a couple of things, we’ll need a user, with a role of a user, a client, a project assigned to that client, and a timesheet.

Let’s include all those models in our file.

```php
use App\Models\Client;
use App\Models\Project;
use App\Models\Role;
use App\Models\TimeEntry;
use App\Models\TimeSheet;
use App\Models\User;
```

We can also get rid of the sample test and it’s comment leaving the TimeEntryTest class body empty for now.

```php
class TimeEntryTest extends TestCase
{

}
```

We know we’ll need our testing database, so let’s include the RefreshDatabase in our class body.

```php
class TimeEntryTest extends TestCase
{
  use RefreshDatabase;
  
}
```

The first logical test would be to see if a user can add a time entry. In order to test that, we’ll need the user, client, the client’s project, and the user’s timesheet all setup. That’s a lot of setup that we will have to do for each of our tests in this test file.

Fortunately for us we can use the setup() method to perform those tasks once and provide all those items to each of our tests in this file.

```php
public function setUp(): void
{
  parent::setUp();
  $this->artisan('db:seed --class=RoleSeeder');
  $this->user = User::factory()->create();
  $this->user->roles()->attach(Role::IS_USER);
  $this->client = Client::factory()->create();
  $this->project = Project::factory()->create(['client_id' => $this->client->id]);
  $this->timesheet = TimeSheet::factory()->create(['user_id' => $this->user->id]);
}
```

You can see we did these in the order we need them so they’re built up in such a way we can use the ids from the factories previously created.

Speaking of which, we don’t have a timesheet factory, built yet, so let’s do that next.

Open database/factories/TimeSheetFactory.php

![Screen Shot 2021-12-29 at 7.37.04 PM.png](S6-02:%20TimeEntry%20+%20TimeSheet.assets/Screen%20Shot%202021-12-29%20at%207.37.04%20PM.png)

Update the return array to the following:

```php
return [
  "user_id" => 1,
  "date" => Date("Y-m-d"),
];
```

The Date() method will return today’s date in whatever format you provide as the argument. “Y-m-d” will return a date in the YYYY-MM-DD format (i.e. 2021-12-29)

Head back to the TimeEntryTest.php file

![Screen Shot 2021-12-29 at 7.42.04 PM.png](S6-02:%20TimeEntry%20+%20TimeSheet.assets/Screen%20Shot%202021-12-29%20at%207.42.04%20PM.png)

Now that our setup() method is complete, we can write our first test.

Let’s test the post store() method on our TimeEntryController.

```php
public function test_user_can_add_a_time_entry_to_their_timesheet()
{
  $response = $this->actingAs($this->user)->post('/time', [
    'project_id' => $this->project->id,
    'time_sheet_id' => $this->timesheet->id,
    'duration' => 2
  ]);
  $this->assertTrue(TimeEntry::all()->count() == 1);
}
```

A couple of things to point out. The first is the method name. We’re using a descriptive name just like in all our other tests. Next, you’ll notice I’m using the actingAs() method to perform the post as the user we built up in the setup() method. Next, the url is “time”, I wanted to use something user friendly and since all their entering their time, we’ll use that url for all the CRUD methods for TimeEntry. I did not forget to add the user_id to this call because we want the user id to be assigned in the controller.  We never want to have our controller dependent on a form value because that could easily be hacked and then we would be entering time for another user in the database.

Finally, the duration is going to be stored in minutes, but we want the user to be able to enter hours and minutes, so we’ll convert 1.5 or 1:30 to 90 minutes. If they enter 9 we’ll assume they mean 9 hours and convert that to 450 minutes. The value for duration will always be stored in the database as minutes.

With that we should be able to run our first test that will fail.

```other
$ sail artisan test --filter=TimeEntryTest
```

![Screen Shot 2021-12-29 at 8.05.35 PM.png](S6-02:%20TimeEntry%20+%20TimeSheet.assets/Screen%20Shot%202021-12-29%20at%208.05.35%20PM.png)

We know what we need to do to make our test pass, first, we need to create our routes rule, then finish the store() method in the TimeEntryController.

### TimeEntry Route Rules

Open routes/web.php

![Screen Shot 2021-12-29 at 8.07.57 PM.png](S6-02:%20TimeEntry%20+%20TimeSheet.assets/Screen%20Shot%202021-12-29%20at%208.07.57%20PM.png)

You can see we have three groups; one for admins, one for owners, and one for users. Where do you think we should put the route for time entries? We want owners and even admins to enter time too, so we can’t put it in the user’s group, because those routes will be excluded from the others. We also can’t leave it out of a group because we don’t want guests to be able to follow those routes.

We’re going to need another group, one that applies to all logged in users, regardless of which role assignment they may or may not have. The answer is to use the auth middleware which applies to all logged in users and prevents guests from accessing those routes.

```other
// LOGGED IN USER ROUTES
Route::group(['middleware'=>'auth'], function(){ 
  Route::resource('/time','App\Http\Controllers\TimeEntryController');
});
```

In adding this route group, I also updated the comments of the other groups to make it more clear that we’re segmenting them by role. You should do the same.

![Screen Shot 2021-12-29 at 8.17.14 PM.png](S6-02:%20TimeEntry%20+%20TimeSheet.assets/Screen%20Shot%202021-12-29%20at%208.17.14%20PM.png)

While we’re here, let’s add a TODO to remind us to protect the clients and projects routes too. Remember, we don’t want to do that now, because that work does not apply to our timesheet tasks.

```php
// TODO: Protect these routes by adding them to the auth group
Route::resource('/clients','App\Http\Controllers\ClientController');
Route::resource('/projects','App\Http\Controllers\ProjectController');
```

![Screen Shot 2021-12-29 at 8.20.15 PM.png](S6-02:%20TimeEntry%20+%20TimeSheet.assets/Screen%20Shot%202021-12-29%20at%208.20.15%20PM.png)

### TimeEntryController

On to the controller to write our store() method.

Open App/Http/Controllers/TimeEntryController.php

![Screen Shot 2021-12-29 at 8.23.11 PM.png](S6-02:%20TimeEntry%20+%20TimeSheet.assets/Screen%20Shot%202021-12-29%20at%208.23.11%20PM.png)

Scroll down to find the store() method in the controller.

```php
public function store(Request $request)
{
  $timeEntry = new TimeEntry();
  $timeEntry->project_id = $request->project_id;
  $timeEntry->time_sheet_id = $request->time_sheet_id;
  $timeEntry->user_id = auth()->user()->id;
  $timeEntry->duration = $request->duration;
  $timeEntry->save();
}
```

You can see this is pretty straightforward. We’re using a more concise method of accessing the request, instead of $request->input(‘project_id’), we’re using $request->project_id. The other notable thing is the user_id line.

```php
$timeEntry->user_id = auth()->user()->id;
```

How does that work? Well actually right now it doesn’t. We have to include the Auth class for this to work. Do that first then I’ll explain.

Include auth in the top of your class file.

```php
use Auth;
```

![Screen Shot 2021-12-29 at 8.44.30 PM.png](S6-02:%20TimeEntry%20+%20TimeSheet.assets/Screen%20Shot%202021-12-29%20at%208.44.30%20PM.png)

We can use Auth to access the authenticated user. The accessor method user() let’s us get anything from the users record, including their ID. So whomever is logged in and submitting a time entry will be the user in this method.

If you run the test again, you’ll see it should pass now.

```Bash
$ sail artisan test --filter=TimeEntryTest
```

![Screen Shot 2021-12-29 at 8.48.14 PM.png](S6-02:%20TimeEntry%20+%20TimeSheet.assets/Screen%20Shot%202021-12-29%20at%208.48.14%20PM.png)

Excellent work. Up next I want to talk a little bit about user input. Remember we cannot predict what the user will put in our forms, and in this case, we can’t stop them from doing crazy and unpredictable things like adding letters in the duration form field. How do we validate the data? And for that matter how do we take some of the data a user gives us and convert it so that we can store it in the database properly. Like, for example the 1.5 1:30 entry which should be stored as the integer 90? We’ll cover all that and more in the next episode.

