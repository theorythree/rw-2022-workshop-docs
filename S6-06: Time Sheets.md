# S6-06: Time Sheets

Our timesheets are going to require quite a bit of management to utilize properly. Here are a couple of goals I came up with to complete their work.

1. > We always want a timesheet created/available when the user adds a time entry
2. > If a timesheet isn’t available, one should be automatically created
3. > We can create timesheets for any date
4. > If no date is provided we can assume a timesheet will be created for the current date
5. > If the last time entry for a time sheet is deleted, I also want the timesheet to be deleted too

As always, we’re going to express all of this in the form of some tests that we will retroactively work to make pass.

Open the tests/Feature/TimeSheetTest.php file

![Screen Shot 2021-12-31 at 11.42.38 AM.png](S6-06:%20Time%20Sheets.assets/Screen%20Shot%202021-12-31%20at%2011.42.38%20AM.png)

Remove the example test that was provided when we created the file.

![Screen Shot 2021-12-31 at 11.43.16 AM.png](S6-06:%20Time%20Sheets.assets/Screen%20Shot%202021-12-31%20at%2011.43.16%20AM.png)

I want to test the different scenarios that a timesheet will be created in our application. I do not want the user to have to proactively create a timesheet, I just want it to automatically happen in the background as a user adds and removes entries.

Setup the test file as we’ve done in with the others, we’re going to need  TimeEntry and TimeSheet models and not much else.

```php
use App\Models\TimeEntry;
use App\Models\TimeSheet;
```

Also be sure to include the RefreshDatabase in the class body.

```php
use RefreshDatabase;
```

![Screen Shot 2021-12-31 at 12.15.23 PM.png](S6-06:%20Time%20Sheets.assets/Screen%20Shot%202021-12-31%20at%2012.15.23%20PM.png)

Excellent. For our first test we want to see if we can create a time entry and have the time sheet automatically get created for that date.

```php
public function test_timesheet_is_created_when_time_entry_is_created()
{
  $timeEntry = TimeEntry::factory()->create([
    'time_sheet_id' => 1,
    'duration' => '1:30'
  ]);

  $this->assertDatabaseHas('time_entries', ['duration' => 90]);
  
  $timeSheets = TimeSheet::all();
  $this->assertTrue(TimeSheet::all()->count() == 1);
}
```

This test creates a time entry and then checks to see we get a timesheet automatically created in the database.

Notice, I’m not creating a time entry by using the post method, we’re not testing our TimeEntryController here, we’re testing the TimeSheet functionality, so we’re just going to use a factory to create the time entry instead.

Speaking of which, we need to finish our TimeEntryFactory file.

Open up database/factories/TimeEntryFactory.php

![Screen Shot 2021-12-31 at 12.20.32 PM.png](S6-06:%20Time%20Sheets.assets/Screen%20Shot%202021-12-31%20at%2012.20.32%20PM.png)

Update the return array with the following:

```php
return [
  "project_id" => 1,
  "user_id" => 1,
  "duration" => '1:30',
];
```

![Screen Shot 2021-12-31 at 12.22.27 PM.png](S6-06:%20Time%20Sheets.assets/Screen%20Shot%202021-12-31%20at%2012.22.27%20PM.png)

> You’ll notice, I’m just faking the project_id, user_id, and duration. These can always be overridden, but they’re the required data of a factory, so I like to provide values.

Head back to the TimeSheetTest file and let’s run our tests.

```Bash
$ sail artisan test --filter=TimeSheetTest
```

![Screen Shot 2021-12-31 at 12.26.09 PM.png](S6-06:%20Time%20Sheets.assets/Screen%20Shot%202021-12-31%20at%2012.26.09%20PM.png)

Hmm, looks like our first assertion that we find 90 as the duration value failed, for some reason our service didn’t run, why not?

The reason is simple. We’re just creating a record, we’re not using an Http request of any kind. So when we make records like this, there’s no validation and as such our TimeEntryRequest class was never instantiated, and our prepareForValidation() method was never called, and so our DurationService was never invoked either.

We can fix this and we’ll do so in the next episode.

