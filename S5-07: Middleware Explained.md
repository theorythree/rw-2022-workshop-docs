# S5-07: Middleware Explained

I want to take a brief moment to explain middleware. What exactly is it?

You can think of middleware as software that runs after a user has put in a request and before any request handler (most commonly a controller) handles the request. The most common way we’ll use middleware is by protecting our routes so unauthorized users cannot access certain parts of our application.

For example, we just installed Laravel Breeze and it created a dashboard that we are free to use as our application back-end. We’re going to restrict the dashboard to just admins. I want other roles to only see the  front-end part of the site.

In fact, I envision us having four sections of our site, the image below shows my current thought process.

![2022-laravel-rwanda-middleware-02.001.png](S5-07:%20Middleware%20Explained.assets/2022-laravel-rwanda-middleware-02.001.png)

1. Public pages will include things like the login and registration pages.
2. Private pages will be all the pages an account holder needs to do their time entry.
3. Owner features represents the features that owners will have that other users do not. This may include special pages for the owners that manage accounts, clients, etc. And it may also include certain functionality that other users do not have within the pages that a logged in user may have access to. For example, owners might have a button on a user’s profile page that allows them to edit the details on accounts that are not their own.
4. Dashboard pages will be the full back-end, access to, and full control of, everything. Right now, my thought is that we’ll possibly have accounts, so imagine multiple businesses using our application, each of their people and their people’s time is tracked and exclusive to them. An administrator could use the dashboard to help setup owner accounts, and do other administrative tasks, and the owners never see or even know about the dashboard at all.

So you can see how all of this pertains to the middleware part of our application. We need to make sure that the requests from guests never access private pages, users never access the owner pages (or features) and guests, user, and owners never access the administrator dashboard.

To do this we’re going to protect our routes, and appropriately block the users that cannot access our protected pages.

Hope on over to the next episode and we’ll get started.

