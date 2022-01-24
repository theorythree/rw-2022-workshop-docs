# S7-08: ToDos

I don’t know about you, but I’m happy. We have pretty good test coverage for our back-end, and we’re almost through all of our refactoring.

I want to take a moment to complete our list of todos in the project. We don’t want to forget that these things need to be handled before our list grows our of control.

As previously mentioned, I’m using a TODO plugin for Visual Studio Code that pulls all the TODOs into a nice list.

![Screen Shot 2022-01-11 at 1.37.04 PM.png](S7-08:%20ToDos.assets/Screen%20Shot%202022-01-11%20at%201.37.04%20PM.png)

Let’s start at the top and knock a few of these out.

First, let’s create a feature branch. You should be on main branch when you do this.

```Bash
$ git status
$ git pull
$ git checkout -b dan/todos
```

Next, open our first todo in routes/web.php

![Screen Shot 2022-01-11 at 1.37.44 PM.png](S7-08:%20ToDos.assets/Screen%20Shot%202022-01-11%20at%201.37.44%20PM.png)

We can make this better, now that we’re using policies to limit the functionality, we may not even need middleware for our owner and user route groups. Still, once we get into the front-end work, we may need these groups, so I’m inclined to leave them for now.

But we do want to address those open routes (projects and clients).

Let’s move them into the logged in user route group.

```php
// LOGGED IN USER ROUTES
Route::group(['middleware'=>'auth'], function(){ 
  Route::resource('/clients','App\Http\Controllers\ClientController');
  Route::resource('/projects','App\Http\Controllers\ProjectController');
  Route::resource('/time','App\Http\Controllers\TimeEntryController');
});
```

And let’s run our tests to see we still see green.

```Bash
$ sail artisan test
```

![Screen Shot 2022-01-11 at 1.45.12 PM.png](S7-08:%20ToDos.assets/Screen%20Shot%202022-01-11%20at%201.45.12%20PM.png)

I’m also going to remove the TODO and collapse those route groups that do not have rules in them quite yet to save room.

Here’s the whole updated file for your reference.

```php
<?php

use Illuminate\Support\Facades\Route;

/*
|--------------------------------------------------------------------------
| Web Routes
|--------------------------------------------------------------------------
|
| Here is where you can register web routes for your application. These
| routes are loaded by the RouteServiceProvider within a group which
| contains the "web" middleware group. Now create something great!
|
*/

Route::get('/', function () {
    return view('welcome');
});

require __DIR__.'/auth.php';

// ADMIN ROLE ROUTES
Route::group(['prefix'=>'dashboard', 'middleware'=>'is_admin'], function(){
  Route::get('/', function () { return view('dashboard'); })->name('dashboard');
});

// OWNER ROLE ROUTES
Route::group(['middleware'=>'is_owner'], function(){ });

// USER ROLE ROUTES
Route::group(['middleware'=>'is_user'], function(){ });

// LOGGED IN USER ROUTES
Route::group(['middleware'=>'auth'], function(){ 
  Route::resource('/clients','App\Http\Controllers\ClientController');
  Route::resource('/projects','App\Http\Controllers\ProjectController');
  Route::resource('/time','App\Http\Controllers\TimeEntryController');
});
```

On to the next TODO.

![Screen Shot 2022-01-11 at 1.47.41 PM.png](S7-08:%20ToDos.assets/Screen%20Shot%202022-01-11%20at%201.47.41%20PM.png)

Open tests/Feature/AdminTest.php

![Screen Shot 2022-01-11 at 1.48.12 PM.png](S7-08:%20ToDos.assets/Screen%20Shot%202022-01-11%20at%201.48.12%20PM.png)

We decided that the dashboard would be a good example page for us to test the Admin’s access, so we can just remove that first TODO. We can also remove the second one for the same reason.

While we’re here, we could improve these tests. After all we didn’t have any real work so it seems a shame to leave this one untouched.

I want to add an admin user to the setup() method, like we did in some of our other tests.

Here’s the updated setup() method.

```php
public function setUp(): void
{
  parent::setUp();
  $this->artisan('db:seed --class=RoleSeeder');
  $this->admin = User::factory()->create();
  $this->admin->roles()->attach(Role::IS_ADMIN);
}
```

Now, let’s put this admin user to use in our tests.

```php
public function test_admin_can_access_home_page()
{
  $response = $this->actingAs($this->admin)->get('/');
  $response->assertStatus(200);
}
```

Much better right?!?

```php
public function test_admin_can_access_dashboard()
{
  $response = $this->actingAs($this->admin)->get('/dashboard');
  $response->assertStatus(200);
}
```

