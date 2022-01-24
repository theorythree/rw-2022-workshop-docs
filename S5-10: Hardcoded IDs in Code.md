# S5-10: Hardcoded IDs in Code

As you progress in your development skills, certain things will start to bother you. The little hairs on the back of your neck will stand up alerting you to the fact that you’re doing something that you know you shouldn’t be doing. Pay attention to those instincts.

For me, I’ve been bitting my tongue for several episodes, because I knew we had to address one very important issue. It has to do with the hard-coded ids in our code.

Let me refresh your memory if you don’t know what I’m talking about. In the last few episodes we created roles, three to be exact, and for each of those roles we hardcoded the Ids in some pretty critical points in our code.

Here’s an example of one culprit in the `App/Http/Middleware/IsAdminMiddleware.php` file

```php
if (!auth()->check() || !in_array(3, auth()->user()->roles()->pluck('id')->toArray())) {
  abort(403);
}
```

Do you see that hardcoded 3? Oh boy does this trigger me. That ID is meant to refer to the ID of the admin role in our database, which we know has an id of 3 because we created a database seeder and explicitly defined it.

There’s a lot wrong with this approach, so patiently waited until we got to a place in the progress of our project so I could address it directly.

First and foremost, it’s not exactly clear what 3 even is in our if statement. It could be the id of the user’s favorite color in the database. What is 3?

Second, it would be very easy to mistakenly edit the ID value and not catch it. If, for example, you were to copy this if statement to use in different middleware, you might forget to change the ID value and unknowingly create middleware that doesn't do what you expect. Hopefully by now you'd catch it by writing a test for such a condition, but nevertheless it's a pretty big test we're doing in this if statement and I'd like it to be more clear to future developers exactly what we're testing on.

To do that we’re going to use a constant variable that will contain the ID values, and use those constants in our code.

Let’s clean up these IDs in our code now.

The first step is to define our constants. We can do that in our Role model file.

Open the App/Models/Role.php file

![Screen Shot 2021-12-27 at 10.38.20 AM.png](S5-10:%20Hardcoded%20IDs%20in%20Code.assets/Screen%20Shot%202021-12-27%20at%2010.38.20%20AM.png)

Add the new constants we will use throughout the rest of our codebase to the top of the Role class body.

```php
class Role extends Model
{
  use HasFactory;

  public const IS_USER = 1;
  public const IS_OWNER = 2;
  public const IS_ADMIN = 3;
  ...
}
```

![Screen Shot 2021-12-27 at 10.44.59 AM.png](S5-10:%20Hardcoded%20IDs%20in%20Code.assets/Screen%20Shot%202021-12-27%20at%2010.44.59%20AM.png)

> You can see we defined three constants using the const keyword; IS_USER, IS_OWNER, IS_ADMIN. We're following the naming convention for constant variables defined in  [[S2-06: Naming Conventions]].

> Constant variables also do not typically contain the $ before them, this is another way to make them stand out in your code.

### Middleware Updates

Let’s start by refactoring our middleware tests to use our new constants that we just created.

### App/Http/Middleware/IsAdminMiddleware.php

1. Include the model class file in our use statement at the top of each file.

```php
use App\Models\Role;
```

2. Replace the hardcoded role ID with the constant `IS_ADMIN` that we defined in the Role model class file.

```php
if (!auth()->check() || !in_array(Role::IS_ADMIN, auth()->user()->roles()->pluck('id')->toArray())) { ... }
```

![Screen Shot 2021-12-27 at 10.59.30 AM.png](S5-10:%20Hardcoded%20IDs%20in%20Code.assets/Screen%20Shot%202021-12-27%20at%2010.59.30%20AM.png)

> You can see accessing the constant is done by using the `Role::` then the name of the constant variable name (`IS_ADMIN`).

3. Repeat these steps for the other middleware files.

### App/Http/Middleware/IsOwnerMiddleware.php

```php
use App\Models\Role;
```

```php
if (!auth()->check() || !in_array(Role::IS_OWNER, auth()->user()->roles()->pluck('id')->toArray())) { ... }
```

![Screen Shot 2021-12-27 at 10.59.46 AM.png](S5-10:%20Hardcoded%20IDs%20in%20Code.assets/Screen%20Shot%202021-12-27%20at%2010.59.46%20AM.png)

