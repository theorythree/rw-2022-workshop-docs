# S7-06: Policies

We’re well into our refactoring and have made some good improvements to our code, but we have a bit of a problem right now.

To see what I mean, let’s open up the web routes folder.

Open routes/web.php

![Screen Shot 2022-01-10 at 5.00.52 PM.png](S7-06:%20Policies.assets/Screen%20Shot%202022-01-10%20at%205.00.52%20PM.png)

Take a look a the clients and projects routes. Right now they are unprotected, any person, including guests, can add, edit, and delete a client or a project. That’s not what we want.

So we have to protect these routes, but where do they go?

Remember, we only want owners to be able to add a client or a project, and we don’t want the user to be able to do that. But we can’t just protect these routes with the owner role middleware, because we do want users to be able to view clients and projects.

What to do?

The answer is to use a feature of Laravel called Policies. Policies are classes that help organize the authorizations of certain actions in our application. We can pick and choose which roles can do which things on any of our models.

Before we start our work, we're going to create another branch from the refactor branch. That way we can safely do our work and we have a clean spot to revert to if we want to undo this initiative.

```Bash
$ git checkout -b dan/policies
```

![Screen Shot 2022-01-10 at 5.18.39 PM.png](S7-06:%20Policies.assets/Screen%20Shot%202022-01-10%20at%205.18.39%20PM.png)

Creating a policy is simple, and I’m sure by now you know the way we do that is by using an Artisan command.

Let’s start off by creating a policy for clients.

```Bash
$ sail artisan make:policy ClientPolicy --model=Client
```

![Screen Shot 2022-01-10 at 5.25.18 PM.png](S7-06:%20Policies.assets/Screen%20Shot%202022-01-10%20at%205.25.18%20PM.png)

By adding the model flag, we can specify which model we want to bind this policy to. Laravel will automatically register this policy for our model even without the model flag as long as the policy is named with the exact name of the model and the word Policy after it. For example a user policy would be UserPolicy, because User is the name of the model we want to associate with the policy.

The model flag has one benefit, it provides a set of example methods we can use and it will automatically include the model in the header of the policy file for us.

Open App/Policies/ClientPolicy.php

![Screen Shot 2022-01-10 at 5.28.20 PM.png](S7-06:%20Policies.assets/Screen%20Shot%202022-01-10%20at%205.28.20%20PM.png)

You can see Laravel stubbed out a few methods. Each of these is named to reference the ability of the user for this policy. The method names are not exactly the same, so here’s a reference table for you to look at to align what method the controller uses and what method name it will call in the policy class.

| **Controller Method** | **Policy Method** |
| --------------------- | ----------------- |
| index                 | viewAny           |
| show                  | view              |
| create                | create            |
| store                 | create            |
| edit                  | update            |
| update                | update            |
| destroy               | delete            |

Each of our policy methods expects to return a bool, true if the user is allowed to perform this action, and false if they are not.

### ViewAny()

We want all users to be able to see a full list of clients, so we can just return `true`.

```php
public function viewAny(User $user)
{
  return true;
}
```

### View()

We wan all users to be able to see a detail view of a client, so we can just return `true` for this one too.

```php
public function view(User $user, Client $client)
{
  return true;
}
```

### Create()

We only want owners to be able to create clients, so for this one, we’re going to borrow our owner check from our middleware.

Add our Auth facade and Role model to the header of this file.

```php
use Auth;
use App\Models\Role;
```

And in our create() method, we can use that owner check that we talked about.

```php
public function create(User $user)
{
  return auth()->check() && in_array(Role::IS_OWNER, $user->roles()->pluck('id')->toArray());
}
```

The only difference is I’m checking the roles of the $user that is getting passed into this method, not the logged in user. This is so we can access any user to see if they can, in this case, create, a client.

Also, you can see we can just return the result of our check, a long form version of this would be:

```php
// LONG FORM EXAMPLE
public function create(User $user)
{
  if (auth()->check() && in_array(Role::IS_OWNER, $user->roles()->pluck('id')->toArray())) {
    return true;
  } else {
    return false;
  }
}
```

I think you’ll agree the short version is much cleaner.

### Update()

We’re going to do the exact same check on the update() method.

