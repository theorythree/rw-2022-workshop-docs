# S2-02: What is MVC

## MVC stands for Model View Controller.

These three things represent the categories in which we organize our codebase, the naming conventions we use, and the separation of responsibility that each code file has in our project.

### Model

![workhops-2022-rwanda.003.png](S2-02:%20What%20is%20MVC.assets/workhops-2022-rwanda.003.png)

The `Model` describes our data. What exactly does that mean? Let’s say you have a `users table` in your database, the Model would describe the columns of data and describe their types along with any other related information.

If your users table had the following columns, your model would be an object with each of these column values in it.

- `id` (Unique Int) => `1`
- `first_name` (String) => `Dan`
- `last_name` (String) => Merfeld
- `email` (String) => `dan@theorythree.com`
- `age` (Int) => `46`

> It’s important to remember the Model is not the actual data, nor is it the database itself. It is only a description of the data. You can think of it as a template specifically used for all data that belongs to its type (in this example `User`). In fact, Models don’t even have to have a database table at all. Model Objects can be used in our code to store information. Any use of that Model would help describe the data so it could be used appropriately in our code.

### View

![workhops-2022-rwanda.004.png](S2-02:%20What%20is%20MVC.assets/workhops-2022-rwanda.004.png)

The `View` might be the most familiar of the three types to you. It’s often referred to as the presentation layer or user interface. It’s what the users of our site will see and interact. In our project, the views will be HTML templates which will be constructed from Blade templates (more on that later). Those views will be made of static and dynamic content. Some pulled from the database, other content might be in the form of an image asset, which is represented on the page.

### Controller

![workhops-2022-rwanda.005.png](S2-02:%20What%20is%20MVC.assets/workhops-2022-rwanda.005.png)

The `Controller` is the brains of the operation. It sits between the `View` and the `Model`, and interacts with both. The `Controller` handles the majority of the requests that the user makes when using our site. It may access the database (through the `Model`), perform some functions, and determine which view the user should see after it's done processing the request.

For example, let’s say the user of our site wants to register an account. They would fill out a form and submit that information. The controller is responsible for handling that request, so it would take the information provided, refer to the `Model` to see what the information is supposed to look like, then it would match up the submitted data to the `Model`. After that, it would save that information to the database. Finally, the controller might direct the user to a certain page on our site and inform that page view to display a success message.

### Putting it All Together

The whole request transaction would look something like this:

![workhops-2022-rwanda.006.png](S2-02:%20What%20is%20MVC.assets/workhops-2022-rwanda.006.png)

### Final Thing to Mention

Each topic of interest in our project will have three files; one for the `Model`, one (or more) for the `View`, and one for the `Controller`. Part of using MVC is having a set naming convention and location in our project for each of these types of files.

For example, if we had a `products table` in our database our naming convention would be as follows:

1. database table: `products`  (always plural, lowercase)
2. The `Model` would be called `Product` (always singular, uppercase and stored in the `/app/Modles/` directory
3. The `Controller` would be called `ProductController` (always matches the model name with the word `Controller` after it. It would be stored in the `/app/Http/Controllers` directory.
4. The View would actually be several views, including the following:
   1. index.blade.php
   2. show.blade.php
   3. edit.blade.php
   4. store.blade.php

   Each of these fines would be stored in a folder called `products` which would be in the `resources/views`/ folder.

> Wondering why we have these particular names for our product views? It’s because each template will handle a different interaction with our users and the products. `Index.blade.php` would be used to display a listing of products, `show.blade.php` is used to show a single product, `edit.blade.php` is used to edit that product (assuming the user has privileges to do so), and `store.blade.php` would be used to store a new product (again, assuming the user has the proper privileges to do so).

> We might also have other views, for example, we might have a view called `results.blade.php` which would display a list of product search results.

> The key point to remember about views is that the names for all our views will be the same and we’ll organize them in a folder named after our model.

