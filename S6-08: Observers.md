# S6-08: Observers

For the last part of our test, we need to automatically create a time sheet when a user creates an entry if one does not exist, and delete a timesheet after the last time entry is removed from it.

To do this, we’re going to use an observer.

Observers can listen in to events that happen to our models, and when that event occurs we can trigger a bit of code.

For example, you might want to use an observer on the User model so every time a new user registers they get a welcome email sent to them.

In our case we have two events we want to observe, when a time entry is being created and when one is being deleted. So our observer will be on the TimeEntry model.

### Setup Relationship between TimeEntry and TimeSheet

Before we dig into observers, we have some tiding up we need to do. Specifically with our TimeEntry and TimeSheet model files.

We never established their relationship methods, so let’s do that now. Those relationships will come in handy when we’re checking for a timesheet.

Open App/Models/TimeSheet.php

![Screen Shot 2022-01-02 at 2.59.01 PM.png](S6-08:%20Observers.assets/Screen%20Shot%202022-01-02%20at%202.59.01%20PM.png)

Remember, a timesheet can have many time entries, so the relationship method looks like this:

```php
public function timeEntries()
{
  return $this->hasMany(TimeEntry::class);
}
```

![Screen Shot 2022-01-02 at 3.00.24 PM.png](S6-08:%20Observers.assets/Screen%20Shot%202022-01-02%20at%203.00.24%20PM.png)

And now define the reverse relationship method.

Open App/Models/TimeEntry.php

![Screen Shot 2022-01-02 at 3.01.21 PM.png](S6-08:%20Observers.assets/Screen%20Shot%202022-01-02%20at%203.01.21%20PM.png)

A time entry belongs to a timesheet, so the method looks like this:

```php
public function timeSheet() {
  return $this->belongsTo(TimeSheet::class);
}
```

![Screen Shot 2022-01-02 at 3.03.03 PM.png](S6-08:%20Observers.assets/Screen%20Shot%202022-01-02%20at%203.03.03%20PM.png)

### Creating an Observer

As previously mentioned, observers work by listening in on events in our model files. Artisan has a command to create an observer and the command has a flag that you can assign to a specific model.

Run this command to make our observer file.

```Bash
$ sail artisan make:observer TimeEntryObserver --model=TimeEntry
```

![Screen Shot 2022-01-02 at 3.07.38 PM.png](S6-08:%20Observers.assets/Screen%20Shot%202022-01-02%20at%203.07.38%20PM.png)

Open the App/Observers/TimeEntryObserver.php file

![Screen Shot 2022-01-02 at 3.08.36 PM.png](S6-08:%20Observers.assets/Screen%20Shot%202022-01-02%20at%203.08.36%20PM.png)

You can see the TimeEntry model is already included in the header, and there are several methods in this file stubbed out for us.

Each method is named after the event that will trigger it. `Created()`, for example, will be triggered right after a TimeEntry is stored in the database. That sounds close to what we want, but ideally, we’d be able to access it before it's created. Don't worry, I didn't lead us astray, sit tight for a second.

There are other events that are not provided, including the one we want, which is named `creating()`. Creating() will be called just before the time entry is stored in the database.

Let’s do some cleanup on this file, and remove the methods we do not intend on using, which will be all of them except, deleted().

![Screen Shot 2022-01-02 at 3.19.20 PM.png](S6-08:%20Observers.assets/Screen%20Shot%202022-01-02%20at%203.19.20%20PM.png)

Include the TimeSheet model in the file header.

```php
use App\Models\TimeSheet;
```

Add the creating() method before the deleted() method.

```php
public function creating(TimeEntry $timeEntry) 
{  

}
```

You may also have noticed, all these methods have a time entry passed to them for us to work on, this is the same for our creating() method as well.

For the creating() method body, we just have to check to see if there’s a time sheet associated with the time entry that is about to be saved to the database. If there isn’t then we can create one and assign it to the time entry. If there is we do not need to do anything.

Here’s the updated method:

```php
public function creating(TimeEntry $timeEntry) 
{  
  if (!$timeEntry->timeSheet()->exists()) {
    $timeSheet = new TimeSheet();
    $timeSheet->date = Date("Y-m-d");
    $timeSheet->user_id = $timeEntry->user_id;
    $timeSheet->save();

    $this->time_sheet_id = $timeSheet->id;
  }
}
```