```php
public function update(User $user, Client $client)
{
  return auth()->check() && in_array(Role::IS_OWNER, $user->roles()->pluck('id')->toArray());
}
```

### Delete()

And same check for delete.

```php
public function delete(User $user, Client $client)
{
  return auth()->check() && in_array(Role::IS_OWNER, $user->roles()->pluck('id')->toArray());
}
```

### Restore() + ForceDelete()

For these two, we’re not going to let anyone do these things, so we’ll return false for both.

```php
public function restore(User $user, Client $client)
{
  return false;
}
```

```php
public function forceDelete(User $user, Client $client)
{
  return false;
}
```

And here’s the fully updated ClientPolicy class in case you want to doublecheck your work.

```php
<?php

namespace App\Policies;

use App\Models\Client;
use App\Models\User;
use Illuminate\Auth\Access\HandlesAuthorization;
use Auth;
use App\Models\Role;

class ClientPolicy
{
    use HandlesAuthorization;

  /**
   * Determine whether the user can view any models.
   *
   * @param  \App\Models\User  $user
   * @return \Illuminate\Auth\Access\Response|bool
   */
    public function viewAny(User $user)
    {
      return true;
    }

    /**
     * Determine whether the user can view the model.
     *
     * @param  \App\Models\User  $user
     * @param  \App\Models\Client  $client
     * @return \Illuminate\Auth\Access\Response|bool
     */
    public function view(User $user, Client $client)
    {
      return true;
    }

    /**
     * Determine whether the user can create models.
     *
     * @param  \App\Models\User  $user
     * @return \Illuminate\Auth\Access\Response|bool
     */
    public function create(User $user)
    {
      return auth()->check() && in_array(Role::IS_OWNER, $user->roles()->pluck('id')->toArray());
    }

    /**
     * Determine whether the user can update the model.
     *
     * @param  \App\Models\User  $user
     * @param  \App\Models\Client  $client
     * @return \Illuminate\Auth\Access\Response|bool
     */
    public function update(User $user, Client $client)
    {
      return auth()->check() && in_array(Role::IS_OWNER, $user->roles()->pluck('id')->toArray());
    }

    /**
     * Determine whether the user can delete the model.
     *
     * @param  \App\Models\User  $user
     * @param  \App\Models\Client  $client
     * @return \Illuminate\Auth\Access\Response|bool
     */
    public function delete(User $user, Client $client)
    {
      return auth()->check() && in_array(Role::IS_OWNER, $user->roles()->pluck('id')->toArray());
    }

    /**
     * Determine whether the user can restore the model.
     *
     * @param  \App\Models\User  $user
     * @param  \App\Models\Client  $client
     * @return \Illuminate\Auth\Access\Response|bool
     */
    public function restore(User $user, Client $client)
    {
      return false;
    }

    /**
     * Determine whether the user can permanently delete the model.
     *
     * @param  \App\Models\User  $user
     * @param  \App\Models\Client  $client
     * @return \Illuminate\Auth\Access\Response|bool
     */
    public function forceDelete(User $user, Client $client)
    {
      return false;
    }
}
```

So how do we hook up this policy to where we want to use it? Well, we can use these policies in our Blade templates and in our controllers, so for now, let’s work on the ClientController.

Open App/Http/Controllers/ClientController.php

![Screen Shot 2022-01-11 at 8.55.36 AM.png](S7-06:%20Policies.assets/Screen%20Shot%202022-01-11%20at%208.55.36%20AM.png)

All we have to do is do an authorization check with the policy action we’re performing for each method in this controller.

We do not have a test for index() so we’re going to skip that controller method for now. We’ll come back to it once we work on our application front-end.

### Create() + Store()

You might remember from the table which mapped the names of the Controller methods and Policy methods, the create() and store() controller methods both check the create() policy method.

But we have one thing we need to do first. Right now our store() method is using our custom ClientRequest class and it is doing some authorization for us in its method authorize(). We do not want the ClientRequest to handle some of our request authorizations if we’re using policies to handle them all in one centralized location.

Open the App/Http/Requests/ClientRequest.php file

![Screen Shot 2022-01-11 at 9.08.58 AM.png](S7-06:%20Policies.assets/Screen%20Shot%202022-01-11%20at%209.08.58%20AM.png)

