# S6-05: Services

Services (not to be confused with Service Providers) are a pattern used in our code to perform simple repeatable tasks. You can think of them as a bit of stored logic, that you want to be located in one location and used throughout the project whenever and wherever you need them.

For us, we have a need to do some crazy conversions of what a user may enter for duration value and we might want to use that conversion elsewhere in our code, and maybe even in our tests.

### Create a Service Class

Services will live in a new folder called Services and that folder will live in the app folder. Now this is the rare case that Artisan won’t help us make the file (booo), but that’s okay it’s a simple class.

Create a new folder in the App folder called Services and within that folder create a new file called DurationService.php

![Screen Shot 2021-12-30 at 2.48.10 PM.png](S6-05:%20Services.assets/Screen%20Shot%202021-12-30%20at%202.48.10%20PM.png)

Manually setting up classes can be a bit of a pain, but there’s really only a few things we need to remember, one the open PHP tag, declare the namespace and then write the class body where our class files will go.

```php
<?php

namespace App\Services;

class DurationService
{

}
```

![Screen Shot 2021-12-30 at 2.50.45 PM.png](S6-05:%20Services.assets/Screen%20Shot%202021-12-30%20at%202.50.45%20PM.png)

Add a method for our service called convertToMinutes(). It should take one parameter, a value which we will pass to our method as $value.

```php
public function convertToMinutes($value)
{

}
```

![Screen Shot 2021-12-30 at 2.59.26 PM.png](S6-05:%20Services.assets/Screen%20Shot%202021-12-30%20at%202.59.26%20PM.png)

Next up, we’re going to take the logic out of our TimeEntryRequest file and put it in the new method we just created.

Open the App/Http/Requests/TimeEntryRequest.php file

![Screen Shot 2021-12-30 at 2.52.57 PM.png](S6-05:%20Services.assets/Screen%20Shot%202021-12-30%20at%202.52.57%20PM.png)

Line 36 in my screenshot looks like this:

```php
$duration = $this->duration;
```

We want to have $duration be the end value we pass on to our validator. Remember this might be a string, a number or one of the colon-formatted or decimal-formatted values. So our service is going to take whatever is passed to it and return the proper minute value OR just pass back the invalid value for our validator to catch.

Copy this code from the file:

```php
$duration = $this->duration;

if (str_contains((String) $this->duration, ':')) {
  $durationParts = explode(":",$this->duration);
  $duration = ($durationParts[0] * 60) + $durationParts[1];
}

if (str_contains((String) $this->duration, '.')) {
  $duration = ($this->duration * 60);
}
```

And paste it into the DurationService.php file:

![Screen Shot 2021-12-30 at 3.00.34 PM.png](S6-05:%20Services.assets/Screen%20Shot%202021-12-30%20at%203.00.34%20PM.png)

Every place we use $this→ will no longer work, because that was referring to the Request, not our service. So replace the $this->duration with $value.

```php
public function convertToMinutes($value)
{
  $duration = $value;

  if (str_contains((String) $value, ':')) {
    $durationParts = explode(":",$value);
    $duration = ($durationParts[0] * 60) + $durationParts[1];
  }

  if (str_contains((String) $value, '.')) {
    $duration = ($value * 60);
  }
  
}
```

You can see the same logic applies to when we used this in the TimeEntryRequest file. We set $duration to whatever is passed to the method, then we do a check for our characters (`:` and `.`) and if they are there the value of $duration is overridden.

The last thing to do is return $duration with this line at the end of the method:

```php
return $duration;
```

![Screen Shot 2021-12-30 at 3.05.56 PM.png](S6-05:%20Services.assets/Screen%20Shot%202021-12-30%20at%203.05.56%20PM.png)

Save and close this file, we’re done with it for now.

Now we have a logic stored up and ready to use anywhere in our project. And let’s put that into practice from the place we stole the code from, the TimeEntryRequest file.

Open App/Http/Requests/TimeEntryRequest.php

![Screen Shot 2021-12-30 at 3.08.06 PM.png](S6-05:%20Services.assets/Screen%20Shot%202021-12-30%20at%203.08.06%20PM.png)

We can delete the two if statements, that will be handled by our new service.

![Screen Shot 2021-12-30 at 3.09.00 PM.png](S6-05:%20Services.assets/Screen%20Shot%202021-12-30%20at%203.09.00%20PM.png)

Now all we have to do is instantiate our service class and pass the $this->duration value to it to work its magic.

Include our new service class in the header of the TimeEntryRequest file.

```php
use App\Services\DurationService;
```

Then instantiate the new service and set $duration equal to what it returns.

```php
$duration = (new DurationService())->convertToMinutes($this->duration);
```

> This code might look a bit strange to you, so let’s break it down. We’re creating a new instance of DurationService, then piping to the class the name of the method we want to call. That method is expecting some value, so we’re passing the value that our Request class has stored as `$this->duration`.

And with that we’re ready to run our tests.

```Bash
$ sail artisan test --filter=TimeEntryTest
```

![Screen Shot 2021-12-30 at 3.15.52 PM.png](S6-05:%20Services.assets/Screen%20Shot%202021-12-30%20at%203.15.52%20PM.png)

Excellent work, this service is going to make data entry much much safer!

Time to add, commit, and push up your changes to GitHub.

```Bash
$ git status
$ git pull
$ git add .
$ git commit -m "adds service for handling mutation of duration data entry"
$ git push
```

