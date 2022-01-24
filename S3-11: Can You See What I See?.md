# S3-11: Can You See What I See?

When building a project using TDD, we can, at times, loose sight of the end user experience. At this point in the project, we have our very first view for the client, but we haven’t looked at it from the user’s perspective quite yet.

Let’s open our browser and pretend we’re a user who wants to look at that client page we just created.

The route, you’ll remember, was `/clients/{client}` where `{client}` would be the id of a client record we want to look at.

Right away that presents a problem, what’s our client id? Let’s try faking it with the following made up id.

1. Go to: localhost/clients/1 in your browser.

![Screen Shot 2021-12-16 at 3.10.03 PM.png](S3-11:%20Can%20You%20See%20What%20I%20See?.assets/Screen%20Shot%202021-12-16%20at%203.10.03%20PM.png)

You’ll notice we get an entirely different kind of error. This is what a Laravel error looks like. It provides all kinds of information that will help us figure out why it’s being displayed.

You can see the error we’re getting is a SQL error and it tells us that our clients table does not exist in our database. You may be saying, but I thought we created that table? Haven’t we been writing tests on that database? No, in fact we haven’t.

> Remember, every single test we run builds up our database from scratch and tears it back down when the test is finished. And, most importantly, it does this on a completely different test database than the one we use in our development. This is so we can have dummy data in our project database, and test data to create, use, and destroy in our test methods.

Next you might be noticing that big green box. That’s Laravel providing a potential solution for us to consider that might fix our problem. In fact, the solution its suggesting is the correct solution. Before you click that Run Migrations button, let me show you what I mean.

It’s time to look at our database, or rather our currently empty database.

