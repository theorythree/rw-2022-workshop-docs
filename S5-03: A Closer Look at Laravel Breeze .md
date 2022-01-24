# S5-03: A Closer Look at Laravel Breeze

The feature branch we’re using has proven to be quite useful. I highly recommend creating branches before you install anything into the project that you’re not 100% sure will not cause problems. In most cases, I create branches even if I’m sure. Doing so gives us a reliable place in time to rewind our codebase if something goes wrong with the install.

We’re going to merge our branch back to main in a bit, but before we do, I want to take a closer look at what exactly Laravel Breeze installed into our project.

Let’s start off by taking a closer look at that routes file that got messed up when we did the install.

1. Open routes/web.php

![Screen Shot 2021-12-21 at 10.57.08 AM.png](S5-03:%20A%20Closer%20Look%20at%20Laravel%20Breeze%20.assets/Screen%20Shot%202021-12-21%20at%2010.57.08%20AM.png)

You can see our welcome route remained intact, and there’s a new route on line 20 through line 22.

```php
Route::get('/dashboard', function () {
    return view('dashboard');
})->middleware(['auth'])->name('dashboard');
```

You can see the route URL is /dashboard and it returns a view called dashboard (`resources/views/dashboard.blade.php`)

It also has this method call hanging on the end of it called `middleware()`, and it is passing a string called `auth`, which appears to be in an array `[` `]`.

That method is telling Laravel to apply a middleware check before it allows this route to be followed. Middleware is how we protect our routes. This one is saying you must be an authorized user (logged in) in order to view the dashboard page.

By applying middleware it protects the route from, in this case a non-logged in user. We’re going to be protecting our routes too. More on that later. The middleware part is referring to the part in the request where a user has made a request, but it hasn’t been passed off to a handler. So the middleware is checked before the controller is even considered.

> You may also have noticed `name()`, which, by the way, is how we name our routes.

lastly, there’s this include line.

```php
require __DIR__.'/auth.php';
```

2. Open the auth.php file in the same routes folder the `web.php` file is in.

![Screen Shot 2021-12-21 at 12.22.26 PM.png](S5-03:%20A%20Closer%20Look%20at%20Laravel%20Breeze%20.assets/Screen%20Shot%202021-12-21%20at%2012.22.26%20PM.png)

These are the routes that Laravel Breeze installed and is using to make everything happen. I’m not going to go over every route, but I will point out they look a bit different than the routes we wrote.

Here’s an example:

```php
Route::get('/register', [RegisteredUserController::class, 'create'])
	->middleware('guest')
	->name('register');
```

The first part looks the same `Route::get(‘/register’,`… but after that it seems different. This is just another way to assign the controller to the route, the method we want to invoke on the controller is passed as a string instead of using our `Controller@method` approach.

We can also see that they are adding the `->middleware(‘guest’)` and `->name(‘register’)` on separate lines. This is a personal preference, they could appear on one long line of code and work the exact same way. Some people find that adding those lines makes the route rule more manageable to read.

In case you’re wondering, ‘`guest`’ refers to a user that is not logged in. So this route is open to the public, which makes sense because it’s a registration route, and a user wouldn’t need an account to access that page.

### Controllers

You can see Laravel Breeze added a few controllers as well. They are in a new folder called app/Http/Controllers/Auth

Let’s open one up and I can show you around.

3. Open app/Http/Controllers/Auth/RegisterUserController.php and scroll to the bottom of the file.

![Screen Shot 2021-12-22 at 4.38.30 AM.png](S5-03:%20A%20Closer%20Look%20at%20Laravel%20Breeze%20.assets/Screen%20Shot%202021-12-22%20at%204.38.30%20AM.png)

This is the controller that handles the registration of the user, as the name would imply. There are a couple of noteworthy things to point out in the store() method.

First, let’s checkout that code block `$request-validate()`

```php
$request->validate([
  'name' => ['required', 'string', 'max:255'],
  'email' => ['required', 'string', 'email', 'max:255', 'unique:users'],
  'password' => ['required', 'confirmed', Rules\Password::defaults()],
]);
```

This is a validation method, and you can see it’s validating the name, email and password fields. So the form that we filled out when you registered your account used these rules to validate the information you entered was valid.

After the field name, an array of validation rules are provided. Adding required means if there isn’t a value provided, validation will fail. For name and email, we are making sure they are providing a string, for name and email  we are not accepting strings over 255 characters in length. And email has two other interesting bits. It’s validating that it’s a properly formatted email address, and that the email must be unique in the users table `‘unique:users’`.

