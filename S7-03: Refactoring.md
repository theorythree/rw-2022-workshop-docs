# S7-03: Refactoring

In our last episode, we started to work on some of the changes in our project plan. Before we get too far, I want to do some code cleanup in our project.

Our changes are going to be touching nearly all of our files so we're going to want to create a branch so we can isolate our changes just in case we need to revert them later on.

As always, I want to set clear goals.

1. > Refactor TimeEntryController
2. > Complete TimeEntryController CRUD methods
3. > Update and Complete CRUD methods in ProjectController and ClientController
4. > Complete and Cross off all project TODOs
5. > Remove any unwanted or unused code

For a refactor project such as this, I’m not really planning on adding any functionality, so our tests won’t need to be updated. In fact, we’re going to use these tests to validate the fact that our refactoring did not break anything.

I’m going to create a local branch which we will merge into the main branch when our work is done and tested.

Let’s do that now:

```Bash
$ git status
$ git pull
$ git checkout -b dan/refactor
```

### Refactor + Complete TimeEntryController

Open the TimeEntryController

![Screen Shot 2022-01-04 at 11.56.09 AM.png](S7-03:%20Refactoring.assets/Screen%20Shot%202022-01-04%20at%2011.56.09%20AM.png)

We’re going to finish up all of the methods in this controller, so let’s start at the top.

### index()

For the index() method, we’re going to do something slightly unorthodox, This is the method that will be called when the user goes to /time url in their browser. I want to use that route to display all of the time entries for the user for a particular date. The default date will be today’s date. And on that screen they can manage their time entries (add, delete, edit) for that particular day.

So we’re going to want to return a single time sheet which will contain all of the time entries for that sheet.

Include TimeSheet in the header of the file.

```php
use App\Models\TimeSheet;
```

Back to the index() method itself, we want to find a time sheet or create one if it doesn’t exist, then return a view with the time sheet and its time entries.

That might look something like this:

```php
public function index()
{
  $timeSheet = TimeSheet::where('date',Date('Y-m-d'))->where('user_id',auth()->user()->id)->first();
  if (!$timeSheet) {
    $timeSheet = new TimeSheet();
    $timeSheet->date = Date('Y-m-d');
    $timeSheet->user_id = auth()->user()->id;
    $timeSheet->save();
  }
}
```

But there’s actually a better way to do the timesheet creation if it doesn’t exist in a single line. We can use the `firstOrCreate()` method, which will first check to see if there’s a time sheet and if there isn’t it will create one.

The line looks like this:

```php
$timeSheet = TimeSheet::firstOrCreate(['date' => Date('Y-m-d'), 'user_id' => auth()->user()->id]);
```

```php
public function index()
{
  $timeSheet = TimeSheet::firstOrCreate(['date' => Date('Y-m-d'), 'user_id' => auth()->user()->id]);
}
```

This is a much better approach, however, we did create an issue, and to see what I mean go to localhost/time in your browser.

![Screen Shot 2022-01-04 at 2.24.32 PM.png](S7-03:%20Refactoring.assets/Screen%20Shot%202022-01-04%20at%202.24.32%20PM.png)

The mass assignment error is telling us we cannot just create a new time sheet using the create() method without specifying which database columns are safe to fill in. That’s because the create() method is often times used to insert data directly from a request. I’ll show you an example of that when we update the store() method on this controller. For now, you can think of it as an extra layer of security protecting us from malicious intent.

To authorize the columns, we need to mark them as fillable in the model, in this case the TimeSheet model.

Open the App/Models/TimeSheet.php file

![Screen Shot 2022-01-04 at 2.31.16 PM.png](S7-03:%20Refactoring.assets/Screen%20Shot%202022-01-04%20at%202.31.16%20PM.png)

We want to authorize the user_id and date of the timesheet. To do so, we just have to define a protected array named $fillable and provide the column names that we want to authorize for the create() method.

```php
protected $fillable = ['user_id', 'date'];
```

![Screen Shot 2022-01-04 at 2.33.35 PM.png](S7-03:%20Refactoring.assets/Screen%20Shot%202022-01-04%20at%202.33.35%20PM.png)

Now refresh your browser and you should see another error about the timesheets.index page not being found.

![Screen Shot 2022-01-04 at 4.11.06 PM.png](S7-03:%20Refactoring.assets/Screen%20Shot%202022-01-04%20at%204.11.06%20PM.png)

The blank screen is expected because the controller isn’t returning anything right now.

At this point there should be a timesheet entry in the time_sheets database table. Go a head an open that up and take a look if you’d like.

![Screen Shot 2022-01-04 at 2.36.47 PM.png](S7-03:%20Refactoring.assets/Screen%20Shot%202022-01-04%20at%202.36.47%20PM.png)

If you refresh your browser multiple times, there will always only be one time entry, that’s what the firstOrCreate() method does.

### Create()

