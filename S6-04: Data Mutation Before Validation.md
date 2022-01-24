# S6-04: Data Mutation Before Validation

Now I’m sure the suspense is just killing you. But before we proceed to fixing our issue, I want to point out that Laravel does offer a very handy way to mutate values when you create a new record. We’ll learn more about mutators and accessors in just a bit.

However, the out-of-the-box solution Laravel provides isn’t going to help us in this particular case. The reason for that is the validator check happens before the Laravel mutator runs. In other words the check that this the duration is a number will happen before we can alter any of the data.

To see what I mean, open the TimeEntryController.

Open App/Http/Controllers/TimeEntryController.php

![Screen Shot 2021-12-30 at 11.31.03 AM.png](S6-04:%20Data%20Mutation%20Before%20Validation.assets/Screen%20Shot%202021-12-30%20at%2011.31.03%20AM.png)

We can’t just provide some kind of override here, because the validator is going to run first, and it’s going to look at the data submitted in the request, before we have a chance to do anything with it. So All requests that have a duration will be validated that the data is numeric, even the exceptions we do want to allow.

We also can’t reverse the order of this validation to happen after the data is stored in the database, so we have a bit of a challenge here.

> In these moments, it’s important to ignore the instinct to just toss out the thing that’s blocking us from doing our work. It would be easy for us to just remove that validation rule, or all the validation rules for that matter, and go on about our day. Do not do this. Validation is important and it will save our bacon, trust me.

As it turns out the Request class uses a trait that has a method just for this particular situation that we find ourselves. The method is called `prepareForValidation()`.

The file for that class can be found deep in the belly of the laravel framework code

`vendor/laravel/framework/src/Illuminate/Validation/ValidateWhenResolvedTrait.php`

![Screen Shot 2021-12-30 at 11.48.19 AM.png](S6-04:%20Data%20Mutation%20Before%20Validation.assets/Screen%20Shot%202021-12-30%20at%2011.48.19%20AM.png)

```php
/**
* Prepare the data for validation.
*
* @return void
*/
protected function prepareForValidation()
{
  //
}
```

This method will be called before any validation on our data happens. But we can’t just edit this method here, this is the core code of Laravel, and it is meant to be read only.

We can, however extend the Request class with our own class and just a method named `prepareForValidation()` to perform the work we need.

That’s exactly what we’re going to do.

And to no one’s surprise, there’s an Artisan command to make these request classes.

```Bash
$ sail artisan make:request TimeEntryRequest
```

We follow the same naming convention as with all our other files name, then the thing we’re creating, in this case a Request class.

![Screen Shot 2021-12-30 at 12.22.37 PM.png](S6-04:%20Data%20Mutation%20Before%20Validation.assets/Screen%20Shot%202021-12-30%20at%2012.22.37%20PM.png)

You’ll see a new file in App/Http/Requests called TimeEntryRequest.

Open TimeEntryRequest.php

![Screen Shot 2021-12-30 at 12.23.29 PM.png](S6-04:%20Data%20Mutation%20Before%20Validation.assets/Screen%20Shot%202021-12-30%20at%2012.23.29%20PM.png)

We’re going to use this class to handle all of the methods to validate and prepare to validate our time entry requests. This will be for both our store() and update() methods in our TimeEntryController class.

If you take a look at the file, you’ll see an authorize() method, which is used to validate if the user can make these requests, and the rules() method which is the new home for our validation rules, which are currently stored in our controller.

### TimeEntryRequest Setup

Setup is pretty simple. We just need to handle the authorization check, then copy our rules from the controller to this file. Then, finally, setup the prepareForValidation() method we need to mutate our duration data values before the validation occurs.

For the authorize() method, we just need to check for a logged in user. We’ve done that before.

Include Auth in the header of the file

```php
use Auth;
```

Then in the authorize() method return the auth check like this:

```php
public function authorize()
{
  return auth()->check();
}
```

For the rules() method, copy the array of rules from our TimeEntryController store() method.

```php
public function rules()
{
  return [
    'project_id' => ['required', 'numeric'],
    'time_sheet_id' => ['required', 'numeric'],
    'duration' => ['numeric'],
  ];
}
```

Pretty easy, now let’s head on over to the TimeEntryController file.

Include the TimeEntryRequest class in the header

```php
use App\Http\Requests\TimeEntryRequest;
```

In the store() method, replace Request with TimeEntryRequest in the argument of the method and remove our validation code.

```php
public function store(TimeEntryRequest $request)
{
  $timeEntry = new TimeEntry();
  $timeEntry->project_id = $request->project_id;
  $timeEntry->time_sheet_id = $request->time_sheet_id;
  $timeEntry->user_id = auth()->user()->id;
  $timeEntry->duration = $request->duration;
  $timeEntry->save();
}
```

This is all it takes to use our new request class. Before we fix our problem, let’s make sure we didn’t create any more problems by running our test.

```Bash
$ sail artisan test --filter=TimeEntryTest
```

![Screen Shot 2021-12-30 at 12.39.00 PM.png](S6-04:%20Data%20Mutation%20Before%20Validation.assets/Screen%20Shot%202021-12-30%20at%2012.39.00%20PM.png)

Great, we still see our failed tests that were failing before, and our other tests are passing as they were before.

