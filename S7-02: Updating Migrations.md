# S7-02: Updating Migrations

In the previous episode I discussed some changes to the project plan. One of those changes was the addition of a date field to the time entry database table.

When updating a database table you have two options.

### The manual approach

You can manually update the database original migration file and add, remove or edit the database schema and rerun your migrations. You’ll have to manually delete the record in the migrations table so the altered migration runs again.

This approach comes with a big catch/warning. You can only do this if you know for absolute certain that the edited migration file has not run elsewhere. For example if this code was on a testing server or a production server, those environments may have run migrations and editing the migration file is a bad idea.

The other reason this is a bad idea is it erases history, so the manual approach, while possible, is not recommended for most circumstances.

### The automatic approach

You also have the ability to write another migration file that adds, removes, and/or alters existing migrations that ran before it. Because migrations are run in the order they were created, you can, for example, add a column to a database table that already exists.

In the real world, I almost always use the automatic approach, but I want to show you both ways so you know how to do it.

Open up TablePlus (or whatever database client you’re using) and connect to the tempo database.

![Screen Shot 2022-01-03 at 5.20.43 PM.png](S7-02:%20Updating%20Migrations.assets/Screen%20Shot%202022-01-03%20at%205.20.43%20PM.png)

Open up the migrations database table.

![Screen Shot 2022-01-03 at 5.21.09 PM.png](S7-02:%20Updating%20Migrations.assets/Screen%20Shot%202022-01-03%20at%205.21.09%20PM.png)

You’ll see in my case (yours may be slightly different), I’ve run migrations twice. I can tell because there are two batches.

You’ll also notice, our time entries, and time sheets migration files have not yet been run. We’ve tested these tables in our tests, but we still have not run them.

In this particular case we could just add the date field to the time entries migration file without any issue, but I want to demonstrate how to undo a migration manually and automatically, so we’re going to run our migrations intentionally, so we can learn how to undo them.

Run our third batch of migrations

```Bash
$ sail artisan migrate
```

![Screen Shot 2022-01-03 at 5.25.01 PM.png](S7-02:%20Updating%20Migrations.assets/Screen%20Shot%202022-01-03%20at%205.25.01%20PM.png)

And back in TablePlus refresh your migrations table by hitting `Command` + `R` (`Control` + `R` on a PC)

![Screen Shot 2022-01-03 at 5.26.19 PM.png](S7-02:%20Updating%20Migrations.assets/Screen%20Shot%202022-01-03%20at%205.26.19%20PM.png)

Open the time_sheets database table.

![Screen Shot 2022-01-03 at 5.26.46 PM.png](S7-02:%20Updating%20Migrations.assets/Screen%20Shot%202022-01-03%20at%205.26.46%20PM.png)

You can see the migration worked as it was written and we now we want to add the date field to the list of columns.

### Manual Approach

Because we know no one else ran the migrations in our project, we can safely fix this issue manually.

Open the original migration file we created for time entries `xxxx_xx_xx_xxxxxx_create_time_entries_table.php`

![Screen Shot 2022-01-03 at 5.29.23 PM.png](S7-02:%20Updating%20Migrations.assets/Screen%20Shot%202022-01-03%20at%205.29.23%20PM.png)

Adding the field is pretty simple, we just, well, add the field.

Add the following line after `user_id` and before the `duration` fields.

```php
$table->date('date');
```

![Screen Shot 2022-01-03 at 5.31.04 PM.png](S7-02:%20Updating%20Migrations.assets/Screen%20Shot%202022-01-03%20at%205.31.04%20PM.png)

If you try to run migrations now, you’re not going to see our change take affect, but let’s try anyway for demonstration purposes.

```Bash
$ sail artisan migrate
```

![Screen Shot 2022-01-03 at 5.32.28 PM.png](S7-02:%20Updating%20Migrations.assets/Screen%20Shot%202022-01-03%20at%205.32.28%20PM.png)

