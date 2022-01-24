# S2-04: Deep Dive of the Laravel Project Files and Folders

Now that we know our way around Visual Studio Code and our Laravel project, let’s take a deeper dive into the file structure and setup of our Laravel project. We will again be using the project we created in the first series of the workshop named `s1-laravel-project`.

Open up your project root folder in Visual Studio Code. You should see something like this.

![Screen Shot 2021-11-29 at 11.53.19 AM.png](S2-04:%20Deep%20Dive%20of%20the%20Laravel%20Project%20Files%20and%20Folders.assets/Screen%20Shot%202021-11-29%20at%2011.53.19%20AM.png)

### A Detailed Look at Our Project Files

Let’s take a peek at our sample project that we created so we can see what files do what. We’ll start at the top of the file explorer and work our way down.

1. app - this folder contains some of the app-specific files we will need to use throughout the workshop. There are several folders inside it, open it in the file explorer so we can explore and discuss them all.

![Screen Shot 2021-11-29 at 1.02.22 PM.png](S2-04:%20Deep%20Dive%20of%20the%20Laravel%20Project%20Files%20and%20Folders.assets/Screen%20Shot%202021-11-29%20at%201.02.22%20PM.png)

   - Console - this folder contains just one item for now. A file called `Kernal.php`. We will not be editing that file in this workshop. Eventually we will be adding some of our own console command files, but that will be in the more advanced portion of the workshop, so we can ignore this one for now.
   - Exceptions - this folder contains the exception handlers for our project. These are the files that handle the errors, such as form validation errors, etc. Again, no need to dig into these files right now.

![Screen Shot 2021-11-29 at 2.19.27 PM.png](S2-04:%20Deep%20Dive%20of%20the%20Laravel%20Project%20Files%20and%20Folders.assets/Screen%20Shot%202021-11-29%20at%202.19.27%20PM.png)

   - `Http` - this folder contains some useful sub folders that we will be using right away in our project.
      - Controllers - the files stored in this directory will be responsible for controlling the interactions between our Models and our Views (controllers are the the “C” in MVC)
      - Middleware - middleware files are used in cases where we need to do something before a request is made. For example, if a user makes a page request by clicking on a link, middleware code can run before that request is fulfilled and check to see if the user is authorized to even view that page before the page is even served up. Middleware, as the name would imply, sits between a user and their request.
      - Models - this folder contains our models. You can think of them as a template for a particular set of data. In this case, we have one model already the `User` model describes the attributes and relationships of a user of our site. Models are extremely important and we'll be using a lot of them throughout this workshop. They are the “M" in the MVC.
      - Providers - these files act as a global set of services that we can tap into in various parts of our project. Laravel comes with some services baked in, such as authentication services, that handle how our users are authenticated. We will be adding some of our own services in the advanced portion of this workshop.
2. `Bootstrap` - this folder contains files that Laravel needs to boot up and run, we will not be editing these files at all. It’s safe to ignore this directory.
3. `Config` - these are the configuration files for our Laravel project. These files allow us to set certain settings that are in use by the project code. We may edit some of these, but doing so is not commonplace, so it’s safe to skip past this one.
4. `database` - this folder contains all of the cod related to our database management and testing.
   1. `factories` - these are builders of data models that we can use for testing. We’ll cover these in greater detail when we discuss testing.
   2. `migrations` - this folder contains the database migrations. We will be adding files to this folder when we want to make updates to our database schema. Adding tables, and columns to those tables is all handled through these migration files.
   3. `seeders` - seeder files seed the database with data. You can think of them as a way to put data into your database tables automatically.
5. `public` - this is the folder root folder. Anything in this folder is accessible to the public through a URL. So if your website was `www.mywebsite.com,` then `www.mywebsite.com/css/mycss.css` would be available to the public if that file did indeed live in the public folder.

All of the files outside of the public folder are, by nature, private. The public cannot access them, because the web server will not allow the user to request files that are not in the public folder. This is for security reasons.

6. `resources` - this folder contains all of the front-end resources we will need for our project.
   1. `css` - this file contains all of the pre-processed CSS for our project. These files will be compiled at some point and when they are, they’ll create public versions and be stored somewhere in our `public` folder.
   2. `js` - as with the css folder, this contains our precompiled javascript files. They will be compiled and stored in our `public` folder as we update the js code.
   3. `views` - this directory contains all of the view templates for our project. They are Blade template files (you can see the word blade appear in the file name of the `welcome.blade.php`). We'll go into great detail about how to work with these kind of templates, for now, just know that these templates are used to create the final HTML page that gets served up to the user. In fact, if you take a look at the welcome.blade.php file you'll see the code responsible for the welcome page we saw when we first served up our Laravel project on `localhost`.
