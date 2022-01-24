# S5-08: Protecting Routes With Middleware

Now that we understand the basics of middleware, we’re going to put it into practice so we can use it to protect our routes.

To create and implement middleware, we have just three steps to complete.

1. > Create middleware class
2. > Register middleware class with Http Kernel
3. > Protect routes by using the registered name in route grouping

### Creating Middleware Class

I’ve said it before and I’ll say it again, Laravel, makes file creation easy.

1. Issue the following Artisan command in the terminal

```Bash
$ sail artisan make:middleware IsAdminMiddleware
```

The new middleware file named IsAdminMiddleware will be created in the `App/Http/Middleware` directory.

2. Open App/Http/Middleware/IsAdminMiddleware.php

![Screen Shot 2021-12-26 at 8.35.07 PM.png](S5-08:%20Protecting%20Routes%20With%20Middleware.assets/Screen%20Shot%202021-12-26%20at%208.35.07%20PM.png)

> The stubbed method handle() is what handles our middleware check. And as the name would imply, we’re going to check to see if our user is logged in and they are an admin. Technically we’re going to test on the opposite of both conditions.

The current `handle()` method looks like this:

```php
public function handle(Request $request, Closure $next)
{
  return $next($request);
}
```

Let’s add our middleware check in the handle() method just above the return line (`line 19` in my screenshot).

2. Add the following middleware check above line 19.

```php
if (!auth()->check() || !in_array(3, auth()->user()->roles()->pluck('id')->toArray())) {
  abort(403);
}
```

This if statement checks to see if the user is NOT logged in by using the auth()->check() method, which when adding the not operator `!` before it checks if the user is NOT logged in. If the user is logged in then we have a secondary check, which reads a bit long.

The `in_array()` method will check to see if a particular value is in the array which proceeds it, again I’m using the not operator `!` to check for conditions where the value is not found in the array. I'm looking for a 3 to not be in the array.

The `auth()->user()->roles()->pluck('id')->toArray()` needs a bit more explanation:

> I’m creating an array of all the user roles (remember, there may be many) by accessing the logged in user through `auth()->user(),` then I’m accessing the users roles with `roles()`, which should return a collection of roles (which would include the `id`, `name`, `created_at`, and `updated_at` values for each of the returned roles for that user). The `pluck()` method, will extract just the `ids` and return a collection with just that column, finally the `toArray()` turns the found collection of ids into an array.

So if the user is not logged in and/or they do not have a 3 value for the list of roles assigned to them, then the if condition will pass and the abort() method will be called. The abort() method is passed a 403 - which is the error code for denied access.

### Register Middleware

Now that we have a middleware file, we need to register it before we can use it in our routes.

1. Open App/Http/Kernal.php

![Screen Shot 2021-12-26 at 9.00.56 PM.png](S5-08:%20Protecting%20Routes%20With%20Middleware.assets/Screen%20Shot%202021-12-26%20at%209.00.56%20PM.png)

Scroll to the bottom of the file and look for the protected `$routeMiddleware = [ … ]` array of registered middleware classes.

2. Add our middleware class to the list and give it a name of `is_admin`.

```php
protected $routeMiddleware = [
  ...
  'is_admin' => \App\Http\Middleware\IsAdminMiddleware::class,
]:
```

![Screen Shot 2021-12-26 at 9.05.50 PM.png](S5-08:%20Protecting%20Routes%20With%20Middleware.assets/Screen%20Shot%202021-12-26%20at%209.05.50%20PM.png)