This is because the migrations table in our tempo database already has the create_time_entries_table migration marked as already executed.

Open TablePlus again and open the migrations table.

![Screen Shot 2022-01-03 at 5.34.24 PM.png](S7-02:%20Updating%20Migrations.assets/Screen%20Shot%202022-01-03%20at%205.34.24%20PM.png)

To force the migration file to run again, we can just delete that record in the migrations table.

Click on the create_time_entries record and delete it by clicking the delete button.

![Screen Shot 2022-01-03 at 5.35.56 PM.png](S7-02:%20Updating%20Migrations.assets/Screen%20Shot%202022-01-03%20at%205.35.56%20PM.png)

Confirm the delete by hitting `Command` + `S` (`Control` + `S` on a PC)

![Screen Shot 2022-01-03 at 5.36.57 PM.png](S7-02:%20Updating%20Migrations.assets/Screen%20Shot%202022-01-03%20at%205.36.57%20PM.png)

Now we have to delete the time_entries database table by selecting it and hitting the delete key (again if you had data in this table, the manual migration method would not work).

![Screen Shot 2022-01-03 at 5.38.56 PM.png](S7-02:%20Updating%20Migrations.assets/Screen%20Shot%202022-01-03%20at%205.38.56%20PM.png)

And again hit  `Command` + `S` (`Control` + `S` on a PC) to commit your changes.

Now, back in VS Code, run your migrations again.

```Bash
$ sail artisan migrate
```

![Screen Shot 2022-01-03 at 5.40.01 PM.png](S7-02:%20Updating%20Migrations.assets/Screen%20Shot%202022-01-03%20at%205.40.01%20PM.png)

Back in TablePlus, refresh your database by hitting Command + R (Control + R on a PC)

![Screen Shot 2022-01-03 at 5.41.02 PM.png](S7-02:%20Updating%20Migrations.assets/Screen%20Shot%202022-01-03%20at%205.41.02%20PM.png)

There’s the new database column named date in the table.

Now let’s try the automatic way. To do that, we’re going to once again delete the migration record.

![Screen Shot 2022-01-03 at 5.43.44 PM.png](S7-02:%20Updating%20Migrations.assets/Screen%20Shot%202022-01-03%20at%205.43.44%20PM.png)

Select the record and hit the delete key as we did before.

![Screen Shot 2022-01-03 at 5.44.21 PM.png](S7-02:%20Updating%20Migrations.assets/Screen%20Shot%202022-01-03%20at%205.44.21%20PM.png)

TablePlus will allow us to make multiple changes before we commit them, so select the time_entires table and hit the delete key.

![Screen Shot 2022-01-03 at 5.46.15 PM.png](S7-02:%20Updating%20Migrations.assets/Screen%20Shot%202022-01-03%20at%205.46.15%20PM.png)

And now commit all the changes with `Command` + `S` (`Control` + `S` on a PC) to save our changes.

![Screen Shot 2022-01-03 at 5.46.49 PM.png](S7-02:%20Updating%20Migrations.assets/Screen%20Shot%202022-01-03%20at%205.46.49%20PM.png)

Back in VS Code, we just need to revert our changes, so we can let git help us with that.

```Bash
$ git checkout .
```

Open the create_time_entries_table migration to verify our date column is no longer defined.

![Screen Shot 2022-01-03 at 5.48.33 PM.png](S7-02:%20Updating%20Migrations.assets/Screen%20Shot%202022-01-03%20at%205.48.33%20PM.png)

Excellent, now let’s run the migrations again.

```Bash
$ sail artisan migrate
```

And once again verify our database table does not contain the date column.

Open TablePlus and hit Command + R (Control + R on PC) to refresh the database.

![Screen Shot 2022-01-03 at 5.26.46 PM.png](S7-02:%20Updating%20Migrations.assets/Screen%20Shot%202022-01-03%20at%205.26.46%20PM%20(2).png)