And now we should verify our AdminTest still works.

```Bash
$ sail artisan test --filter=AdminTest
```

![Screen Shot 2022-01-11 at 1.56.12 PM.png](S7-08:%20ToDos.assets/Screen%20Shot%202022-01-11%20at%201.56.12%20PM.png)

Okay great!

![Screen Shot 2022-01-11 at 1.57.02 PM.png](S7-08:%20ToDos.assets/Screen%20Shot%202022-01-11%20at%201.57.02%20PM.png)

Open test/Feature/GuestTest.php

![Screen Shot 2022-01-11 at 1.57.36 PM.png](S7-08:%20ToDos.assets/Screen%20Shot%202022-01-11%20at%201.57.36%20PM.png)

This is pretty good too, but maybe we should add a test to make sure that guests cannot access a page restricted to users as well.

```php
public function test_guest_can_not_access_clients_page()
{
  $response = $this->get('/clients');
  $response->assertStatus(403);
}
```

Now let’s run our new test.

```Bash
$ sail artisan test --filter=test_guest_can_not_access_clients_page
```

![Screen Shot 2022-01-11 at 2.56.44 PM.png](S7-08:%20ToDos.assets/Screen%20Shot%202022-01-11%20at%202.56.44%20PM.png)

And we can see it failed… But why? It’s literally the same test as the dashboard test right?

Well, yes, the test is the same but if you look closely you’ll see accessing the clients route is returning a 302, which is a response code we get when the user is redirected NOT a restricted response.

And if you open our web routes file you’ll see why this is happening.

![Screen Shot 2022-01-11 at 3.01.09 PM.png](S7-08:%20ToDos.assets/Screen%20Shot%202022-01-11%20at%203.01.09%20PM.png)

You can see that the clients, projects, and time routes are all using the auth middleware and it behaves a bit differently than the middleware we setup for admins. Our IS_ADMIN middleware will return a 403 unauthorized to anyone who is not an admin, but the auth middleware does a redirect to the login.

I think this gives us an opportunity to rethink the user flow here. For example, if an admin user knows the url for the dashboard and they just happen to not be logged in, we want to give them something useful to do, namely direct them to the login screen. In other words our guest user may just be an admin, owner, or user who hasn’t logged in yet. So let’s redirect all guests to the login screen when they try to access a page, and if they are logged in we show them a 403 if they’re not allowed access.

To do this, open App/Http/Middleware/IsAdminMiddleware.php

![Screen Shot 2022-01-11 at 3.05.34 PM.png](S7-08:%20ToDos.assets/Screen%20Shot%202022-01-11%20at%203.05.34%20PM.png)

I want to break that test up into two tests.

Here’s the updated code:

```php
public function handle(Request $request, Closure $next)
{
  if (!auth()->check()) {
    return redirect(route('login'));
  }
  
  if (!auth()->user()->isAdmin()) {
    abort(403);
  }
  
  return $next($request);
}
```

Yes, there’s more code, but if a user is not logged in, they immediately get redirected to the login screen, and if they are logged in then the next test kicks in and verifies they’re an admin and allowed to be on the page. This is a much nicer user experience.

Let’s do the same for the IsOwnerMiddleware and IsUserMiddleware files too.

### IsOwnerMiddleware.php

```php
public function handle(Request $request, Closure $next)
{
  if (!auth()->check()) {
    return redirect(route('login'));
  }

  if (!auth()->user()->isOwner()) {
    abort(403);
  }
  
  return $next($request);
}
```

### IsUserMiddleware.php

```php
public function handle(Request $request, Closure $next)
{
  if (!auth()->check()) {
    return redirect(route('login'));
  }

  if (!auth()->user()->isUser()) {
    abort(403);
  }
  
  return $next($request);
}
```

Great now let’s run our test again.

```Bash
$ sail artisan test --filter=GuestTest
```

![Screen Shot 2022-01-11 at 3.13.28 PM.png](S7-08:%20ToDos.assets/Screen%20Shot%202022-01-11%20at%203.13.28%20PM.png)

Now we see two test failures and not just one. But this is okay, because we just need to update our test to use the proper http response code (302).

Open tests/Feature/GuestTest.php

![Screen Shot 2022-01-11 at 3.14.47 PM.png](S7-08:%20ToDos.assets/Screen%20Shot%202022-01-11%20at%203.14.47%20PM.png)

Replace the http code from 403 to 302 in each of the assertStatus() methods.

```php
public function test_guest_can_not_access_dashboard()
{
  $response = $this->get('/dashboard');
  $response->assertStatus(302);
}
```