2. Open up your database client software. I’m using TablePlus, and that’s the software I suggested you use too.
3. Click on the “Create a new connection…” button in the lower-left part of your screen. (You may also have a button in the middle saying something similar if you have never used this program before.

![Screen Shot 2021-12-16 at 3.19.07 PM.jpeg](S3-11:%20Can%20You%20See%20What%20I%20See?.assets/Screen%20Shot%202021-12-16%20at%203.19.07%20PM.jpeg)

4. Choose MySQL from the popup window that shows up.

![Screen Shot 2021-12-16 at 3.23.53 PM.png](S3-11:%20Can%20You%20See%20What%20I%20See?.assets/Screen%20Shot%202021-12-16%20at%203.23.53%20PM.png)

5. Fill out the required fields

![Screen Shot 2021-12-16 at 3.22.57 PM.png](S3-11:%20Can%20You%20See%20What%20I%20See?.assets/Screen%20Shot%202021-12-16%20at%203.22.57%20PM.png)

- For the name, I just put `Docker`, because this same configuration will work for all our projects, but you can put whatever name you would like.
- For the host, put `127.0.0.1`
- Port: `3306`
- User: `sail`
- Password: `password`
6. Click Save and you should see that connection appear in your list of saved connections.

Wait a second smarty pants, where did you get those credentials?!?

When we first setup our project Laravel created a database, named it and set a password. It stored those values in our .env file so our project could connect to the database out of the box. Open the .env file and you’ll see where those values came from.

![Screen Shot 2021-12-16 at 3.33.58 PM.png](S3-11:%20Can%20You%20See%20What%20I%20See?.assets/Screen%20Shot%202021-12-16%20at%203.33.58%20PM.png)

> Since this is our local environment we can get away with weak passwords, but never use `password` as your password on a production website.

7. Open your database connection by clicking on the connection you just saved in TablePlus

![Screen Shot 2021-12-16 at 3.36.41 PM.png](S3-11:%20Can%20You%20See%20What%20I%20See?.assets/Screen%20Shot%202021-12-16%20at%203.36.41%20PM.png)

8. Click on the database icon in the upper-left portion of your screen

![Screen Shot 2021-12-16 at 3.37.09 PM.png](S3-11:%20Can%20You%20See%20What%20I%20See?.assets/Screen%20Shot%202021-12-16%20at%203.37.09%20PM.png)

You should see our `tempo` database along with another database MySQL uses (do not delete or manually edit that database)

![Screen Shot 2021-12-16 at 3.37.05 PM.png](S3-11:%20Can%20You%20See%20What%20I%20See?.assets/Screen%20Shot%202021-12-16%20at%203.37.05%20PM.png)

9. Click on tempo to open the database.

![Screen Shot 2021-12-16 at 3.39.47 PM.png](S3-11:%20Can%20You%20See%20What%20I%20See?.assets/Screen%20Shot%202021-12-16%20at%203.39.47%20PM.png)

Now you can see there’s nothing in this database. No tables, nothing.

We’re going to fix that real quick.

Back in Visual Studio Code, open up our project and run through the steps for starting our work session here: [[Workflow: Starting and Ending Your Work Session]]

With that out of the way, we’re going to run our our database migrations. You may remember this process happens automatically on our test database with every test we run, but we’ve never actually run those migration files on our development database.

10. Open up your terminal pane in Visual Studio Code and run the following command:

```Bash
$ sail artisan migrate
```

![Screen Shot 2021-12-16 at 3.45.33 PM.png](S3-11:%20Can%20You%20See%20What%20I%20See?.assets/Screen%20Shot%202021-12-16%20at%203.45.33%20PM.png)

You’ll see two outputs for every migration file. If you look closely, you’ll see the migration file we created for our `clients` table at the bottom.

To see what work the migration did, let’s head back over to TablePlus

![Screen Shot 2021-12-16 at 3.47.06 PM.png](S3-11:%20Can%20You%20See%20What%20I%20See?.assets/Screen%20Shot%202021-12-16%20at%203.47.06%20PM.png)

11. Type `Command` + `R` (`Control` + `R` on Windows) or you can refresh using the refresh icon located in the upper-right portion of your screen right after the status field.

![Screen Shot 2021-12-16 at 3.48.40 PM.png](S3-11:%20Can%20You%20See%20What%20I%20See?.assets/Screen%20Shot%202021-12-16%20at%203.48.40%20PM.png)

You should see some tables built and listed under the `Tables` heading, including our `clients` table.

### The Migrations Table

Most of these tables are empty, including our clients table, however the migrations table isn’t. Click on that one and take a peek at what records have been stored there.

![Screen Shot 2021-12-16 at 3.50.38 PM.png](S3-11:%20Can%20You%20See%20What%20I%20See?.assets/Screen%20Shot%202021-12-16%20at%203.50.38%20PM.png)

This is a table that Laravel uses to keep track of which migration files have been run. You can see we ran 5 migrations and they all have the same batch id (1). Laravel does this so you can safely run that migration command any time and it will only run the migrations it hasn’t run before.

Each time you run `$ sail artisan migrate` Laravel will do a comparison between the records in this table and the files in the `/database/migrations/` folder in our project. If it spots one more more files that are not in the database, it will only run those migrations.

You can test this out by heading on over to Visual Studio Code and in the terminal running that same migration command again.

```Bash
$ sail artisan migrate
```

![Screen Shot 2021-12-16 at 3.55.07 PM.png](S3-11:%20Can%20You%20See%20What%20I%20See?.assets/Screen%20Shot%202021-12-16%20at%203.55.07%20PM.png)

This time, you’ll see the message `Nothing to migrate`.

As we build out more migrations we will be running this command to make our changes to the database.

These migrations also serve as version control for our database schema.

Let’s head on back to our browser and refresh our page to see if that fixed our issue.

![Screen Shot 2021-12-16 at 3.57.07 PM.png](S3-11:%20Can%20You%20See%20What%20I%20See?.assets/Screen%20Shot%202021-12-16%20at%203.57.07%20PM.png)

Looks like it did. We’re now getting a 404. This is expected because we entered a dummy client id in our url and Laravel couldn’t find a client with that id in our database so it returned a 404.

But we still can’t see that view…

To fix that, let’s just create a client in our database directly using TablePlus

### Create a fake client record in the database

1. In TablePlus, open up our connection and the database if it’s not already open.
2. Click on the `clients` table

![Screen Shot 2021-12-16 at 4.00.15 PM.png](S3-11:%20Can%20You%20See%20What%20I%20See?.assets/Screen%20Shot%202021-12-16%20at%204.00.15%20PM.png)

3. Insert a fake client by clicking on the first row under the header row

![Screen Shot 2021-12-16 at 4.01.30 PM.png](S3-11:%20Can%20You%20See%20What%20I%20See?.assets/Screen%20Shot%202021-12-16%20at%204.01.30%20PM.png)

   4. Add the following values for each column
      1. > id: leave blank (one will be assigned automatically)
      2. > name: “Test Client”
      3. > code: “TEST”
      4. > phone: 817-6309
      5. > address: 111 Fake Lane, Fake City, 53716
      6. > created_at: select the now() option in the drop down

![Screen Shot 2021-12-16 at 4.04.55 PM.png](S3-11:%20Can%20You%20See%20What%20I%20See?.assets/Screen%20Shot%202021-12-16%20at%204.04.55%20PM.png)

   TablePlus gives us a chance to make changes without saving to the database, you’ll notice the green color shows we’re going to create a new record, and the yellow table name says the table has been modified.

   5. To commit our changes and save them to the database, click on the `commit icon` in the upper left-portion of the screen or just hit `Command` + `S` (`Control` + `S` on Windows)

![Screen Shot 2021-12-16 at 4.06.59 PM.png](S3-11:%20Can%20You%20See%20What%20I%20See?.assets/Screen%20Shot%202021-12-16%20at%204.06.59%20PM.png)

   When you’re changes have been saved, the colors will go away.

![Screen Shot 2021-12-16 at 4.08.06 PM.png](S3-11:%20Can%20You%20See%20What%20I%20See?.assets/Screen%20Shot%202021-12-16%20at%204.08.06%20PM.png)

   6. Refresh your browser one more time and you'll see our beautifully undesigned view!

![Screen Shot 2021-12-16 at 4.09.36 PM.png](S3-11:%20Can%20You%20See%20What%20I%20See?.assets/Screen%20Shot%202021-12-16%20at%204.09.36%20PM.png)

   We know these values are coming from our development database and not our tests because we named the client something completely different.

   You can run your tests again and they should still pass because we have not made any code changes in this session.

```Bash
$ sail artisan test
```

![Screen Shot 2021-12-16 at 4.13.06 PM.png](S3-11:%20Can%20You%20See%20What%20I%20See?.assets/Screen%20Shot%202021-12-16%20at%204.13.06%20PM.png)

   And because there were no code changes, we have nothing to commit.

   We’re done here, let’s move on shall we?

