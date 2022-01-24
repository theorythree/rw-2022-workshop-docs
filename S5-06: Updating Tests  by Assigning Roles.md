# S5-06: Updating Tests  by Assigning Roles

It’s time to deal with those nasty failing tests. We actually have to fix three tests, all of them deal with access for our Admins, Owners, and Users access.

If it’s been so long since you checked the tests and you’ve forgotten how they failed, you can run them now to refresh your memory.

```Bash
$ sail artisan test
```

![Screen Shot 2021-12-26 at 4.53.06 PM.png](S5-06:%20Updating%20Tests%20%20by%20Assigning%20Roles.assets/Screen%20Shot%202021-12-26%20at%204.53.06%20PM.png)

1. Open the tests/Feature/OwnerTest.php file

The goal is to assign a role to the user we build up on `line 19.`

But you might remember our test database will be empty when this method is run, so how do we create the roles? Do we make a RoleFactory and build them up? Well, we could do that, but a better way for this particular case would be to use the seeder we created in the previous lesson.

To do that, we just have to add a `setup()` method which will run our seeder before our tests run.

2. Add the `setup()` method as the first method of the `OwnerTest` class;

```php
public function setUp(): void
{
  parent::setUp();
  $this->artisan('db:seed --class=RoleSeeder');
}
```

> This method will run every time the `OwnerTest` method gets setup, so it's. a perfect way for use to setup things we'll need in every test.

> As you can see we're running our artisan command `'db:seed —class=RoleSeeder’` which will seed our database with the RoleSeeder we made. Before that, we're required to call the `parent::setup()`.

3. Add the `use AppModelsRole` line at the bottom of the other use include lines at the top of the file.

```php
use App\Models\Role;
```

Now in our  `test_owner_can_access_home_page()` method, we need to just fetch the owner role and assert we found it. There’s a couple of ways to do this, but I’m just going to do a query and look for it by name then assert we get the expected id.

4. Add the following two lines of code to the `test_owner_can_access_home_page()` method.

```php
$ownerRole = Role::where('name','owner')->first();
$this->assertTrue($ownerRole->id == 2);
```

To assign the role that we just fetched from the test database to our user, we can use the `attach()` method to attach the role to the user (this method works on all many-to-many relationships).

5. Attach the role to the user using this line which should appear right after we create the user.

```php
$user->roles()->attach($ownerRole->id);
```

> You can see we access the relationship by its name as a method, then we can pass the ID of the role we want to assign to that relationship by passing a role id to the `attach()` method.

We’re accessing the role id through the $ownerRole.

The updated `test_owner_can_access_home_page()`  method will look like this:

```php
public function test_owner_can_access_home_page()
{
  $this->withoutExceptionHandling();

  $ownerRole = Role::where('name','owner')->first();
  $this->assertTrue($ownerRole->id == 2);

  $user = User::factory()->create();
  $user->roles()->attach($ownerRole->id);
  
  $response = $this->actingAs($user)->get('/');
  $response->assertStatus(200);
}
```

We’ll use the same strategy to update the `test_owner_can_not_access_dashboard()` method.

6. Update the `test_owner_can_not_access_dashboard()` method to match the following code:

```php
public function test_owner_can_not_access_dashboard()
{
  $ownerRole = Role::where('name','owner')->first();
  $this->assertTrue($ownerRole->id == 2);

  $user = User::factory()->create();
  $user->roles()->attach($ownerRole->id);

  $response = $this->actingAs($user)->get('/dashboard');
  $response->assertStatus(302);
}
```

We’re going to use the exact same strategy for AdminTest and UserTest.

1. Update the AdminTest file to match

```php
<?php

namespace Tests\Feature;

use Illuminate\Foundation\Testing\RefreshDatabase;
use Illuminate\Foundation\Testing\WithFaker;
use Tests\TestCase;
use App\Models\User;
use App\Models\Role;

class AdminTest extends TestCase
{
  use RefreshDatabase;

  public function setUp(): void
  {
    parent::setUp();
    $this->artisan('db:seed --class=RoleSeeder');
  }

  public function test_admin_can_access_home_page()
  {
    $this->withoutExceptionHandling();

    $adminRole = Role::where('name','admin')->first();
    $this->assertTrue($adminRole->id == 3);

    $user = User::factory()->create();
    $user->roles()->attach($adminRole->id);

    $response = $this->actingAs($user)->get('/');
    $response->assertStatus(200);
  }

  // TODO: Add a private page access granted test for admins
  // TODO: Add a admin-specific page access granted test for admins

  public function test_admin_can_access_dashboard()
  {
    $this->withoutExceptionHandling();

    $adminRole = Role::where('name','admin')->first();
    $this->assertTrue($adminRole->id == 3);

    $user = User::factory()->create();
    $user->roles()->attach($adminRole->id);

    $response = $this->actingAs($user)->get('/dashboard');
    $response->assertStatus(200);
  }
}
```

2. Update the UserTest file to match

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
  }

  public function test_user_can_access_home_page()
  {
    $this->withoutExceptionHandling();

    $userRole = Role::where('name','user')->first();
    $this->assertTrue($userRole->id == 1);

    $user = User::factory()->create();
    $user->roles()->attach($userRole->id);

    $response = $this->actingAs($user)->get('/');
    $response->assertStatus(200);
  }
  
  // TODO: Add a private page access granted test for users

  public function test_user_can_not_access_dashboard()
  { 
    $userRole = Role::where('name','user')->first();
    $this->assertTrue($userRole->id == 1);
    
    $user = User::factory()->create();
    $user->roles()->attach($userRole->id);

    $response = $this->actingAs($user)->get('/dashboard');
    $response->assertStatus(302);
  }
}
```

> You may have noticed, I added the `$this->withoutExceptionHandling()` method to each of the tests but NOT to the tests that are testing denied access, because we need error handling to test the error code we get.

Excellent. This is a good time to backup our work by checking in our code and pushing it up to GitHub.

```Bash
$ git status
$ git add .
$ git commit -m "adds tests for guests, users, owners, and admins, adds a role model and seeder"
$ git push --set-upstream origin dan/auth
```

