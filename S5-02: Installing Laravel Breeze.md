# S5-02: Installing Laravel Breeze

Laravel Breeze is a starter kit provided by the makers of Laravel to quickly build up all of the authentication scaffolding we’ll need to handle the registration and authentication of our users.

You can learn more about it [on the official Laravel documentation page](https://laravel.com/docs/8.x/starter-kits).

Of course we could build all of this from scratch, but using this starter kit will make life a lot easier for us and absolutely everything is customizable so there really are no downsides for getting a little help from the package.

---

### Start up your work session

Go ahead and take the steps to start up your works session found here: [[Workflow: Starting and Ending Your Work Session]]

---

We’re going to be installing something into our project, this means there’s the potential that things could go wrong. To play it safe, we'll do all our work for this series in a feature branch, like we did before.

1. Setup a feature branch (use your name in place of dan)

```Bash
$ git pull
$ git status
$ git checkout -b dan/breeze
```

2. Run the composer command to install Laravel Breeze

Remember, we’re using the sail command to perform all of our commands on our Docker container, so the beginning of our commands will be slightly different than what the official documentation shows.

```Bash
$ sail composer require laravel/breeze --dev
```

This command may take a while to complete, so just sit tight until it finishes and you get your terminal prompt back.

![Screen Shot 2021-12-20 at 3.02.19 PM.png](S5-02:%20Installing%20Laravel%20Breeze.assets/Screen%20Shot%202021-12-20%20at%203.02.19%20PM.png)

You may notice that the composer.json and composer.lock files turned yellow, indicating that they were modified. This is because the composer command we ran added Laravel Breeze to our package dependencies. At this point the package isn’t actually installed. To do that we need to issue another command.

3. Install Laravel Breeze using the artisan command

```Bash
$ sail artisan breeze:install
```

![Screen Shot 2021-12-20 at 3.05.56 PM.png](S5-02:%20Installing%20Laravel%20Breeze.assets/Screen%20Shot%202021-12-20%20at%203.05.56%20PM.png)

The message recommends we run two `npm` commands (they’re separated by the `&&`), we just need to do the sail equivalent commands.

4. Run the npm commands using the sail equivalents

```Bash
$ sail npm install && sail npm run dev
```

Again, this may take some time, so sit tight until both commands have run and you get your terminal prompt back.

Also, don’t worry if you see some warnings in the output as the installations occur. We only have to be concerned if we see red.

![Screen Shot 2021-12-20 at 3.09.27 PM.png](S5-02:%20Installing%20Laravel%20Breeze.assets/Screen%20Shot%202021-12-20%20at%203.09.27%20PM.png)

5. Finally, the documentation wants us to run a database migration.

```Bash
$ sail artisan migrate
```

We already ran our migrations earlier and it looks like Laravel Breeze didn't have any migrations that it needed to run, so you should see `“Nothing to migrate.”`

![Screen Shot 2021-12-20 at 3.13.58 PM.png](S5-02:%20Installing%20Laravel%20Breeze.assets/Screen%20Shot%202021-12-20%20at%203.13.58%20PM.png)

### Okay, now what?

Laravel Breeze is installed and working at this point. We can see the work that was done and put it to the test by registering an account on our site.

6. Visit localhost in a browser to see the Laravel Welcome Screen

![Screen Shot 2021-12-20 at 3.17.14 PM.png](S5-02:%20Installing%20Laravel%20Breeze.assets/Screen%20Shot%202021-12-20%20at%203.17.14%20PM.png)

You’ll see two new links in the upper-right part of your screen Log in and Register. These were not here before, but now that we have turned on these features the template is showing the proper links for both.

7. Click on Register to register a new account

![Screen Shot 2021-12-20 at 3.18.45 PM.png](S5-02:%20Installing%20Laravel%20Breeze.assets/Screen%20Shot%202021-12-20%20at%203.18.45%20PM.png)

You can see that we have a nice view, and a working registration form. Pretty slick.

8. Set yourself up an account by filling out the form and clicking the `Register` button.

![Screen Shot 2021-12-20 at 3.20.39 PM.png](S5-02:%20Installing%20Laravel%20Breeze.assets/Screen%20Shot%202021-12-20%20at%203.20.39%20PM.png)

You should be directed to the dashboard page, this view will come in quite handy for our admins.

The forms that are built for us are fulling functioning, including error handling and everything. Let’s logout and check some of those features out too.

9. Logout by clicking on your name in the upper right of your screen and selecting Log Out from the dropdown menu that shows up.

You should be directed back to the welcome screen.

10. Click on the `Log in` link in the upper right part of the window
11. Try to enter any combination of an incorrect email address or incorrect password and you’ll see the validation in affect.
12. Click on the Forgot your password? link and you’ll see the view for that as well

Right now password reset will not send an email, because we have not setup our project to do so, however the functionality is working.

13. Fill out the forgot password form by providing the email address you used when you registered your account.

Let’s checkout the data in the database… we should have a user and a record in our password_resets table too.

14. Open TablePlus, connect to the database server and open up the tempo database
15. Click on the users table

![Screen Shot 2021-12-20 at 3.31.03 PM.png](S5-02:%20Installing%20Laravel%20Breeze.assets/Screen%20Shot%202021-12-20%20at%203.31.03%20PM.png)

You should see a record in there with your user information.

16. Click on the password_resets table

![Screen Shot 2021-12-20 at 3.31.50 PM.png](S5-02:%20Installing%20Laravel%20Breeze.assets/Screen%20Shot%202021-12-20%20at%203.31.50%20PM.png)

This table keeps track of all the password reset requests. It builds up a special token that must be used by the user in order to reset do the actual reset.

```Bash
$ git add .
$ git commit -m "Installs Laravel Breeze into project"
```

Also, let’s push this up our feature branch to the repository. We didn’t do that with the projects feature branch but we will do this moving forward.

Try to do a git push

```Bash
$ git push
```

![Screen Shot 2021-12-20 at 4.01.21 PM.png](S5-02:%20Installing%20Laravel%20Breeze.assets/Screen%20Shot%202021-12-20%20at%204.01.21%20PM.png)

You’ll see something about setting up an upstream.. what’s that all about?

Right now your feature branch is local. That means it’s not on the GitHub repository. Local branches are often used for quick fixes, and or works you want to do on your project to test out locally. Upstream branches are available on GitHub and anyone who has access to the repository can pull them down to work on them as well.

To push this branch and set the remote as upstream we use the command that is suggested. Yours may be different than mine if your name is not Dan. This is a one-time operation on new branches so we won’t have to do this in future pushes on this branch.

```Bash
$ git push --set-upstream origin dan/breeze
```

![Screen Shot 2021-12-20 at 4.06.58 PM.png](S5-02:%20Installing%20Laravel%20Breeze.assets/Screen%20Shot%202021-12-20%20at%204.06.58%20PM.png)

Laravel Breeze does quite a bit for us, it installs some views, routes, and even tests for us. Let’s run those tests now and we’ll have some good news and bad news to discuss.

```Bash
$ sail artisan test
```

![Screen Shot 2021-12-20 at 4.56.44 PM.png](S5-02:%20Installing%20Laravel%20Breeze.assets/Screen%20Shot%202021-12-20%20at%204.56.44%20PM.png)

Oh cool, look at those new tests, and they all pass that’s great!

But look at our tests, literally all of them failed. What’s up with that?!?

Before you panic and toss all your TDD good thoughts in the trash can, let’s take a step back and think about what we just did. At the end of this you’ll see exactly why TDD just saved our bacon.

We installed Breeze, and that’s pretty much all we did, so obviously something in the install broke some of our code, our tests should be fine, we can see them failing as they are supposed to when something in our code gets changed. In this case, the culprit was Laravel Breeze and not another developer.

Let’s run through our entire flow, focus on one failing test. Let’s start with `test_user_can_create_a_client(`).

