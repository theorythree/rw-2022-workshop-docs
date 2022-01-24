# S7-07: Policies - Projects

We made some great progress, and now we just need to zip through the portions of our code that deal with projects.

As always, we’ll move a bit quicker on this one.

Let’s start off by running our full set of tests, we’re going to want everything green when we wrap up our work.

```Bash
$ sail artisan test
```

![Screen Shot 2022-01-11 at 12.54.33 PM.png](S7-07:%20Policies%20-%20Projects.assets/Screen%20Shot%202022-01-11%20at%2012.54.33%20PM.png)

These results are expected, we can make quick work of this.

Let’s start with the ProjectRequest, it’s a simple change.

Open App/Http/Requests/ProjectRequest.php

![Screen Shot 2022-01-11 at 1.04.11 PM.png](S7-07:%20Policies%20-%20Projects.assets/Screen%20Shot%202022-01-11%20at%201.04.11%20PM.png)

All we have to do is delete the authorize() method.

Delete authorize() method from the ProjectRequest file.

![Screen Shot 2022-01-11 at 1.04.58 PM.png](S7-07:%20Policies%20-%20Projects.assets/Screen%20Shot%202022-01-11%20at%201.04.58%20PM.png)

Next, let’s create our policy file for Projects.

```Bash
$ sail artisan make:policy ProjectPolicy --model=Project
```

![Screen Shot 2022-01-11 at 1.06.33 PM.png](S7-07:%20Policies%20-%20Projects.assets/Screen%20Shot%202022-01-11%20at%201.06.33%20PM.png)

Open App/Policies/ProjectPolicy.php

![Screen Shot 2022-01-11 at 1.07.15 PM.png](S7-07:%20Policies%20-%20Projects.assets/Screen%20Shot%202022-01-11%20at%201.07.15%20PM.png)

Include the Auth facade in the header.

```php
use Auth;
```

Return true for the viewAny() and view() policy methods.

```php
public function viewAny(User $user)
{
  return true;
}
```

```php
public function view(User $user, Project $project)
{
  return true;
}
```

Do a check on the user for owner role on create(), update() and delete() methods.

```php
public function create(User $user)
{
  return auth()->check() && $user->isOwner();
}
```

```php
public function update(User $user, Project $project)
{
  return auth()->check() && $user->isOwner();
}
```

```php
public function delete(User $user, Project $project)
{
  return auth()->check() && $user->isOwner();
}
```

And set restore(), forceDelete() methods to false.

```php
public function restore(User $user, Project $project)
{
  return false;
}
```

```php
public function forceDelete(User $user, Project $project)
{
  return false;
}
```

Here’s the entire ProjectPolicy code for your reference.

```php
<?php

namespace App\Policies;

use App\Models\Project;
use App\Models\User;
use Illuminate\Auth\Access\HandlesAuthorization;
use Auth;

class ProjectPolicy
{
    use HandlesAuthorization;

    /**
     * Determine whether the user can view any models.
     *
     * @param  \App\Models\User  $user
     * @return \Illuminate\Auth\Access\Response|bool
     */
    public function viewAny(User $user)
    {
      return true;
    }

    /**
     * Determine whether the user can view the model.
     *
     * @param  \App\Models\User  $user
     * @param  \App\Models\Project  $project
     * @return \Illuminate\Auth\Access\Response|bool
     */
    public function view(User $user, Project $project)
    {
      return true;
    }

    /**
     * Determine whether the user can create models.
     *
     * @param  \App\Models\User  $user
     * @return \Illuminate\Auth\Access\Response|bool
     */
    public function create(User $user)
    {
      return auth()->check() && $user->isOwner();
    }

    /**
     * Determine whether the user can update the model.
     *
     * @param  \App\Models\User  $user
     * @param  \App\Models\Project  $project
     * @return \Illuminate\Auth\Access\Response|bool
     */
    public function update(User $user, Project $project)
    {
      return auth()->check() && $user->isOwner();
    }

    /**
     * Determine whether the user can delete the model.
     *
     * @param  \App\Models\User  $user
     * @param  \App\Models\Project  $project
     * @return \Illuminate\Auth\Access\Response|bool
     */
    public function delete(User $user, Project $project)
    {
      return auth()->check() && $user->isOwner();
    }

    /**
     * Determine whether the user can restore the model.
     *
     * @param  \App\Models\User  $user
     * @param  \App\Models\Project  $project
     * @return \Illuminate\Auth\Access\Response|bool
     */
    public function restore(User $user, Project $project)
    {
      return false;
    }

    /**
     * Determine whether the user can permanently delete the model.
     *
     * @param  \App\Models\User  $user
     * @param  \App\Models\Project  $project
     * @return \Illuminate\Auth\Access\Response|bool
     */
    public function forceDelete(User $user, Project $project)
    {
      return false;
    }
}
```

Now open the App/Http/Controllers/ProjectController.php file

![Screen Shot 2022-01-11 at 1.15.19 PM.png](S7-07:%20Policies%20-%20Projects.assets/Screen%20Shot%202022-01-11%20at%201.15.19%20PM.png)

For the create() and store() methods, add the authorization for create.

```php
public function create()
{
  $this->authorize('create', Project::class);
  return view('projects.create');
}
```

```php
public function store(ProjectRequest $request)
{
  $this->authorize('create', Project::class);      
  $project = Project::create($request->validated());
}
```

And add the update authorization check for edit() and update() methods.

```php
public function edit(Project $project)
{
  $this->authorize('update', $project);
  return view('projects.edit',compact('project'));
}
```

```php
public function update(ProjectRequest $request, Project $project)
{
  $this->authorize('update', $project);
  $project->update($request->validated());
}
```

And, finally, add the delete authorization check on the destroy() method.

```php
public function destroy(Project $project)
{
  $this->authorize('delete', $project);
  $project->delete();
}
```

Our middleware requires no updates, so we should be ready to run our full set of tests.

```Bash
$ sail artisan test
```

![Screen Shot 2022-01-11 at 1.20.50 PM.png](S7-07:%20Policies%20-%20Projects.assets/Screen%20Shot%202022-01-11%20at%201.20.50%20PM.png)

From now on and new models or controllers that we create, we will also put policies in place for them as well.

It’s time to merge and merge again!

First, let’s commit these changes in our current policies feature branch.

```Bash
$ git status
$ git add .
$ git commit -m "adds policies for projects"
```

Now we have two options, we can merge this back into our refactor branch or we can directly merge the policy branch into main. Both will accomplish the same result so it’s a matter of taste.

I prefer to merge the policy branch back into main in these cases, We know everything is working on this branch and since they’re both local branches, we need not worry about a conflict.

```Bash
$ git status
$ git checkout main
$ git status
$ git pull
$ git merge dan/policies
```

![Screen Shot 2022-01-11 at 1.28.14 PM.png](S7-07:%20Policies%20-%20Projects.assets/Screen%20Shot%202022-01-11%20at%201.28.14%20PM.png)

Let’s double check our tests still pass after our merge.

```Bash
$ sail artisan test
```

![Screen Shot 2022-01-11 at 1.28.57 PM.png](S7-07:%20Policies%20-%20Projects.assets/Screen%20Shot%202022-01-11%20at%201.28.57%20PM.png)

And finally, let’s push these changes up to GitHub.

```Bash
$ git push
```

Excellent work my friend!