The create() method is where we would typically put a create form for the user to fill out to create a time entry. As mentioned before, I want to handle that time creation on the time index page. Again, this is not a typical use case, but for what we’re doing it makes sense since this is the main functionality of our application to have a single page both display time entries and allow the user to manage them on one page.

If someone were to visit the time/create url, we could just redirect them back to the index page. So let’s do that.

```php
public function create()
{
  return redirect(route('time.index'));
}
```

You can go to the following url: localhost/time/create and you should be redirected to localhost/time.

![Screen Shot 2022-01-04 at 4.12.54 PM.png](S7-03:%20Refactoring.assets/Screen%20Shot%202022-01-04%20at%204.12.54%20PM.png)

### Store()

The store() method is working, but we can make it better.

```php
public function store(TimeEntryRequest $request)
{
  $timeEntry = new TimeEntry();
  $timeEntry->project_id = $request->project_id;
  $timeEntry->time_sheet_id = $request->time_sheet_id;
  $timeEntry->user_id = auth()->user()->id;
  $timeEntry->date = $request->date;
  $timeEntry->duration = $request->duration;
  $timeEntry->save();
}
```

Again, we can use the create() method and turn this into a single line of code, which will make it much more readable.

The Request class has a method called validated() and as the name would imply it’s an array of validated key/values which can be mass-assigned in the create() method.

So we can turn this:

```php
public function store(TimeEntryRequest $request)
{
  $timeEntry = new TimeEntry();
  $timeEntry->project_id = $request->project_id;
  $timeEntry->time_sheet_id = $request->time_sheet_id;
  $timeEntry->user_id = auth()->user()->id;
  $timeEntry->date = $request->date;
  $timeEntry->duration = $request->duration;
  $timeEntry->save();
}
```

Into this:

```php
public function store(TimeEntryRequest $request)
{ 
  $timeEntry = TimeEntry::create($request->validated());   
}
```

Much cleaner and easier to manage.

However in order for this to work, we’re going to need to do the same mass-assignment trick in our TimeEntry model.

Open up App/Models/TimeEntry.php

![Screen Shot 2022-01-04 at 3.56.09 PM.png](S7-03:%20Refactoring.assets/Screen%20Shot%202022-01-04%20at%203.56.09%20PM.png)

And let’s list all of the columns we want to authorize for mass-assignment in the protected $fillable variable.

```php
protected $fillable = ['invoice_id','project_id','time_sheet_id','user_id','date','duration','invoiced'];
```

![Screen Shot 2022-01-04 at 3.57.10 PM.png](S7-03:%20Refactoring.assets/Screen%20Shot%202022-01-04%20at%203.57.10%20PM.png)

If we run our tests we’ll see them failing:

```Bash
$ sail artisan test
```

![Screen Shot 2022-01-04 at 3.59.16 PM.png](S7-03:%20Refactoring.assets/Screen%20Shot%202022-01-04%20at%203.59.16%20PM.png)

And this is because we removed some of the data required to insert a time entry, namely the user_id.

We don’t want the user to have to submit their own id, but we do want it to be required, so to fix this little situation we need to take a look at the TimeEntryRequest, which is where our data is being validated.

Open App/Http/Requests/TimeEntryRequest.php

![Screen Shot 2022-01-04 at 4.02.00 PM.png](S7-03:%20Refactoring.assets/Screen%20Shot%202022-01-04%20at%204.02.00%20PM.png)

We want to make user_id required in the rules, and we can use the prepareForValidation() method to supply a user_id of the current user.

```php
public function rules()
{
  return [
    'project_id' => ['required', 'numeric'],
    'time_sheet_id' => ['required', 'numeric'],
    'user_id' => ['required', 'numeric'],
    'date' => ['required', 'date'],
    'duration' => ['numeric'],
  ];
}
```

```php
protected function prepareForValidation(): void
{
  $duration = (new DurationService())->convertToMinutes($this->duration);

  $this->merge([
    'user_id' => auth()->user()->id,
    'duration' => $duration,
  ]);
}
```

Now we are supplying the user id when we create a time entry.

Run our tests to see if this refactoring has brought us back to green.

```Bash
$ sail artisan test
```

![Screen Shot 2022-01-04 at 4.06.15 PM.png](S7-03:%20Refactoring.assets/Screen%20Shot%202022-01-04%20at%204.06.15%20PM.png)

And we’re good!

Back to the TimeEntryController.php file.

### Show() + Edit() Methods

We may not need these two methods, but I can see a use case for both of them in the future. For show() we may want to have a time entry detail screen, that shows more details about a specific time entry. And for the edit() method we may want to offer more editing capabilities on a time entry that we would require a bigger form.

In both cases, however, the controller part of that will be to just return a view, so we can leave these two methods blank for now.

### Update()

We will very much use this method for updating a time entry. And we can take what we’ve learned for the time entry store() method and apply it here too.

We just have to update the Request class to our TimeEntryRequest class in the method argument and there is one little gotcha I want to make you aware of in the update() method arguments.

```php
public function update(TimeEntryRequest $request, TimeEntry $timeEntry) { ... }
```

