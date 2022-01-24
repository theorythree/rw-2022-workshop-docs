# S6-03: Validation

We’ve been living in the wild west with our controllers up into this point. We’re counting on the fact that the user is just going to enter data correctly, but that’s not going to work in the real world.

We need to setup some validation tests and rules.

The goal of this episode is to protect ourselves from attempting to enter invalid data into our time-entries database table before we try to store the data.

To do that we’re going to write some rules to check for invalid data entry and verify that our controller handles those situations correctly.

Open tests/Feature/TimeEntryTest.php

![Screen Shot 2021-12-30 at 9.46.08 AM.png](S6-03:%20Validation.assets/Screen%20Shot%202021-12-30%20at%209.46.08%20AM.png)

I want to test on a couple of conditions:

1. We do not want to allow a string value for duration
2. We do not want time entries create without the required fields
3. We want the user to be able to enter time with a colon separator (1:30) and decimal values (1.5) which should be converted into minutes (90).
4. If the user tries to enter invalid data, we want them to be redirected with a set of invalidation errors back to the page they submitted the form

With that in mind, here’s our first test.

```php
public function test_user_cannot_add_string_value_for_duration_value()
{
  $response = $this->actingAs($this->user)->post('/time', [
    'project_id' => $this->project->id,
    'time_sheet_id' => $this->timesheet->id,
    'duration' => 'abc'
  ]);
  
  $this->assertTrue(TimeEntry::all()->count() == 0);
  $response->assertStatus(302);
  $response->assertSessionHasErrors();
}
```

This test, checks to see if the user can submit a string into the database. We’re using our user to do a post and they provided ‘abc’ as the duration value. We assert that this attempt will be unsuccessful and we won’t have a time entry in the database, that we get redirected (302) and we have some validation errors.

I’m using a new assertion method assertSessionHasErrors() which specifically checks for the validation errors we expect the user to have when entering invalid data.

Let’s run our test and see how it turns out.

```Bash
$ sail artisan test --filter=TimeEntryTest
```

![Screen Shot 2021-12-30 at 10.02.38 AM.png](S6-03:%20Validation.assets/Screen%20Shot%202021-12-30%20at%2010.02.38%20AM.png)

You can see the assertion that we did not store this timesheet in the database is failing. In other words, the user can add strings to the duration column. We’d expect that to fail under real world conditions because in our time-entries database table schema we set that up as an integer, not a string. However, the testing database uses sqlite, which actually doesn’t check for such data type restrictions. So it’s allowing such a value to be stored. This is an important thing to remember, we should not rely on our database as a validation check on our data. Because, as you can see, this particular database is allowing it.

Instead, we should provide some validation rules that will validate all the data before it can be stored in the database.

The best way to do that is with some validation rules in our TimeEntryController

Open App/Http/Controllers/TimeEntryController.php

![Screen Shot 2021-12-30 at 10.08.01 AM.png](S6-03:%20Validation.assets/Screen%20Shot%202021-12-30%20at%2010.08.01%20AM.png)

You can see that in our store() method, we’re just taking whatever the user gives us and attempting to store it in the database. Before this action happens, we want to validate the data.

Laravel provides a simple way to do that with a validation() method which we will use before the time entry is actually stored.

Here’s what that looks like, add this block of code at the top of the store() method of our controller:

```php
$request->validate([
  'project_id' => ['required', 'numeric'],
  'time_sheet_id' => ['required', 'numeric'],
  'duration' => ['numeric'],
]);
```

![Screen Shot 2021-12-30 at 11.06.45 AM.png](S6-03:%20Validation.assets/Screen%20Shot%202021-12-30%20at%2011.06.45%20AM.png)

As you can see we’re taking the request and using the validate() method, which takes an array of form fields to check agains their rules.

We have required marked on the project_id and time_sheet_id, but we’re allowing null values on the duration, and we want them all to be numeric.

> If you’re curious about all the available validation rules, check out the [Laravel documentation](https://laravel.com/docs/8.x/validation) which goes into great detail about how to apply validation and what rules do what.

Now run our tests and see how we did.

```Bash
$ sail artisan test --filter=TimeEntryTest
```

![Screen Shot 2021-12-30 at 10.14.04 AM.png](S6-03:%20Validation.assets/Screen%20Shot%202021-12-30%20at%2010.14.04%20AM.png)

Now we know anytime someone tries to enter a string for duration they’ll be redirected back to the page they submitted the data from and they’ll be given some user-friendly errors to help them correct their mistake. And, as a bonus our database data will remain clean regardless if the data type restrictions aren’t working properly.

Up next, we want to verify that the required field rule is working too.

```php
public function test_user_can_not_create_time_entry_without_required_fields()
{
  $response = $this->actingAs($this->user)->post('/time', [
    'duration' => 2
  ]);

  $this->assertTrue(TimeEntry::all()->count() == 0);
  $response->assertStatus(302);
  $response->assertSessionHasErrors();
}
```

As you can see this is nearly identical to the last test, but we’re only suppling the duration (which is optional) and not providing the other required fields. This should fail in the same way, so the assertions are all the same.

Run our tests to see this pass too.

```Bash
$ sail artisan test --filter=TimeEntryTest
```

![Screen Shot 2021-12-30 at 10.30.53 AM.png](S6-03:%20Validation.assets/Screen%20Shot%202021-12-30%20at%2010.30.53%20AM.png)

Now we want to write some tests to make sure the user can enter values with the colon format (1:30) and the decimal format (1.5) and ensure those values end up being numeric before we put them in the database.

```php
public function test_user_can_add_duration_with_colon_format()
{
  $response = $this->actingAs($this->user)->post('/time', [
    'project_id' => $this->project->id,
    'time_sheet_id' => $this->timesheet->id,
    'duration' => '1:30'
  ]);
  
  $this->assertTrue(TimeEntry::all()->count() == 1);
  $this->assertDatabaseHas('time_entries', ['duration' => 90]);
}
```

So as you can see, we’re just replacing that duration value with the expected colon format. Let’s run our tests and see why this is going to cause a problem.

```Bash
$ sail artisan test --filter=TimeEntryTest
```

![Screen Shot 2021-12-30 at 11.22.22 AM.png](S6-03:%20Validation.assets/Screen%20Shot%202021-12-30%20at%2011.22.22%20AM.png)

Oh boy, look at that, we couldn’t enter the colon value. If you think about it, this makes sense, currently our validator is getting in the way, the validation is looking for a number, and we provided a string. So how do we handle these use cases?

Before we go into answering that question, I want to write the same test for decimal formats, because we want both.

```php
public function test_user_can_add_duration_with_decimal_format()
{
  $response = $this->actingAs($this->user)->post('/time', [
    'project_id' => $this->project->id,
    'time_sheet_id' => $this->timesheet->id,
    'duration' => '1.5'
  ]);
  
  $this->assertTrue(TimeEntry::all()->count() == 1);
  $this->assertDatabaseHas('time_entries', ['duration' => 90]);
}
```

This is nearly the same test, except we’re adding a 1.5 as the duration. It will fail just like the previous test and for the same exact reason.

With that little business out of our way, what can we do about this little validation conflict?

### Can't we just remove the validation?

We could do that, but then we’d be opening ourselves up to the very situation we’re trying to prevent, which is strings in our database.

### Can we check for the colon (or decimal) in our controller before validation?

The answer to that question is yes, we can do that. How we go about that is what we’ll discuss in the next episode.