So at this point in time, our migrations have been restored back to a place and time where they have run and we need to add our data column to the time_entries database table. How exactly do we do that?

We create another migration file, which will add the column.

```Bash
$ sail artisan make:migration add_date_column_to_time_entries_table --table=time_entries
```

![Screen Shot 2022-01-03 at 5.56.00 PM.png](S7-02:%20Updating%20Migrations.assets/Screen%20Shot%202022-01-03%20at%205.56.00%20PM.png)

You can see we added the —table=time_entries to help with the file setup.

Open the newly created migration file.

![Screen Shot 2022-01-03 at 5.57.12 PM.png](S7-02:%20Updating%20Migrations.assets/Screen%20Shot%202022-01-03%20at%205.57.12%20PM.png)

So with the table assignment already done for us, we just need to add the new table column in the up() method, and this time, we’ll have to write our own down() method too.

```php
$table->date('date')->after('user_id');
```

You can see I’m using the after() method to define where I want this new column to go. Do not forget to do this, or the column will be added at the end of the database table, and that’s not where we want it.

For the down() method, we need to drop the column we just created. For that, we can use a method called dropColumn() which takes the name of the column to drop as an argument.

```php
$table->dropColumn('date');
```

![Screen Shot 2022-01-03 at 6.01.31 PM.png](S7-02:%20Updating%20Migrations.assets/Screen%20Shot%202022-01-03%20at%206.01.31%20PM.png)

And with that, we can run our migrations and the new migration we just created will be the only one that gets run.

```Bash
$ sail artisan migrate
```

![Screen Shot 2022-01-03 at 6.02.49 PM.png](S7-02:%20Updating%20Migrations.assets/Screen%20Shot%202022-01-03%20at%206.02.49%20PM.png)

And we can verify all is well on the database side, by opening up the time_entries table and refreshing the database with `Command` + `R` (`Control` + `R` on PC)

![Screen Shot 2022-01-03 at 6.04.11 PM.png](S7-02:%20Updating%20Migrations.assets/Screen%20Shot%202022-01-03%20at%206.04.11%20PM.png)

Now we can expect our tests are going to fail because every time we add a time entry in our tests we now need to include a date value as well.

```Bash
$ sail artisan test
```

![Screen Shot 2022-01-03 at 6.05.56 PM.png](S7-02:%20Updating%20Migrations.assets/Screen%20Shot%202022-01-03%20at%206.05.56%20PM.png)

Let’s fix those up real quick.

Open the database/factories/TimeEntryFactory.php file.

![Screen Shot 2022-01-03 at 6.06.55 PM.png](S7-02:%20Updating%20Migrations.assets/Screen%20Shot%202022-01-03%20at%206.06.55%20PM.png)

Add a date key and value.

```php
"date" => Date("Y-m-d"),
```

![Screen Shot 2022-01-03 at 6.07.45 PM.png](S7-02:%20Updating%20Migrations.assets/Screen%20Shot%202022-01-03%20at%206.07.45%20PM.png)

We’ll still see fails on the tests that did not use the factory to create a time entry. Those are in the TimeEntryTest file.

Open tests/Feature/TimeEntryTest.php

![Screen Shot 2022-01-03 at 6.09.59 PM.png](S7-02:%20Updating%20Migrations.assets/Screen%20Shot%202022-01-03%20at%206.09.59%20PM.png)

And add a date key and value for all those tests.

