# S3-02: Project Planning (Controllers)

Now that we’ve defined our Models, we turn our attention to the Controllers part of our planning. This should be a lot simpler to plan than the Models, so let’s get started.

For our controllers we just have to ask ourselves one set of questions about each of our Models in our project.

1. Will a user need to `create` one of their types in the database?
2. Will a user need to `read` one of their types in the database?
3. Will a user need to `update` one of their types in the database?
4. Will a user need to `remove` one of their types in the database?

We refer to these actions as CRUD (Create, Read, Update and Delete).

If the answer to all four questions is YES, then we can setup the controller as what’s called a resource controller and Laravel will automatically stub some of our code out for us. If it’s a combination of yes/nos then we simply build out our controllers accordingly. Finally, if our answers are all No then we may not need to create a controller at all.

I’ve taken the liberty of running through all the Models in our project and determining the CRUD.

- > Users (CRUD)
- > Roles (R)
- > Projects (CRUD)
- > Time Sheets (CRUD)
- > Time Entries (CRUD)
- > Hourly Rates (CRUD)
- > Clients (CRUD)
- > Teams (CRUD)
- > Estimates (CRUD)
- > EstimateEntries (CRUD)
- > Invoices (CRUD)
- > Invoice Entries (CRUD)

You’ll see that most of our controllers will be resource controllers, with the exception of Roles, which is readonly. This is because we want user roles to be constant in our application. We don’t want anyone deleting or even adding a role because we want to be able to define specific actions that are dependent on roles in our database.