### App/Http/Middleware/IsUserMiddleware.php

```php
use App\Models\Role;
```

```php
if (!auth()->check() || !in_array(Role::IS_USER, auth()->user()->roles()->pluck('id')->toArray())) { ... }
```

![Screen Shot 2021-12-27 at 10.59.53 AM.png](S5-10:%20Hardcoded%20IDs%20in%20Code.assets/Screen%20Shot%202021-12-27%20at%2010.59.53%20AM.png)

This is a good time to run our tests to make sure they’re still all passing.

```Bash
$ sail artisan test
```

![Screen Shot 2021-12-27 at 11.19.12 AM.png](S5-10:%20Hardcoded%20IDs%20in%20Code.assets/Screen%20Shot%202021-12-27%20at%2011.19.12%20AM.png)

Excellent. Let’s continue with our refactoring.

Next I want to refactor the places in our tests that have hardcoded values.

### Test/Feature/AdminTest.php

1. Change the two locations in this file that had a hardcoded role id

```php
public function test_admin_can_access_home_page()
{
  ...
  $this->assertTrue($adminRole->id == Role::IS_ADMIN);
```

```php
public function test_admin_can_access_dashboard()
{
  ...
  $this->assertTrue($adminRole->id == Role::IS_ADMIN);
```

![Screen Shot 2021-12-27 at 11.24.06 AM.png](S5-10:%20Hardcoded%20IDs%20in%20Code.assets/Screen%20Shot%202021-12-27%20at%2011.24.06%20AM.png)

### Test/Feature/OwnerTest.php

2. Change the two locations in this file that had a hardcoded role id

```php
public function test_owner_can_access_home_page()
{
  ...
  $this->assertTrue($ownerRole->id == Role::IS_OWNER);
```

```php
public function test_owner_can_not_access_dashboard()
{
  ...
  $this->assertTrue($ownerRole->id == Role::IS_OWNER);
```

![Screen Shot 2021-12-27 at 11.27.05 AM.png](S5-10:%20Hardcoded%20IDs%20in%20Code.assets/Screen%20Shot%202021-12-27%20at%2011.27.05%20AM.png)

### Test/Feature/UserTest.php

3. Change the two locations in this file that had a hardcoded role id

```php
public function test_user_can_access_home_page()
{
  ...
  $this->assertTrue($userRole->id == Role::IS_USER);
```

```php
public function test_user_can_not_access_dashboard()
{
  ...
  $this->assertTrue($userRole->id == Role::IS_USER);
```

![Screen Shot 2021-12-27 at 11.29.42 AM.png](S5-10:%20Hardcoded%20IDs%20in%20Code.assets/Screen%20Shot%202021-12-27%20at%2011.29.42%20AM.png)

4. Run the tests again to make sure we didn’t break anything.

```Bash
$ sail artisan test
```

![Screen Shot 2021-12-27 at 11.31.25 AM.png](S5-10:%20Hardcoded%20IDs%20in%20Code.assets/Screen%20Shot%202021-12-27%20at%2011.31.25%20AM.png)

Excellent work on this refactor. We made our code more readable and safe, both worth pursuits.

Let’s wrap this one up, by adding our changes, committing them, and pushing them up to GitHub. It’s also a good time to merge this back into the main branch.

```Bash
$ git status
$ git pull
$ git add .
$ git commit -m "refactor: using new role constants defined in Role model and used  in our tests and middleware"
$ git push
```

And for that merge I mentioned, I’ll break that up into two, with a test in between to make sure our test pass before we push up the merge to main:

```Bash
$ git status
$ git checkout main
$ gir pull
$ git merge dan/auth
$ sail artisan test
```

![Screen Shot 2021-12-27 at 11.38.24 AM.png](S5-10:%20Hardcoded%20IDs%20in%20Code.assets/Screen%20Shot%202021-12-27%20at%2011.38.24%20AM.png)

```Bash
$ git push
```

![Screen Shot 2021-12-27 at 11.37.20 AM.png](S5-10:%20Hardcoded%20IDs%20in%20Code.assets/Screen%20Shot%202021-12-27%20at%2011.37.20%20AM.png)

Good work, we’re done here. Let’s move on to the next episode.

