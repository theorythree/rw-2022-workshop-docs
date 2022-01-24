# S5-09: Protecting Routes With Middleware (Continued)

In the last episode, we secured one set of routes using a route group for users that have an admin role. Because we have two other roles, I’d like to duplicate that work for them as well. This way we’ll cover all our roles with middleware protection if and when we need it.

1. > Guests - they have access to all public routes so there’s no need to create a route group for them at all. All our unprotected routes will work for this user.
2. > Users - they should have a route group for the routes that are specific to their access.
3. > Owners - same as users, they should have a route group that is specific to their access

### Steps for Creating Middleware

As a review, here are the steps we need to perform to create our new middleware for owners and user roles.

1. Create the middleware files using the Artisan command

```Bash
$ sail artisan make:middleware IsOwnerMiddleware
$ sail artisan make:middleware IsUserMiddleware
```

2. Register middleware files in the `App/Http/Kernal.php` file

```php
protected $routeMiddleware = [
    ...
    'is_owner' => \App\Http\Middleware\IsOwnerMiddleware::class,
    'is_user' => \App\Http\Middleware\IsUserMiddleware::class,
];
```

![Screen Shot 2021-12-27 at 9.47.13 AM.png](S5-09:%20Protecting%20Routes%20With%20Middleware%20(Continued).assets/Screen%20Shot%202021-12-27%20at%209.47.13%20AM.png)

3. Add a route group for owner and user roles in our /routes/web.php file

```php
// OWNER ROUTES
Route::group(['middleware'=>'is_owner'], function(){
  
});

// LOGGED IN USER ROUTES
Route::group(['middleware'=>'is_user'], function(){ 
  
});
```

![Screen Shot 2021-12-27 at 9.46.35 AM.png](S5-09:%20Protecting%20Routes%20With%20Middleware%20(Continued).assets/Screen%20Shot%202021-12-27%20at%209.46.35%20AM.png)

> Notice, I didn’t include a prefix for the owner or user route grouping, and I also didn’t create any routes for these groups either. This is because we do not have any routes that are specific to these user roles yet. For now creating placeholders for the groups will suffice.

4. Update the `handle()` methods for each of our new middleware files.

### IsOwnerMiddleware

```php
public function handle(Request $request, Closure $next)
{
  if (!auth()->check() || !in_array(2, auth()->user()->roles()->pluck('id')->toArray())) {
    abort(403);
  }
  
  return $next($request);
}
```

![Screen Shot 2021-12-27 at 9.52.37 AM.png](S5-09:%20Protecting%20Routes%20With%20Middleware%20(Continued).assets/Screen%20Shot%202021-12-27%20at%209.52.37%20AM.png)

Notice, this `handle()` method is identical as the IsAdminMiddleware class, with one very important difference, I changed the id on `line 19` from `3` to `2` - which is the id of the owner role.

### IsUserMiddleware

```php
public function handle(Request $request, Closure $next)
{
  if (!auth()->check() || !in_array(1, auth()->user()->roles()->pluck('id')->toArray())) {
    abort(403);
  }
  
  return $next($request);
}
```

![Screen Shot 2021-12-27 at 9.55.16 AM.png](S5-09:%20Protecting%20Routes%20With%20Middleware%20(Continued).assets/Screen%20Shot%202021-12-27%20at%209.55.16%20AM.png)

Notice, this `handle()` method is identical as the IsAdminMiddleware and IsOwnerMiddleware classes, with one very important difference, I changed the id on `line 19` from `3` to `1` - which is the id of the user role.

### Wrapping Up

5. Run the tests to verify that we still see green

```Bash
$ sail artisan test
```

![Screen Shot 2021-12-27 at 10.02.09 AM.png](S5-09:%20Protecting%20Routes%20With%20Middleware%20(Continued).assets/Screen%20Shot%202021-12-27%20at%2010.02.09%20AM.png)

6. Add, commit, and push up your changes to GitHub

```Bash
$ git status
$ git pull
$ git add .
$ git commit -m "adds middleware for owner and user roles"
$ git push
```

---

### What do I do if one of my tests failed?

If you're having unexpected tests failures, checkout my troubleshooting guide on the topic. [[Troubleshooting: Unexpected Test Failures]]