Now for the fix.

Open the new TimeEntryRequest file

![Screen Shot 2021-12-30 at 12.40.27 PM.png](S6-04:%20Data%20Mutation%20Before%20Validation.assets/Screen%20Shot%202021-12-30%20at%2012.40.27%20PM.png)

Let’s add the new method we just learned about.

Add this method below the rules() method.

```php
protected function prepareForValidation(): void
{
  $this->merge([
    'duration' => '',
  ]);
}
```

I wanted to show you this method incomplete first, because I didn’t want the logic of what we need to do here distract you from what this method is actually doing.

You can see it’s accessing it’s own values and merging whatever fields we put in the array. In other words as is, any duration value submitted by the user would be overridden by an empty string, before being passed off to the validator.

So the first thing we’re going to want to do is capture the submitted data for duration, then check to see if it contains a : or a ., if it does then we need to do some calculation on the values and convert them all to total number of minutes.

Here’s the method updated, and checking for a colon in the submitted value.

```php
$duration = $this->duration;

if (str_contains((String) $this->duration, ':')) {
  $durationParts = explode(":",$this->duration);
  $duration = ($durationParts[0] * 60) + $durationParts[1];
}

$this->merge([
  'duration' => $duration,
]);
```

You can see I’m setting $duration to the current request value, then I’m testing to see if it contains a colon : by casting it as a String and performing the str_contains() method. If that string does contain a colon, then I’m breaking it up into two parts, those values on the left are hours and need to be multiplied by 60 to get the total number of minutes, then I’m adding it to the values on the right of the colon because those are expected to be minutes.

We can run our tests to see if this mutation worked as we expected.

```Bash
$ sail artisan test --filter=TimeEntryTest
```

![Screen Shot 2021-12-30 at 12.56.10 PM.png](S6-04:%20Data%20Mutation%20Before%20Validation.assets/Screen%20Shot%202021-12-30%20at%2012.56.10%20PM.png)

You can see our `test_user_can_enter_duration_with_colon_format()` passed!

Let’s apply roughly the same logic for the decimal place format too.

```php
if (str_contains((String) $this->duration, '.')) {
  $duration = ($this->duration * 60);
}
```

For the decimal format, all we have to do is multiple it’s value by 60 to get the total number of minutes.

Let’s run our test to see if it works.

```Bash
$ sail artisan test --filter=TimeEntryTest
```

![Screen Shot 2021-12-30 at 1.15.14 PM.png](S6-04:%20Data%20Mutation%20Before%20Validation.assets/Screen%20Shot%202021-12-30%20at%201.15.14%20PM.png)

That did it, our tests passed!

Before we go off an celebrate just yet, let’s think about a potential weak point in our approach. I don’t like the fact that we’re testing just one value and we’re doing some math too, so there’s alway the potential we might mess up our math in our test and in our entry. For example, what if we provide a float that results in a half a minute, how do we handle those situations.

Also, this is a handy bit of code, I could imagine  a world where we might want to reuse this calculation elsewhere in our project.

The answer to all our problems is provided by a little thing we call services, and we’ll learn more about them in our next lesson.

Before we do, let’s add, checkin our code and push it up to GitHub.

```Bash
$ git status
```

![Screen Shot 2021-12-30 at 1.20.01 PM.png](S6-04:%20Data%20Mutation%20Before%20Validation.assets/Screen%20Shot%202021-12-30%20at%201.20.01%20PM.png)

When doing our git status I noticed something I didn’t like. So now would be a good time to mention the issue and put in a quick fix.

Take a peek at one of these untracked files:

![Screen Shot 2021-12-30 at 1.20.54 PM.png](S6-04:%20Data%20Mutation%20Before%20Validation.assets/Screen%20Shot%202021-12-30%20at%201.20.54%20PM.png)

It’s called .DS_Store, that’s a directory meta file created by MacOS and we really do NOT want that checked in with our files. This file was created by MacOS when the Request directory was created in our project. That’s data not a part of our project, we want Git to ignore it.

There’s a file explicitly for such purposes in the root of our project, it’s called .gitignore, open that puppy up real quick.

![Screen Shot 2021-12-30 at 1.24.13 PM.png](S6-04:%20Data%20Mutation%20Before%20Validation.assets/Screen%20Shot%202021-12-30%20at%201.24.13%20PM.png)

You’ll see a list of things that Git should ignore and we want to add .DS_Store to that list.

```Bash
.DS_Store
```

![Screen Shot 2021-12-30 at 1.25.16 PM.png](S6-04:%20Data%20Mutation%20Before%20Validation.assets/Screen%20Shot%202021-12-30%20at%201.25.16%20PM.png)

Now save and close that file and let’s try our git status one more time.

![Screen Shot 2021-12-30 at 1.25.59 PM.png](S6-04:%20Data%20Mutation%20Before%20Validation.assets/Screen%20Shot%202021-12-30%20at%201.25.59%20PM.png)

Now you’ll see that little bugger off our list, and you’ll see the .gitignore file is going to be updated to prevent other people from checking in that file to the repository too!

On to the rest of your commit steps.

```Bash
$ git add .
$ git commit -m "adds, controllers, routes, tests, migrations for timesheets and time entries"
$ git push --set-upstream origin dan/timesheet
```