Remove the entire authorize() method from this file. We still want it to validate our user’s input, but we want our policies to handle our authorizations from now on.

```php
// REMOVE THIS METHOD
public function authorize()
{
    return auth()->check();
}
```

![Screen Shot 2022-01-11 at 9.10.32 AM.png](S7-06:%20Policies.assets/Screen%20Shot%202022-01-11%20at%209.10.32%20AM.png)

Great, now returning to the ClientController, we can add our authorization check in the create() and store() methods.

The authorization check looks like this for create() and store()

```php
$this->authorize('create',Client::class);
```

The method takes just two arguments, the policy method name, in this case create, and the model class we want to associate or bind to, in this case the Client.

```php
public function create()
{
  $this->authorize('create',Client::class);
  return view('clients.create');
}
```

```php
public function store(ClientRequest $request)
{
  $this->authorize('create',Client::class);
  $client = Client::create($request->validated());
}
```

### Show()

We’ll skip the show() method as well for the same reason we skipped the index() method.

### Edit() + Update()

Just like create() and store() these both use the same policy to verify a user can perform this action.

For this one however, Laravel will we pass the client to be updated and Laravel will infer which model class we want to check.

```php
$this->authorize('update',$client);
```

```php
public function edit(Client $client)
{
  $this->authorize('update',$client);
  return view('clients.edit',compact('client'));
}
```

```php
public function update(ClientRequest $request, Client $client)
{
  $this->authorize('update',$client);
  $client->update($request->validated());
}
```

### Destroy()

The destroy() method calls the delete() policy method, and it too requires we pass the client that is being deleted.

```php
public function destroy(Client $client)
{
  $this->authorize('delete',$client);
  $client->delete();
}
```

And now, let’s check our work by running our client tests.

```Bash
$ sail artisan test --filter=ClientTest
```

![Screen Shot 2022-01-11 at 9.29.02 AM.png](S7-06:%20Policies.assets/Screen%20Shot%202022-01-11%20at%209.29.02%20AM.png)

This is pretty pretty good. We have all green on clients.

Before we move on, let’s take a peek at something that’s not looking so good in our ClientPolicy class.

Open App/Policies/ClientPolicy.php

![Screen Shot 2022-01-11 at 9.30.53 AM.png](S7-06:%20Policies.assets/Screen%20Shot%202022-01-11%20at%209.30.53%20AM.png)

The lines of code that have me concerned are the ones that perform the owner check.

```php
return auth()->check() && in_array(Role::IS_OWNER, $user->roles()->pluck('id')->toArray());
```

It’s okay the way that we’re checking, but we’re performing the same logic multiple times in the same file, and in fact, we borrowed this from our middleware, so it also has the same logic check as well. This isn’t very safe. If we decided to change how we define who an owner is we would have to check all the places in our codebase to do so.

There’s a better way.

What I’d like to do is have the User model tell us if the user is an admin, owner, or user. Then we could access the appropriate method through the model to verify the user has a particular role.

Open the App/Models/User.php file

![Screen Shot 2022-01-11 at 9.41.11 AM.png](S7-06:%20Policies.assets/Screen%20Shot%202022-01-11%20at%209.41.11%20AM.png)

Now there’s two approaches to this. We could make one method called something like hasRole() which would take the name of the role and check that against the user, or we could create three different methods isAdmin(), isOwner(), and isUser() to do the different logic. With just a few roles in our application, I prefer the second strategy because it prevents possible typos when passing the name of the role.

Add the following three methods to the bottom of the class body (just below the roles() method).

```php
public function isAdmin(): Bool
{

}

public function isOwner(): Bool
{

}

public function isUser(): Bool
{

}
```

You’ll notice I’m adding `: Bool` to the end of the method name, what’s that all about?

That’s called return type casting. What it means is I’m setting up these methods and declaring that we will return a bool. It’s an optional step, but it makes these methods more safe because we’re explicitly declaring what the return value type should be.

We’re going to need to import a Role model in our header because we use that in our tests.

```php
use App\Models\Role;
```

And for the methods themselves, we’re going to borrow our logic from our ClientPolicy, because that's where we're going to replace the code that's currently there.

