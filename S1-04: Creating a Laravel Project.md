# S1-04: Creating a Laravel Project

Now that we have all the tools installed on your computer we can start to setup our first Laravel project to test the setup.

> If you have not installed the software please refer to [[S1-03: Tools You’ll Need]] for more information

### Install and Setup Docker

We’re going to be using Docker, which should already be installed and running on your computer. There’s no setup required for Docker, but once it’s installed you should see the Docker Desktop Icon on your computer when it’s running.

## Creating a Laravel Project

Let’s begin by creating a new Laravel project. We’ll first need to pick a location on our computer to store the project codebase.

> I highly recommend you have a single directory to contain all of your web projects that you will be working on. For me, I store all of my web projects in a folder called `workshop` which is in a folder called `Sites` in my home directory. This means my path to the `project root folder` will be `~/Sites/workshop/my-project`  where `my-project` is the name of my project. In this workshop I will refer to this location as the `project root folder`, so just know this as the location on your computer where you choose to store your projects. I'll refer to the `web root folder` as the folder that contains all your projects (in my case my web root folder is `~/Sites/workshop` ).

### Steps

1. Open up Visual Studio Code and you should see something like the screenshot below.

![Screen Shot 2021-11-28 at 1.52.23 PM.png](S1-04:%20Creating%20a%20Laravel%20Project.assets/Screen%20Shot%202021-11-28%20at%201.52.23%20PM.png)

2. Click on “Open…”  and choose your `web root folder`. This will tell Visual Studio Code that it should only be interested in the files contained within this directory. We're about to create a new Laravel project, so a new folder will be created within your `web root folder` and named whatever we decide to name the project.

![Screen Shot 2021-11-28 at 2.05.15 PM.png](S1-04:%20Creating%20a%20Laravel%20Project.assets/Screen%20Shot%202021-11-28%20at%202.05.15%20PM.png)

3. Visual Studio Code has a baked in terminal we can use to execute all the commands we need, in this case, we're going to issue a command that downloads and installs the Laravel project.
4. Open the Terminal in Visual Studio Code by selecting `new terminal` from the `Terminal` dropdown menu.

![Screen Shot 2021-11-28 at 2.08.05 PM.png](S1-04:%20Creating%20a%20Laravel%20Project.assets/Screen%20Shot%202021-11-28%20at%202.08.05%20PM.png)

5. You should see the terminal pane appear in the lower right portion of the main window. The terminal prompt (in my case `dmerfeld workshop: $`) will look different for you, but don't let that be a distraction.
6. Make sure your terminal pane is active by clicking on it to give it focus
7. Now we can issue the command to create the Laravel project in our `web root directory`

```Bash
$ curl -s "https://laravel.build/s1-laravel-project" | bash
```

> Remember to not copy the $ when you execute this command in your terminal. This will be the last time I remind you. :)

8. Laravel will begin to install and do the initial setup of your project. This may take some time, so be patient and do not interrupt the the installation.
9. During the installation, the installer will ask you for your computer password, this will be the password you use to login to your computer. The installer needs this to write files with the proper permissions.
10. We named our project `s1-laravel-project` so that will be the name of our `project root folder` moving forward.
11. You should see a success message, which tells us what our next steps will be.

![Screen Shot 2021-11-28 at 2.22.56 PM.png](S1-04:%20Creating%20a%20Laravel%20Project.assets/Screen%20Shot%202021-11-28%20at%202.22.56%20PM.png)

> You may have noticed the message returned after we installed the new Laravel project says to issue this command: `cd s1-laravel-project && ./vendor/bin/sail up` . This is actually two commands. In the terminal you can issue multiple commands by separating them with `&&`. This will tell the terminal to do each of these commands (in order) and it will wait until one command is completed before moving on to the next. This command tells the terminal to change directory (using `cd`) into our project, then use a `sail` command with an argument of `up` which will start up our Laravel Project.

> ### A brief word about Laravel Sail

