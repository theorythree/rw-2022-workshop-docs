# S4-02: Projects - Tags & Branches

Before we get to writing any code, I want to pause us for just one second.

Let’s take stock of the fact that we’ve done a lot of work on our project by implementing the client features to our application. Right now our project is clean, all our tests pass and we are at a good stopping point with the client features. It might be good for us to mark this moment in time in our version control management software (Git).

### A word about Git

Git gives us a couple of options to do this. We could create and checkin a `tag`, which serves as kind of a bookmark in the repository. We can name our tags anything we want and rewind our code to that exact moment in time that the tag was created.

> Typically we use tags to indicate when we have something significant to bookmark like a new version of the entire application, and it’s usually not meant for specific features or enhancements in the application.

Another option is for us to use what’s called a `feature branch`. It too can be called whatever we want, and it works a bit differently than a tag does because it branches off from the main branch at a specific moment in time and then both branches (main, and our feature branch) can move forward independently of one another.

Eventually our feature branch will merge back into main branch and they’ll once again be in sync with one another.

> Typically a feature branch is used for just that, a feature. One advantage it has over tags is that if, for any reason we decided to abandon a particular feature or reject it we can unwind the whole set of changes made and restore our repository back to a state before the changes were merged in. This is quite powerful. This approach also lets other developers create their own feature branches at the same time and work in isolation, with the intent to merge their branches into the main branch eventually.

We’re going to use a feature branch in this particular case, so let’s discuss what we're going to call our feature branch.

As with anything naming is very important. We have several goals to keep in mind when coming up with a name of our feature branch.

1. We have to clear it's a feature branch because it's common practice to use branches for other purposes (bug fixes, major releases, different environments, etc).
2. The name should be short. We don’t want long branch names because we have to type these names into a terminal to switch between branches.
3. The name should be clearly describe what it is. We’ll likely have potentially dozens of branches so no generic names.
4. In cases where a single person might be working on the feature it should identify who that person is. There are exceptions to this rule, especially in large teams, the convention may be different.

There are two strategies for naming branches:

1. {user}/feature-{name-of-feature} (i.e. dan/feature-projects)
2. feature/{name-of-feature} (i.e. feature/projects}

Branches are usually listed alphabetically so they’ll appear grouped by feature and/or person if we adhere to one of the conventions above.

I’m planning on potentially open sourcing this project, so I’m going to go with the dan/feature-projects approach. You can follow along.