```php
<?php

namespace Tests\Feature;

use Illuminate\Foundation\Testing\RefreshDatabase;
use Illuminate\Foundation\Testing\WithFaker;
use Tests\TestCase;
use App\Models\Client;
use App\Models\Project;
use App\Models\Role;
use App\Models\TimeEntry;
use App\Models\TimeSheet;
use App\Models\User;

class TimeEntryTest extends TestCase
{
  use RefreshDatabase;
  
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

  public function test_user_can_add_a_time_entry_to_their_timesheet()
  {
    $response = $this->actingAs($this->user)->post('/time', [
      'project_id' => $this->project->id,
      'time_sheet_id' => $this->timesheet->id,
      'date' => Date("Y-m-d"),
      'duration' => 2
    ]);

    $this->assertTrue(TimeEntry::all()->count() == 1);
  }

  public function test_user_cannot_add_string_value_for_duration_value()
  {
    $response = $this->actingAs($this->user)->post('/time', [
      'project_id' => $this->project->id,
      'time_sheet_id' => $this->timesheet->id,
      'date' => Date("Y-m-d"),      
      'duration' => 'abc'
    ]);
    
    $this->assertTrue(TimeEntry::all()->count() == 0);
    $response->assertStatus(302);
    $response->assertSessionHasErrors();
  }

  public function test_user_can_add_duration_with_colon_format()
  {
    $response = $this->actingAs($this->user)->post('/time', [
      'project_id' => $this->project->id,
      'time_sheet_id' => $this->timesheet->id,
      'date' => Date("Y-m-d"),
      'duration' => '1:30'
    ]);
    
    $this->assertTrue(TimeEntry::all()->count() == 1);
    $this->assertDatabaseHas('time_entries', ['duration' => 90]);
  }

  public function test_user_can_add_duration_with_decimal_format()
  {
    $response = $this->actingAs($this->user)->post('/time', [
      'project_id' => $this->project->id,
      'time_sheet_id' => $this->timesheet->id,
      'date' => Date("Y-m-d"),
      'duration' => '1.5'
    ]);
    
    $this->assertTrue(TimeEntry::all()->count() == 1);
    $this->assertDatabaseHas('time_entries', ['duration' => 90]);
  }

}
```

![Screen Shot 2022-01-03 at 6.13.08 PM.png](S7-02:%20Updating%20Migrations.assets/Screen%20Shot%202022-01-03%20at%206.13.08%20PM.png)

And finally, we need to update the TimeEntryController to accept the new date column values and store them in the database.

Open App/Http/Controllers/TimeEntryController.php

![Screen Shot 2022-01-03 at 6.14.32 PM.png](S7-02:%20Updating%20Migrations.assets/Screen%20Shot%202022-01-03%20at%206.14.32%20PM.png)

Scroll down to the store() method and let’s add the new column to our code.

```php
$timeEntry->date = $request->date;
```

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

Add the date field to our validation rules.

Open App/Http/Requests/TimeEntryRequest.php

![Screen Shot 2022-01-03 at 6.19.51 PM.png](S7-02:%20Updating%20Migrations.assets/Screen%20Shot%202022-01-03%20at%206.19.51%20PM.png)

Add the new date column, as a validation rule.

```php
'date' => ['required', 'date'],
```

```php
public function rules()
{
  return [
    'project_id' => ['required', 'numeric'],
    'time_sheet_id' => ['required', 'numeric'],
    'date' => ['required', 'date'],
    'duration' => ['numeric'],
  ];
}
```

Save and run the tests again.

```Bash
$ sail artisan test
```

![Screen Shot 2022-01-03 at 6.18.46 PM.png](S7-02:%20Updating%20Migrations.assets/Screen%20Shot%202022-01-03%20at%206.18.46%20PM.png)

Excellent work!

Let’s add, commit, and push up our changes to GitHub.

```Bash
$ git status
$ git pull
$ git add .
$ git commit -m "adds new date column to time entries table"
$ git push
```

### Final Thoughts About Migrations

> As you can see adding new migration files is a trivial task, so I highly encourage you to default to doing so instead of the manual approach I showed you.

> I opted to show you the manual approach for those rare cases when you may have made a mistake in your original migration and there was no data in the database table. But, as you’ll no doubt understand after your projects launch, manually overriding the database migrations is risky and might cause issues to the integrity of your database.