```php
public function isAdmin(): Bool
{
  return in_array(Role::IS_ADMIN, $this->roles()->pluck('id')->toArray());
}

public function isOwner(): Bool
{
  return in_array(Role::IS_OWNER, $this->roles()->pluck('id')->toArray());
}

public function isUser(): Bool
{
  return in_array(Role::IS_USER, $this->roles()->pluck('id')->toArray());
}
```

You can see I’m using $this instead in the places that used $user so that the check will apply to which ever user this model is bound to.

Now we can use these methods wherever we have a user in our project. Let’s test this out by going back to our ClientPolicy file.

In the create() method, let’s update it:

```php
public function create(User $user)
{
  return auth()->check() && $user->isOwner();
}
```

Much much cleaner and much easier to read.

The same check will apply to the update() and delete() methods as well.

```php
public function update(User $user, Client $client)
{
  return auth()->check() && $user->isOwner();
}
```

```php
public function delete(User $user, Client $client)
{
  return auth()->check() && $user->isOwner();
}
```

And now let’s run our ClientTest to see if we still pass all of our tests.

```Bash
$ sail artisan test --filter=ClientTest
```

![Screen Shot 2022-01-11 at 12.03.15 PM.png](S7-06:%20Policies.assets/Screen%20Shot%202022-01-11%20at%2012.03.15%20PM.png)

Now let’s fix up our Middleware. We can use our new user methods to check if a user is an admin, owner or user.

### App/Http/Middleware/IsOwnerMiddleware.php

```php
if (!auth()->check() || !auth()->user()->isOwner()) {
 abort(403);
}
```

### App/Http/Middleware/IsAdminMiddleware.php

```php
if (!auth()->check() || !auth()->user()->isUser()) {
  abort(403);
}
```

### App/Http/Middleware/IsUserMiddleware.php

```php
if (!auth()->check() || !auth()->user()->isAdmin()) {
  abort(403);
}
```

And, for your convenience here is the full set of code for each:

```php
<?php

namespace App\Http\Middleware;

use Closure;
use Illuminate\Http\Request;
use App\Models\Role;

class IsOwnerMiddleware
{
    /**
     * Handle an incoming request.
     *
     * @param  \Illuminate\Http\Request  $request
     * @param  \Closure(\Illuminate\Http\Request): (\Illuminate\Http\Response|\Illuminate\Http\RedirectResponse)  $next
     * @return \Illuminate\Http\Response|\Illuminate\Http\RedirectResponse
     */
    public function handle(Request $request, Closure $next)
    {
      if (!auth()->check() || !auth()->user()->isOwner()) {
        abort(403);
      }
      
      return $next($request);
    }
}
```

```php
<?php

namespace App\Http\Middleware;

use Closure;
use Illuminate\Http\Request;
use App\Models\Role;

class IsAdminMiddleware
{
    /**
     * Handle an incoming request.
     *
     * @param  \Illuminate\Http\Request  $request
     * @param  \Closure(\Illuminate\Http\Request): (\Illuminate\Http\Response|\Illuminate\Http\RedirectResponse)  $next
     * @return \Illuminate\Http\Response|\Illuminate\Http\RedirectResponse
     */
    public function handle(Request $request, Closure $next)
    {
      if (!auth()->check() || !auth()->user()->isAdmin()) {
        abort(403);
      }
      
      return $next($request);
    }
}
```

```php
<?php

namespace App\Http\Middleware;

use Closure;
use Illuminate\Http\Request;
use App\Models\Role;

class IsUserMiddleware
{
    /**
     * Handle an incoming request.
     *
     * @param  \Illuminate\Http\Request  $request
     * @param  \Closure(\Illuminate\Http\Request): (\Illuminate\Http\Response|\Illuminate\Http\RedirectResponse)  $next
     * @return \Illuminate\Http\Response|\Illuminate\Http\RedirectResponse
     */
    public function handle(Request $request, Closure $next)
    {
      if (!auth()->check() || !auth()->user()->isUser()) {
        abort(403);
      }
      
      return $next($request);
    }
}
```

We’ve done a lot of good work, let’s add and commit our changes (and not push them up)

```Bash
$ git status
$ git add .
$ git commit -m "adds policies for Client, adds role check methods for user"
```

