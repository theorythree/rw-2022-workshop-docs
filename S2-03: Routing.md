# S2-03: Routing

Routing is one key concept I want you to be clear with before we move on any further. Routing refers to a set of routing rules that the Laravel framework uses to handle where requests from our users go and how they get managed.

The router checks a user request against a set of rules. If there is match to one of the rules, the matched rule tells the router what to do next.

The router will typically do one of three things:

1. Handle the request itself and provide a response
2. Return a view
3. Pass the request to a specific controller (which may return a view or another response type after it performs some function

Typically the third option is the most common, because the other two do not allow for us to do anything meaningful with the request. We’re just returning a message or a static view. But there are times it makes sense to just return a view directly in the route file. A login page, for example.

Here’s an overview of the request process.

![2022-laravel-rwanda-routing.003.png](S2-03:%20Routing.assets/2022-laravel-rwanda-routing.003.png)

That’s it at a high level, let’s take a look at routing in practice.

Open up your `project root folder` in Visual Studio Code. You should see something like this.

![Screen Shot 2021-11-29 at 11.53.19 AM.png](S2-03:%20Routing.assets/Screen%20Shot%202021-11-29%20at%2011.53.19%20AM.png)

Open up the folder called routes to see the files inside.

::(picture here)::

As you can see we have four files in there. We’re going to focus on the `web.php` file for now. More on the others later. Open up the `web.php` file now by clicking on it from the file explorer.

The block of code we’re interested in is at the bottom.

> Route::get('/', function () {
  return view('welcome');
});

This is an example of a route rule. Let’s break down what it’s doing.

1. The key word `Route` is required to define this as a route
2. The Route is using a function called `get()` which is going to handle any `get` requests. It takes two arguments:
   1. the get `request url` to test on (in this case the `/` means the website domain only)
   2. a `function` to execute if the routing rule is matched (in this case it’s going to just return a view called “welcome”)

This routing rule is actually in use, it’s the one that was used to serve up the Laravel welcome page.

> Notice that Laravel only needs part of the view file name and none of the path information to find this file? This is MVC at work. Laravel knows all views are stored in the `/resources/views` folder so that’s where it looks. It also doesn’t require we put the full file name (`welcome.blade.php)` so the full path and file name, `/resources/views/welcome.blade.php` can be referenced with just `welcome` .

### Setup a Simple Route

Let’s setup our own route to see this in all in action.

At the bottom of the file (around line 20) add the following route rule to your `web.php` file.

```php
Route::get('/message', function () {
  return "hello world!";
});
```

You should have two route rules when you’re done. Save and let’s test them out.

Steps

1. Start up your project by opening the terminal in Visual Studio Code
   1. Select New Terminal from the Terminal menu
   2. In the terminal type the Sail command we learned about earlier which will start up our container

```Bash
$ sail up
```

Your project container will start up, and serve your site on `localhost` .

Open a browser window and type in `localhost` in the address field.

You should see the Laravel startup screen:

![Screen Shot 2021-12-01 at 4.28.42 PM.png](S2-03:%20Routing.assets/Screen%20Shot%202021-12-01%20at%204.28.42%20PM.png)

This is the / route in use.

Now let’s test the route we created. Go to the following url `localhost/message`

You should see our message returned from the route.

![Screen Shot 2021-12-01 at 4.30.31 PM.png](S2-03:%20Routing.assets/Screen%20Shot%202021-12-01%20at%204.30.31%20PM.png)

Excellent work! You just used a route rule to serve up a message!

> This is just a taste of how routing works and we will be going in much more detail as we build our project. For now, just remember that all the user requests start with routes and our `web.php` route file will handle all the routing we need for this workshop.

