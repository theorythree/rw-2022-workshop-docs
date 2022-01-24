# S5-04: Testing Routes

As previously mentioned, we have some routing work to do, but before you open up the web.php route file, we’re going to write some test.

---

### Start up your work session

Go ahead and take the steps to start up your works session found here: [[Workflow: Starting and Ending Your Work Session]]

---

Let’s create a test for each role that we believe will be in our application (guest, user, owner, admin).

Even though these tests relate to authentication I do not want to put our tests in the Auth folder that Laravel Breeze created. The reason for this is that I see that as a folder belonging to Laravel Breeze, and if we add tests in there we might get confused later on as to which ones are ours and which ones are theirs.

But before we do that, let’s remember the lesson from the previous series, and create a feature branch. I’m going to call mine dan/auth because the work we’re doing all relates to authentication.

1. Create an auth feature branch

```Bash
$ git checkout main
$ git pull
$ git checkout -b dan/auth
```

2. Create tests for each of our planned user roles

```Bash
$ sail artisan make:test GuestTest
$ sail artisan make:test UserTest
$ sail artisan make:test OwnerTest
$ sail artisan make:test AdminTest
```

We want these tests to be about access, so let’s get busy shall we?

---

### GuestTest Updates

3. Open up `tests/feature/GuestTest.php`

![Screen Shot 2021-12-22 at 10.29.21 AM.png](S5-04:%20Testing%20Routes.assets/Screen%20Shot%202021-12-22%20at%2010.29.21%20AM.png)

The first thing we need to do when we setup our test, is make sure our tests are including our User model, because we’re going to be creating one.

4. Include the user model at the bottom of the other use statements at the top of the file.

```php
use App\Models\User;
```

And we're going to need our database for this one, so let's not forget to add RefreshDatabase which builds up and tears down our test database for each test.

5. Include RefreshDatabase in our method

```php
class GuestTest extends TestCase
{
  use RefreshDatabase;
  ...
}
```

Let’s just rename that test example, it’s pretty close to what we need. You can remove the comment above it, since it will no longer apply.

6. Rename `test_example()` to `test_guest_can_access_home_page()`

```php
public function test_guest_can_access_home_page()
{
  $response = $this->get('/');
  $response->assertStatus(200);
}
```

> I feel pretty good leaving that one as is for now. Guests should absolutely be able to access our homepage.

Next, we want to test the things a guest `cannot` access. They should never be able to access the dashboard, and they shouldn’t be able to access a private page. Right now, we don’t have a private page, so I’m going to skip that particular test until we’re do so. But I will add a TODO to remind us that we still need to add that test later.

7. Add the following test method to GuestTest with the TODO reminder comment above it.

```php
// TODO: Add a private page access denied test for guests

public function test_guest_can_not_access_dashboard()
{
  $response = $this->get('/dashboard');
  $response->assertStatus(302);
}
```

> You can see I’m just using the route `/dashboard` and then checking for a `302` response - which is the response code that guests should get if they try to access any page they are not authorized to view. 302 is a redirect, and so we're asserting that the redirect happens.

---

### UserTest Updates

Let’s repeat these steps in the `UserTest.php` file.

1. Open `tests/feature/UserTest.php`
2. Include the user model at the bottom of the other use statements at the top of the file.

```php
use App\Models\User;
```

3. Include RefreshDatabase in our method

```php
class UserTest extends TestCase
{
  use RefreshDatabase;
  ...
}
```

4. Rename `test_example()` to `test_user_can_access_home_page()` and add a TODO to remind us to create a private page test.

```php
public function test_user_can_access_home_page()
{
  $response = $this->get('/');
  $response->assertStatus(200);
}
```

> This first test should be run as a logged in user, so how exactly do we login as a user in our tests? Well, the answer is pretty simple. We first have to create a user (using the user Factory), then we perform any action they do with the `actingAs()` method, and we pass in our user into that method and that particular action will be performed by that logged in user.

Now let’s create a user and test update the rest of the testing method to perform that get request as a logged in user.

5. Add the user factory line and update the git request to match the following code.