```php
public function test_guest_can_not_access_clients_page()
{
  $response = $this->get('/clients');
  $response->assertStatus(302);
}
```

Run the test again.

```Bash
$ sail artisan test --filter=GuestTest
```

![Screen Shot 2022-01-11 at 3.17.04 PM.png](S7-08:%20ToDos.assets/Screen%20Shot%202022-01-11%20at%203.17.04%20PM.png)

Back to green. This is a better flow, so well done!

Now remember, we did change some of the other middleware, so just to be safe, let’s run the full set of test.

```Bash
$ sail artisan test
```

![Screen Shot 2022-01-11 at 3.18.25 PM.png](S7-08:%20ToDos.assets/Screen%20Shot%202022-01-11%20at%203.18.25%20PM.png)

Okay great!

Be sure to delete that TODO too.

```php
// DELETE THIS TODO
// TODO: Add a private page access denied test for guests
```

And here’s the whole file for your reference.

```php
<?php

namespace Tests\Feature;

use Illuminate\Foundation\Testing\RefreshDatabase;
use Illuminate\Foundation\Testing\WithFaker;
use Tests\TestCase;
use App\Models\User;

class GuestTest extends TestCase
{
  use RefreshDatabase;

  public function test_guest_can_access_home_page()
  {
      $response = $this->get('/');
      $response->assertStatus(200);
  }

  public function test_guest_can_not_access_dashboard()
  {
    $response = $this->get('/dashboard');
    $response->assertStatus(302);
  }

  public function test_guest_can_not_access_clients_page()
  {
    $response = $this->get('/clients');
    $response->assertStatus(302);
  }
}
```

Next up, let’s squash the next TODO

![Screen Shot 2022-01-11 at 3.22.06 PM.png](S7-08:%20ToDos.assets/Screen%20Shot%202022-01-11%20at%203.22.06%20PM.png)

Open tests/Feature/OwnerTest.php

![Screen Shot 2022-01-11 at 3.22.57 PM.png](S7-08:%20ToDos.assets/Screen%20Shot%202022-01-11%20at%203.22.57%20PM.png)

Two TODOs to deal with and we can do some improvements while where here.

Let’s add an owner to the setup() method so we can use it in our tests.

```php
public function setUp(): void
{
  parent::setUp();
  $this->artisan('db:seed --class=RoleSeeder');
  $this->owner = User::factory()->create();
  $this->owner->roles()->attach(Role::IS_OWNER);
}
```

And in our first test, let’s put this owner to good use.

```php
public function test_owner_can_access_home_page()
{    
  $response = $this->actingAs($this->owner)->get('/');
  $response->assertStatus(200);
}
```

Much better.

And before we hit those TODOs, let’s do the same for the other test method.

```php
public function test_owner_can_not_access_dashboard()
{
  $response = $this->actingAs($this->owner)->get('/dashboard');
  $response->assertStatus(403);
}
```

No change on the http status code on these ones, because we decided that the owner cannot access the dashboard and so they should get a 403.

Now, on to the TODOs.

```php
// TODO: Add a private page access granted test for owners
// TODO: Add a owner-specific page access granted test for owners
```

We can use the clients or projects page for the first TODO, and we do not need and that will qualify as an owner-specific page too, so we can just delete that second TODO when we’re done.

```php
public function test_owner_can_access_clients_page()
{
  $response = $this->actingAs($this->owner)->get('/clients');
  $response->assertStatus(200);
}
```

And let’s run our new test.

```Bash
$ sail artisan test --filter=OwnerTest
```

![Screen Shot 2022-01-11 at 3.30.46 PM.png](S7-08:%20ToDos.assets/Screen%20Shot%202022-01-11%20at%203.30.46%20PM.png)

Looks pretty good!

Now delete those TODOs

```php
// DELETE TODOs
// TODO: Add a private page access granted test for owners
// TODO: Add a owner-specific page access granted test for owners
```

And here’s the full code so you can stay in sync with me.

```php
<?php

namespace Tests\Feature;

use Illuminate\Foundation\Testing\RefreshDatabase;
use Illuminate\Foundation\Testing\WithFaker;
use Tests\TestCase;
use App\Models\User;
use App\Models\Role;

class OwnerTest extends TestCase
{
  use RefreshDatabase;
  
  public function setUp(): void
  {
    parent::setUp();
    $this->artisan('db:seed --class=RoleSeeder');
    $this->owner = User::factory()->create();
    $this->owner->roles()->attach(Role::IS_OWNER);
  }

  public function test_owner_can_access_home_page()
  {    
    $response = $this->actingAs($this->owner)->get('/');
    $response->assertStatus(200);
  }

  public function test_owner_can_access_clients_page()
  {
    $response = $this->actingAs($this->owner)->get('/clients');
    $response->assertStatus(200);
  }

  public function test_owner_can_not_access_dashboard()
  {
    $response = $this->actingAs($this->owner)->get('/dashboard');
    $response->assertStatus(403);
  }
}
```