> You’l notice we applied the path `App\Http\Middleware\` along with the name of our class , this is the same as using the `use App\Http\Middleware\IsAdminMiddleware` at the top of our file and then just the `IsAdminMiddleware` in the value of the array. Personal preference, but I tend to the `use Class\Path\ClassName` method when I refer to the class multiple times in the file and the `Class\Path\ClassName` method when it's only referenced once in the file. In both cases, the goal is to save on lines of code/typing.

### Protect Routes

Now that we have our middleware registered as is_admin, we can use that name in our routes to protect them.

1. Open routes/web.php

![Screen Shot 2021-12-26 at 9.14.59 PM.png](S5-08:%20Protecting%20Routes%20With%20Middleware.assets/Screen%20Shot%202021-12-26%20at%209.14.59%20PM.png)

To keep things simple, we’re going to create a route group and have a simple testing route within that group.

2. Add a route group with a prefix of dashboard which we will use to protect all our dashboard routes.

```php
Route::group(['prefix'=>'dashboard', 'middleware'=>'is_admin'], function(){

});
```

The routing group will prefix any route within it to test on /dashboard/XXX so a route testing for a url of test will actually test for /dashboard/test instead.

3. Add a testing route within the route group

```php
Route::get('test', function(){
  return "hello there!";
});
```

The entire block of code will look like this:

```php
Route::group(['prefix'=>'dashboard', 'middleware'=>'is_admin'], function(){
  Route::get('test', function(){
    return "hello there!";
  });
});
```

Open up a browser and test the route by going to the following address: `localhost/dashboard/test`

![Screen Shot 2021-12-26 at 9.21.44 PM.png](S5-08:%20Protecting%20Routes%20With%20Middleware.assets/Screen%20Shot%202021-12-26%20at%209.21.44%20PM.png)

> Notice, we got a 403 Forbidden message NOT a 404, page not found. This is our middleware test working.

Let’s grant ourselves the admin role in the directly in the database to test.

1. Open TablePlus and connect to our `tempo` database
2. Open the role_user table

![Screen Shot 2021-12-26 at 9.25.52 PM.png](S5-08:%20Protecting%20Routes%20With%20Middleware.assets/Screen%20Shot%202021-12-26%20at%209.25.52%20PM.png)

We just need to insert a record to assign the role to the user we registered earlier. This table only requires a role_id, which we know is going to be 3, and a `user_id`, which should be 1, but you can double check that for the user in your users table.

3. Insert the `role_id` and `user_id` in the `role_user` database table.

![Screen Shot 2021-12-26 at 9.28.32 PM.png](S5-08:%20Protecting%20Routes%20With%20Middleware.assets/Screen%20Shot%202021-12-26%20at%209.28.32%20PM.png)

4. Save your insert by clicking on the commit icon in the upper left part of the window.

![Screen Shot 2021-12-26 at 9.30.00 PM.png](S5-08:%20Protecting%20Routes%20With%20Middleware.assets/Screen%20Shot%202021-12-26%20at%209.30.00%20PM.png)

> Adding a record in the `role_user` pivot table, it establishes a relationship between a role and a user.

5. Refresh your browser window (or go to `localhost/dashboard/test` again)

![Screen Shot 2021-12-26 at 9.31.55 PM.png](S5-08:%20Protecting%20Routes%20With%20Middleware.assets/Screen%20Shot%202021-12-26%20at%209.31.55%20PM.png)

> So as you can see with our test route, any routes within the routing group we created will only be accessible by admins because our middleware checks explicitly for admins before those grouped route requests will be handled.

Let’s apply our grouping to our real-world need to protect all the /dashboard routes.

1. Open routes/web.php

![Screen Shot 2021-12-26 at 9.37.27 PM.png](S5-08:%20Protecting%20Routes%20With%20Middleware.assets/Screen%20Shot%202021-12-26%20at%209.37.27%20PM.png)

We already have the group, and we can eliminate the test route within that group. The only thing we have to do after that is move the current dashboard route into our group and update it.

2. Update the dashboard route group to match the following:

```php
Route::group(['prefix'=>'dashboard', 'middleware'=>'is_admin'], function(){
  Route::get('/', function () { return view('dashboard'); })->name('dashboard');
});
```

![Screen Shot 2021-12-26 at 9.41.03 PM.png](S5-08:%20Protecting%20Routes%20With%20Middleware.assets/Screen%20Shot%202021-12-26%20at%209.41.03%20PM.png)

> You can see we removed the ‘`dashboard`’ from the url of the route rule. If we didn’t do that the route would be testing a url of `dashboard/dashboard`. We also didn't need middleware for that route, because we replaced it with our own in the route group, and any middleware defined on the group applies to ALL of the routes contained within it.

Now that our dashboard routes are protected from all users that do not have an admin role, we can run our tests to see if they sync up with our expected reality.

```Bash
$ sail artisan test
```

![Screen Shot 2021-12-26 at 10.23.33 PM.png](S5-08:%20Protecting%20Routes%20With%20Middleware.assets/Screen%20Shot%202021-12-26%20at%2010.23.33%20PM.png)

> We’re still getting a couple of failed tests. If we look closely we’ll see the error for each fail is in the status code we get back for the `test_xxxx_can_not_access_dashboard()` methods in each of our tests. The status returned in `403`, not `302`.

> This happens from time-to-time, we expect certain things to happen when we write the tests, then we build the things that should be tested and that process undoes or forces us to make a modification to our tests.

> When I originally wrote those tests, my expectation was that we would receive a redirect response, but when we wrote the middleware I decided it makes more sense to deny access to those pages with an `abort(403)`. We could adjust the middleware to redirect the user to the login screen, but I prefer the `403 response`, so we’ll keep our middleware the same and adjust our tests to match.

1. Open the `GuestTest`, `OwnerTest`, and `UserTest` and change the `$response->assertStatus(302)` to `$response->assertStatus(403)` in the `test_xxxxx_can_not_access_dashboard()` methods.

### GuestTest.php

```php
public function test_guest_can_not_access_dashboard()
{
  $response = $this->get('/dashboard');
  $response->assertStatus(403);
}
```

### OwnerTest.php

```php
public function test_owner_can_not_access_dashboard()
{
  $ownerRole = Role::where('name','owner')->first();
  $this->assertTrue($ownerRole->id == 2);
  
  $user = User::factory()->create();
  $user->roles()->attach($ownerRole->id);

  $response = $this->actingAs($user)->get('/dashboard');
  $response->assertStatus(403);
}
```

### UserTest.php

```php
public function test_user_can_not_access_dashboard()
{  
  $userRole = Role::where('name','user')->first();
  $this->assertTrue($userRole->id == 1);
  
  $user = User::factory()->create();
  $user->roles()->attach($userRole->id);

  $response = $this->actingAs($user)->get('/dashboard');
  $response->assertStatus(403);
}
```

2. Run tests

```Bash
$ sail artisan test
```

![Screen Shot 2021-12-26 at 10.33.50 PM.png](S5-08:%20Protecting%20Routes%20With%20Middleware.assets/Screen%20Shot%202021-12-26%20at%2010.33.50%20PM.png)

And we’re finally back to green. After you’re done celebrating, be sure to commit and push up your code.

3. Add, commit, and push your changes up to GitHub

```Bash
$ git status
$ git add .
$ git commit -m "updates guest, owner, and user tests, adds middleware check for admins on dashboard route"
$ git push
```

---

### Extra Credit

If you’re interested, here are some thoughts I’d like to share with you about redirecting routes. This is just commentary, so if you’d like to move on to the next episode you may, but if you stick around.

### Redirect to Login, Deny Access, or Page Not Found?

When our users find themselves in places they shouldn’t what’s the best thing to do in our middleware check? There are three options that are most common.

1. 302: Redirect them to the login screen
2. 403: Return access restricted error
3. 404: Return page not found error

Which one is the best? Each of these has their place, and should be used knowing xxx

### 302: Redirect

Redirects have their place, they point the user to another page, but should only be used when that other page is helpful to them.

So, for example, if a user is not logged in and they try to access a page that requires a login, redirecting them to the login screen is quite helpful. However, if they are logged in and they try to access a page they do not have access to, then redirecting a logged in user to the login screen will be confusing to them. They’ll likely log in again only to be routed back to the login screen without any explanation why they were redirected. It’s a bad user experience.

### 403: Restricted Access

When a user tries to access a page they do not belong to, sending them a 403 error is appropriate. The error is clear to the user, yes we have some content on this page, but you're not allowed to view it. Seems pretty straightforward, but it does have one notable drawback.

By showing a page response at all, you’re giving the user some information that they might not have had available to them. If, for example, we return a 403 to users who are not logged in for the dashboard routes, then all the users who try to access the dashboard, now know that there is a dashboard and it has a url of /dashboard. It’s as if we revealed the location of a locked door, that may be locked, but now they know the location of a locked door. Some less-than-honest people might try to exploit that fact and see that as an invitation to attempt to hack the protection of that route because they know it’s there.

### 404: Page Not Found

We could update our middleware to return a page not found (404) to users that are not logged in or do not have the proper role. By doing this, we’re not even hinting that there is a restricted route that they may have found by accident. I use this approach quite a bit in my projects, but it does have one drawback.

It’s not very helpful to those who really should have access but, for some reason, don’t. It’s quite common to setup accounts for people and forget to give them all the proper access they need. Imagine a site with hundreds or thousands of users. If one of those users was supposed to be an admin, and we they didn’t get that role assigned to them, they wouldn’t have much to go on to fix their issue. For example, they might try to access the dashboard and just assume that they mistyped the url, then when they reach out to another admin, they wouldn’t know why the url is not working for them but it is for another admin. It could be more challenging for everyone to debug this situation. A 403 is more clear.