This controller was setup for us, and you can see I’ve already updated the TimeEntryRequest, but I want you to look at the TimeEntry argument. We’re using Route Model binding which passes the TimeEntry to this method and that is where the gotcha lives.

In order for route model binding to work, the variable name has to exactly match the name the route expects. Normally that name would match, however in this case we decided to change the route name to use just time, instead of the full name which would have a url of `time-entry`.

We can can check to see what the route expects that name to be by checkout our route list.

```Bash
$ sail artisan route:list
```

![Screen Shot 2022-01-05 at 1.32.51 PM.png](S7-03:%20Refactoring.assets/Screen%20Shot%202022-01-05%20at%201.32.51%20PM.png)

You can see the routes are defined as time/{time}, which means our variable in our controller must match that name.

Update the $timeEntry variable name to $time.

```php
public function update(TimeEntryRequest $request, TimeEntry $time) { ... }
```

Now we can just access that TimeEntry passed to us and do an update.

```php
public function update(TimeEntryRequest $request, TimeEntry $time)
{
  $time->update($request->validated());
}
```

We also want to write a test for that.

Open tests/Feature/TimeEntryTest.php

![Screen Shot 2022-01-05 at 1.37.52 PM.png](S7-03:%20Refactoring.assets/Screen%20Shot%202022-01-05%20at%201.37.52%20PM.png)

Our test just needs to create a time entry, then use the put() method to make a change to that time entry.

```php
public function test_user_can_update_time_entry()
{
  $timeEntry = TimeEntry::factory()->create([
    'project_id' => $this->project->id,
    'time_sheet_id' => $this->timesheet->id,
    'date' => Date("Y-m-d"),
    'duration' => '1:30'
  ]);

  $this->assertDatabaseHas('time_entries', ['duration' => 90]);

  $response = $this->actingAs($this->user)->put('/time/'.$timeEntry->id, [
    'project_id' => $this->project->id,
    'time_sheet_id' => $this->timesheet->id,
    'date' => Date("Y-m-d"),
    'duration' => '1:15'
  ]);

  $this->assertDatabaseHas('time_entries', ['duration' => 75]);
}
```

![Screen Shot 2022-01-05 at 1.40.33 PM.png](S7-03:%20Refactoring.assets/Screen%20Shot%202022-01-05%20at%201.40.33%20PM.png)

You can see, we’re just changing the duration and asserting we see that change in the database.

```Bash
$ sail artisan test --filter=TimeEntryTest
```

![Screen Shot 2022-01-05 at 1.41.45 PM.png](S7-03:%20Refactoring.assets/Screen%20Shot%202022-01-05%20at%201.41.45%20PM.png)

### Delete()

While we’re in the TimeEntryTest file, let’s write a test for deleting a time entry

Add this test at the bottom of the file:

```php
public function test_user_can_delete_a_time_entry()
{
  $timeEntry = TimeEntry::factory()->create([
    'project_id' => $this->project->id,
    'time_sheet_id' => $this->timesheet->id,
    'date' => Date("Y-m-d"),
    'duration' => '2:30'
  ]);

  $this->assertDatabaseHas('time_entries', ['duration' => 150]);
  $this->assertTrue(TimeSheet::all()->count() == 1);

  $response = $this->actingAs($this->user)->delete('/time/'.$timeEntry->id);

  $this->assertTrue(TimeEntry::all()->count() == 0);
  $this->assertTrue(TimeSheet::all()->count() == 0);
}
```

This will check to see the time sheet exists and is removed as well.

The test will fail now, but we’re going to fix that in the controller.

```Bash
$ sail artisan test --filter=TimeEntryTest
```

![Screen Shot 2022-01-05 at 1.49.47 PM.png](S7-03:%20Refactoring.assets/Screen%20Shot%202022-01-05%20at%201.49.47%20PM.png)

Head back to the TimeEntryController to finish up the delete() method.

```php
public function destroy(TimeEntry $time)
{
  $time->delete();
}
```

We’re using the same $time variable name for this method as the update() method, so all we have to do is run the delete() method on it and we’re done.

Run our tests to verify:

```Bash
$ sail artisan test --filter=TimeEntryTest
```

![Screen Shot 2022-01-05 at 1.53.02 PM.png](S7-03:%20Refactoring.assets/Screen%20Shot%202022-01-05%20at%201.53.02%20PM.png)

Here’s an overview of what we completed in this episode:

1. > ~~Refactor TimeEntryController~~
2. > ~~Complete TimeEntryController CRUD methods~~
3. > Update and Complete CRUD methods in ProjectController and ClientController
4. > Complete and Cross off all project TODOs
5. > Remove any unwanted or unused code

We’ll finish up the rest in the upcoming episode.

Before we do, though, let’s commit our changes to our local branch.

```Bash
$ git status
$ git add .
$ git commit -m "Update: finished CRUD methods for time entry, adds test for updating time entry"
```

Notice, we’re not pushing this branch up because we still have work to do.