Just one more TODO left, we’re on the home stretch!

![Screen Shot 2022-01-11 at 3.32.42 PM.png](S7-08:%20ToDos.assets/Screen%20Shot%202022-01-11%20at%203.32.42%20PM.png)

Open the tests/Feature/UserTest.php

Same treatment on this one, let’s add a user to the setup() method to use in our tests.

```php
public function setUp(): void
{
  parent::setUp();
  $this->artisan('db:seed --class=RoleSeeder');
  $this->user = User::factory()->create();
  $this->user->roles()->attach(Role::IS_USER);
}
```

And put this little guy to use in our tests.

```php
public function test_user_can_access_home_page()
{
  $response = $this->actingAs($this->user)->get('/');
  $response->assertStatus(200);
}
```

```php
public function test_user_can_not_access_dashboard()
{  
  $response = $this->actingAs($this->user)->get('/dashboard');
  $response->assertStatus(403);
}
```

And for the TODO, how about we point them to the

```php
public function test_user_can_access_time_sheet_page()
{
  $response = $this->actingAs($this->user)->get('/time');
  $response->assertStatus(200);
}
```

That’s a safe test, they should always be able to access that page.

Now run our UserTest

```Bash
$ sail artisan test --filter=UserTest
```

![Screen Shot 2022-01-11 at 3.37.10 PM.png](S7-08:%20ToDos.assets/Screen%20Shot%202022-01-11%20at%203.37.10%20PM.png)

And green is good, delete that TODO please.

```php
// DELETE TODO
// TODO: Add a private page access granted test for users
```

And here’s the full code for the UserTest

```php
<?php

namespace Tests\Feature;

use Illuminate\Foundation\Testing\RefreshDatabase;
use Illuminate\Foundation\Testing\WithFaker;
use Tests\TestCase;
use App\Models\User;
use App\Models\Role;

class UserTest extends TestCase
{
  use RefreshDatabase;
  
  public function setUp(): void
  {
    parent::setUp();
    $this->artisan('db:seed --class=RoleSeeder');
    $this->user = User::factory()->create();
    $this->user->roles()->attach(Role::IS_USER);
  }

  public function test_user_can_access_home_page()
  {
    $response = $this->actingAs($this->user)->get('/');
    $response->assertStatus(200);
  }
  
  public function test_user_can_access_time_sheet_page()
  {
    $response = $this->actingAs($this->user)->get('/time');
    $response->assertStatus(200);
  }

  public function test_user_can_not_access_dashboard()
  {  
    $response = $this->actingAs($this->user)->get('/dashboard');
    $response->assertStatus(403);
  }
}
```

No more TODOs!

Let’s run one more full test to make sure we didn’t accidentally break anything before we merge this into our main branch.

```Bash
$ sail artisan test
```

![Screen Shot 2022-01-11 at 3.39.35 PM.png](S7-08:%20ToDos.assets/Screen%20Shot%202022-01-11%20at%203.39.35%20PM.png)

We’re up to 48 tests and they all pass! Doesn’t that make you feel good?

Now commit and merge please.

```Bash
$ git status
$ git add .
$ git commit -m "refactors admin, guests, owner, and user tests and adds redirect for guests"
$ git status
$ git checkout main
$ git pull
$ git merge dan/todos
```

Run our full tests before we push.

```Bash
$ sail artisan test
```

![Screen Shot 2022-01-11 at 3.43.16 PM.png](S7-08:%20ToDos.assets/Screen%20Shot%202022-01-11%20at%203.43.16%20PM.png)

Whoot! Now it’s okay to push up our changes to GitHub.

```Bash
$ git status
$ gits push
```

![Screen Shot 2022-01-11 at 3.44.06 PM.png](S7-08:%20ToDos.assets/Screen%20Shot%202022-01-11%20at%203.44.06%20PM.png)

Here’s a summary of the work we completed in this series.

1. > ~~Refactor TimeEntryController~~
2. > ~~Complete TimeEntryController CRUD methods~~
3. > ~~Update and Complete CRUD methods in ProjectController and ClientController~~
4. > ~~Complete and Cross off all project TODOs~~
5. > ~~Remove any unwanted or unused code~~

Again, excellent work, we’ll see you in the next one.