What’s that test doing:

```php
public function test_user_can_create_a_client()
{
  $response = $this->post('/clients', ['name' => 'ABC Company', 'code' => 'ABCCO']);
  $this->assertTrue(Client::all()->count() == 1);
}
```

And the fail message for that test looks like this:

![Screen Shot 2021-12-20 at 5.05.28 PM.png](S5-02:%20Installing%20Laravel%20Breeze.assets/Screen%20Shot%202021-12-20%20at%205.05.28%20PM.png)

Okay, so it’s failing to assert that there’s one record in the database. Maybe the database connection is broken? It’s probably not that because the tests above passed and they’re using the database to register a user.

Maybe it’s failing because there was an error in the post call?

Let’s improve our test a bit to help us out. Let’s see what the HTTP response code is being returned on line 16.

1. Add an assertion to check the status code of the response after line 16.

The whole method will look like this when you’re done.

```php
public function test_user_can_create_a_client()
{
  $response = $this->post('/clients', ['name' => 'ABC Company', 'code' => 'ABCCO']);
  $response->assertStatus(200);
  $this->assertTrue(Client::all()->count() == 1);
}
```

2. Now run just the `test_user_can_create_a_client()` test method.

```php
$ sail artisan test --filter=test_user_can_create_a_client
```

We still get a fail, but this time the message reads: `Failed asserting that 200 is identical to 404`. That sounds like the request was sent, but the Laravel router didn't recognize that as a route. Let's head over to our web routes file to make sure everything is okay there.

