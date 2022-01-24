# Workflow: Starting and Ending Your Work Session

As a handy reminder, here are the steps you should perform each time you start a work session.

---

### Starting your work session:

Every time (yes every time) you start up a work session, you should make sure to perform these steps.

1. Ensure Docker is running
2. Open the project in Visual Studio Code
3. Issue the sail up command (if it’s not already running) in a terminal window

```other
$ sail up
```

4. Ensure our project is running in the browser by visiting localhost
5. Check the status of our local code to make sure we have no uncommitted changes

```other
$ git status
```

6. If we have any uncommitted changes, either commit them or revert them before we proceed
7. Check to make sure our local copy of code is up to date

```other
$ git pull
```

8. Run our tests to make sure they are all still passing

```other
$ sail artisan test
```

9. Fix any failed tests and commit those changes
10. Set a goal for the work you wish to accomplish in this session
11. Begin your work

---

### Ending your work session

At the end of every work session you should perform the following steps:

1. Run your tests to verify they are all passing

```other
$ sail artisan test
```

> If you don’t see passing tests for everything, do not commit your changes until you fix them.

From now on, I’m just going to give you the commands to commit your code along with a brief description of what they’re doing. After that, I’ll just give you the commands. Read? Okay let’s go.

2. First, we’re going to check the status to see what’s been changed on our local machine (new files, etc)

```other
$ git status
```

3. This is a good time to do a pull to make sure someone else hasn't changed anything while we did our work

```other
$ git pull
```

4. After reviewing the files to make sure we don’t see any surprises (files we didn’t mean to modify, add, or delete) we’ll add the files to this commit.

```other
$ git add .
```

5. Then we’ll commit them and provide a simple, but descriptive commit message

```other
$ git commit -m "Adds a test, model, controller, and route rule for creating a Client"
```

6. Finally, let’s push them up

```other
$ git push
```

After your code is committed, if you’re done working was can turn off our Docker container.

```other
$ sail down
```