7. `routes` - this folder contains our route files. These files are responsible for directing a user requests to a specific file (typically a controller or a view). They use route rules to decide how to fulfill a user request. These files are organized by the kinds of request. for example `web.php` handles all of the web requests, if we had an API, those requests would be handled by `api.php`. Those are the two most commonly used route files. We will be using `web.php` exclusively until we build an api project then we will be using the `api.php` route file as well.
8. `storage` - this directory is used to store asset files.
   1. app/public - this is the folder where asset files are kept. These files might include file uploads, images, etc. These files are kept private so we can control access to them .
   2. `app/framework` - this folder is used by Laravel to store such things as cache, session files, etc. We will not be doing anything with these files in this workshop. For the most part, it’s best to leave these files alone. Laravel uses them and they are autogenerated.
   3. `app/log` - these are the log files generated by Laravel. We may take a peek at one of these log files at some point, because they provide some additional details we might need for debugging our code.
9. `tests` - this folder contains all of our test files. We'll be using and making more of these files when we cover testing our code.
10. vendor - the vendor directory is where all of our composer packages are kept. Never, ever, ever, ever, ever (yes that’s 4x ever) edit these files under any circumstance. These files we installed for us when we created our project, but they are managed by a special thing called a `package manager` (called Composer) which will not be happy if you edit one of their packages. You can think of these packages as bits of code that we get to use in our project but **never edit**.

### Files

Let’s continue our deep dive exploration of the root files that are in our Laravel Project.

1. `.editorconfig` - this file is a preference file for our editor - it can be used to sync up the settings for the editors that our entire team uses when contributing this project. We won’t be doing that in this workshop, so you may safely ignore this file.
2. `.env` - this file is where our environment variables are kept. These variables contain highly sensitive data, so this file is not shared with anyone and any edits made to this file will be ignored by the version control software and apply only to your local environment. We’ll dig into this file a bit more later.
3. `.env.example` - this file is an example file which is provided to all the developers who may contribute to the project. It is meant to show them only the variable names so they may create their own .env file and provide their own values.
4. `.gitattributes` - this is a file used by our version control management software, you may ignore it.
5. `.gitignore` - this is also used by our version control management software (git). It’s a file that lists all of the files that git should ignore. You’ll notice the `.env` file I mentioned listed among the files that git should ignore when tracking and committing changes.
6. `.styleci.yml` - this is a a file Laravel uses and we do not need to edit it in any way.

> You may have noticed the files we’ve looked at thus far all start with a period `.`  this naming convention is to tell your computer's operating system to hide the file. If you visit the same directory using your computer's file browser they may not be visible. Visual Studio Code knows we may need to edit these files (and we do) so it shows them amongst the other files in the directory.

7. `artisan` - this is an executable file that handles all of our artisan commands. You'll be using this executable to setup files in our project, run database migrations, and even run our own custom scripts, among other things.
8. `composer.jso`n + `composer.lock` - this is a set of two files that list the various Composer packages, and their dependencies, that we have installed in our project. These packages have already been installed and they are all contained in the `vendor` folder I mentioned earlier. We won't be directly editing these files, instead we'll be using a command that will take care of all of that. The locked version of this file is not meant to be edited by us at all.
9. `docker-compose.yml` - this is a configuration file that Docker uses to setup our container. It specifies the specific versions of the various services the container uses. For example the specific versions of PHP and MySQL are specified in this file so Docker can manage the installation of those services.
10. `package.json` - this file, much like the compose.json file manages the packages (specifically the node packages) and their dependencies. You may also notice it has a script section which names the scripts we can run to various tasks, such as compile our CSS and JS in our project.
11. `phpunit.xml` - this is a configuration file for our unit testing, specifically the automated testing we’ll be using later in the workshop. We won’t have a need to edit this file.
12. `README.md` - this is a standard markdown file that exists in nearly all software projects. The purpose of the file is to provide instruction on how to install and run the project. We’ll be editing this file when we talk more about documentation.
13. `server.php` - this is the file that Laravel uses to boot up the framework. We will not be editing this file so you may safely ignore it.
14. `webpack.mix.js` - webpack is a technology that can take a set of code (such as postCss, Js, SCSS) and compile and combine it down into one file and save that file to our public folder for use in our website project. We will be adjusting this file in this workshop so you’ll have an opportunity to learn more about `webpack` and how it works.