```php
public function test_user_can_access_home_page()
{
  $user = User::factory()->create();

  $response = $this->actingAs($user)->get('/');
  $response->assertStatus(200);
}
```

> With these updates, the user making the request will be a logged in user. Pretty simple and sweet!

The next text covers the restriction of the user not being able to access the dashboard.  With the aforementioned TODO

5. Add the `test_user_can_not_access_dashboard()` method and add a TODO reminder for us to write a passing test that checks to see that a user can access a private page.

```php
// TODO: Add a private page access granted test for users

public function test_user_can_not_access_dashboard()
{
  $user = User::factory()->create();

  $response = $this->actingAs($user)->get('/dashboard');
  $response->assertStatus(302);
}
```

This is nearly identical to the test for guests. We just updated the method name.

Here’s a screenshot so you can make sure you have correctly made all of the updates.

![Screen Shot 2021-12-22 at 1.52.50 PM.png](S5-04:%20Testing%20Routes.assets/Screen%20Shot%202021-12-22%20at%201.52.50%20PM.png)

---

### OwnerTest Updates

Since we’re going to do nearly the exactly same two tests for both OwnerTest and AdminTest, with different assertions, I’ll provide the finished code and screenshots for you, with some commentary.

1. Open `tests/Feature/OwnerTest.php` and add make the required changes as we have done in the other tests.

```php
<?php

namespace Tests\Feature;

use Illuminate\Foundation\Testing\RefreshDatabase;
use Illuminate\Foundation\Testing\WithFaker;
use Tests\TestCase;
use App\Models\User;

class OwnerTest extends TestCase
{
  use RefreshDatabase;
  
  public function test_owner_can_access_home_page()
  {
    $this->withoutExceptionHandling();

    // TODO: Assign Owner role to user
    $user = User::factory()->create();

    $response = $this->actingAs($user)->get('/');
    $response->assertStatus(200);
  }

  // TODO: Add a private page access granted test for owners
  // TODO: Add a owner-specific page access granted test for owners

  public function test_owner_can_not_access_dashboard()
  {
    $user = User::factory()->create();

    $response = $this->actingAs($user)->get('/dashboard');
    $response->assertStatus(302);
  }
}
```

You can see I added a couple more TODOs in this one because we will have to learn how to assign a user a role of owner, and owners will have some owner-only functionality which we will have to test too.

2. Open `tests/Feature/AdminTest.php` and add make the required changes as we have done in the other tests.

```php
<?php

namespace Tests\Feature;

use Illuminate\Foundation\Testing\RefreshDatabase;
use Illuminate\Foundation\Testing\WithFaker;
use Tests\TestCase;
use App\Models\User;

class AdminTest extends TestCase
{
  use RefreshDatabase;

  public function test_admin_can_access_home_page()
  {
    $this->withoutExceptionHandling();

    // TODO: Assign Admin role to user
    $user = User::factory()->create();

    $response = $this->actingAs($user)->get('/');
    $response->assertStatus(200);
  }

  // TODO: Add a private page access granted test for admins
  // TODO: Add a admin-specific page access granted test for admins

  public function test_admin_can_access_dashboard()
  {
    $this->withoutExceptionHandling();

    $user = User::factory()->create();

    $response = $this->actingAs($user)->get('/dashboard');
    $response->assertStatus(200);
  }
}
```

---

### Run our tests and finish up

With our first draft of tests done, let’s run all our tests and see what we get.

```Bash
$ sail artisan test
```

![Screen Shot 2021-12-22 at 2.07.15 PM.png](S5-04:%20Testing%20Routes.assets/Screen%20Shot%202021-12-22%20at%202.07.15%20PM.png)

Okay, looks like we have 2 failed tests; one on the `OwnerTest` and one on the `UserTest`. Both regarding the access of our dashboard.

This makes sense when you think of it, because when we installed Laravel Breeze, it restricted access to the dashboard for guests, but then it lets everyone with an account in. We want to change that behavior. And we’re going to do that by doing two things, setting up some roles and assigning them to the users we’re using in the tests and protecting our routes with middleware.

