# S5-05: Creating Roles For Our Users

Well the time has finally come. We get to extend the usefulness of the users of our application by assigning them roles.

You may remember we decided to have four roles in total

1. Guests
2. Users (logged in users)
3. Owners
4. Administrators

Each is granted a bit more access than the one listed above it.

Remember way back in our planning phase we talked about the relationships between all of our models? No? Okay, here’s a refresher on that topic and I’ve highlighted the relevant relationships:

> User

   - > can have many roles
   - > can be working on multiple projects
   - > can have multiple time sheets
   - > can have multiple time entries
   - > can be working on multiple clients
   - > can belong to multiple teams
   - > can author multiple estimates
   - > can author multiple invoices
   - > can generate multiple reports

> Role

   - > can have multiple users belong to a role
   - > can have a single hourly rate for a role

So it sounds like there could be multiple roles and each of them could belong to many users. Or there could be many users and each of the users could belong to multiple roles.

### Many-to-Many Relationships

In the dev world, we describe this kind of relationship as a many-to-many relationship. This is a bit of a different kind of relationship than the one we setup for Clients and Projects because a project can *only* belong to one client, that relationship was stored in the `projects` database table in the `client_id` column in that table.

In this case, where would we put the user_id and role_id in the database? We can’t store it in either table because there will be multiple ids needed for both users and roles.

This problem has already been solved, and the answer is what’s called a pivot table.

Here’s a visual of how this might look in the database.

![2022-laravel-rwanda-middleware.003.png](S5-05:%20Creating%20Roles%20For%20Our%20Users.assets/2022-laravel-rwanda-middleware.003.png)

> I color-coded the id columns so it might make more sense.

> As you can see the `users` and `roles` tables are untouched. We’re just adding a new table called `role_user` into the mix. You can see the pivot table can contain multiple entries for both roles and users.

> Also worth noting, guest is not a role we store in the database, because guests do not have an account to attach a role to.

In order for our pivot table to work, we have to follow a strict naming convention.

1. The pivot table must be named singular tense (i.e. role not ~~roles~~) and have both of the other tables in its name.
2. The order of the name matters, it’s always alphabetical so `role_user` never `user_role`
3. The id columns in the pivot table must also be singular and be the table name `_id` after it. Just as I have it in my example image above.

If we follow that naming convention, and perform a quick relationship setup in our User and Role models, Laravel will do the heavy lifting for us.

So to get to the business of setting up roles for our users, we’re going to need a couple of things.

1. > Create a Role Model
2. > Create a migration file for roles table and the pivot table
3. > Create a database Seeder for roles
4. > Create a relationship from User to Role in the User Model
5. > Create a relationship from Role to User in the Role Model

We’ve made a model before, so that’s an easy one.

### Create a Role Model and a Migration for Roles Database Table

1. Open up that terminal window and issue this command

```Bash
$ sail artisan make:model Role -m
```

> Notice, I’m not creating a controller along with the model, only a migration, so the flag for this one is just `-m` (m is for migration)

Let’s work on the migration file first. You can find it will be in database/migrations/`xxxx_xx_xx_xxxxx_create_roles_table.php`

All we really need to add to this migration file is the name column to store our role name.

2. Update your schema to add the name column

```php
Schema::create('roles', function (Blueprint $table) {
    $table->id();
    $table->string('name')->unique();
    $table->timestamps();
});
```

You may notice, I’m requiring the role names be unique, we don’t want two roles with the same name.

Now let’s build that pivot table we were talking about.

This time, just the migration is needed

```Bash
$ sail artisan make:migration create_role_user_table
```

The updated schema will look like this

```php
Schema::create('role_user', function (Blueprint $table) {
  $table->unsignedInteger('role_id');
  $table->unsignedInteger('user_id');
  $table->primary(['role_id','user_id']);
});
```

> A couple of things to point out on this one. We don’t need the `$table->id();` or the `$table->timestamps();` lines for a pivot table. Pivot tables have a special primary id which you can see in the last line of method, `$table->primary(['role_id','user_id']);` that's assigning the primary id to both ids, so the database table will not allow for the same combination of ids to be in the table.

> The other two columns above that line are just unsigned integers like the `client_id` was when we made the projects table migration.

> Also, just a reminder, the table being created here is named `role_user` because role appears before user when alphabetically ordered.

We can now run our migrations to update our database.

```Bash
$ sail artisan migrate
```

![Screen Shot 2021-12-22 at 4.08.26 PM.png](S5-05:%20Creating%20Roles%20For%20Our%20Users.assets/Screen%20Shot%202021-12-22%20at%204.08.26%20PM.png)

### Database Seeders

A quick word about database seeders. They are responsible for creating records in our database (that process is called seeding). They serve a very specific role in the development of our application. You can use seeders for any database table you wish, however a word of caution. You should never create seeders for database tables which may contain live information, like a users table, because seeders will truncate the database table and build it back up with the records you define in your seeder file.

We’re going to use a seeder for our roles. Those will not, and should not ever change. If we need to add a role, we’ll add it to our seeder and run that seeder. As opposed to entering that information in the database manually or creating a controller and a form for a user to enter that information on the site. Because the entire system is going to be using these roles to ensure a user does not access an area they shouldn’t.