Notice, I’m using a not operator ! at the beginning of my if statement, so  ->exists() will return true if it exists and the not operator will reverse the logic.

Also, notice, I’m not looking up the user, I’m just pulling the user id from the time entry that is about to be stored. That should be defined and always aligned with the time sheet.

The final thing to point out is I’m not saving the time entry here. That is done by the code that’s going to trigger the creating() event method.

We’ll come back and finish the deleted() method later, for now we just have one more thing for our observer to work.

We need to register our observer so Laravel knows that we want these observer methods to be triggered when an event occurs on the model.

We do that in the EventServiceProvider, which can be found in the Providers directory.

Open App/Providers/EventServiceProvider.php

![Screen Shot 2022-01-02 at 3.31.12 PM.png](S6-08:%20Observers.assets/Screen%20Shot%202022-01-02%20at%203.31.12%20PM.png)

We need to include both the Observer and the Model

```php
use App\Models\TimeEntry;
use App\Observers\TimeEntryObserver;
```

Then in the boot() method, we register like so:

```php
public function boot()
{
  TimeEntry::observe(TimeEntryObserver::class);
}
```

That’s it, our observer’s creating() method should be called and build up a timesheet if there isn’t one.

Run our tests to confirm.

```Bash
$ sail artisan test --filter=TimeSheetTest
```

Now, we should add a test to confirm we don’t create a new time sheet every single time a time entry is created.

Open tests/Feature/TimeSheetTest.php

![Screen Shot 2022-01-02 at 3.36.11 PM.png](S6-08:%20Observers.assets/Screen%20Shot%202022-01-02%20at%203.36.11%20PM.png)

Add this test below the current test.

```php
public function test_timesheet_is_not_created_when_second_time_entry_is_created()
{
  $firstTimeEntry = TimeEntry::factory(2)->create([
    'time_sheet_id' => 1,
    'duration' => '1:30'
  ]);

  $this->assertTrue(TimeSheet::all()->count() == 1);  
}
```

> You can see this just creates two time entries and verifies there is only one time sheet record in the database .

While we’re here, let’s clean up a line in our other test that I noticed we don’t need.

Remove `$timeSheets = TimeSheet::all();` line from the `test_timesheet_is_created_when_time_entry_is_created()` method.

The updated method will look like this:

```php
public function test_timesheet_is_created_when_time_entry_is_created()
{
  $timeEntry = TimeEntry::factory()->create([
    'time_sheet_id' => 1,
    'duration' => '1:30'
  ]);

  $this->assertDatabaseHas('time_entries', ['duration' => 90]);

  $this->assertTrue(TimeSheet::all()->count() == 1);
}
```

Run our tests to see if we see green.

```Bash
$ sail artisan test --filter=TimeSheetTest
```

![Screen Shot 2022-01-02 at 3.37.55 PM.png](S6-08:%20Observers.assets/Screen%20Shot%202022-01-02%20at%203.37.55%20PM.png)

### Last Time Entry Deletion

We do not want a bunch of time sheet records in the database, so we’re going to do a check when someone is deleting a time entry to see if it’s the last one in the time sheet, and if it is, we’ll also delete the timesheet.

We can use the same methodology as we did for the time sheet creation and store our logic in an observer.

Let’s right our test first.

Open the tests/Feature/TimeSheetTest.php file

![Screen Shot 2022-01-03 at 12.38.57 PM.png](S6-08:%20Observers.assets/Screen%20Shot%202022-01-03%20at%2012.38.57%20PM.png)

Add a new test that checks for the time sheet deletion:

```php
public function test_timesheet_is_deleted_when_last_time_entry_is_deleted()
{
  $timeEntry = TimeEntry::factory()->create([
    'time_sheet_id' => 1,
    'duration' => '1:30'
  ]);

  $this->assertTrue(TimeSheet::all()->count() == 1);

  $timeEntry->delete();

  $this->assertTrue(TimeSheet::all()->count() == 0);
}
```

You can see this test creates an entry, asserts we have just one time sheet, then it deletes the entry and asserts that we have zero time sheets in the database.

This test will of course fail, but let’s run it to verify.