We’re not currently doing validation in our controllers, but don’t worry we’ll fix that soon enough, and when we do we can discuss validation and handling the validation errors when they occur.

Up next, let’s look at the the code responsible for creating our user.

```php
$user = User::create([
  'name' => $request->name,
  'email' => $request->email,
  'password' => Hash::make($request->password),
]);
```

Just two things to point out here. You can see the code references the request like this `$request->name` This is the same as accessing the request like $request->input(’name’) like we did in our controllers. This is a personal preference. I actually prefer using the `$request->name` because it's shorter. We may switch this in our code when we update our controllers later on in the series.

The last three lines are also worth discussing

```php
event(new Registered($user));

Auth::login($user);

return redirect(RouteServiceProvider::HOME);
```

The first of these lines has an event() method. What that does is act as a notifier to other parts of our code that a particular event occurred.  We’ll learn more about events and their counterpart observers later on.

The `Auth::login($user)` is the line of code responsible for creating a session (logging in) the newly registered user.

The last line returns a redirect request. And instead of a hardcoded URL it’s passing a constant variable called `HOME` from the RouteServiceProvider. Let’s open the RouteServiceProvider up and see what the value of the HOME constant is.

4. Open `app/Providers/RouteServiceProvider.php`

![Screen Shot 2021-12-22 at 5.05.46 AM.png](S5-03:%20A%20Closer%20Look%20at%20Laravel%20Breeze%20.assets/Screen%20Shot%202021-12-22%20at%205.05.46%20AM.png)

We can find our answer on line 20. The public const HOME is set to ‘/dashboard` This is a pretty common practice to create a constant variable instead of just hardcoding the URL in the controller.

### Views

You can see the new directory added in `resources/views/auth/`

![Screen Shot 2021-12-21 at 3.34.13 PM.png](S5-03:%20A%20Closer%20Look%20at%20Laravel%20Breeze%20.assets/Screen%20Shot%202021-12-21%20at%203.34.13%20PM.png)

Let’s open up that first file confirm-password.blade.php

![Screen Shot 2021-12-21 at 3.34.54 PM.png](S5-03:%20A%20Closer%20Look%20at%20Laravel%20Breeze%20.assets/Screen%20Shot%202021-12-21%20at%203.34.54%20PM.png)

This looks a bit strange, so let me explain what’s going on in this file.

Laravel Breeze uses Blade Components to build this file. We haven’t discussed what Blade Components are, but they work just like any other kind of component. You can think of them as front-end modular templates that can be reused anywhere in our code. More than just a simple include, these components can have access to our data and can perform certain functionality related to the component itself.

The `<x-` part of the tag is a call to the component itself. It’s how Blade tells the difference between a Blade Component and some other, standard HTML element.

The rest of the tag tells us what component is in use, and of course as you can see you can have components within components to create a layout.

In this particular case, the view is using a layout called `guest`, we can see that file in the `resources/views/layouts/` directory. Open that file up and we can take a peek at it too.

![Screen Shot 2021-12-21 at 3.43.23 PM.png](S5-03:%20A%20Closer%20Look%20at%20Laravel%20Breeze%20.assets/Screen%20Shot%202021-12-21%20at%203.43.23%20PM.png)

You can see this layout file looks like a pretty standard HTML file, with a couple of exceptions.

It looks like we’re echoing out some information, the one I wanted to point out was the {{ $slot }}, this is a placeholder for the template it tells any other partial template that uses this as its layout to insert itself there.

You’ll see other named slots back in the confirm-password.blade.php file too. Again, this is just a spot to indicate where in the template code another template can insert some code.

We’ll learn more about Blade and Blade Components later, but I wanted to point this out for you now so the concept isn’t completely foreign to you when we start that work.

### All Done With Our Tour

Now that we’ve walked through all the new files added by installing Laravel Breeze, it’s a good time to merge our feature branch back into the main branch.

1. Make sure our feature branch is clean
2. Checkout main branch, and do a pull
3. Merge our feature branch into main
4. Push up our merge to GitHub

Here are the commands for all those steps for your reference.

```other
$ git status
$ git checkout main
$ git pull
$ git merge dan/breeze
$ git push
```

With our changes to our feature branch pushed up, we’re ready to move on to next steps.