Let’s create our seeder and you’ll see more of what I mean as we start to implement roles into our application code.

Of course by now, you a probably expecting that there is an artisan command to create a seeder and , of course there is.

1. Open up the terminal and issue this command

```Bash
$ sail artisan make:seeder RoleSeeder
```

You should follow the naming convention for seeders as we do with all of our other files. The model name, then the word Seeder, all in upper camel case.

2. Include our Role Model with the use statements at the top of the file.

```php
use App\Models\Role;
```

3. Next up, update the run() method which is run when the seeder is called.

```php
\DB::table('roles')->truncate();

Role::create(['id' => 1, 'name' => 'user']);
Role::create(['id' => 2, 'name' => 'owner']);
Role::create(['id' => 3, 'name' => 'admin']);
```

![Screen Shot 2021-12-22 at 5.25.26 PM.png](S5-05:%20Creating%20Roles%20For%20Our%20Users.assets/Screen%20Shot%202021-12-22%20at%205.25.26%20PM.png)

> You’ll notice that first line uses \DB, which is an available facade which let’s us truncate the table without using the model. This is what erases any data in that table, so we can run this seeder as often as we want and we can be sure only the records we add explicitly in this seeder will be in the database after the seeder runs.

> The other notable thing is I’m explicitly assigning an id. We normally don’t do that , but it’s extremely important in this case that those particular ids get assigned to those particular roles. We wouldn’t want to leave such an important detail left to chance.

Final point, this is a seeder we want to run every time we seed the database. It’s okay to run this with other seeders or by itself. I’ll show you both ways.

4. The command we want to run to run ALL our seeders is:

```Bash
$ sail artisan db:seed
```

Right now that command won’t work for our RoleSeeder, because it must be explicitly registered in DatabaseSeeder.php because that’s the seeder that gets run when we do not specify a specific seeder. It’s still safe to run anyway.

Register our new RoleSeeder in the DatabaseSeeder.php file

```Bash
public function run()
{
  // \App\Models\User::factory(10)->create();
  $this->call([
    RoleSeeder::class,
  ]);
}
```

You can see we are calling the RoleSeeder from the run() method. We’ll likely list more seeders here in the future and they are just comma separated, which is why I left a trailing comm after the `RoleSeeder::class` line.

If you run the same command again, this time you’ll see some output

![Screen Shot 2021-12-22 at 5.33.25 PM.png](S5-05:%20Creating%20Roles%20For%20Our%20Users.assets/Screen%20Shot%202021-12-22%20at%205.33.25%20PM.png)

This is great, but what if we just want to run just the RoleSeeder?

We can do that by specifying the class in the command. It looks like this.

```Bash
$ sail artisan db:seed --class=RoleSeeder
```

![Screen Shot 2021-12-22 at 5.35.00 PM.png](S5-05:%20Creating%20Roles%20For%20Our%20Users.assets/Screen%20Shot%202021-12-22%20at%205.35.00%20PM.png)

> You’ll see a much more generic output, but since you specified the class it should be clear what seeder you ran. Also, there are ways to customize the output of the seeders when they’re called. We’ll learn about that later.

If you’re so inclined, you can open up TablePlus and checkout the new tables we created and you should see the seeder data we added for the `roles` table.

![Screen Shot 2021-12-22 at 5.37.27 PM.png](S5-05:%20Creating%20Roles%20For%20Our%20Users.assets/Screen%20Shot%202021-12-22%20at%205.37.27%20PM.png)

1, 2, 3, easy. You now have a database seeder which you can use anytime you might need to insert this data in the database.

### Setup the Model Relationships between User and Role

With our seeder out of the way, we need to establish our relationships between the User and Role model. You may remember, we do these both at the same time.

1. Open app/Models/Role.php

![Screen Shot 2021-12-26 at 4.41.00 PM.png](S5-05:%20Creating%20Roles%20For%20Our%20Users.assets/Screen%20Shot%202021-12-26%20at%204.41.00%20PM.png)

From this side, we want to create a users() method which defines the relationship. Many-to-many relationships have a belongsToMany() method on each side.

2. Put the following method in the body of the Role class

```php
public function users()
{
  return $this->belongsToMany(User::class);
}
```

![Screen Shot 2021-12-26 at 4.46.03 PM.png](S5-05:%20Creating%20Roles%20For%20Our%20Users.assets/Screen%20Shot%202021-12-26%20at%204.46.03%20PM.png)

3. Open app/Models/User.php

![Screen Shot 2021-12-26 at 4.43.56 PM.png](S5-05:%20Creating%20Roles%20For%20Our%20Users.assets/Screen%20Shot%202021-12-26%20at%204.43.56%20PM.png)

The reverse relationship uses the same belongsToMany() method, but it refers to the Role model.

4. Put the following method at the bottom of the body of the User class

```php
public function roles()
{
  return $this->belongsToMany(Role::class);
}
```

![Screen Shot 2021-12-26 at 4.47.10 PM.png](S5-05:%20Creating%20Roles%20For%20Our%20Users.assets/Screen%20Shot%202021-12-26%20at%204.47.10%20PM.png)

