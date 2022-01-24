# S3-03: Project Planning (Views)

View planning can be even more simplified than Models and Controllers. As discussed in the previous series, we want to make sure we have the following views for each of our Models

1. create (a screen where a user can create a record in the database)
2. edit (a screen where a user can edit the record)
3. index (a listing of the records in the database)
4. show (a screen where a user can see a specific record)

Our views will line up with our controller `CRUD` methods. `Create` uses the `create.blade.php` view, `Read` uses the `index.blade.php` and the `show.blade.php` file, `Update` uses the `edit.blade.php`, and `Delete` will most likely be triggered by a button on the `show.blade.php` and/or the `index.blade.php` files if the user has the proper privileges.

We will, of course, have limitations as to who can see each of these views. For example we may not want a user to be able to edit a rate, that ability may be reserved for the user who has the proper role/privileges. These limitations will be handled by our routes and our controllers.

We may also have other views beyond the standard four listed above, but we will cover those as we need them when we build our application.