> [Laravel Sail](https://laravel.com/docs/8.x/sail) is a command-line interface that allows us to issue commands on our project within the Docker container. For example, if you were to want to see what version of PHP was installed in our container you would use this command: `$ ./vendor/bin/sail php -v` and if you wanted to check what version of PHP you had installed globally on your computer, you would issue the same command, but without the Sail interface part: `$ php -v` .

> We’re going to be using Sail on all of our commands, so you will be prefixing all commands with `./vendor/bin/sail`. Typing the full path to the Sail command-line interface would be tedious, so I recommend setting up an alias in your terminal to allow for the full command `$ ./vendor/bin/sail` to be shortened to just `$ sail` . The steps to adding an alias to your terminal will be different depending on which terminal you have.

> For the purposes of this workshop, I will assume moving forward that you have also setup an alias and I will be showing the Sail command as `$ sail` in all the examples. Just know that if you do not have an alias setup, you’ll have to type the full path wherever I use `$ sail …` .

12. With that out of the way, let's actually spin up our Laravel project with the provided command in the message we got after we installed the project.

```Bash
$ cd s1-laravel-project && ./vendor/bin/sail up
```

![Screen Shot 2021-11-29 at 10.11.55 AM.png](S1-04:%20Creating%20a%20Laravel%20Project.assets/Screen%20Shot%202021-11-29%20at%2010.11.55%20AM.png)

The first time you issue this command it may take some time to install and setup all the various dependencies for our project, so please be patient.

> You may notice that the terminal seems to hang, and never gives you back your terminal prompt to type in more commands. This is because this terminal window is keeping these services up and running. Any output (errors and accessing the website) will appear here in real time. We cannot use this particular terminal window for anything else while it’s in this state. If we close this terminal window, the services will automatically be deactivated. That’s okay, Visual Studio will let us use multiple terminal windows at the same time. I’ll show you how to do that in the next step.

Once the terminal pauses, you should be able to open a browser and go to localhost and see the default Laravel welcome page.

![Screen Shot 2021-11-29 at 10.13.04 AM.png](S1-04:%20Creating%20a%20Laravel%20Project.assets/Screen%20Shot%202021-11-29%20at%2010.13.04%20AM.png)

> If you get an error it’s most likely because you do not have Docker installed and running on your computer. Please make sure to do that, then reissue the `$ sail up` command in your `project root directory`.

> Another issue that may come up is if you’re computer is already using the required ports that Docker needs to serve up the Laravel site. If that is the case, you may see a warning referencing the alternative ports that Docker is using, and you’ll need to append localhost with the port (i.e. `localhost:xx`) where `xx` is the port specified in the warning.

### What Just Happened?

If you’re curious what $ sail up command does, it spun up a Docker container and that container has a number of services installed and running for us. Including PHP, MySQL, MailHog, etc.

You can see the usage and status of all these services in your Docker Dashboard > Containers / Apps screen. It should look something like this.

![Screen Shot 2021-11-29 at 10.27.53 AM.png](S1-04:%20Creating%20a%20Laravel%20Project.assets/Screen%20Shot%202021-11-29%20at%2010.27.53%20AM.png)

Sail also did a check on the database service (MySQL) to make sure it was available and that a database was created to match our project name.

We can check that database in a new terminal window in Visual Studio Code.

To do that, simply click the `+` button in the upper right corner of the terminal window pane.

![Screen Shot 2021-11-29 at 10.44.02 AM.png](S1-04:%20Creating%20a%20Laravel%20Project.assets/Screen%20Shot%202021-11-29%20at%2010.44.02%20AM.png)

Then type the following command in the terminal to verify that MySQL is running and that we can connect to the database.

```Bash
$ sail mysql -u sail -p && password
```

That command starts up mysql using a user Sail setup for us which has a username of `sail` and a password of `password`. You can see I’m using `&&` to issue two commands, the second one provides the password and bypass the prompt which would ask for our password which happens to be `password` in this case.

You should see the mysql prompt `mysql >` if all is well.

![Screen Shot 2021-11-29 at 10.39.14 AM.png](S1-04:%20Creating%20a%20Laravel%20Project.assets/Screen%20Shot%202021-11-29%20at%2010.39.14%20AM.png)

We’re now using a MySQL, and we can issue and MySQL command while we are in this mode.

Show the databases available by issuing the following MySQL command

```sql
> SHOW DATABASES;
```

> Note that you should not copy the prompt `>` and I’m using all `CAPS` for this command and I’m ending the command with a semicolon `;`. These commands are not case-sensitive, but it is tradition to type SQL commands using all caps, so I will be following that convention in this workshop.

> The semicolon is required and it tells MySQL that you’re done issuing commands because it is possible to issue commands that have multiple lines.

You should see something like this after you issue the `SHOW DATABASES` command.

![Screen Shot 2021-11-29 at 10.48.17 AM.png](S1-04:%20Creating%20a%20Laravel%20Project.assets/Screen%20Shot%202021-11-29%20at%2010.48.17%20AM.png)

You can see MySQL returned a list of databases that it knows about. One of which is the named after our project `s1_laravel_project`. That's the database we will be using in this workshop, so let's open it up and see what it contains with the following MySQL command.

```sql
> USE s1_laravel_project;
```

![Screen Shot 2021-11-29 at 10.54.57 AM.png](S1-04:%20Creating%20a%20Laravel%20Project.assets/Screen%20Shot%202021-11-29%20at%2010.54.57%20AM.png)

You should see the message `Database changed` and a new mysql prompt awaiting your next command. Let's see what database tables are in this database.

```sql
> SHOW TABLES;
```

![Screen Shot 2021-11-29 at 10.56.23 AM.png](S1-04:%20Creating%20a%20Laravel%20Project.assets/Screen%20Shot%202021-11-29%20at%2010.56.23%20AM.png)

As you can see our database is empty. That’s okay, that’s what we would suspect, we haven’t done any work.

Let’s exit MySQL and get back our standard terminal

```sql
> EXIT;
```

You should see the standard terminal prompt after you exit out of MySQL.

![Screen Shot 2021-11-29 at 11.03.15 AM.png](S1-04:%20Creating%20a%20Laravel%20Project.assets/Screen%20Shot%202021-11-29%20at%2011.03.15%20AM.png)

### Shutdown Your Environment

At the end of your working sessions, you’ll want to shut down your container. This will turn off all the services you had running.

From your project root folder issue this command in the terminal.

```Bash
$ sail down
```

You’ll see the services shutting down and being removed.

![Screen Shot 2021-11-29 at 11.36.29 AM.png](S1-04:%20Creating%20a%20Laravel%20Project.assets/Screen%20Shot%202021-11-29%20at%2011.36.29%20AM.png)

### Wrapping Up

We’ve covered a lot in this portion of the workshop, let’s take a moment and summarize the work we’ve done thus far.

1. We setup your local environment
2. We installed and ran Docker
3. We installed and ran our first Laravel project
4. We used Sail to access setup and serve our website project
5. We used Sail to run MySQL
6. We checked out the database that was setup for us automatically when we installed the project
7. We closed out our session by issuing the sail down command

