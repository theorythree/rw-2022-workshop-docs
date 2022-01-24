# S5-01: What’s Next?

We’ve completed two main feature areas of our app, clients and projects. We even have a relationship between the two.

Here’s our master project checklist updated with the work we’ve completed thus far:

- > User*
- > Role
- > ~~Project~~
- > TimeSheet
- > TimeEntry
- > HourlyRate
- > ~~Client~~
- > Team
- > Estimate
- > EstimateEntry
- > Invoice
- > InvoiceEntry

* User model is technically ready for us, but we do have some enhancements we need to make for us to apply authentication, roles, etc. For now, I’m going to pass on that one as a potential next effort.

But let’s not spend too much time patting ourselves on the back, we have a lot of work to do!

I think it’s time we tackle that litter asterisk next to the User model in our list.

As previously mentioned, Laravel gives us some user functionality right out of the box. We have a User model, factory, and even some authentication services built up (even though at this moment we’re not using them). Pretty cool.

It’s time for us to extend all of this functionality, and to do that we need to first map out what we mean when we say user.

A User in terms of Laravel refers to a person who has a username and password in our database. The user who uses our application may be a guest or sometimes referred to as a public user, because they can freely access all the public pages on our site.

We’re building a time tracking application and the time needs to be connected to a User so we can distinguish their time entries from everyone else’s. Make sense?

So there are a lot of ways we might build out the users in our application and we should map all that out now before we just dig in.

When planning how users will use any application, we need to discuss the following:

1. Roles
2. Permissions
3. User Authentication

### Roles

Rolls represent the kind of users we are going to have. Typically you’ll see this split between a user and an admin. An admin can do things the user cannot, like add, edit, and delete certain things in the database.

In our case, I can think of several potential roles, we might want:

1. user - a person who can login and enter their time, manage their account, and not much else.
2. contractor - someone who may be a guest user of sorts, they can enter their time, but not see the team’s time details and would have limited account editing capabilities.
3. owner - the owner of the account, they could add team members, set restrictions, generate reports, enter billing information to pay for the service, invoice clients, and even edit the time entries of their employees and contractors.
4. admin - the person who can setup owners and their accounts

### Permissions

You may have noticed, when I was describing the roles, I was also describing what I think each role may or may not be allowed to do, these are permissions. By having roles, we can assign a set of permissions to a particular role. So, for example all users who have the role of owner can do everything in the app that all the other owners can do.

Later we’ll define exactly what each permission will be, but I can think of a few permissions we might use in this application.

1. Add a user (admins and owners only)
2. Edit user information (admins, owner, and the user could edit their own record)
3. Delete a user (admins and owners only)
4. View a user (users, contractors, owners, and admins)

You can see how the combination of these permissions could get pretty complex. And if you’re thinking that it would make for a good use of our automated testing, then you’re my new best friend.

### User Authentication

We can’t have users without having a way for them to login. A modern-day application, such as the one we’re building will require a whole set of features.

1. Login
2. Password and email validation
3. Password reset / Forgot password
4. Email verification
5. Registration
6. Views for all of the above

These things are pretty standard, and can be easily forgotten or ignored. Luckily we can use a starter kit to help us build up all of these things with just a few commands.

