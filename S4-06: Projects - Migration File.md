# S4-06: Projects - Migration File

When we last left off, we made some excellent progress by knocking out our initial controller methods for ProjectController. Good work by the way, we’re starting to get into a groove.

As a matter of review, here’s an updated checklist

1. > ~~Create a feature branch for our project features~~
2. > ~~Create a Model, Controller, Database Migration, Test files, and View files~~
3. > ~~Write our initial suite of tests for projects~~
4. > ~~Create our routes for projects~~
5. > ~~Update our controller CRUD code~~
6. > Update our migration file to build up a projects table in the database
7. > Discuss Model relationships and implement our first relationship between clients and projects
8. > Finish our ProjectFactory file
9. > Update our dummy views for projects
10. > Run our tests and fix any issues we have
11. > Commit our finished feature, then merge it into main branch

Next up is our migration file. This is the file responsible for building our database tables.

We’ll want to refer to our project plan to remind ourselves what the field names are supposed to be, here’s what we came up with.

- > Project
   - > id (int | unique)
   - > client_id (int)
   - > name (string)
   - > description (text | optional)
   - > budget (float, 2)
   - > created_at (date time)
   - > updated_at (date time)

   Remember the id, created_at, and updated_at fields will write themselves and are already included in our migration file. Thanks Laravel!

   Here’s what the `up()` method code looks like:

```php
public function up()
{
  Schema::create('projects', function (Blueprint $table) {
      $table->id();
      $table->unsignedInteger('client_id');
      $table->string('name');
      $table->text('description')->nullable();
      $table->float('budget',10,2)->nullable();
      $table->timestamps();
  });
}
```

   A couple of new things to point out.

   First, you’ll notice I’m using `unsignedInteger` as a column type. What the heck is that? That’s simply a way of telling the database we will only have positive values for this integer (no negative values) and it doesn’t need to reserve any space in storing those kind of values.

   > The unsigned part refers the the minus sign (`-`) that typically appears before a negative number `-1`, `-2`, etc. By telling our database. We know our IDs will never be negative, so it’s smart to tell the database that fact.

   The second thing of note is the `float()` (haha on the unintentional rhyming there). This takes three arguments, the name of the `database column name`, just like all the others, then two numbers.

   Those two numbers define the precision we need in our float. We’re saying we want to store numbers that have up to `10` integers before the decimal place, and `2` after. Since we’re storing money, we want to allow for values like 35.25 (thirty-five dollars and 25 cents). This means we can store any value from `$0.0` to `$9,999,999,999.99`. I think we’ll be okay assuming we won’t have a $10 Billion dollar budget. But if we did, we’d bump that up.

   > Again, this is a memory saving technique which helps the our database from becoming bloated.

   > One last thing, 10,2 are the standard currency numbers used in databases, avoid decreasing those numbers because if you do your stored values will round when they’re put into the database.

   The `down()` method is already done for us (again, thanks Laravel!), it will drop the table when the database gets torn down in our testing methods.

```php
public function down()
{
  Schema::dropIfExists('projects');
}
```

   With that out of the way, we can run our migrations to setup the database table for our project, then we’re done.

```Bash
$ sail artisan migrate
```

![Screen Shot 2021-12-18 at 6.37.15 PM.png](S4-06:%20Projects%20-%20Migration%20File.assets/Screen%20Shot%202021-12-18%20at%206.37.15%20PM.png)

   > It’s a good idea to run our migrations as soon as we’re sure they’re ready to go. This is because when we run them, we’ll verify that there are no errors in the migration file.

   If we want, we can open TablePlus and checkout the database table to make sure all the columns are there as we would expect them to be.

   1. Open TablePlus
   2. Open the connection we saved when we setup the program (mine is called Docker)
   3. Click on the Database icon in the upper left of the window
   4. Click on the `tempo` database, and then the `open` button to open the database
   5. Then, finally, click on the `projects` table in the left column under the `tables` heading

![Screen Shot 2021-12-18 at 6.41.31 PM.png](S4-06:%20Projects%20-%20Migration%20File.assets/Screen%20Shot%202021-12-18%20at%206.41.31%20PM.png)

   Looks good! We just need to do our one-file commit and then we’re done here.

```Bash
$ git status
$ git add database/migrations/{your-time-stamp-here}_create_projects_table.php
$ git commit -m "adds migration for creating a projects table"
```

   > Be sure to use your migration file name, not mine, you can copy and paste it when you do your `$ git status`, which is what I always do when committing just one file.