```Bash
$ sail artisan test --filter=TimeSheetTest
```

![Screen Shot 2022-01-03 at 12.40.43 PM.png](S6-08:%20Observers.assets/Screen%20Shot%202022-01-03%20at%2012.40.43%20PM.png)

You can see the test fails exactly where we want it to, on the second assertion.

Let’s fix our code to make our test pass.

Open App/Observers/TimeEntryObserver.php

![Screen Shot 2022-01-03 at 12.41.55 PM.png](S6-08:%20Observers.assets/Screen%20Shot%202022-01-03%20at%2012.41.55%20PM.png)

Update the deleted() method to check for a zero count of time entries for the timesheet.

```php
public function deleted(TimeEntry $timeEntry)
{
  if ($timeEntry->timeSheet->timeEntries()->count() == 0) {
    $timeEntry->timeSheet->delete();
  }
}
```

I want to point out a few things. First, you can access the $timeEntry even after its been deleted. This is because the object is passed to us after the record has been deleted from the database, but it remains in memory.

Second you’ll notice I can access the timesheet’s time entries through the time entry that’s being deleted.

Run the tests to see if our check works as expected.

```Bash
$ sail artisan test --filter=TimeSheetTest
```

![Screen Shot 2022-01-03 at 12.50.09 PM.png](S6-08:%20Observers.assets/Screen%20Shot%202022-01-03%20at%2012.50.09%20PM.png)

This looks good. However, to make sure we have good test coverage on this, we should add another test to verify that when we delete a time entry that is not empty that we still have a time sheet. Let’s do that now.

Open tests/Feature/TimeSheetTest.php

![Screen Shot 2022-01-03 at 12.51.17 PM.png](S6-08:%20Observers.assets/Screen%20Shot%202022-01-03%20at%2012.51.17%20PM.png)

To do that, let’s just write a test that creates two time entries for one time sheet and only deletes one of the entries.

```php
public function test_timesheet_is_not_deleted_when_stil_contains_a_time_entry()
{
  $timeEntries = TimeEntry::factory(2)->create([
    'time_sheet_id' => 1,
    'duration' => '1:30'
  ]);

  $this->assertTrue(TimeSheet::all()->count() == 1);

  $timeEntries->first()->delete();

  $this->assertTrue(TimeSheet::all()->count() == 1);
}
```

You can see with this one, I’m creating two time entries for the same time sheet. Then I’m checking that the time sheet remains in the database after only deleting the first one.

Run the tests to see if this test passes as well.

```Bash
$ sail artisan test --filter=TimeSheetTest
```

![Screen Shot 2022-01-03 at 12.55.24 PM.png](S6-08:%20Observers.assets/Screen%20Shot%202022-01-03%20at%2012.55.24%20PM.png)

And you can see we’re still good.

Now we could add a bunch more testing to cover all kinds of use cases, but I think the testing coverage we have now works pretty well for our needs.

Let’s run all of our tests and if they pass we can check in our code.

```Bash
$ sail artisan test
```

![Screen Shot 2022-01-03 at 12.57.01 PM.png](S6-08:%20Observers.assets/Screen%20Shot%202022-01-03%20at%2012.57.01%20PM.png)

We’re good to go.

Add, commit, and push up your changes to GitHub.

```Bash
$ git status
$ git pull
$ git add .
$ git commit -m "adds observer to time entry to ensure existance of timesheet when appropriate"
$ git push
```

And since we have all green tests and we’re wrapped up in this feature branch for now, let’s merge it back into the main branch.

```Bash
$ git status
$ git checkout main
$ git pull
$ git merge dan/timesheet
$ git push
$ git status
```

![Screen Shot 2022-01-03 at 1.06.19 PM.png](S6-08:%20Observers.assets/Screen%20Shot%202022-01-03%20at%201.06.19%20PM.png)

### With Great Power...

Now that you’ve learned a new trick, I want to offer a word of caution. Do not overuse mutators, accessors and observers in your projects.

I know it can be tempting, but the idea behind all of them is to use them to decrease the functionality in one region of your code, namely controllers. Be careful to only add these into your projects when you need them, and not just because it’s easier to add them than to write some code in a more appropriate place and in a more appropriate manor.