3. Open routes/web.php

![Screen Shot 2021-12-20 at 5.18.08 PM.png](S5-02:%20Installing%20Laravel%20Breeze.assets/Screen%20Shot%202021-12-20%20at%205.18.08%20PM.png)

Woah, that file doesn’t look like where we left it. Where did all of our client and project routes go?

Well it turns out the Laravel Breeze assumed (incorrectly in this case) that we would be installing it on a fresh install of a Laravel project, not an existing one. So it just overwrote our route file with its own version that included all its routes. That’s why their tests passed and ours failed.

So should we just retype our routes? What were they anyway, I don’t remember.

We’re not going to be rewriting anything. Why do that when we can use version control to make it a simple copy and paste job.

Because we have the foresight to create a feature branch, all we have to do is switch back to the main branch and the version of our routes file will be still be intact. You can do this switch with the routes file open if you’d like and the command in the terminal looks like this:

```Bash
$ git checkout main
```

![Screen Shot 2021-12-20 at 5.26.35 PM.png](S5-02:%20Installing%20Laravel%20Breeze.assets/Screen%20Shot%202021-12-20%20at%205.26.35%20PM.png)

Look at that, those are our route rules. So let’s just copy all of our routes and head back to our feature branch to put them back where they belong. Also grab that TODO comment, while were at it we might as well refactor that puppy too.

4. Copy the comment and routes for clients and projects
5. Switch back to your feature branch

```Bash
$ git checkout dan/breeze
```

6. Now let’s paste the routes at the bottom of the file after the `require DIR.’/auth.php’;` **line.**

![Screen Shot 2021-12-20 at 5.30.32 PM.png](S5-02:%20Installing%20Laravel%20Breeze.assets/Screen%20Shot%202021-12-20%20at%205.30.32%20PM.png)

8. Save your changes and rerun your tests, we don't want to refactor the routes until we're sure it's our fix and that there's nothing else we have to tend to.

```Bash
$ sail artisan test
```

![Screen Shot 2021-12-20 at 5.31.52 PM.png](S5-02:%20Installing%20Laravel%20Breeze.assets/Screen%20Shot%202021-12-20%20at%205.31.52%20PM.png)

That’s great, green all around.

So we see that replacing our routes was the fix, and now our tests are all passing. That means we don’t need to bother checking the other parts of our code, our tests worked as they should.

Now let’s go back and refactor the clients routes.

9. Replace all the client routes with this line:

```php
Route::resource('/clients','App\Http\Controllers\ClientController');
```

![Screen Shot 2021-12-20 at 5.34.34 PM.png](S5-02:%20Installing%20Laravel%20Breeze.assets/Screen%20Shot%202021-12-20%20at%205.34.34%20PM.png)

And, of course, let’s run our tests to see that they still work.

```Bash
$ sail artisan test
```

![Screen Shot 2021-12-20 at 5.35.31 PM.png](S5-02:%20Installing%20Laravel%20Breeze.assets/Screen%20Shot%202021-12-20%20at%205.35.31%20PM.png)

Looks good to me.

### Good Things Just Happened

It may seem frustrating when you first see a bunch of tests fail after doing some work like we did. Panic may set in as you race to think of how quickly you can undo the work you did so you can get back to passing tests. Avoid this impulse, remain calm and work the problem. Let the tests tell you what to do. And, as in this case, even improve the tests to make them more useful along the way.

The whole reason for writing tests are for moments like this one.

Imagine how this experience may have gone if you didn’t have tests in place. You would have unknowingly checked in your code merged your changes (and the new bug) which would have broken the site for everyone.

Days may have gone by before someone decided they wanted to create a client and discovered that there was even a problem. Without our views built, it may not have been discovered until the front-end developer tested a form they just built. Who knows, the bug could have even been pushed to production and affected a real user.

Because of automated testing, we were able to catch the problem and the time it took to fix was minimal compared to what it would have taken after we had moved on to other work on the project.

We’re in a good spot, thanks to our automated tests, to commit and push up our code now.

```other
$ git status
$ git pull
$ git add .
$ git commit -m "Fix: restores routes overwritten by Laravel Breeze installer, refactors client routes"
$ git push
```

> Notice, I did a git pull because our branch is on GitHub, and there’s a chance that someone pushed up a change to our branch. I know it’s a nearly 0% chance in this case, but it’s a good practice.

